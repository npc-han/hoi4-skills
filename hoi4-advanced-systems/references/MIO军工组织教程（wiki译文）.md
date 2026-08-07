> 本文为 Paradox Wiki 页面「Military industrial organization modding」的中文翻译，原文: Paradox Wiki「Military industrial organization modding」

这是一个由社区维护的 Wiki。如果你发现错误，请帮忙修正。

[军工组织（MIO）](</Military_industrial_organization> "Military industrial organization")代表你国家的应用研究与开发。它们会提升其专注领域的科研速度，并修改在其协助下生产的装备的属性。

## 目录

  * 1 警告
  * 2 设计一个 MIO
    * 2.1 你能修改什么？
    * 2.2 文件与文件夹
    * 2.3 错误日志
  * 3 完整的 MIO（Full MIO）
    * 3.1 结构布局
    * 3.2 属性说明
    * 3.3 特性布局
    * 3.4 设计一个特性
    * 3.5 初始特性
    * 3.6 特性属性说明
  * 4 Delta MIO
  * 5 政策
  * 6 AI 加成权重
  * 7 附录：加成
    * 7.1 装备加成
    * 7.2 生产加成
    * 7.3 组织加成
  * 8 参考

## 警告

军工组织及其政策只有在 _创建新游戏时_ 才会加载。无法向正在进行的游戏中注入新的 MIO/政策——在进行开发/测试时，你必须始终开一局新游戏。

你可以自由修改某个特性授予的加成、使用的图标或本地化。添加特性也是可以的，但移除现有特性可能导致错误。

## 设计一个 MIO

### 你能修改什么？

从根本上说，MIO 制作有四个方面可以修改：

  * 一个完全独立的军工组织（Full MIO，或称为原型 Archetype）
  * 一种基于其他 MIO 的 MIO 修改，可添加、移除或更改授予的特性（Delta MIO）
  * 一种可以应用到 MIO 上的政策（通常在达到 6 级之后）
  * AI 加成权重（AI Bonus Weights），用于控制 AI 如何优先选择特性授予的加成

### 文件与文件夹

MIO 定义在 /Hearts of Iron IV/common/military_industrial_organization/organizations/ 中，你可以在任意数量的 txt 文件中放置任意数量的 MIO，文件须以 UTF8 编码且 _不带_ 字节顺序标记（BOM）。例如，游戏自带一个默认的 "generic organization" 文件，其中包含基础模板，然后每个对该基础有改动/扩展的 TAG 各有一个文件。

政策定义在 /Hearts of Iron IV/common/military_industrial_organization/policies/ 中，同样可以在任意数量的 txt 文件中放置任意数量的政策，文件须以 UTF8 编码且 _不带_ 字节顺序标记（BOM）。

最后，AI 加成权重定义在 /Hearts of Iron IV/common/military_industrial_organization/ai_bonus_weights/ 中。

在游戏的这三个目录中，你都可以找到一个 "documentation.info" 文本文件，其中记录了设计师的一些思路和一些指引。

### 错误日志

虽然这对大部分制作方面都适用，但检查错误日志在制作 MIO 时比其他任何时候都更加关键：大多数错误都影响游戏开局、游戏一旦运行就无法修复，加上 MIO 对游戏有长期影响，这些事实很容易让你浪费大量时间，最后却只能重新开局。

你可以在 "%userprofile%\Documents\Paradox Interactive\Hearts of Iron IV\logs" 中找到 error.log 文本文件。在游戏启动后、浏览 MIO 菜单时（浏览菜单会触发与可见性过滤器相关的错误），请先检查它。

## 完整的 MIO（Full MIO）

