

来到另一个世界
注意：严禁使用中文标点和
全角输入法
严禁在代码里用空格，如需
使用，一律改成“_”


语言初识C 目录
11
22
33
44
地图篇| 目录
 我给小马国画了新地图！
花花短裤又尿床了？


——工欲善其事，必先利其器


钢丝地图
都有哪些文件
第一节
strategicregions：战略区域文件夹，游戏中的战略区域在此定义
supplyareas：补给区域文件夹，补给中心出现后已失去实际功能
terrain：地形图像文件夹，游戏里显示的地形就是这里的图像
adjacencies.csx：用于定义pros之间的特殊通道
adjacency_rules.txt：用于定义游戏中海峡的可通行状态
airports.txt ：用于定义游戏中机场模型的位置
ambient_object.txt：用于定义游戏中上下边框
buildings.txt：用于定义游戏中建筑模型的位置
cities.bmp：用于定义游戏中城市模型的位置
cities.txt：用于将不同的城市模型分配给世界的不同国家
colors.txt：用于定义游戏中城市模型的灯光
continent.txt：用于定义游戏中的大洲
default.map：执行文件，辅助游戏读取其他文件
definition.csv：与provinces.bmp共同构成
地图的基本组成文件


钢丝地图
都有哪些文件
第一节
heightmap.bmp：地形高度图
positions.txt：空文件
provinces.bmp：地图的基本组成文件
railways.txt：开局时的铁路网
rivers.bmp：用于定义游戏中河流的位置和粗细
rocketsites.txt：用于定义游戏中火箭基地的位置
seasons.txt：用于定义游戏中四季的长度和始末
supply_nodes.txt：开局时的补给中心
terrain.bmp：用于定义游戏中各地块的地形
trees.bmp：用于定义游戏中树的位置
unitstacks.txt：用于定义游戏中兵模的位置
weatherpositions.txt：用于定义游戏中的天气
world_normal.bmp：法线地图，决定了地图的
3D 渲染中每个像素的确切斜率。用于照明


—— 不要用记事本修改


adjacency_rules.txt
这是干嘛的？
ambient_object.txt
这又是干嘛的？
supply_nodes.txt
世上无难事，只要肯放弃！溜了溜了


我们先来看看
哪些文件是
txt吧！
第一节
简单翻译下文件名
这两个文件看名字就知道肯定
有关系，所以就放在一块讲


游戏中存在着大量不按常理的地形，比如非军事区，通行很复杂的海峡，
莫名其妙可以跨海水上行走的岛屿，你一定很好奇，这是怎么做到的？


这就是定义特殊通道的
adjacencies.csv文件啦！
官方还贴心的给了注释
翻译：起始地块id；目标地块id；类型；起始X；Y坐标；目标X；Y坐标；通行规则名字；注释
官方注释：From;To;Type;Through;start_x;start_y;stop_x;stop_y;adjacency_rule_name;Comment
代码示例：12251;10321;sea;8313;-1;-1;-1;-1;;Andaman
起始地块和目标地块可以互换，就是单纯指出是这两个地块相连而已
Type包括海峡sea/非军事区impassable
X，Y坐标是指决定了在两个陆地省份之间穿越海峡而创建的红线的起点和终点位置
如果将X，Y坐标设置为-1，则游戏将自动计算位置
adjacency_rule_name假如不是可通行的运河或者海峡，即adjacency_rules.txt需要引用
到，则可以空置
此文件的最后一行必须是-1;-1;-1;-1;-1;-1;-1;-1;-1，否则可能出现无法结束读取的bug
此文件必须以 UTF-8 编码


adjacency_rules.txt决定了
adjacencies.csv文件中海峡/运河这种
特殊连接的通行规则
常见用法如图


