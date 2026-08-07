> 本文为 Paradox Wiki 页面「Map modding（Supply 与 Railways 章节）」的中文翻译，原文: Paradox Wiki「Map modding (Supply Nodes and Railways)」

## 补给（Supply）

_另见：[Nudger § 补给](</Nudger#Supply> "Nudger")_

补给节点和铁路的初始位置分别定义在 /Hearts of Iron IV/map/supply_nodes.txt 和 /Hearts of Iron IV/map/railways.txt 中。**无效的定义可能导致崩溃**——例如在尝试打开单人游戏时，或尝试在 nudger 中打开 'Supply'（补给）部分时。这里的无效定义指的是跨越不存在或无省份归属的省份的定义，或非常不连贯的铁路定义。  
建议使用 nudger 的 Supply 部分来指派补给节点和铁路。

补给节点文件中的一条记录格式如下（不含分号）：

    Level; Province

Level 表示补给节点的等级。默认情况下，补给节点的最高等级为 1[11]，因此这里仅限于 1。  
Province 表示补给节点所在省份的 ID。示例记录为 `1 1234`

铁路文件中的一条记录格式如下（不含分号）：

    Level; Amount of provinces; List of provinces

Level 表示铁路的等级。默认情况下，最高不超过 5。[12]  
Amount of provinces 是铁路经过的省份数量。  
List of provinces 是以空白字符分隔的省份 ID 列表，铁路通过这些省份。  
一条有效的铁路定义如下：`4 4 693 1444 12 11`

### 补给区域（适用于 1.11 之前版本）

**注意：随着 1.11 版本和 No Step Back 的发布，补给区域已被弃用，初始后勤/补给系统改为通过 supply_nodes.txt 和 railways.txt 定义；见上一节。关于如何将你的地图从 1.10 更新到 1.11 的更多信息，见[这篇文章](<https://www.reddit.com/r/hoi4modding/comments/r2876d/updating_custom_map_mods_to_work_with_nsb/>)。**

所有地区都必须与一个补给区域相关联。每个补给区域可以包含任意数量的地区，而每个地区只能属于一个补给区域。

补给区域的格式如下：

    supply_area={
    	id=1
    	name="SUPPLYAREA_1"
    	value=12
    	states={
    		5 85 
    	}
    }