### 结构布局

    <MIO 名称> = {
    	# 可选的本地化键
    	name = LOC_infernal_mio_tanks
    	icon = GFX_key
    
    	equipment_type = { <装备类型> <装备类型> }
    	research_categories = { <科研类型> <科研类型> }
    
    	# 必填。在国家作用域中判定。
    	allowed = <条件>
    	# 可选。在 MIO 作用域中判定。FROM = 国家。
    	visible = <条件>
    	# 可选。在 MIO 作用域中判定。FROM = 国家。
    	available = <条件>
    
    	# 如需要，可添加额外事件
    #	on_design_team_assigned_to_tech = { ... }
    #	on_design_team_assigned_to_variant = { ... }
    #	on_industrial_manufacturer_assigned = { ... }
    #	on_tech_research_cancelled = { ... }
    #	on_tech_research_completed = { ... }
    #	on_industrial_manufacturer_unassigned = { ... }
    
    	# 指派科研的加成
    	research_bonus = 0.2
    	# MIO 可以同时执行多少个任务？
    	task_capacity = 999
    
    	# 提供自定义 AI 权重，帮助 AI 选择合适的 MIO
    #	ai_will_do = {
    #		...
    #	}
    
    	# 表头按需任意添加
    	tree_header_text = {
    		text = "Label1" # 可以是纯文本或本地化键
    		x = 0 # 标签在表头栏上的位置。0 = 最左，9 = 最右
    	}
    	tree_header_text = {
    		text = "Label2"
    		x = 2
    	}
    	tree_header_text = {
    		text = "Label3"
    		x = 4
    	}
    	tree_header_text = {
    		text = "LabelN"
    		x = 8
    	}
    
    	initial_trait = <初始特性>
    
    	trait = <特性>
    	trait = <特性>
    	trait = <特性>
    	# ... 按需继续添加
    }

### 属性说明

完整的 MIO 的属性 键 | 必填？ | 描述 | 示例   
---|---|---|---  
name | false | 若提供，将按以下顺序解析 MIO 的显示名称：1. <tag>_<name> 2\. <name> | name = LOC_infernal_mio_tanks   
icon | false | MIO 在界面中使用的图标 | icon = GFX_idea_generic_tank_manufacturer_1   
background  | false  | 详细信息窗口的背景图片。若未指定，将根据支持的装备类型使用标准背景之一 | background = GFX_key   
equipment_type | true | MIO 影响的装备类型列表。用于选择 MIO 可以应用到哪些生产线，以及特性中的各种加成会影响什么 | equipment_type = { mio_cat_eq_all_light_tank mio_cat_eq_all_medium_tank }   
research_categories | true | 受 MIO 影响的科研类别列表。 | research_categories = { mio_cat_tech_light_armor_and_modules mio_cat_tech_medium_armor_and_modules }   
allowed | true | 在游戏开始时检查的条件，决定 MIO 是否适用于某个国家。_目前，某些条件尚不适用——例如玩家尚未接管，因此 "is_ai" 条件没有意义。_ | allowed = { always = true }   
visible | false | MIO 对该国是否可见。在 MIO 作用域中判定，国家可通过 FROM 作用域获取。MIO 必须可见 _且_ 可用，AI 才能使用它。 | visible = { always = true }   
available | false | MIO 对该国是否可用。若不可用，在界面中会显示为灰色。在 MIO 作用域中判定，国家可通过 FROM 作用域获取。MIO 必须可见 _且_ 可用，AI 才能使用它。 | available = { always = true }   
on_design_team_assigned_to_tech | false | 当 MIO 被指派到科研项目时触发的事件 | 无   
on_design_team_assigned_to_variant | false | 当 MIO 被指派到舰船/坦克/飞机设计器中的某个型号时触发的事件 | 无   
on_industrial_manufacturer_assigned | false | 当 MIO 被指派到生产线时触发的事件 | 无   
on_industrial_manufacturer_unassigned | false | 当从生产线取消指派时触发的事件 | 无   
on_tech_research_cancelled | false | 当关联科研被取消时触发的事件（务必添加一些非常恼火、带着毁灭性国家惩罚的科研人员） | 无   
on_tech_research_completed | false | 当关联科研成功完成时触发的事件 | 无   
research_bonus | false | MIO 默认授予关联科研的科研 % 加成。默认为 DESIGN_TEAM_RESEARCH_BONUS define 值 | research_bonus = 0.2   
task_capacity | false | MIO 可以并行处理多少件事。默认为 DEFAULT_INITIAL_TASK_CAPACITY define 值 | task_capacity = 3   
ai_will_do | false | 与其他可用 MIO 相比，AI 会如何优先使用这个 MIO | 无   
tree_header_text | false | 可以多次提供。MIO 菜单中特性树上方的表头。"x" 表示它被放置到右侧多远的位置，"0" 位于左边界，"9" 是最靠右的位置。文本可以是本地化键，也可以是直接标签。 | 
    
    
    tree_header_text = {
        text = my_flavor_text_loc_key
        x = 1
    }  
  
