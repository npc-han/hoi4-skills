> 本文为 Mathieu Ropert《What makes a game tick? Part 4 - Gameplay Script Performance》的中文翻译，原文: Paradox Wiki「Script performance」

# 游戏是如何运转的？第 4 部分 —— 游戏玩法脚本性能（Gameplay Script Performance）

> 来源: https://mropert.github.io/2025/05/27/making_games_tick_part4/ | 作者: Mathieu Ropert（Paradox 前技术总监）| 抓取 2026-08-08


让我们来聊聊游戏模拟。今天我们要看一些我不得不修复的真实脚本性能问题。

在[上一篇文章](/2025/05/07/making_games_tick_part3/)中，我提出过一个观点：游戏中的脚本常常成为性能瓶颈的来源，因为它是一种强大的、类似代码的工具，而使用它的人通常没有受过程序员训练。说实话，我原本预计会招来更多抨击，但出乎我意料的是，评论大多是在指出那些找到了通过限制脚本来获得更好性能的游戏（尽管有趣的是，它们都使用自研引擎，而不是那些最常见的授权引擎）。所以我想，本周我们就来看一些「糟糕」的脚本，并讨论我们是如何走到这一步的。我希望这能给我身为设计师和程序员的读者们带来一些启发。

这些例子将取材于我在 Paradox 游戏中不得不修复的真实问题。如果你好奇，大概可以在你 Steam 文件夹里的游戏文件中找到原版（除非你是通过 Microsoft Store 购买的游戏——但究竟为什么有人会这么做呢？！）。

## 费尽周折找保加利亚

钢铁雄心 IV 是一款关于第二次世界大战的游戏，我曾担任其技术负责人多年。世界上的每个国家都由一个实体表示，而这些实体中的每一个（在许多许多其他事情之外）都可以从一组取决于游戏状态的可用选项中做出政治决策（decision）。设计师们一直在添加新的决策，为游戏带来更多风味和内容。下面这个决策适用于这样的情形：你正在玩保加利亚，想向你的盟友让渡铀矿开采权，帮助他们推进原子研究计划：


    target_trigger = {
    	FROM = {
            is_in_faction_with = ROOT
            OR = {
                is_faction_leader = yes
                AND = {
                    is_major = yes
                    BUL = { is_faction_leader = yes }
                }
            }
        }
        controls_state = 48
    	has_completed_focus = BUL_uranium_prospecting
    }

我猜我的大多数读者并不熟悉 PDS 脚本之美，所以让我带你过一遍。这个 `target_trigger` 是一个条件，它必须为真，决策才能可用并显示在国家政治决策界面中。游戏中的每一天，每个国家实体都会针对其他每个国家实体检查此触发器，看它是否为真。接下来，我们要做 3 项检查。

  1. `FROM` 块有点冗长，但它的核心就是检查「我是否与对方国家在同一个阵营」以及「我们之中是否有一个是阵营领袖或主要国家」。
  2. 我是否控制着 48 号省份（索菲亚/维丁地区）。
  3. 我是否完成了国策 `BUL_uranium_prospecting`，其核心是：我作为保加利亚，是否通关了那段开启铀加工的内容。

你现在看到问题了吗？让我们写一段等效的伪 C++ 代码：


    for ( const auto& ourCountry : AllCountries )
    {
        for ( const auto& theirCountry : AllCountries )
        {
            if ( ourCountry != theirCountry && ourCountry.getFaction() == theirCountry.getFaction()
                && ( ourCountry.isFactionLeader() || theirCountry.isFactionLeader() )
                && ourCountry.controlsState( 48 ) && ourCountry.hasCompletedFocus( "BUL_uranium_processing" ) )
            {
                // Decision available, add it to the UI...
            }
        }
    }

这种方式更清楚地表明这是一个二次方算法（复杂度随国家数量的平方增长），而且它本不必如此。在这个实现中，每个国家都要针对其他每个国家检查一个始终为真或始终为假的条件（我是保加利亚吗、我是否完成了铀加工内容、我是否控制着索菲亚）。这是常见的循环/条件错误，大多数软件工程师在大学里都应该遇到过（并学会不再重犯）。一个好的静态分析器甚至可能发现它（尽管考虑到遗留 C++ 代码对副作用和 `const_cast` 的滥用程度，它可能会产生误报）。