这是官方注释
翻译如下：
# 邻接规则决定了军队、海军或贸易是否可以通过邻接
区域（例如运河或海峡）。
#
# 如果两个交战国家控制邻接省份，则视为有争议地区。
# 可触发选项按is_enemy、is_friend和is_neutral的顺序进
行验证，如果其中一个为真，则使用相应的规则。
# 如果is_enemy和is_friend验证为假，则使用is_neutral通
行规则。在这种情况下，根本不会触发is_neutral验证。
# 如果is_friend和is_neutral验证为假，则使用is_enemy规
则。
# 如果is_enemy和is_neutral验证为假，则使用is_friend规
则。
# 如果有任何邻接省份控制者与国家交战，则使用
is_enemy规则；除非is_enemy验证为假，如此则会使用
is_neutral规则。
# 如果任何邻接省份控制者是该国的盟友，并授予该国
军事访问权限，则使用is_friend规则，除非is_friend评估
为false，如此则会使用中立规则。
# 使用中立规则，除非is_neutral评估为 false，如此则会
使用is_enemy规则。
#
# “name”的内容请您参阅adjacencies.csv


1
2
3
4
5
6
7
8
adjacency_rule ={
name = "" … … … … … … … … … … … … … … … … …adjacencies.csv 文件中定义对象的name
通行规则在此（见下页）
required_provinces = {} …………………………………… 涉及到的prov 地块，以空格间隔
is_disabled = {} … … … … … … … … … … … … … … … … … … … … … … … … … …在什么情况下禁用
icon = … … … … … … … … … … … … … … … … … … … … … … … … … … … … … … … … … … … … … …图标
offset = {} … … … … … … … … … … … … … … … … … … … … … … … … … … … …偏移量， 以空格间隔
}
对敌对国家、盟友和中立国家的通行规则写法与争议地区的通行规则相同
四个通行规则不可省略，四个通行对象也不可省略，ppt里是简略的写法不要学


1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
contested ={ ………………………………………… 争议地区的通行规则
army = no ……………………………………… 陆军能否通过yes/no
navy = no ……………………………………………… 海军能否通过
submarine = no ……………………………………… 潜艇能否通过
trade = no ………………………………………… 民用舰船能否通过
}
enemy = { ………………………………………… 对敌对国家的通行规则
army = no
navy = no
submarine = no
trade = no
}
friend = { ……………………………………………… 对盟友的通行规则
army = no
navy = no
submarine = no
trade = no
}
neutral = { ………………………………………… 对中立国家的通行规则
army = no
navy = no
submarine = no
trade = no
}
对敌对国家、盟友和中立国家
的通行规则写法与争议地区的
通行规则相同
四个通行规则不可省略，四个
通行对象也不可省略，假如没
有写的话会按官方注释顺延，
所以建议写一下


假如需要大量修改
建议利用nudge 模式
进行制作
ambient_object.txt决定了游戏上下边
框和钢丝LOGO的位置和大小形状
格式为
依次为


假如需要大量修改
建议利用nudge 模式
进行制作
buildings.txt文件决定了每个state的建筑模型的位置
在哪个prov
格式为State ID (integer); building ID (string); X 
position; Y position; Z position; Rotation; Adjacent 
sea province (integer)
依次为省份id；建筑代码； X轴；Y轴；Z轴位置；
旋转；省份相邻的海洋地块的id
以分号间隔
State ID和Adjacent sea province必须为整数
Rotation以弧度完成，0 是默认状态，正值按逆时针
旋转。旋转一圈的数字约等于 6.28，也即π乘以 2
building ID包括arms_factory（军用工厂）；
industrial_complex（民用工厂）； air_base （空军
基地）； naval_base （海军基地）； bunker （要
塞）； coastal_bunker（海军要塞）； dockyard
（船坞）； anti_air_building （防空炮）；
synthetic_refinery （合成精炼厂）；
nuclear_reactor（核反应堆）等


假如需要大量修改
建议利用nudge 模式
进行制作
airports.txt决定了游戏内各个state内
建造机场的prov地块
格式为State ID = {Province ID}
依次为州的ID= {省份/地块ID}


假如需要大量修改
建议利用nudge 模式
进行制作
rocketsites.txt决定了游戏内各个state
内建造火箭基地的prov地块
格式为State ID = {Province ID}
依次为州的ID= {省份/地块ID}


cities.txt用于将不同的城市模型分配
给世界的不同国家


colors.txt决定了游戏内城市的灯光
格式为
color = { R G B }
RGB代表的是该颜色对应的RGB颜色
表中的数字
三个数字之间以分号间隔