initial_trait | true | MIO 无需投入任何东西就拥有的初始特性。 | 见 "特性布局"   
trait | true | 可以重复任意次数。MIO 可以解锁的各个特性。 | 见 "特性布局"   
  
### 特性布局

    trait = {
    	token = upgrade_1 # 必填
    	name = loc_key # 可选
    
    	icon = GFX_key # 可选
    
    	special_trait_background = yes # 可选 - 默认为否
    
    	parent = {
    		traits = { 父特性 }
    		num_parents_needed = X
    	}
    	any_parent = { 父特性 }
    	all_parents = { 父特性 }
    
    	mutually_exclusive= { upgrade4 }
    
    	position = { x=1 y=0 }
    	relative_position_id = trait_token
    
    	visible = <条件>
    	available = <条件>
    
       	on_complete = <事件>
    
    	limit_to_equipment_type = { ... } # 可选
    	equipment_bonus = <加成>
    	production_bonus = <加成>
    	organization_modifier = <加成>
    
    
    	ai_will_do = <修正> # 可选
    }

### 设计一个特性

从布局可以看出，设计一个特性需要几个关键步骤：

  * 元数据
  * 条件
  * 摆放位置及与其他特性的关系
  * 授予的加成
  * AI（通常省略）

从元数据的角度来说，"token" 是 _当前 MIO 内_ 的唯一名称。你可以保持简单，不用担心与其他 MIO 冲突。一个好图标有助于在视觉上区分你的特性。最简单的方法是在 "00_generic_organization.txt" 中搜索一个授予与你的特性同类加成的特性，然后从那里复制。name 用于你想要选择自定义本地化键时（默认是 "<mio_name>_<trait_token>"）。

条件非常适合将 MIO 与国家的国策树绑定。使用 "available" 作为通用的解锁条件，而当 MIO 在不同情况下都可使用时，"visible" 就很好用。虽然对于基于 TAG 的完整修改，我们通常倾向于使用 Delta MIO，但可以想想西班牙内战的情形：你只想根据具体是哪个叛军派系来用一个特性替换另一个——只需在同一坐标上定义多个特性，并在可见性之间切换即可。

摆放位置和关系是最容易变得复杂的地方。从根本上说，你有一个宽 10 格、高 5 格的摆放棋盘（可以放得更深，但那样用户就得滚动屏幕）。这使 x 从 0 到 9，y 从 0 到 4。parent 设置决定 MIO 的树状结构。没有父特性意味着该特性可以立即被选择——可以考虑将它们放在树的最顶端（y = 0），然后从这里往下构建。"any_parent" 提供"这些中的任意一个"的父逻辑，"all_parents" 提供"每一个都满足"的依赖关系。而很少见的普通 "parent" 节点允许"这些中至少 X 个"的依赖关系。最后，互斥（mutual exclusion）非常适合让玩家在两个或多个选项之间做出选择，而不是让他们把 _所有_ 都拿到手。务必在互斥集合中的 _所有_ 特性上都放置这个条件，否则玩家可以先选择带有排除条件的特性，然后再选择另一个。

授予的加成——列表见下文附录——允许你定义特性授予的实际收益（和权衡）。它们分为三类：

  * 装备：修改所生产装备的效果。这就是让你的坦克更坚固、舰船更快、战斗机更难被击中的东西。
  * 生产：让生产更快或更便宜、生产线效率增长更快或更高、或装备转换更迅速的效果。
  * 组织：修改 MIO 本身的效果：支持更多生产线、更高的科研加成、更快的成长、更低的指派成本等。

