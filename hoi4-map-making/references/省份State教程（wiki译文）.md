> 本文为 Paradox Wiki 页面「State modding」的中文翻译，原文: Paradox Wiki「State modding」

这是由社区维护的 wiki。如果你发现错误，请帮助修正。

州（及其历史）定义在 /Hearts of Iron IV/history/states/*.txt 文件中。虽然通常一个州对应一个文件，但这并非必需。与例如 /Hearts of Iron IV/history/countries/ 不同，**文件名不会决定文件应如何处理**，只有文件内容才决定。因此，除非为该文件夹定义了 [replace_path](</Modding#Mod_definition> "Modding")，否则应避免更改现有文件的文件名，因为这样可能导致原版游戏和 mod 的州文件都会被读取。

## 目录

  * 1 参数
    * 1.1 必填
    * 1.2 可选
    * 1.3 历史
  * 2 示例
  * 3 注意事项
  * 4 使用微调器
    * 4.1 建筑
  * 5 建筑类型
  * 6 州类别

## 参数

每个州都包含在一个 `state = { ... }` 块内，该块必须包含全部内容。以下是可用的参数。

### 必填

`id = 123` 是州的 ID 号。必须是整数。
**州 ID 必须按数字顺序排列**，从 1 开始：游戏会期望 mod 中 1 到最大州 ID 之间的每个数字都是一个州。如果这一期望未能满足，在未开启 [调试模式](</Modding#Advantages_to_using_debug> "Modding") 时游戏会在加载时崩溃，因为地图被认为错误过多而无法正常游玩。
因此，在删除一个州时，必须相应调整州 ID 使其保持顺序，例如将最后一个州的 ID 改为填补缺失的 ID。这样做时，所有引用了这些已更改州 ID 的内容都需要调整，可以通过 [使用文本编辑器搜索 mod 中的所有文本文件](</Modding#Search_in_files> "Modding") 来完成。

`name = STATE_123` 是一个本地化键，将根据当前启用的语言成为州的名称。对于英语，可在任意 /Hearts of Iron IV/localisation/english/*_l_english.yml 文件中按如下方式定义：

    l_english:
     STATE_123: "My state name"

默认情况下，游戏使用 `state_names_l_english.yml`。

`manpower = 500000` 是游戏开始时该州的总人口，包括可征召与不可征召人口。这将是在每个剧本中的初始人口，第一个开始日期与剧本开始之间不会模拟人口增长。不过，每个剧本都会进行一次月度人口增长，使该州人口增加 0.125%。[1]

`state_category = my_category` 是该州使用的类别。这设定了州的初始州修正（包括初始解锁的共享建筑槽数量），并为州视图地图模式中的州分配颜色。州类别的细节在本文后面介绍。

`provinces = { 123 456 7890 }` 是被定义为属于该州的省份列表，用空白字符分隔。

### 可选

`impassable = yes` 若添加到州中，会将其标记为不可通行。这包括：使部队无法进入；其省份归属最近的可行省份的控制者；无法在其内部建造省级建筑；并使 [impassable 触发器](</Conditions#impassable> "Conditions") 对其判定为真。

`resources = { steel = 10 aluminium = 20 }` 为该州分配资源。每种资源以 `<resource> = <int>` 的格式添加。原版游戏资源包括 `oil`、`aluminium`（采用英式拼写）、`rubber`、`tungsten`、`steel` 和 `chromium`，不过[可以添加更多](</Resource_modding> "Resource modding")。

`local_supplies = 8.3` 决定[州的补给基础值](</Logistics#State_supply> "Logistics")。1 单位的 local_supplies 等于 0.2 单位的补给。如果未定义，默认为 0。

`buildings_max_level_factor = 0.5` 为解锁的共享建筑槽数量添加一个额外乘数。建议避免使用，而是使用州类别。

### 历史

所有这些都包含在 `history = { ... }` 中，它定义在州内。此外，它们还可以放在 history 内以 YYYY.MM.DD 格式日期标记的块中，例如 `1939.1.1 = { ... }`。这会使它们仅在开始日期严格晚于指定日期时执行。

`owner = POL` 定义州的初始拥有者。如果州没有拥有者，游戏可以正常运行；但对该州执行几乎任何效果（例如将其转移给某个国家）都会导致崩溃。

`controller = LIT` 定义州的初始控制者。可选项——仅在拥有者与控制者不同时才需要定义。

`victory_points = { 1234 10 }` 定义指定省份上的胜利点数，其中第一个数字是省份，第二个数字是胜利点数。**一个 victory_points 内只能定义一个省份**。要让一个州内多个省份拥有胜利点，需要放入多条 `victory_points = { ... }`。
根据游戏语言使用的胜利点本地化键是 `VICTORY_POINTS_1234`，其中 1234 是省份的 ID。对于英语，可在任意 /Hearts of Iron IV/localisation/english/*_l_english.yml 文件中按如下方式定义：

    l_english:
     VICTORY_POINTS_1234: "My city name"

默认情况下，游戏使用 `victory_points_l_english.yml`。要在地图上定位胜利点图标，需要编辑 [unitstacks 文件](</Map_modding#unitstacks> "Map modding")。请注意，胜利点图标不必位于省份内部：如果多个胜利点出现在同一位置，可能是不同省份的 unitstacks 文件过时所致，需要在微调器的单位（Units）部分相应调整。

`buildings = { ... }` 定义该州的初始建筑。单个建筑条目由建筑 ID 后跟等号和数量组成，如 `dockyard = 10`。在 `buildings = { ... }` 中，还可以通过 `1234 = { ... }` 指定省份，并将该省份的建筑放入其中，其中 1234 是省份 ID。一个使用此方式的建筑定义示例：

    buildings = {
        dockyard = 10
        1234 = {
            bunker = 5
            coastal_bunker = 6
        }
    }

如果未提及某个建筑，则不会改变其初始值（默认为 0）；不过，如果 buildings 块位于日期标记中而不是立即执行，初始建筑等级可能不同。在内陆州中，只能建在沿海州/省份的建筑无法定义，即使设为 0 也不行。

此外，history 充当一个[效果块](</Effect> "Effect")。州历史中常用的效果包括 `add_core_of = POL` 或 `add_claim_by = LIT`，但可以使用任何效果。

## 示例

最低限度：

    state = {
        id = 123
        name = STATE_123
        manpower = 50000
        state_category = large_town
        
        history = {
            owner = ITA
        }
        
        provinces = {
            1234 5678
        }
    }

普通州：

    state = {
        id = 124
        name = STATE_124
        manpower = 50035
        state_category = megalopolis
        
        resources = {
            oil = 10
            chromium = 50
        }
        
        history = {
            owner = SWI
            add_core_of = SWI
            buildings = {
                infrastructure = 3
                industrial_complex = 1
                arms_factory = 1
                dockyard = 10
                7777 = {
                    coastal_bunker = 5
                    naval_base = 10
                }
            }
            victory_points = { 5555 15 }
            victory_points = { 6666 10 }
            1939.1.1 = {
                controller = ITA
                set_state_name = ITA_STATE_124
                buildings = {
                    infrastructure = 4  # 这不会改变民用或军用工厂的数量。
                }
            }
        }
        
        provinces = { 1111 2222 3333 4444 5555 6666 7777 8888 9999 }
        
        local_supplies = 10
    }

## 注意事项

每个州的建筑模型位置与州本身分开定义，而是定义在 /Hearts of Iron IV/map/buildings.txt 中。不一致会导致错误，占用日志空间，并可能导致崩溃。例如，如果某个省份缺少海军基地或浮港的定义——无论是因为在 buildings.txt 中设错了州还是完全缺失——**在该省份内尝试使用它们（无论是玩家还是 AI）都会导致崩溃**，日志标记为 [最后读取的脚本为 client_ping](</Troubleshooting#Crash_data_log> "Troubleshooting")。编译模型位置的最简单方法是使用 [微调器](</Nudger> "Nudger") 中的建筑部分。
/Hearts of Iron IV/map/airports.txt 和 /Hearts of Iron IV/map/rocketsites.txt 决定游戏应将机场或火箭发射基地放在该州的哪个省份。这也可以在 [微调器](</Nudger> "Nudger") 的建筑部分编辑。**如果其中任何一项不正确或缺失，游戏将无法在不开调试模式的情况下打开。**

州边界必须符合 [战略区域](</Strategic_region_modding> "Strategic region modding")（定义在 /Hearts of Iron IV/map/strategicregions/*.txt 中）。如果州中的一个省份属于一个战略区域，而同一州的另一个省份属于不同的战略区域，将产生地图错误，在未开启调试模式时会导致游戏启动崩溃。务必确保州边界与战略区域边界一致，可以通过调整州或战略区域来实现。

## 使用微调器

_本节部分内容摘自[Nudger § States](</Nudger#States> "Nudger")_

[微调器](</Nudger> "Nudger") 是一个地图编辑工具，通过启用 `-debug` [启动选项](</Launch_option> "Launch option") 后从主菜单进入。对于州，它可用于更改州边界以及生成建筑模型。

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

###  建筑

|  请帮助改进本文章或小节，[**扩充其内容**](<https://hoi4.paradoxwikis.com/index.php?title=State_modding&action=edit>)。  
---|---

  * 

## 建筑类型

_主条目：[建筑修改](</Building_modding> "Building modding")_

以下是游戏中的不同建筑类型（也可以在 /Hearts of Iron IV/common/buildings/00_buildings.txt 中找到）：

图标 | 本地化名称 | 内部名称 | 最高等级 | 类型   
---|---|---|---|---  
 | 基础设施  | infrastructure  | 5  | 非共享   
 | 军工工厂  | arms_factory  | 20  | 共享   
 | 民用工厂  | industrial_complex  | 20  | 共享   
 | 空军基地  | air_base  | 10  | 非共享   
 | 补给中心  | supply_node  | 1  | 省级   
 | 铁路  | rail_way  | 5[2] | 省级   
 | 海军工程设施  | naval_facility  | 1  | 省级   
 | 海军基地  | naval_base  | 10  | 省级   
 | 陆地要塞  | bunker  | 10  | 省级   
 | 海岸要塞  | coastal_bunker  | 10  | 省级   
 | 堡垒网络  | stronghold_network  | 1  | 共享   
 | 海军船坞  | dockyard  | 20  | 共享   
 | 防空  | anti_air_building  | 5  | 非共享   
 | 合成炼油厂  | synthetic_refinery  | 3  | 共享   
 | 燃油储存库  | fuel_silo  | 15  | 共享   
 | 雷达站  | radar_station  | 6  | 非共享   
 | 多弹头大口径火炮(*) | mega_gun_emplacement  | 1  | 共享   
 | 火箭发射基地(*) | rocket_site  | 3  | 共享   
 | 海军补给中心 (*) | naval_supply_hub  | 1  | 省级   
 | 海军总部 (*) | naval_headquarters  | 1  | 省级   
 | 核反应堆  | nuclear_reactor  | 1  | 共享   
 | 重水核反应堆  | nuclear_reactor_heavy_water  | 1  | 共享   
 | 民用核反应堆  | commercial_nuclear_reactor  | 1  | 共享   
 | 核研究设施  | nuclear_facility  | 1  | 省级   
 | 空气动力学与航空电子设施  | air_facility  | 1  | 省级   
 | 陆战设施  | land_facility  | 1  | 省级   
 | 水坝  | dam  | 1  | 省级   
 | 水坝  | dam_mountain  | 1  | 省级   
 | 基尔运河船闸  | canal_kiel  | 1  | 省级   
 | 巴拿马运河船闸  | canal_panama  | 1  | 省级   
 | 强化电网(*) | energy_infrastructure  | 1  | 共享   
 | 高容量电网(*) | industrial_infrastructure  | 1  | 共享   

请注意，虽然铁路和补给节点属于建筑，但并非所有常规建筑操作都适用于它们。它们的初始等级定义在[州历史之外](</Map_modding#Supply_nodes_and_railways> "Map modding")，且游戏中建造铁路时必须使用[单独的效果](</Effect#build_railway> "Effect")，默认的 [add_building_construction](</Effect#add_building_construction> "Effect") 或其他建筑相关效果会导致崩溃。

## 州类别

原版游戏的州类别及其对应的建筑槽数量：

本地化名称 | 内部名称 | 槽位数量 | 颜色   
---|---|---|---  
荒地  | wasteland  | 0  |   
飞地  | enclave  | 0  |   
微型岛屿  | tiny_island  | 0  |   
田园地区  | pastoral  | 1  |   
小型岛屿  | small_island  | 1  |   
乡村地区  | rural  | 2  |   
发达乡村地区  | town  | 4  |   
稀疏城市地区  | large_town  | 5  |   
城市地区  | city  | 6  |   
密集城市地区  | large_city  | 8  |   
大都会地区  | metropolis  | 10  |   
特大城市地区  | megalopolis  | 12  |   

州类别可以在 /Hearts of Iron IV/common/state_category/*.txt 文件中添加。每个州类别都包含在 `state_categories = { ... }` 中，作为以州类别 ID 命名的代码块。

州类别是一个[修正块](</Modifiers#State_scope> "Modifiers")，可以使用任何州作用域修正。原版游戏唯一使用的修正是 `local_building_slots`（设为整数），但可以使用任何修正。此外，`color = { 0 0 255 }` 块对应州视图地图模式中该州的颜色。它以 RGB 格式定义，其中每个值都是 0 到 255 之间的整数。

示例：

    state_categories={
        my_state_category = {
            color = { 0 255 0 }
            local_building_slots = 14
        }
        my_second_category = {
            color = { 255 0 0 }
            local_building_slots = 4
            resistance_growth = 0.1
        }
    }

可以使用 `set_state_category = category_id` 效果在游戏中途更改州的类别。

**[Modding](</Modding> "Modding")**

文档  | [效果](</Effect> "Effect") • [触发器](</Conditions> "Conditions") • [Defines](</Defines> "Defines") • [修正](</Modifiers> "Modifiers") • [修正列表](</List_of_modifiers> "List of modifiers") • [作用域](</Scopes> "Scopes") • [本地化](</Localisation> "Localisation") • [事件动作](</On_actions> "On actions") • [数据结构](</Data_structures> "Data structures")（[Flags](</Data_structures#Flags> "Data structures")、[事件目标](</Data_structures#Event_targets> "Data structures")、[国家标签别名](</Data_structures#Country_tag_aliases> "Data structures")、[变量](</Data_structures#Variables> "Data structures")、[数组](</Data_structures#Arrays> "Data structures")）
---|---  
脚本  | [成就](</Achievement_modding> "Achievement modding") • [AI](</AI_modding> "AI modding") • [AI 国策](</AI_focuses> "AI focuses") • [自治州](</Autonomy_state_modding> "Autonomy state modding") • [势力平衡](</Balance_of_power_modding> "Balance of power modding") • [书签/剧本](</Bookmark_modding> "Bookmark modding")（[游戏规则](</Bookmark_modding#Game_rules> "Bookmark modding")）• [建筑](</Building_modding> "Building modding") • [角色与特质](</Character_modding> "Character modding") • [外观标签](</Cosmetic_tag_modding> "Cosmetic tag modding") • [国家](</Country_creation> "Country creation") • [师](</Division_modding> "Division modding") • [决议](</Decision_modding> "Decision modding") • [学说](</Doctrine_modding> "Doctrine modding") • [装备](</Equipment_modding> "Equipment modding") • [事件](</Event_modding> "Event modding") • [阵营](</Faction_modding> "Faction modding") • [理念](</Idea_modding> "Idea modding") • [意识形态](</Ideology_modding> "Ideology modding") • [军事工业组织](</Military_industrial_organization_modding> "Military industrial organization modding") • [国策](</National_focus_modding> "National focus modding") • [资源](</Resources_modding> "Resources modding") • [脚本 GUI](</Scripted_GUI_modding> "Scripted GUI modding") • [科技与学说](</Technology_modding> "Technology modding") • [单位](</Unit_modding> "Unit modding")
---|---  
地图  | [地图](</Map_modding> "Map modding") • 州 • [补给区](</Supply_areas_modding> "Supply areas modding") • [战略区域](</Strategic_region_modding> "Strategic region modding")
---|---  
图形  | [界面](</Interface_modding> "Interface modding") • [图形资源](</Graphical_asset_modding> "Graphical asset modding") • [实体](</Entity_modding> "Entity modding") • [后处理效果](</Posteffect_modding> "Posteffect modding") • [粒子](</Particle_modding> "Particle modding") • [字体](</Font_modding> "Font modding")
---|---  
外观  | [肖像](</Portrait_modding> "Portrait modding") • [人名表](</Namelist_modding> "Namelist modding") • [音乐](</Music_modding> "Music modding") • [声音](</Sound_modding> "Sound modding")
---|---  
其他  | [控制台命令](</Console_commands> "Console commands") • [故障排查](</Troubleshooting> "Troubleshooting") • [Mod 结构](</Mod_structure> "Mod structure") • [Mods](</Mods> "Mods") • [Nudger](</Nudger> "Nudger")
---|---  

  1. ↑ `NDefines.NCountry.POPULATION_YEARLY_GROWTH_BASE = 0.015`
  2. ↑ `NDefines.NSupply.MAX_RAILWAY_LEVEL = 5`，位于 [Defines](</Defines> "Defines") 中。