假如需要大量修改
建议利用nudge 模式
进行制作
continent.txt决定了游戏内总共有哪
些大洲
格式为
continents = {
大洲A
大洲B
大洲C
}
大洲在该文件中的顺序将被自动视为
该大洲的数字代号
如地块xx所处大洲位置被填写为1，
则会被游戏自动读取为处于大洲A
大洲名字要去本地化定义


没有实际用途
随着更新已经失去存在意义
甚至P社自己已经把代码删光了
厂商因为历史遗留代码结构不敢删文件


假如需要大量修改
建议利用nudge 模式
进行制作
railways.txt文件决定了游戏开局时即
存在的每条铁路
格式为Level; Amount of provinces; 
List of provinces
依次为铁路等级；铁路经过的地块数
量；铁路经过的所有地块id
以空格间隔
Level取值范围为1-5，必须为整数
错误的铁路可能导致游戏崩溃


假如需要大量修改
建议利用nudge 模式
进行制作
supply_nodes.txt文件决定了游戏开局
时即存在的每个补给中心
格式为Level; Province
依次为补给中心等级；所在地块id
以空格间隔
Level必须为1


seasons.txt决定了游戏内各个季节的
起始和结束时间等


假如需要大量修改
建议利用nudge 模式
进行制作
unitstacks.txt文件决定了每个省份内
的士兵模型和胜利点的位置
格式为Province ID; Type; X position; 
Y position; Z position; Rotation; 
Offset
依次为地块id；类型；X轴；Y轴；Z
轴位置；旋转；偏移
以分号间隔
Type 是一个介于 0 到 38 之间的整数，
用于分配用途。详见下页
Rotation以弧度完成，0 是默认状态，
正值按逆时针旋转。旋转一圈的数字
约等于 6.28，也即π乘以 2
Offset用于动画，以使所有兵模动画
不是完全相同，而是具有延迟。


ID Internal name Notes
0 Standstill
1 Moving 0 Different numbers represent different needed levels of rotation
2 Moving 1 Different numbers represent different needed levels of rotation
3 Moving 2 Different numbers represent different needed levels of rotation
4 Moving 3 Different numbers represent different needed levels of rotation
5 Moving 4 Different numbers represent different needed levels of rotation
6 Moving 5 Different numbers represent different needed levels of rotation
7 Moving 6 Different numbers represent different needed levels of rotation
8 Moving 7 Different numbers represent different needed levels of rotation
9 Attacking
10 Defending
11 Disembarck 0 Different numbers represent different needed levels of rotation
12 Disembarck 1 Different numbers represent different needed levels of rotation
13 Disembarck 2 Different numbers represent different needed levels of rotation
14 Disembarck 3 Different numbers represent different needed levels of rotation
15 Disembarck 4 Different numbers represent different needed levels of rotation
16 Disembarck 5 Different numbers represent different needed levels of rotation
17 Disembarck 6 Different numbers represent different needed levels of rotation
18 Disembarck 7 Different numbers represent different needed levels of rotation
19 Ship in port
20 Ship in port moving
ID Internal name Notes
21 Standstill RG RG stands for regrouping
22 Moving 0 RG Different numbers represent different needed levels of rotation. RG stands for 
regrouping
23 Moving 1 RG Different numbers represent different needed levels of rotation. RG stands for 
regrouping
24 Moving 2 RG Different numbers represent different needed levels of rotation. RG stands for 
regrouping
25 Moving 3 RG Different numbers represent different needed levels of rotation. RG stands for 
regrouping
26 Moving 4 RG Different numbers represent different needed levels of rotation. RG stands for 
regrouping
27 Moving 5 RG Different numbers represent different needed levels of rotation. RG stands for 
regrouping
28 Moving 6 RG Different numbers represent different needed levels of rotation. RG stands for 
regrouping
29 Moving 7 RG Different numbers represent different needed levels of rotation. RG stands for 
regrouping
30 Disembarck 0 RG Different numbers represent different needed levels of rotation. RG stands for 
regrouping
31 Disembarck 1 RG Different numbers represent different needed levels of rotation. RG stands for 
regrouping
32 Disembarck 2 RG Different numbers represent different needed levels of rotation. RG stands for 
regrouping
33 Disembarck 3 RG Different numbers represent different needed levels of rotation. RG stands for 
regrouping
34 Disembarck 4 RG Different numbers represent different needed levels of rotation. RG stands for 
regrouping
35 Disembarck 5 RG Different numbers represent different needed levels of rotation. RG stands for 
regrouping
36 Disembarck 6 RG Different numbers represent different needed levels of rotation. RG stands for 
regrouping
37 Disembarck 7 RG Different numbers represent different needed levels of rotation. RG stands for 
regrouping
38 Victory point