最后还有 AI，但除非你真的想要，否则最好别碰。特性的优先级最好通过 AI 如何权衡加成来处理（见下文中关于如何修改它的章节）。

### 初始特性

你指派给 MIO 的初始特性在几个方面有所不同：

  * 它在棋盘上没有位置
  * 它没有图标
  * 不需要做任何选择

换句话说，大部分特性属性与初始特性无关。事实上，所有属性都是可选的，但只有以下五个是有意义的：

  * name：本地化键，覆盖默认的 "<mio name>_initial_trait" 键。
  * limit_to_equipment_type：将初始特性的装备加成限制到其装备类型的一个子集
  * equipment_bonus：授予装备的加成
  * production_bonus：授予相关装备生产的加成
  * organization_bonus：授予 MIO 本身的加成

### 特性属性说明

特性的属性 键 | 必填？ | 描述 | 示例   
---|---|---|---  
token | true | 特性的系统名称。必须提供，可以保持简单 | 
    
    
    token = gunnery_1  
  
name | false | 本地化键，覆盖默认的 "<mio name>_<trait token>" 键。若定义，系统将优先使用 "<tag>_<trait name>" 而非 "<trait name>" | 
    
    
    name = my_MIO_gunnery_1_Name  
  
icon | false | 用于你特性的图标。默认为 GFX_idea_unknown。检查其他授予相同加成的特性使用的图标。 | 
    
    
    icon = GFX_generic_mio_trait_icon_hg_attack  
  
special_trait_background | false | 若为 yes，特性背景将变为金色，以表示这是一个有趣的特性。默认为 "no"。 | 
    
    
    special_trait_background = yes  
  
parent | false | 提供自定义的特性依赖链接。这允许设置"这些特性中至少拥有 2 个"之类的条件。更简单的依赖关系见 "any_parent" 或 "all_parents" | 
    
    
    parent = {
    	traits = {
    		gunnery_1 stealth_1 mines_1
    	}
    	num_parents_needed = 2
    }  
  
any_parent | false | 列出的特性中必须已选择任意一个。 | 
    
    
    any_parent = { gunnery_1 stealth_1 }  
  
all_parents | false | 列出的特性中每一个都必须已选择。 | 
    
    
    all_parents = { gunnery_1 stealth_1 }  
  
mutually_exclusive | false | 让多个特性互相排斥。必须在每一个特性中都定义，才能按预期工作。 | 
    
    
    mutually_exclusive = { heavy_guns_specialization }  
  
position | true | 特性在棋盘上的位置。（x = 0 y = 0）是左上角，（x = 9 y = 4）是右下角。也可以使用大于 4 的 y 值再往深放（但不推荐），因为这需要玩家滚动屏幕，不推荐。 | 
    
    
    position = { x = 0 y = 0 }  
  
relative_position_id | false | 提供此设置后，可将 "position" 提供的坐标转换为相对于所列特性的坐标。"position = { x=0 y=1 }" 就会变成"直接在父特性正下方" | 
    
    
    relative_position_id = gunnery_1  
  
visible | false | 特性是否显示在 MIO 特性菜单中。该条件在当前 MIO 的作用域中执行，国家可通过 FROM 作用域获取。 | 
    
    
    visible = {
    	FROM = { has_country_flag = <flag name> }
    }  
  
available | false | 特性是否可用（或显示为灰色）。用于将 MIO 与国策树绑定。该条件在当前 MIO 的作用域中执行，国家可通过 FROM 作用域获取。 | 
    
    
    available = {
    	FROM = { has_completed_focus = <focus name> }
    }  
  
on_complete | false | 当玩家选择该特性时发生的事件。同样，这是将 MIO 融入国家叙事的一种方式。该事件在当前 MIO 的作用域中执行，国家可通过 FROM 作用域获取。 | 
    
    
    on_complete = {
    	FROM = { set_country_flag = <flag name> }
    }  
  
