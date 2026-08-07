> 本文为 Paradox Wiki 页面「Nudger」的中文翻译，原文: Paradox Wiki「Nudger」

这是由社区维护的 wiki。如果你发现错误，请帮助修正。

微调器（Nudger）是《钢铁雄心4》中用于创建地图相关文件的工具。

## 目录

  * 1 概述
  * 2 州
    * 2.1 建筑
  * 3 战略区域
    * 3.1 天气
      * 3.1.1 天气位置
      * 3.1.2 天气概率
  * 4 数据库
  * 5 单位
  * 6 补给
  * 7 参考与注释

微调器可通过 [nudge 控制台命令](</Console_commands#nudge> "Console commands") 访问，或在主菜单中点击"Nudge!"按钮进入——该按钮只有在启动游戏时使用了 [调试启动选项](</Modding#Debug_advantages> "Modding") 才会出现。启动后，它会显示世界的默认状态，即被设定为默认的 [剧本书签](</Bookmark_modding> "Bookmark modding") 中呈现的样子。如果没有默认书签，地图将显示为没有任何国家。

微调器总是将文件直接输出到 [用户目录](</User_directory> "User directory")，例如 C:/Users/username/Documents/Paradox Interactive/Hearts of Iron IV/（Windows 上未使用 OneDrive 时的默认位置）。**除非移动这些文件，否则打开原版游戏时生成的这些文件会被加载**，可能导致游戏崩溃，至少也会造成地图定义错误，使游戏在非调试模式下自行关闭。这可能包括 history/states/、map/ 和 localisation/english/ 目录。

## 概述

|  这是由社区维护的 wiki。如果你发现错误，请帮助修正。  
---|---

  * 州：用于编辑州的历史文件。它不会修改州级建筑模型位置，而新州要正常工作必须有这些位置数据。
  * 补给区：补给区在 1.11 版本（即随 [No Step Back](</No_Step_Back> "No Step Back") DLC 推出的更新）中被移除，该按钮目前已失效。
  * 战略区域：编辑战略区域的边界，以及（对海军区域而言）地形和修正。
  * [环境物体](</Ambient_objects> "Ambient objects")：编辑在世界地图上持续渲染的 3D 模型，最显著的是南北两侧的边界。
  * 数据库：编辑 /Hearts of Iron IV/map/definition.csv 文件，用于省份信息。
  * 天气：编辑战略区域内的天气，以及用于天气效果的网格（mesh）位置。
  * 建筑：用于定位建筑模型、为海军基地和浮港指定相邻的沿海省份，并为每个州指定一个放置空军基地和火箭发射基地的省份。**如果创建新州时未调整此项，将导致崩溃。**
  * 单位：用于确定单位模型和胜利点的精确位置。
  * 补给：用于定位补给中心和铁路的起始位置与等级。
  * 离开微调器（Leave nudge）：尝试返回主菜单。如果微调器是通过控制台而不是主菜单进入的，会导致直接崩溃到桌面。

## 州

_另见：[州修改](</State_modding> "State modding")_

微调器的州部分用于定义州的边界和名称。任何州边界更改也会自动更改覆盖这些州的战略区域的边界；对于新州，其省份会完全从战略区域中移除。在用户目录内，它编辑 /Hearts of Iron IV/history/states/ 和 /Hearts of Iron IV/map/strategicregions/ 文件夹，以及 /Hearts of Iron IV/localisation/english/state_names_l_english.yml 文件（针对英语）。
**微调器会移除州文件中的引号，但 `name` 属性除外。** 这可能会破坏位于引号内的其余脚本。最常见的是，这会破坏任何 [has_dlc](</Triggers#has_dlc> "Triggers") 检查，导致此后整个州脚本失效。
**微调器会将没有版本号的[本地化](</Localisation> "Localisation")值解释为版本 -1**，并将其写入输出。由于游戏只期望版本号为数值，这会使本地化在这一点之后失效，报错形如 `Expected quotation mark (") at line 113 and column 16 in ...`。

点击省份用于选中一个省份。选中省份后，`⇧Shift` 点击某个省份会触发以下行为，具体取决于所选省份和被点击的省份：

  * 如果所选省份位于某个州内，而被 Shift 点击的省份位于不同的州或不属于任何州，游戏会调整该州和战略区域的边界以覆盖被 Shift 点击的省份，并将其选中。
    * 如果被 Shift 点击的省份不属于任何州，它会被添加到该州的战略区域中，而不检查它是否已在其中。**这可能导致该省份在同一战略区域中被定义两次，或在两个不同的战略区域中被定义。** 这必须手动修复。
  * 如果所选省份位于某个州内，而被 Shift 点击的省份位于同一州，它将从该州和战略区域中被移除，且不会被选中。如果所选省份不属于任何州，也会发生同样的情况。
  * 如果所选省份和被 Shift 点击的省份都不属于任何州，它会被添加到选中集合中。
  * 如果被 Shift 点击的省份已被选中，它将从选中集合中移除，并且（如果它位于某个州内）从当前所在的州中移除。

如果选中了一个或多个不属于任何州的省份，就可以创建新州。这需要在文本框中输入州名并选择"Create state"。

  * **州名必须只包含可安全用于文件名的 [ASCII](<http://en.wikipedia.org/wiki/ASCII> "wp:ASCII") 字符**。如果存在任何非 ASCII 字符，例如变音符号或非拉丁文字，游戏会直接崩溃到桌面而不是创建州，但在此之前它会先从旧州中移除这些省份并保存那里的更改。在 Windows 上，无法用于文件名的字符包括 `\ / : * " < > |`。
  * **创建新州（偶尔编辑州边界也需要）要求更改建筑模型位置以及机场/火箭发射基地位置，以避免崩溃**。

如果选中的省份位于某个州内，可以选择"Open file"（打开文件）或"Delete state"（删除州）：

  * 打开文件会用系统默认的 .txt 文本编辑器打开用户目录中的该州文件。如果用户目录中没有该文件，会创建一份副本。这份副本不包含微调器中做出的更改，而是包含上次获取文件时（通过打开游戏、"Update"或"Save"）载入内存的内容。如果使用了该按钮且用户目录中的文件版本已被删除，该按钮将不执行任何操作，直到下次获取州文件。
  * "Delete state"（删除州）不一定会删除州本身，而是从用户目录中移除该文件（如果存在），并从内存中卸载在微调器中对其做出的所有更改。这还会使游戏尝试读取与该州 ID 相关的州文件：如果 [已加载文件](</Modding#Loading_files> "Modding") 中含有相同 ID 的州，则会使用它作为该州的信息，否则该州将被删除。

在始终可选的按钮中，还有"Delete all empty"（删除所有空州）和"Find collision"（查找冲突）。

  * "Delete all empty"（删除所有空州）的工作方式与删除单个州类似：它会检查所有在内存中没有省份的州（会考虑未保存的更改）。如果找到任何这样的州，它会被从内存和用户目录中删除。之后，游戏会尝试为每个被删除的州查找一个文件作为新的州信息。
  * "Find collision"（查找冲突）检测同时位于多个州中的省份。按下后，它会将玩家镜头移动到一个这样的省份上，并让玩家选择它必须保留在哪个州中；做出选择后，它将被从其他所有州中移除。

"Update"用于忽略所有未保存的更改，并重新读取 [已加载文件](</Modding#Loading_files> "Modding") 中的州文件。如果州边界被手动修改过，例如将输出从用户目录移动到 mod 文件中，就必须这样做才能在无需重启游戏的情况下加载它们。
"Save"用于将所有更改写入用户目录。完成后，更改将从内存中清除，游戏将重新读取 [已加载文件](</Modding#Loading_files> "Modding") 中的州文件。**如果用户目录中的州文件被 mod 文件覆盖或卸载，会看起来（部分）更改立即还原了，但它们仍然存在于用户目录中。** 这就需要将文件移动到 mod 的文件中，并用"Update"更新游戏状态。保存后，用户目录中只会创建或更改自上次获取文件以来有改动的文件。

### 建筑

|  请帮助改进本文章或小节，[**扩充其内容**](<https://hoi4.paradoxwikis.com/index.php?title=Nudger&action=edit>)。  
---|---

  * 

## 战略区域

|  请帮助改进本文章或小节，[**扩充其内容**](<https://hoi4.paradoxwikis.com/index.php?title=Nudger&action=edit>)。  
---|---

  * 

### 天气

_另见：[地图修改 § 天气](</Map_modding#Weather> "Map modding")_



天气部分中的仪器

微调器的天气部分用于决定战略区域在某时间段内天气相关省份修正出现的概率，以及确定天气效果粒子生成器的位置。它编辑 /Hearts of Iron IV/map/strategic_regions/ 和 /Hearts of Iron IV/map/weatherpositions.txt。

天气仪器大致可分为 4 组：最左侧的效果预览（独立窗口）、左侧的天气概率、右侧的时间段选择，以及最右侧的天气位置修改。对于天气位置，使用最左侧的效果预览和最右侧的天气位置修改；对于修改省份修正的概率，使用左侧的天气概率和右侧的时间段选择。

只有在选中战略区域时才能修改天气。选中区域后，代表天气位置的黄色指示器会显示在地图上，并带有文字"small"或"big"来表示生成粒子的体积。

左下角的"Save"会将所有更改输出到用户目录，"Cancel"会清除内存中的所有更改，而左上角的"Reload regions"会清除内存中的更改并重新读取战略区域和天气位置文件。

#### 天气位置

最右侧的菜单用于天气相关粒子效果的位置，具体使用以下按钮：

  * "Random In Selected"（在选区内随机分布）选中后，会随机改变现有天气位置的位置，使其均匀分布在该战略区域的范围内。这不会改变天气位置的数量或大小。但如果该战略区域没有任何天气位置，微调器会先创建 2 个大天气位置。
  * "Random In All"（全部随机分布）会使用与上面相同的过程随机化每个战略区域的天气位置。
  * "Add"（添加）用于创建新的天气位置。创建后，它会位于该战略区域中省份 ID 最小的省份的中心。
  * 下方的天气位置列表允许选中单个天气位置。选中某个后，地图会高亮显示所选天气位置为红色，并隐藏所有其他位置。右键点击某个位置可以将其移动至该处。
  * "Set Small"（设为小）和"Set Big"（设为大）更改天气位置发射粒子的体积。如果选中了某个天气位置，则只有它会改变大小。否则，所选战略区域中的每个天气位置都会改变大小。
  * "Remove"（移除）用于从区域中删除天气位置。

"Preview effects"（预览效果）用于预览粒子效果在游戏中的样子。一个战略区域一次只能选中一种天气效果，但可以同时在多个战略区域上播放。**必须在图形设置中启用天气，此功能才有效**。选中效果后，会显示天气位置生成的与之相关的粒子。"Disable"（禁用）用于移除当前所选战略区域的粒子效果，而"Disable All"（全部禁用）会移除每个战略区域中的效果。

#### 天气概率

天气概率由时间段决定。右侧是一个时间段列表，"Add Period"（添加时间段）会在列表中创建一个新时间段。右上角的复选框用于选中当前天气时间段，而"Delete"（删除）会将其移除。"Between Day.Month"用于应用该时间段生效的时间，格式为 Day.Month。第一天和第一月均从 1 开始计数，范围包含首尾两端。"Apply"（应用）用于将更改应用到时间段长度上，否则这些更改会在创建新时间段时重置且不会保存。
**微调器中的时间段并非区域专属**：编辑时间段长度以及添加/删除时间段会影响到每一个战略区域。左下角的"Check errors"（检查错误）用于检查与时间段相关的错误，例如某些天没有时间段覆盖，或同时被多个时间段覆盖。

左侧的菜单允许更改温度范围以及每种天气修正的概率。要编辑这些，必须先选中一个战略区域和一个时间段。其中大多数是滑块，允许设置 0 到 1 之间（含两端）的值。以下例外：

  * "Temperature"（温度）是该区域的温度范围。它允许将最低和最高温度设为两个独立范围。极冷或极热的温度会按 [天气 § 温度](</Weather#Temperature> "Weather") 中列出的范围修正战斗。
  * "Mud"（泥泞）旁边的按钮可以将 Arctic Water（北极水域）的概率立即改为 1.00 或 0.00。这对于将大量海洋战略区域设置为在某个时期始终结冰通常很有用。

## 数据库

_另见：[省份修改](</Province_modding> "Province modding")_

微调器的数据库部分用于编辑用于省份定义的 /Hearts of Iron IV/map/definition.csv 文件。在用户目录内，它编辑 /Hearts of Iron IV/map/definition.csv.cache 和 /Hearts of Iron IV/map/definition.csv.fixed.csv 文件。尽管名称不同，"fixed"（修正后）的省份定义仍会被读取，并且优先于 definition.csv。

**如果还没有开始过一局游戏，数据库部分的大多数按钮都很可能导致游戏崩溃。** 建议开始一局新游戏，经历选择国家和开始一局的流程，退出到主菜单，然后从那里进入微调器。使用控制台命令可以避免崩溃，但在尝试离开微调器时会导致游戏崩溃。
大多数可选的按钮都会以某种方式改变世界地图的显示。然而，在更改一次之后，世界地图保持静态，不会反映微调器中的任何更改，也无法切换为其他选项。要刷新地图状态，可以切换到其他标签页退出微调器的数据库部分，或完全退出微调器。

数据库部分包含以下按钮：

  * "Generate RGB"（生成 RGB）用于提供一个肯定尚未出现在 map/definition.csv 文件中的 RGB 代码。这可以用来安全地修改省份位图，而不必担心重叠。
  * "Save"（保存）用于将更改输出到 /Hearts of Iron IV/map/definition.csv.fixed.csv 文件。
  * "Cancel"（取消）清除所有存储在内存中但尚未保存的省份定义更改，并取消选择所有选项。
  * "Type"（类型）用于决定省份是海洋、湖泊还是陆地省份。选择三个选项之一并点击某个省份会更改其类型。请注意，更改沿海状态也是必须的。世界地图会切换为完全空白、没有任何国家的地图模式，但可以通过提示（tooltip）来确定省份的当前类型。
  * "Coastal"（沿海）用于更改省份定义文件中省份是否被视为沿海。点击某个省份会使其变为沿海，如果它已经是沿海则不改变任何内容。此模式下可用的"Generate"（生成）会根据相邻省份的类型自动为每个省份分配沿海状态。地图模式变为空白模式，沿海省份用黄色条纹标记。
  * "Continent"（大陆）用于为省份分配大洲。默认选中第一个大洲，但可以通过显示大洲列表的按钮更改。点击某个省份会将省份的大洲改为当前选中的大洲，并将底部文字改为"Continent"。尽管如此，之前选中的大洲仍会用于涂色省份，点击另一个省份也会更改其大洲。地图模式中每个大洲显示各自的颜色。AI 区域不会在微调器内更新，因此涂色后检查大洲的唯一方法是切换到另一个标签页来重置地图。
  * "Show colors"（显示颜色）将地图模式更改为将 map/provinces.bmp 中每个省份的颜色以条纹形式叠加显示在省份上。

## 单位

|  请帮助改进本文章或小节，[**扩充其内容**](<https://hoi4.paradoxwikis.com/index.php?title=Nudger&action=edit>)。  
---|---

  * 

## 补给

_"Supply hub"（补给中心）与"Supply node"（补给节点）在此可互换使用_

_另见：[地图修改 § 补给](</Map_modding#Supply> "Map modding")_

微调器的补给部分用于确定 [补给中心](</Supply_hub> "Supply hub") 和 [铁路](</Railway> "Railway") 的起始位置与等级。它编辑 /Hearts of Iron IV/map/supply_nodes.txt 和 /Hearts of Iron IV/map/railways.txt 文件。**如果这两个文件中的任何一个包含指向不存在或不属于任何州的省份的引用，微调器会在选中该标签页时崩溃。**

选中它后，微调器会显示世界地图，并高亮补给相关的建筑，具体如下：

  * 如果某省份至少有 2 个胜利点，它会有一个小的青色旋转指示器。
  * 如果该省份是该国的首都，它会有一个大的黄色旋转指示器。
  * 如果该省份有海军基地，它会有一个中等深蓝色旋转指示器。如果该省份是首都，则不显示。
  * 如果该省份在开局时建有补给中心，它会有一个中等粉红色旋转指示器。如果该省份是首都或有海军基地，则不显示。
  * 所有铁路都表示为连接省份的灰色边。

指示器和铁路网络的节点使用 [unitstacks](</Unitstacks> "Unitstacks") 中"Standstill"模型的位置放置。如果找不到，则重置为默认的省份中心位置。

点击一个有现有铁路的省份会选中该铁路。如果发生重叠，只有最先定义的铁路可以被选中。这允许通过 ∧ 和 ∨ 符号更改其等级，以及通过"Delete Railway"（删除铁路）将其删除。

按住 `Ctrl` 时，编辑模式启用。松开 `Ctrl` 会立即退出编辑模式，无论处于何种情境。如果有选中的尚未创建的铁路，其路径将丢失。编辑模式允许以下操作：

  * 如果没有任何选中项，点击位于非不可通行州内的陆地省份会将其选中。这允许创建或编辑补给节点：
    * 如果补给节点不存在，"Create Node"（创建节点）会以指定等级创建一个。如果等级高于 /Hearts of Iron IV/common/buildings/ 中设置的最大等级，则会被限制为最大值。
    * 如果补给节点存在，"Delete Node"（删除节点）会将其从内存和文件中移除。
    * 如果补给节点存在，"Update Node"（更新节点）会更改节点的建筑等级。如果等级高于 /Hearts of Iron IV/common/buildings/ 中设置的最大等级，则会被限制为最大值。
  * 如果只选中了一个省份，再次点击它会取消选中。
  * 如果只选中了一个省份，点击另一个与之有可行陆地连接的省份，游戏会在两个省份之间生成一条路径（位于陆地省份上、不穿越任何不可通行州），并选中一条沿该路径的铁路。但创建的铁路尚未真正创建。路径可能会穿越个别省份之间的不可通行边界。
  * 如果选中了一条铁路，无论它是否存在，点击铁路最边缘的省份会将该省份从铁路中移除，而点击与任一端接壤、不在铁路中的可行陆地省份会扩展铁路路径以覆盖该省份。如果发生重叠，最后添加到铁路的省份优先。要让现有铁路被选中，必须在进入编辑模式之前选中它。
  * 如果选中了一条不存在的铁路，"Add Railway"（添加铁路）会将其创建到世界地图上，使其可以在游戏中和编辑模式外存在。其创建等级与菜单右侧输入的等级相同。如果所选等级高于铁路的最大可能等级，则重置为 1。

"Update"仅重新读取 /Hearts of Iron IV/map/supply_nodes.txt 文件，忽略对铁路的更改。**这不能删除现有的补给节点**，只能创建新的补给节点或更改其建筑等级。要从内存中移除补给节点，必须逐个删除。
"Load"用于忽略所有未保存的更改，并重新读取 [已加载文件](</Modding#Loading_files> "Modding") 中的补给文件。如果铁路被手动修改过，例如将输出从用户目录移动到 mod 文件中，就必须这样做才能在无需重启游戏的情况下加载它们。与"Update"类似，这不能从内存中删除补给节点。
"Save"用于将所有更改写入用户目录。完成后，对铁路的更改将从内存中清除，游戏将重新读取 [已加载文件](</Modding#Loading_files> "Modding") 中的补给文件。**如果用户目录中的铁路文件被 mod 文件覆盖或卸载，会看起来（部分）更改立即还原了，但它们仍然存在于用户目录中。** 这就需要将铁路文件移动到 mod 的文件中，并用"Load"更新游戏状态。与"Update"类似，这不能从内存中删除补给节点。

## 参考与注释

**[Modding](</Modding> "Modding")**

文档  | [效果](</Effect> "Effect") • [触发器](</Conditions> "Conditions") • [Defines](</Defines> "Defines") • [修正](</Modifiers> "Modifiers") • [修正列表](</List_of_modifiers> "List of modifiers") • [作用域](</Scopes> "Scopes") • [本地化](</Localisation> "Localisation") • [事件动作](</On_actions> "On actions") • [数据结构](</Data_structures> "Data structures")（[Flags](</Data_structures#Flags> "Data structures")、[事件目标](</Data_structures#Event_targets> "Data structures")、[国家标签别名](</Data_structures#Country_tag_aliases> "Data structures")、[变量](</Data_structures#Variables> "Data structures")、[数组](</Data_structures#Arrays> "Data structures")）
---|---  
脚本  | [成就](</Achievement_modding> "Achievement modding") • [AI](</AI_modding> "AI modding") • [AI 国策](</AI_focuses> "AI focuses") • [自治州](</Autonomy_state_modding> "Autonomy state modding") • [势力平衡](</Balance_of_power_modding> "Balance of power modding") • [书签/剧本](</Bookmark_modding> "Bookmark modding")（[游戏规则](</Bookmark_modding#Game_rules> "Bookmark modding")）• [建筑](</Building_modding> "Building modding") • [角色与特质](</Character_modding> "Character modding") • [外观标签](</Cosmetic_tag_modding> "Cosmetic tag modding") • [国家](</Country_creation> "Country creation") • [师](</Division_modding> "Division modding") • [决议](</Decision_modding> "Decision modding") • [学说](</Doctrine_modding> "Doctrine modding") • [装备](</Equipment_modding> "Equipment modding") • [事件](</Event_modding> "Event modding") • [阵营](</Faction_modding> "Faction modding") • [理念](</Idea_modding> "Idea modding") • [意识形态](</Ideology_modding> "Ideology modding") • [军事工业组织](</Military_industrial_organization_modding> "Military industrial organization modding") • [国策](</National_focus_modding> "National focus modding") • [资源](</Resources_modding> "Resources modding") • [脚本 GUI](</Scripted_GUI_modding> "Scripted GUI modding") • [科技与学说](</Technology_modding> "Technology modding") • [单位](</Unit_modding> "Unit modding")
---|---  
地图  | [地图](</Map_modding> "Map modding") • [州](</State_modding> "State modding") • [补给区](</Supply_areas_modding> "Supply areas modding") • [战略区域](</Strategic_region_modding> "Strategic region modding")
---|---  
图形  | [界面](</Interface_modding> "Interface modding") • [图形资源](</Graphical_asset_modding> "Graphical asset modding") • [实体](</Entity_modding> "Entity modding") • [后处理效果](</Posteffect_modding> "Posteffect modding") • [粒子](</Particle_modding> "Particle modding") • [字体](</Font_modding> "Font modding")
---|---  
外观  | [肖像](</Portrait_modding> "Portrait modding") • [人名表](</Namelist_modding> "Namelist modding") • [音乐](</Music_modding> "Music modding") • [声音](</Sound_modding> "Sound modding")
---|---  
其他  | [控制台命令](</Console_commands> "Console commands") • [故障排查](</Troubleshooting> "Troubleshooting") • [Mod 结构](</Mod_structure> "Mod structure") • [Mods](</Mods> "Mods") • 微调器
---|---
