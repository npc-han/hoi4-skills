> 本文为 Paradox Wiki 页面「Strategic region modding」的中文翻译，原文: Paradox Wiki「Strategic region modding」

这是由社区维护的 wiki。如果你发现错误，请帮助修正。

[战略区域](</Strategic_Region> "Strategic Region") 定义在 /Hearts of Iron IV/map/strategicregions/*.txt 中。

每个战略区域通常存储在各自独立的文件中，不过你也可以在同一文件中存储多个战略区域定义，因为 ID 是在战略区域定义内部定义的，而不是由文件名决定的。

以下是战略区域的通用示例：

    strategic_region = {
        id = <int>
        name = <localization key>
        
        provinces = {
            <province ids>
        }
        
        weather = {
            period = {
                between = { <min> <max> }
                temperature = { <min> <max> }
                no_phenomenon = <weight>
                rain_light = <weight>
                rain_heavy = <weight>
                snow = <weight>
                blizzard = <weight>
                mud = <weight>
                sandstorm = <weight>
                min_snow_level = <amount>
            }
        }
    }

  * **id** 定义战略区域使用的数字 ID。战略区域 ID 必须按顺序依次添加，跳过数字会导致崩溃。
  * **name** 定义战略区域使用的本地化键。你可以使用非本地化的字符串（例如 "Paris"），但最佳实践是使用本地化字符串。
  * **provinces** 作用域定义该战略区域由哪些省份组成。
  * **weather** 作用域决定战略区域所覆盖省份内的天气。

## 天气

|  这是由社区维护的 wiki。如果你发现错误，请帮助修正。  
---|---

每个战略区域都有一个 **weather** 作用域，决定其中各省份的天气如何变化。

每个天气系统在 **weather** 作用域内以一个 **period** 作用域定义。

  * **between** 作用域决定天气系统发生的时间，记法为 `day.month day.month`，例如 `0.0 30.0` 表示天气系统发生在 1 月 1 日至 31 日之间（含首尾两天）。请注意，第一天和第一月记为 0，而不是 1。
  * **temperature** 作用域决定天气系统的最低和最高温度。
  * ~~**temperature_day_night** 作用域决定天气系统在白天和夜间的最低与最高温度变化范围。~~（自 1.11 版本起不再使用，所有温度范围请通过 temperature 设置）
  * **min_snow_level** 作用域决定天气系统中始终存在的最少降雪量。通常只用于终年积雪的区域。

每个天气状态都会被赋予一个权重（weight），决定该状态在天气系统中出现的可能性。天气状态可在 /Hearts of Iron IV/common/weather.txt 中找到。

## 提示

|  这是由社区维护的 wiki。如果你发现错误，请帮助修正。  
---|---

  * 放置战略区域时，其组成的省份应当是连续的。这意味着岛屿通常是某个_海洋_战略区域的一部分。

**[Modding](</Modding> "Modding")**

文档  | [效果](</Effect> "Effect") • [触发器](</Conditions> "Conditions") • [Defines](</Defines> "Defines") • [修正](</Modifiers> "Modifiers") • [修正列表](</List_of_modifiers> "List of modifiers") • [作用域](</Scopes> "Scopes") • [本地化](</Localisation> "Localisation") • [事件动作](</On_actions> "On actions") • [数据结构](</Data_structures> "Data structures")（[Flags](</Data_structures#Flags> "Data structures")、[事件目标](</Data_structures#Event_targets> "Data structures")、[国家标签别名](</Data_structures#Country_tag_aliases> "Data structures")、[变量](</Data_structures#Variables> "Data structures")、[数组](</Data_structures#Arrays> "Data structures")）
---|---  
脚本  | [成就](</Achievement_modding> "Achievement modding") • [AI](</AI_modding> "AI modding") • [AI 国策](</AI_focuses> "AI focuses") • [自治州](</Autonomy_state_modding> "Autonomy state modding") • [势力平衡](</Balance_of_power_modding> "Balance of power modding") • [书签/剧本](</Bookmark_modding> "Bookmark modding")（[游戏规则](</Bookmark_modding#Game_rules> "Bookmark modding")）• [建筑](</Building_modding> "Building modding") • [角色与特质](</Character_modding> "Character modding") • [外观标签](</Cosmetic_tag_modding> "Cosmetic tag modding") • [国家](</Country_creation> "Country creation") • [师](</Division_modding> "Division modding") • [决议](</Decision_modding> "Decision modding") • [学说](</Doctrine_modding> "Doctrine modding") • [装备](</Equipment_modding> "Equipment modding") • [事件](</Event_modding> "Event modding") • [阵营](</Faction_modding> "Faction modding") • [理念](</Idea_modding> "Idea modding") • [意识形态](</Ideology_modding> "Ideology modding") • [军事工业组织](</Military_industrial_organization_modding> "Military industrial organization modding") • [国策](</National_focus_modding> "National focus modding") • [资源](</Resources_modding> "Resources modding") • [脚本 GUI](</Scripted_GUI_modding> "Scripted GUI modding") • [科技与学说](</Technology_modding> "Technology modding") • [单位](</Unit_modding> "Unit modding")
---|---  
地图  | [地图](</Map_modding> "Map modding") • [州](</State_modding> "State modding") • [补给区](</Supply_areas_modding> "Supply areas modding") • 战略区域
---|---  
图形  | [界面](</Interface_modding> "Interface modding") • [图形资源](</Graphical_asset_modding> "Graphical asset modding") • [实体](</Entity_modding> "Entity modding") • [后处理效果](</Posteffect_modding> "Posteffect modding") • [粒子](</Particle_modding> "Particle modding") • [字体](</Font_modding> "Font modding")
---|---  
外观  | [肖像](</Portrait_modding> "Portrait modding") • [人名表](</Namelist_modding> "Namelist modding") • [音乐](</Music_modding> "Music modding") • [声音](</Sound_modding> "Sound modding")
---|---  
其他  | [控制台命令](</Console_commands> "Console commands") • [故障排查](</Troubleshooting> "Troubleshooting") • [Mod 结构](</Mod_structure> "Mod structure") • [Mods](</Mods> "Mods") • [Nudger](</Nudger> "Nudger")
---|---