limit_to_equipment_type | false | 默认情况下，装备和生产加成适用于 MIO 支持的所有装备类型。此条件允许你将范围缩小到更小的子集。适合用于专精类特性。 | 
    
    
    limit_to_equipment_type = { mio_cat_eq_all_light_tank }  
  
equipment_bonus | false | 应用于 MIO 所适用（除非通过 limit_to_equipment_type 进一步限制）的所有已生产装备的实际改进 | 
    
    
    equipment_bonus = {
    	armor_value = -0.05
    	defense =-0.05
    	build_cost_ic = -0.03
    }  
  
production_bonus | false | 应用于生产线的改进，例如加快的效率增长、更高的产出或更好的转换率。 | 
    
    
    production_bonus = {
    	production_efficiency_gain_factor = 0.15
    	production_resource_need_factor = -0.15
    }  
  
organization_modifier | false | 授予 MIO 本身的加成，例如更便宜的指派、改进的科研加成或更高的任务上限。 | 
    
    
    organization_modifier = {
    	military_industrial_organization_research_bonus = 0.05
    }  
  
ai_will_do | false | 覆盖 AI 通常会根据所授予加成应用到该特性上的默认优先级权重。通常不推荐。 | 
    
    
    ai_will_do = {
    	base = 2
    	modifier = {
    		factor = 1.5
    		date > 1937.1.1
    	}
    }  
  
## Delta MIO

TODO：待编写

## 政策

政策的属性 键 | 必填？ | 描述 | 示例   
---|---|---|---  
name | false | 政策名称。  
可以使用脚本化本地化，作用域将设置为拥有该政策的国家的作用域。 | 
    
    
    name = loc_key  
  
icon | false | 用于你政策的图标。 | 
    
    
    icon = GFX_mio_policy_my_policy  
  