在 C++ 版本中，修复起来很容易，像这样的简单重构应该就能解决问题：


    for ( const auto& ourCountry : AllCountries )
    {
        if ( ourCountry.controlsState( 48 ) && ourCountry.hasCompletedFocus( "BUL_uranium_processing" ) )
        {
            for ( const auto& theirCountry : AllCountries )
            {
                if ( ourCountry != theirCountry && ourCountry.getFaction() == theirCountry.getFaction()
                    && ( ourCountry.isFactionLeader() || theirCountry.isFactionLeader() ) )
                {
                    // Decision available, add it to the UI...
                }
            }
        }
    }

我们可以在 `if` 块末尾加上一个 `break`，因为这个条件同一时间只能由一个国家满足，但这带来的性能提升远不及添加外层 `if`。而且我个人并不太喜欢 `break`（或 `continue`）。

## 修复脚本

显然，原始代码并不是 C++，它是游戏脚本的一部分。而它目前向设计师暴露的方式，使他们只能编写一个在内层循环中被检查的条件。要解决这个问题，需要脚本编写者能够添加第二个触发器，这个触发器在每个国家上只检查一次，并且在内层循环执行之前进行检查。这就是我们当时的修复方式：我们为每个决策添加了一个可选的脚本块，名为 `target_root_trigger`，当它为假时，整个内层循环都会被跳过。

这说明了一个关于脚本绑定的要点：它们需要在便利性与性能之间取得平衡。看看原脚本的这个「修复版」：


    target_trigger = {
    	FROM = {
            is_in_faction_with = ROOT
            OR = {
                is_faction_leader = yes
                AND = {
                    is_major = yes
                    BUL = { is_faction_leader = yes }
                }
            }
        }
    }
    
    target_root_trigger = {
        controls_state = 48
    	has_completed_focus = BUL_uranium_prospecting
    }

它更快吗？是的，快非常多。但如今我们必须向每位设计师讲解 `target_trigger` 与 `target_root_trigger` 的区别，这可能会变得难以招架。钢铁雄心 IV 当前版本中，决策拥有六种不同的触发器，其中一半存在的目的仅仅是为了过滤掉需要求值的脚本触发器空间。它们不会改变游戏行为，只是向脚本系统提供的性能提示。

## 更进一步

那么这能被自动化吗？理论上可以。只要有足够的元数据，系统就可以根据条件语句所依赖的数据对它们进行分类，然后只在必要时运行它们。

这需要能够拆分表示这些 `target_trigger` 中每一个的 [AST（抽象语法树）](https://en.wikipedia.org/wiki/Abstract_syntax_tree)：提取出那些只依赖于外层循环国家的语句，并在内层循环之外执行它们。在上述案例中，这应该不太困难，因为我们可以清楚地看到 `FROM` 块中的部分使用了内层循环变量（在这个例子中，`FROM` 是内层国家，`ROOT` 是外层国家——解释为什么脚本语言要这样设计需要好几篇文章，而且我也不确定自己能回答所有问题）。我们还需要检查那些把 `FROM` 用在右侧的表达式（在本例中不存在）。

假设我们有办法对脚本引擎进行单元测试，就可以带着一定的把握引入这种自动化优化。当然，由于该功能的大部分开发成本并不真的与这一特定的脚本/代码边界挂钩，我们应该考虑把它应用到所有在循环中大量运行脚本触发器的场景。这会有很高的前期成本，但从长远来看很可能会得到回报，因为这不仅会加速游戏，还会为设计师简化脚本系统。

## 结束语

我原本打算在这篇文章中放入更多例子，但我们已经快达到我设定的篇幅了，所以我把它们留到下一篇，让这个系列保持规律而易于消化。下一篇我们将看到另一个真实的性能问题，这次来自美术脚本（art script）。

这篇文章花了一段时间才完成的另一个原因是，我开始了[咨询](</consulting>)业务。如果你需要性能编程方面的专业知识，或需要 C++ 培训，欢迎随时联系我！