weatherpositions.txt 文件决定了云等
天气模型的位置。
格式为strategic region ID;X 
position;Y position;Z position;siz
依次为战略区域id；X轴；Y轴；Z轴
位置，模型大小
以分号间隔
假如需要大量修改
建议利用nudge 模式
进行制作


—— 用ps也比画图强


地图的宽度和高度由provinces.bmp决定，并且其像素数值都要是256的倍数
需要注意，其他图片文件也全都是必须与provinces.bmp大小相同的
一般来说，其他图片文件也都是以provinces.bmp为基础进行制作和修改的
定义文件definition.csv
因为和
省份文件provinces.bmp
必须搭配使用，所以放在
一起讲
都有哪些bmp文件呢？


那么什么是provinces.bmp文件呢？我们一起来看看吧
这是什么鬼？？？这不是一堆色块吗？马赛克吗？错！我们缩小看看


现在明白了吧，游戏里的一个个单独的地块就是在这个文件里定义了形状和位置，
所以说provinces.bmp是整个maps文件夹的基石完全不为过
这也代表着，我们假如想要修改游戏中的地块大小和形状，动这个文件就可以了。


比如说，我们需要扩大左图中心那块草绿色的prov，让他不那么方方
正正的同时，也让它更大一点。那么我们就可以打开画图软件，直接
对它的形状和大小进行修改。在修改完毕后，保存文件就可以了。
虽然可以
但是我并不推荐
使用系统自带的
画图软件
推荐：PS
1.地块不能随意增添或删除，如有需要，请学习下一页
2.修改地块的时候注意不要留白，也不要把地块涂没（删除）
3.不推荐让非沿海地块分成好几块，地块也不要太大或太小
4.不推荐让四个地块有共同的顶点，游戏会报错
5.修改时尽量不要让原本不相邻/相邻的地块变得相邻/不相邻
注意
共同顶点


但是你仍然可能好奇，
左边的像素图是怎么变成游戏中右边的地块的呢？
这就要打开我的文本啦！终于啊！
我们前文中被反复鞭尸提及的
定义文件definition.csv要派上用场了！
瞪大眼睛仔细看！


看不懂？
数字是什么鬼？
单词是干嘛的？
没关系
现在呢→
Excel下打开
Notepad++下打开


地块编号 RGB颜色 是否陆地 是否沿海 地块类型 所处大洲
7; 0;3;170; land; false; forest; 1
我们一一解释：
地块编号：每个地块独一无二的编号，游戏里地块的id即是，每个地块唯一
RGB 颜色：地块在provinces.bmp之中对应的色块的颜色，每个地块唯一
是否陆地：这块地是陆地land/海洋sea/湖泊lake
是否沿海：这块地沿海true/不沿海false
地块类型：这块地是什么类型？地块类型详见/Hearts of Iron IV/common/terrain
所处大洲：这块地在哪个大洲？大洲详见/Hearts of Iron IV /map/continent.txt


还是用刚才的图举例子
我们看看这个地块的颜色是什么
画图就可以做到，很轻松


找到了！
根据数据来看，地块id是9834
陆地省份，沿海，沼泽地形，
处于continent.txt文件里排第二位的大洲
打开游戏
控制台tdebug看看数据
完全一致！


以此类推，其他的内容修改，如地貌，城市模型，高低图
也是找到对应的bmp文件修改就可以了
不过这些文件大多数需要专业工具进行更便利的修改就是了
terrain.bmp是一个控制地形分配和纹理BMP文件
trees.bmp控制地图上树木的摆放和密度
rivers.bmp控制地图上的河流
heightmap.bmp决定了地图的3D网格
world_normal.bmp定义了3D凹凸贴图
就用河流文件rivers.bmp做范例吧
同样是画图就可以打开（不推荐）
黄河，长江，无需多言