cost | false | 以政治点数支付的指派费用。  
默认值为 Defines 中的 [DEFAULT_INITIAL_POLICY_ATTACH_COST](</Defines#DEFAULT_INITIAL_POLICY_ATTACH_COST> "Defines") "25"。 | 
    
    
    cost = 10  
  
cooldown | false | 指派政策后的冷却时间（天数）。  
默认值为 Defines 中的 [DEFAULT_INITIAL_ATTACH_POLICY_COOLDOWN](</Defines#DEFAULT_INITIAL_ATTACH_POLICY_COOLDOWN> "Defines") "180"。 | 
    
    
    cooldown = 60  
  
allowed | true | 在游戏开始时判定。  
若触发器返回 false，该政策在之后的游戏中将永远不会被考虑。 | 
    
    
    allowed = { 
    	OR = {
    		has_mio_equipment_type = motorized
    		has_mio_equipment_type = mechanized
    	} 
    }  
  
visible | false | 在显示政策界面时判定。  
默认为 "always = yes"。 | 
    
    
    visible = { has_mio_size > 3 }  
  
available | false | 在显示政策界面时判定。  
默认为 "always = yes"。 | 
    
    
    available = { has_mio_size > 5}  
  
equipment_bonus | false | 定义当政策被指派且 MIO 被指派到装备型号时给予的加成。  
注意：它与特性中的 equipment_bonus 不同。这里你必须给出装备组/类别/原型/类型。 | 
    
    
    equipment_bonus = {
    	infantry_equipment = {
    		soft_attack = 0.1
    	}
    }  
  
production_bonus | false | 定义当政策被指派且 MIO 被指派到生产线时给予的加成。  
注意：它与特性中的 production_bonus 不同。这里你必须给出装备组/类别/原型/类型。 | 
    
    
    production_bonus = {
    	infantry_equipment = {
    		production_cost_factor = -0.1
    	}
    }  
  
organization_modifier | false | 定义将应用于 MIO 的修正。 | 
    
    
    organization_modifier = {
    	military_industrial_organization_research_bonus = 0.1
    }  
  
on_add | false | 政策被指派时执行的指令。 | 
    
    
    on_add = { ... }  
  
on_remove | false | 政策被取消指派时执行的指令。 | 
    
    
    on_remove = { ... }  
  
ai_will_do | false | 此政策的 AI 权重修正。  
注意：这会影响 AI 愿意在这项政策上花费政治点数的程度。 | 
    
    
    ai_will_do = {
    	...
    }  
  
## AI 加成权重

TODO：待编写

## 附录：加成

### 装备加成

可用于不同装备的属性列表。

**坦克**

修正   
---  
maximum_speed   
reliability   
defense   
breakthrough   
armor_value   
build_cost_ic   
entrenchment   
fuel_capacity   
  
**舰船**

修正   
---  
lg_armor_piercing   
lg_attack   
hg_armor_piercing   
hg_attack   
torpedo_attack   
sub_attack   
anti_air_attack   
armor_value   
surface_detection   
sub_detection   
sub_visibility   
surface_visibility   
naval_speed   
reliability   
naval_range   
max_strength   
fuel_consumption   
build_cost_ic   
manpower   
naval_supremacy_factor   
naval_torpedo_enemy_critical_chance_factor   
naval_torpedo_damage_reduction_factor   
carrier_size   
mines_sweeping   
mines_planting   
naval_torpedo_hit_chance_factor   
naval_light_gun_hit_chance_factor   
naval_heavy_gun_hit_chance_factor   
  
**飞机**

键   
---  
air_superiority   
reliability   
naval_strike_attack   
naval_strike_targetting   
manpower   
fuel_consumption   
build_cost_ic   
resources   
thrust   
weight   
maximum_speed   
air_range   
air_agility   
air_attack   
air_defence   
surface_detection   
sub_detection   
air_ground_attack   
air_bombing   
mines_planting   
mines_sweeping   
night_penalty   
  
### 生产加成

所有生产加成适用于 MIO 已被指派到的生产线。

生产加成 键 | 描述 | 示例   
---|---|---  
production_cost_factor | 降低生产成本。 | production_cost_factor = 0.05   
production_capacity_factor | 提高生产产出，加快每天生产的物品数量。 | production_capacity_factor = 0.1   
production_efficiency_cap_factor | 提高最大生产效率。请记住，舰船没有这个…… | production_efficiency_cap_factor = 0.2   
production_efficiency_gain_factor | 提高生产效率的增长速率。请记住，舰船没有这个…… | production_efficiency_gain_factor = 0.24   
production_resource_need_factor | 改变所需原材料（铁、钨、铬……）的数量。 | production_resource_need_factor = -0.1   
production_resource_penalty_factor | 修改生产线因资源不足而受到的惩罚。 | production_resource_penalty_factor = -0.1   
production_conversion_speed_factor | 改变装备转换的执行速度。 | production_conversion_speed_factor = 0.5   
  
### 组织加成

组织加成全局应用于 MIO，而不针对某条装备线/产品。因此它们不能受到限制。

组织加成 键 | 描述 | 示例   
---|---|---  
military_industrial_organization_research_bonus | 对 MIO 应用的科研加成百分比的直接增加。如果之前它给予 20% 的加成，这里再加上 "0.1"，那么它将给予 30% 的科研加成。 | military_industrial_organization_research_bonus = 0.1   
military_industrial_organization_design_team_assign_cost | 对在坦克/飞机/舰船设计器中指派 MIO 成本的修正。 | military_industrial_organization_design_team_assign_cost = -0.2   
military_industrial_organization_design_team_change_cost | 对从已指派的 MIO 中为给定坦克/飞机/舰船设计拉取最新改动成本的修正。 | military_industrial_organization_design_team_change_cost = -0.1   
military_industrial_organization_industrial_manufacturer_assign_cost | 将 MIO 指派到工业（即非设计器）生产线需要多少成本。 | military_industrial_organization_industrial_manufacturer_assign_cost = -0.2   
military_industrial_organization_task_capacity | 对 MIO 可同时被指派到的任务数量的直接增加。 | military_industrial_organization_task_capacity = 5   
military_industrial_organization_size_up_requirement | 修改 MIO 升级所需的资金，从而有效加快你解锁特性的速度。如果你设计的 MIO 特性数量高于平均水平，可以考虑使用这个。 | military_industrial_organization_size_up_requirement = -0.1   
military_industrial_organization_funds_gain | 修改资金的获得速率，这些资金随后用于提升 MIO 等级和解锁更多特性。这是提高 MIO 升级速度的另一个手段。 | military_industrial_organization_funds_gain = 0.2   
  
## 参考

**[Modding](</Modding> "Modding")**

文档  | [效果](</Effect> "Effect") • [触发器](</Conditions> "Conditions") • [Defines](</Defines> "Defines") • [修正](</Modifiers> "Modifiers") • [修正列表](</List_of_modifiers> "List of modifiers") • [作用域](</Scopes> "Scopes") • [本地化](</Localisation> "Localisation") • [On actions](</On_actions> "On actions") • [数据结构](</Data_structures> "Data structures") ([标志](</Data_structures#Flags> "Data structures")、[事件目标](</Data_structures#Event_targets> "Data structures")、[国家标签别名](</Data_structures#Country_tag_aliases> "Data structures")、[变量](</Data_structures#Variables> "Data structures")、[数组](</Data_structures#Arrays> "Data structures"))   
---|---  
脚本  | [成就](</Achievement_modding> "Achievement modding") • [AI](</AI_modding> "AI modding") • [AI 国策](</AI_focuses> "AI focuses") • [自治领](</Autonomy_state_modding> "Autonomy state modding") • [势力平衡](</Balance_of_power_modding> "Balance of power modding") • [书签/剧本](</Bookmark_modding> "Bookmark modding") ([游戏规则](</Bookmark_modding#Game_rules> "Bookmark modding")) • [建筑](</Building_modding> "Building modding") • [角色与特性](</Character_modding> "Character modding") • [外观标签](</Cosmetic_tag_modding> "Cosmetic tag modding") • [国家](</Country_creation> "Country creation") • [师团](</Division_modding> "Division modding") • [决议](</Decision_modding> "Decision modding") • [学说](</Doctrine_modding> "Doctrine modding") • [装备](</Equipment_modding> "Equipment modding") • [事件](</Event_modding> "Event modding") • [派系](</Faction_modding> "Faction modding") • [理念](</Idea_modding> "Idea modding") • [意识形态](</Ideology_modding> "Ideology modding") • 军工组织 • [国策](</National_focus_modding> "National focus modding") • [资源](</Resources_modding> "Resources modding") • [脚本化 GUI](</Scripted_GUI_modding> "Scripted GUI modding") • [科技与学说](</Technology_modding> "Technology modding") • [单位](</Unit_modding> "Unit modding")  
---|---  
地图  | [地图](</Map_modding> "Map modding") • [地区](</State_modding> "State modding") • [补给区域](</Supply_areas_modding> "Supply areas modding") • [战略区域](</Strategic_region_modding> "Strategic region modding")  
---|---  
图形  | [界面](</Interface_modding> "Interface modding") • [图形资源](</Graphical_asset_modding> "Graphical asset modding") • [实体](</Entity_modding> "Entity modding") • [后处理特效](</Posteffect_modding> "Posteffect modding") • [粒子](</Particle_modding> "Particle modding") • [字体](</Font_modding> "Font modding")  
---|---  
外观  | [肖像](</Portrait_modding> "Portrait modding") • [姓名列表](</Namelist_modding> "Namelist modding") • [音乐](</Music_modding> "Music modding") • [声音](</Sound_modding> "Sound modding")  
---|---  
其他  | [控制台命令](</Console_commands> "Console commands") • [故障排除](</Troubleshooting> "Troubleshooting") • [Mod 结构](</Mod_structure> "Mod structure") • [Mods](</Mods> "Mods") • [Nudger](</Nudger> "Nudger")  
---|---