Index 颜色 意义
0 (0, 255, 0) 河流的源头
1 (255, 0, 0) 流入源。用于把多条河流合并为一条干流。
2 (255, 252, 0) 流出源。用于把一条河流分流为多条支流。
3 (0, 225, 255) 外观最窄的河流
4 (0, 200, 255) 外观较窄的河流
5 (0, 150, 255)
6 (0, 100, 255) 外观较宽的河流
7 (0, 0, 255)
8 (0, 0, 225)
9 (0, 0, 200)
10 (0, 0, 150)
11 (0, 0, 100) 外观最宽的河流
注意：
1.文件rivers.bmp需要保存为索引模式并保存为8位位图（.BMP格式）
2.每一条河都必须被唯一标识，即只有一个起点（绿色/黄色）或一个
终点（红色）
3.地块的边界最好明确的以河为界或者远离河流
4.每条河流必须只有一格像素点的宽度，也必须连续不能断开


——还有点零碎的


游戏中的战略区域在此划定
与states文件夹类似
每个战略区域都被一个txt单独划分
随着 1.11 和 No Step Back 的发布
补给区域被弃用
取而代之的是supply_nodes.txt和railways.txt定义
现随更新已经失去存在意义
游戏中地形的图像
一般不会涉及到此处的修改
colormap_x.dds文件定义了应用于地图的整体颜色色调
colormap_rgb_cityemissivemask_a.dds用于世界的整体色彩和改变城市灯光的不
透明度colormap_water_0.dds用于世界海洋的着色


1
2
3
4
5
6
7
8
9
10
11
strategic_region={ … … … … … … … … … … … … … … … … … … … … … … … … … … … … … …代码抬头
id=1 … … … … … … … … … … … … … … … … … … … … … … … … … … … … … … … … … …战略区域id
name=“STRATEGICREGION_1” … … … … … … … … … … … …战略区域名称（本地化定义）
color = { <r>, <g>, <b> }… … … … … … … … … … … …战略区域的颜色（未定义即随机）
provinces={
2024 ………… 战略区域包含的prov （示例代表id为2024 的prov 属于此战略区域）
}
weather={ … … … … … … … … … … … … … … … … … … … … … … … … … … … … … … … … … … … …天气
见下一章
}
}
 现在我们看到的是一个
尽可能完整的战略区域
每个战略区域id唯一，name 同样唯一
而每个prov只能存在于一个战略区域内
也不要让prov不属于任何战略区域
颜色和天气并非必须的
而其他的代码则是必须的
战略区域手写会累死你
假如需要大量修改
建议利用nudge 模式
进行制作


weather={ … … … … … … … … … … … … … … … … … … … … … … … … … … … … … … … … … … … … …天气（续上篇）
period={
between={ 0.0 30.0 } …… 决定天气在哪个时期适用，数字顺序为{日 月}，从零开始
temperature={ -6.0 12.0 } … … … … … … … … … …指定该时间段的最高温度和最低温度
no_phenomenon=0.500 … … … … … … … … …决定了战略区域中每个省份无现象的几率
rain_light=1.000 … … … … … … … … … … … … … … … … … … … … … … … … … … … …小雨
rain_heavy=0.150 … … … … … … … … … … … … … … … … … … … … … … … … … … …大雨
snow=0.200 … … … … … … … … … … … … … … … … … … … … … … … … … … … … … …降雪
blizzard=0.000 … … … … … … … … … … … … … … … … … … … … … … … … … … … … …暴风雪
arctic_water=0.000 … … … … … … … … … … … … … … … … … … … … … … … … … … …洪水
mud=0.300 … … … … … … … … … … … … … … … … … … … … … … … … … … … … … … …泥泞
sandstorm=0.000 … … … … … … … … … … … … … … … … … … … … … … … … … … …沙尘暴
min_snow_level=0.000 … … … … … … … … … … … … … … … … … … … … … … … …最低降雪等级
}
}
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
此外，/Hearts of Iron IV/map/weatherpositions.txt 文件决定了
云等天气模型的位置。
格式为strategic region ID;X position;Y position;Z position;siz





