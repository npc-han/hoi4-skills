> 本文为 Paradox Wiki 页面「Map modding」的中文翻译，原文: Paradox Wiki「Map modding」
>
> 术语约定：province→省份，state→州，terrain→地形，adjacency→邻接，weather→天气，heightmap→高度图，colourmap/color table→色图/颜色表，bitdepth→位深，positioning→阵型（站位）。所有代码字段、effect/trigger/modifier 名、作用域名、文件路径与格式参数值一律保留英文原名。文中菜单路径保留英文（即界面中实际显示的名称）。

# Map modding

这是一个由社区维护的 wiki。如果你发现错误，请帮忙修正。

游戏的[地图](</Map> "Map")主要通过 `/Hearts of Iron IV/map/` 文件夹中的内容进行修改。这包括省份及其详细信息，以及地图的外观美化：树木、地形、城市。

## 目录

  * 1 快速概览
  * 2 说明
    * 2.1 BMP 格式
    * 2.2 修复损坏的 8 位地图
    * 2.3 坐标系
  * 3 州（State）修改
  * 4 战略区域
    * 4.1 天气
  * 5 省份
    * 5.1 大洲
  * 6 地形
    * 6.1 省级地形
    * 6.2 图形地形
  * 7 高度图
  * 8 法线贴图
  * 9 河流
  * 10 树木
  * 11 彩色贴图
    * 11.1 水体
    * 11.2 RGB 与城市灯光
  * 12 建筑
  * 13 单位模型与胜利点位置
  * 14 邻接
    * 14.1 邻接规则
  * 15 补给
    * 15.1 补给区域（适用于 1.11 之前的版本）
  * 16 环境物体
  * 17 参考资料

`/Hearts of Iron IV/map/*.bmp` 文件被称为位图（bitmap）。除省份位图外，它们通常用于地图的外观美化。  
`/Hearts of Iron IV/map/*.csv` 文件是 CSV 表格。它们可以在文本编辑器或表格编辑器（如 Excel 或 OpenOffice）中编辑。出于性能考虑，用文本编辑器打开这些文件会更合适。

## 快速概览

map 文件夹中这些文件是常见的修改对象：

  * `/Hearts of Iron IV/map/provinces.bmp` 用作指定省份边界的地图。
  * `/Hearts of Iron IV/map/definition.csv` 用于指定游戏内的省份信息，包括地形、大洲和沿海状态。这不会改变图形外观。
  * `/Hearts of Iron IV/map/terrain.bmp` 用作地形图，为指定位置分配纹理。这不会改变实际的省份地形，只改变外观。
  * `/Hearts of Iron IV/map/heightmap.bmp` 用作高度图，为地图上的每个像素分配一个高度。它也用于将区域置于水下。
  * `/Hearts of Iron IV/map/world_normal.bmp` 用作法线贴图，为每个像素分配一个用于光照计算的斜率。它用于生成更精确的阴影。
  * `/Hearts of Iron IV/map/terrain/colormap_rgb_cityemissivemask_a.dds` 用于为世界提供整体着色，并改变城市灯光的透明度。**世界着色的异常通常源于此文件未被调整**。
  * `/Hearts of Iron IV/map/terrain/colormap_water_0.dds` 用于为世界海洋着色。
  * `/Hearts of Iron IV/map/buildings.txt` 用于指定建筑模型的位置，并为需要邻接海洋省份的建筑（即海军基地和浮港）指定其邻接的海洋省份。**在添加新州或新省份时如果未编辑此文件，会导致游戏崩溃。**
  * `/Hearts of Iron IV/map/unitstacks.txt` 用于指定单位模型的位置、旋转信息以及单位模型的位置。
  * `/Hearts of Iron IV/map/rivers.bmp` 用于指定出现在世界中的河流。
  * `/Hearts of Iron IV/map/trees.bmp` 用于指定出现在世界中的树木模型。
  * `/Hearts of Iron IV/map/supply_nodes.txt` 和 `/Hearts of Iron IV/map/railways.txt` 用于指定游戏开始时的补给节点和铁路。**在新地图上不编辑这些文件会导致游戏崩溃。**
  * `/Hearts of Iron IV/map/adjacencies.csv` 用于在两对省份之间建立特殊关系，无论是让边界不可通行，还是创建没有边界的通道。
  * `/Hearts of Iron IV/map/adjacency_rules.txt` 用于为省份关系建立更复杂的规则，主要是关于视情况而定可以通行或不可通行的海峡。
  * `/Hearts of Iron IV/map/ambient_object.txt` 用于创建始终存在于世界地图上的模型。**这包括原版游戏南北两端的世界边界。**
  * `/Hearts of Iron IV/map/cities.txt` 用于为世界不同地区指定不同的城市模型，默认使用 `/Hearts of Iron IV/map/cities.bmp` 作为地图。
  * `/Hearts of Iron IV/map/airports.txt` 和 `/Hearts of Iron IV/map/rocketsites.txt` 已在[补丁 1.15](</Patch_1.15> "Patch 1.15") 中被弃用并移除。

## 说明

  * **由于游戏读取 BMP 文件的方式，许多图像编辑器（如 Paint.net 或 Microsoft Paint）无法用于大多数位图文件**，只有 provinces 和 world_normal 可以。Photoshop 和 GIMP 是始终可用的替代方案，但使用它们时，模式（mode）绝不能从索引模式（indexed）更改。如果地图保存错误，后面关于 BMP 格式细节的章节提供了一种简单的纠正方法。那里描述的方法也可以用来导出新文件。
  * 在 GIMP 中导出地图时，**必须勾选"不写入色彩空间信息"（do not write color space information）**。
  * 通过启动设置开启的[调试模式](</Modding#Advantages_of_using_debug> "Modding")对地图修改几乎必不可少。没有调试模式，任何标记为 MAP_ERROR 的错误都会导致游戏无法加载，而且无法在主菜单中打开 [nudger](</Nudger> "Nudger")：某些地图错误可能导致游戏在单人模式下无法打开。
  * 一些标记为 MAP_ERROR 的错误可能不会在加载主菜单时出现在错误日志中。在单人模式中选择一个国家后，错误日志将确保包含所有地图错误。
  * 使用 [nudger](</Nudger> "Nudger") 可以使地图编辑容易得多，但该工具仍然不稳定。有时手动编辑更好，但了解并使用 nudger 仍然很重要。
  * [nudger](</Nudger> "Nudger") 会将文件直接导出到[用户目录](</Modding> "Modding")，例如 Documents/Paradox Interactive/Hearts of Iron IV/ 文件夹（Windows 下的默认位置），/history/、/map/ 和 /localisation/ 文件夹可能会在该文件夹内被创建。如果这些文件留在那里，原版游戏默认也会加载它们。此外，这些文件的[优先级低于](</Modding#Loading_mods> "Modding") mod 文件：如果某个 mod 通过 [replace_path](</Replace_path> "Replace path") 卸载了生成文件夹中先前加载的文件，或者包含所编辑文件的副本，那么通过 nudger 进行的编辑可能看起来没有生效。然而，输出仍然会被创建，将文件移动到 mod 文件中并使其被加载——例如通过 nudger 中的"更新/加载"按钮（取决于菜单），或通过重新启动游戏——仍然会按预期工作。
  * [replace_path](</Replace_path> "Replace path") 可用于完全卸载在主菜单加载时被索引的单个文件夹（但不包括子文件夹）的内容。这可以用来确保启用 mod 时，原版游戏的任何战略区域或州都不会出现在加载的文件中。
  * `/Hearts of Iron IV/map/default.map` 可用于更改某个用途所使用的文件，例如省份位图的名称。在本文中，将假设 `/Hearts of Iron IV/map/default.map` 未被修改，文件名与原版游戏相同。

### BMP 格式

_本节主要介绍为什么必须遵守前两条说明的技术细节，并解释本文后面使用的一些术语，如"色图（colourmap）"和"8 位 / 8 位深（bitdepth）/ 8 bpp"。_

钢铁雄心 IV 中使用的 BMP 文件有 3 种主要类型：8 位灰度/索引灰度（8-bit greyscale/indexed greyscale）、8 位索引（8-bit indexed）和 24 位 RGB。BMP 文件可以有压缩，但钢铁雄心 IV 要求绝对不能有任何压缩，下文将假设压缩关闭。没有压缩意味着每个像素被分配相同数量的位。  
位深（bitdepth）是每个像素的位数，有时缩写为 -bit（例如 8-bit）或 bpp。每个像素的位数决定了文件中可能颜色的总数：位深为 8 意味着文件中有 2^8 = 256 种颜色，而位深为 24 意味着有 2^24 = 16777216 种不同的可能颜色。由于每个像素的位数如此重要，**mod 版本与原版版本的图像文件之间的大幅文件大小差异可以检测出错误的位深**。  
一些图像编辑器（如 GIMP）没有显式的位深选项。在这些编辑器中，可以通过限制可能颜色的数量来设置位深。RGB 图像模式允许每个像素在红、绿、蓝三色上各取 256 个值，总计 256^3 = 2^24，即位深为 24。如果图像或图层包含阿尔法通道（alpha channel）（即允许透明度）——这在创建作为叠加层的第二个图层时可能被意外打开——透明度选项会增加更多的可能颜色，最高可达 256^4 = 2^32，即位深为 32。如果图像模式设置为灰度（Greyscale），那么每个像素总共有 256 种可能的颜色，即共 2^8 种颜色，位深为 8。在索引图像模式下，可能颜色的数量取决于色图（colourmap）/颜色表的大小，范围从 2 到 256。然而在 Photoshop 中，位深是在将文件导出为 bmp 时的保存设置窗口中显式设置的：RGB 模式图像可以选择 16、24 和 32 位，索引模式图像可以选择 4 或 8 位。

更详细地介绍每种使用的图像模式：

  * 24 位 RGB 用于 `provinces` 和 `world_normal` 位图。每个像素在三个颜色通道中都被分配一个直接的颜色值，游戏会读取它进行解释：前者用于分配省份 ID，后者用于分配向量比例。
  * 8 位灰度/索引灰度用于 `heightmap` 位图。图像模式因图像编辑器而异：GIMP 中使用灰度模式，而 Photoshop 使用索引颜色模式（如下所示），配以灰度颜色表。这是因为 Photoshop 灰度模式的内部行为所致。[1] **Paint.net 和 MS Paint 不能用于编辑高度图位图**，因为它们在保存时会生成全新的色图/颜色表。每个像素被分配一个从 00 到 FF 的值，该值决定其亮度，并由游戏直接转换为高度。
  * 8 位索引用于所有其他位图：`cities`、`rivers`、`terrain` 和 `trees`。这种模式会创建一个色图（在 Photoshop 中称为颜色表 color table），即按 00 到 FF 顺序排列的 256 种颜色的列表。每个像素被分配一个从 00 到 FF 的值，直接对应色图中的一个条目。**游戏检查的是每个像素上的这个数值，而不是颜色**，并根据该 ID 的数值选取条目。因此，为了使文件正常工作，色图不能以任何形式被更改。例如，图像模式不能从索引模式（Indexed）更改为其他模式，因为图像编辑器无法在内存中保留该顺序，当图像模式改回索引模式时会生成新的顺序。出于同样的原因，**Paint.net 和 MS Paint 不能用于编辑索引位图**，因为它们在保存时会生成全新的色图/颜色表，而不是在编辑期间始终保留它。[2] 在 Photoshop 中，可以通过菜单 Image -> Mode -> Color Table 将索引颜色表保存为 .ACT 文件，以便在将图像模式更改为索引模式时稍后加载该表。较新版本的 GIMP 在将图像从 RGB 模式更改为索引模式时，可以通过"使用自定义调色板"（use custom palette）选项使用类似功能：在另一个标签页中打开原版游戏文件，即可将其色图迁移过来。

当使用不能完整支持编辑色图/颜色表的图像编辑器时，8 位索引位图很难被还原到应有的状态。添加新图层或更改图像模式都会破坏地图的解读方式。相反，可以在 mod 文件中保留另一份图像作为备份，当需要使用图层作为叠加层时，编辑备份而不是游戏所解读的文件。完成编辑后，可以在图像编辑器中将备份的内容复制并粘贴到游戏解读的文件中：这样可以在不复制文件其余格式的情况下复制每个像素的颜色。**Paint.net 或 MS Paint 无法做到这一点。**

（图片：在 GIMP 3.2.4 中 provinces.bmp 的正确导出设置）

BMP 文件的开头包含一个 DIB 头（DIB header），用于指定图像文件信息。游戏被设置为期望所有 BMP 文件以 BITMAPINFOHEADER 作为头格式。这取决于图像编辑器，大多数编辑器默认会保存为这种格式。  
然而，还存在其他头格式：最常被意外保存为的是 BITMAPV5HEADER，由 GIMP 基金会编写。它用于添加 ICC 信息，规定图像应在哪个色彩空间中被读取。_钢铁雄心 IV 的引擎无法识别这种头格式_。因此，**在 GIMP 中导出时，必须勾选"不写入色彩空间信息"（do not write color space information）**，以便以 BITMAPINFOHEADER 而非 BITMAPV5HEADER 保存。

### 修复损坏的 8 位地图

由于可以通过保留文件的一份独立 24 位副本来编辑不受调色板限制的 8 位文件，同样也可以用来修复色图被重置或错误地以更高位深保存的文件。这需要在支持索引模式图像的编辑器中完成，Paint.net 和 MS Paint 除外。Photoshop 和较新版本的 GIMP 有更好的替代方法。要进行此类修复，可以遵循以下检查清单：

  * 更改 mod 中损坏位图的文件名。本示例使用 map/terrain_broken.bmp（原文件为 map/terrain.bmp），但同样的方法适用于所有其他位图。
  * 将原版游戏对应的 map/terrain.bmp 文件复制到 mod 中。如果 mod 的地图尺寸已更改，应将此图像调整到正确的尺寸，否则不应改动它。
  * 在编辑器中打开 map/terrain_broken.bmp 和 map/terrain.bmp。**Paint.net 或 MS Paint 无法完成此操作。**
  * 在图像编辑器中，选中 map/terrain_broken.bmp 中的所有内容（使用 ctrl+A 或 Select -> All）并复制到剪贴板。
  * 将内容粘贴到 map/terrain.bmp 中。
  * 以正确的设置导出打开的 map/terrain.bmp 文件。
  * 可选：删除 map/terrain_broken.bmp 文件。

在 Photoshop 中，这可以很容易地通过索引颜色表（Indexed Color Table）菜单完成。对于已经是 8 位索引模式但调色板不正确的 BMP 文件：

  * 在 Photoshop 中打开对应的原版游戏文件。
  * 选择菜单 Image -> Mode -> Color Table。
  * 点击"Save"，保存一个包含颜色表的 .ACT 文件。
  * 在 Photoshop 中打开损坏的文件。
  * 选择菜单 Image -> Mode -> Color Table。
  * 点击"Load"并加载保存的 .ACT 文件，然后点击 OK。
  * 转到"File"菜单并点击"Save"。

同样的方法也可以用于导出新文件，从 RGB 模式开始：

  * 在 Photoshop 中打开 RGB 模式图像。
  * 选择菜单 Image -> Mode -> Indexed Colors。
  * 在调色板（palette）中选择"Custom"。
  * 在 Color Table 菜单中，选择"Load"并选择从对应原版游戏文件保存的 .ACT 文件，然后点击 OK。
  * 转到"File"菜单并点击"Save a copy"。
  * 在保存对话框中，选择 BMP。
  * 在出现的设置对话框中，格式选择"Windows"，位深选择"8 bits"，复选框保持不勾选，然后点击 OK。

在较新版本的 GIMP 中，可以通过索引模式窗口中的"使用自定义调色板"（use custom palette）选项完成：

  * 在 GIMP 中打开对应的原版游戏文件。
  * 在 GIMP 中打开损坏的文件，保持两个文件都打开。
  * 转到 Image -> Mode，如果还不是 RGB 则更改为 RGB。
  * 转到 Image -> Mode 并选择 Indexed。
  * 选择"Use Custom Palette"选项，并点击其下方的图标。
  * 在下拉列表中选择"Colormap of Image#(number)..."，即原版游戏文件。
  * 点击转换（convert）。
  * 导出打开的文件，禁用写入色彩空间信息的选项。

### 坐标系

由于地图是 3D 对象，使用[典型的笛卡尔坐标系](<http://en.wikipedia.org/wiki/Cartesian_coordinate_system> "wp:Cartesian coordinate system")，存在 X、Y、Z 位置，这在许多地图文件中被广泛引用。为避免歧义，以下是游戏使用的坐标：

  * 一个 X 坐标相当于省份位图水平方向上的一个像素。地图的水平边缘为 0，从左到右（或从西到东）。请注意，地图在水平方向上是循环的（左右相接）。
  * 一个 Y 坐标相当于高度图中的一个值 10（十进制）。Y 位置为 0 相当于高度图上的纯黑，而 Y 位置为 25.5 相当于高度图上的纯白。例如，水位位于 Y 位置的 9.5 处[3]。
  * 一个 Z 坐标相当于省份位图垂直方向上的一个像素。地图的下（或南）边缘为 0，从下到上（或从南到北）。**请注意，大多数图像编辑器正好相反**：轴的 0 位置在顶部，从上到下。想知道 Z 坐标时，可以更改编辑器使用的坐标系（如果可能），或暂时将图像上下翻转。

## 州（State）修改

（图片）

_主条目：[State modding（州修改）](</State_modding> "State modding")_

州在 `/Hearts of Iron IV/history/states/*.txt` 文件中定义，包含州的信息：州包含的省份、州类别、初始建筑和拥有者。  
nudger 可以用于编辑州，但存在几个问题：

  * 如果州名包含任何需要超过一个字节才能在 UTF-8 中表示的特殊字符（例如英文字母以外的任何字母），nudger 在尝试创建时会崩溃。
  * 如果 nudger 编辑了州的历史文件，文件中的每个引号都会被移除。这可能会破坏 DLC 检查（例如 `has_dlc = "Waking the Tiger"`），原版土耳其或中国的州中就存在这种情况。

此外，请注意 nudger 会动态地用州更新战略区域：新创建的州，其省份不会被分配到战略区域，必须通过 nudger 来完成。由于战略区域是为每个省份单独分配的，只要战略区域边界不需要调整，删除用户目录 /Hearts of Iron IV/map/strategicregions/ 文件夹中的战略区域输出可能是可行的，因为一个州的所有省份必须位于同一个战略区域内。

## 战略区域

（图片）

_主条目：[Strategic region modding（战略区域修改）](</Strategic_region_modding> "Strategic region modding")_

[战略区域](</Strategic_region> "Strategic region") 在 `/Hearts of Iron IV/map/strategicregions/*.txt` 文件中定义，省份在其中被单独添加。一个省份_必须_拥有一个战略区域。否则，与该省份的许多交互都会导致游戏崩溃，有时在游戏启动前就会出现。战略区域用于海军和空军，作为可以分配的区域，但它们也用于分配天气。海军战略区域还可以通过 `naval_terrain = terrain_name` 分配海军地形。

### 天气

_另见：[Nudger § 天气](</Nudger#Weather> "Nudger")_

每个战略区域都有按时间段定义的天气，如下所示：

    weather={
    		period={
    			between={ 0.0 30.11 }
    			temperature={ -6.0 12.0 }
    			no_phenomenon=0.500
    			rain_light=1.000
    			rain_heavy=0.150
    			snow=0.200
    			blizzard=0.000
    			arctic_water=0.000
    			mud=0.300
    			sandstorm=0.000
    			min_snow_level=0.000
    		}
        }
    }

  * `between = { ... }` 决定天气适用的时间段：时间段定义中的两个日期都包含在时间段内；第一个数字是日，第二个是月；日和月的计数从 0 而不是 1 开始。
  * `temperature = { ... }` 分配该时间段内的最高和最低温度。

大多数行决定战略区域中每个省份出现该天气类型的几率（假设 `no_phenomenon`（无天气）的几率判定为假）。多种天气类型可以同时发生，天气按天计算。  
`min_snow_level` 决定该区域雪的视觉外观。通常在特别多雪的时期（如北斯堪的纳维亚的冬天）设置为 0.1。

此外，`/Hearts of Iron IV/map/weatherpositions.txt` 文件决定天气物体（如云）的_位置_。文件中的单个条目具有以下布局：

    strategic region ID;X position;Y position;Z position;size

例如，`1;2781.24;9.90;1571.49;small` 表示 ID 为 1 的战略区域在指定位置有一个小的天气物体。  
大小只有两个值：small 和 large。同一个战略区域可以有多条定义，也可以完全没有，但为避免游戏崩溃，文件中至少应各有一条 large 和 small 物体的定义。

天气最好在 nudger 的战略区域菜单中生成。

## 省份

_另见：[Nudger § 数据库](</Nudger#Database> "Nudger")_

有两个与省份定义相关的文件：

  * `/Hearts of Iron IV/map/provinces.bmp`，决定省份如何放置在地图上。
  * `/Hearts of Iron IV/map/definition.csv`，决定省份的确切细节：ID、用于关联地图的 RGB 值、沿海状态、地形。

省份位图保存为 24 位 RGB 文件。以 32 位保存会导致"我们不支持 32 位位深"（'We do not support bitdepth at 32'）错误，并在启动时崩溃。省份位图保存错误（如之前的 32 位位深，或错误的格式被重命名为 BMP）会导致"X4008: floating point division by zero"错误。由于其他地图文件的比例，**长度和宽度都必须是 256 的倍数**。由于引擎限制，文件的像素总面积不能超过约 13 238 272。

可以使用 nudger 中的数据库菜单为位图选择一个未使用的颜色。不过，考虑到可能颜色的数量之多，也可以使用 [random.org](<https://www.random.org/colors/hex>) 之类的网站随机化一个颜色，重叠的几率极低。  
如果省份定义不完整或不正确，游戏会在[用户目录](</Modding> "Modding")中创建该文件的"已修正"副本，以替换省份定义的 CSV 表格，并填上任何不完整或缺失的省份定义，文件名为 `/Hearts of Iron IV/map/definition.csv.fixed`，该文件夹中还有其他副本。对原始文件进行修改后，可能有必要删除这个副本。[4]  
这两者都可以用来加快省份创建：在地图上绘制随机颜色，然后让游戏生成 CSV 表格，再将其移植到 mod 中后手动调整。

前面提到的用户目录中省份定义的修正副本，将包含 mod 的 definition.csv 中的每一条省份定义，以及位图上存在但没有定义的所有颜色（这些颜色会有一个默认条目，作为没有大洲、地形未知的陆地省份）。这可以与图像编辑器一起使用，以定位 provinces.bmp 文件中任何意外的颜色。这些颜色可能是选错颜色或在编辑地图时打开了抗锯齿（anti-aliasing）导致的。要定位它们，可以选中新生成省份的颜色，将其涂在安全的涂色位置（如大片的海洋省份或地图的一角），然后使用容差（tolerance）/阈值（threshold）为 0% 的颜色选择工具（选项名称因图像编辑器而异）来选择它。在图像编辑器中，这通常是一个单独的选项（如 GIMP 中），或者是魔棒（magic wand）工具内使其具有全局填充模式（global flood mode）的选项（如 Paint.net 中）。  
定位到该省份后，如何处理它由 modder 决定：可以将其完全做成一个单独的省份（因此应该分配到州和战略区域），可能改变其边界以更好地契合；也可以将其完全从 provinces.bmp 文件中移除。无论哪种情况，为了找到该省份而涂出的位置都应恢复到初始状态。

`/Hearts of Iron IV/map/definition.csv` 中的条目遵循指定的格式：

    Province ID; R value; G value; B value; Province type (land|sea|lake); Coastal status (true/false); Terrain; Continent (integer)

示例定义包括：

    7;212;179;179;sea;true;ocean;0
    114;40;15;15;land;false;plains;1
    260;170;235;235;land;true;urban;1

每个省份的 RGB 值应该是唯一的，以便在位图上识别它们，并且是在 0 到 255 范围内的整数。大洲必须是一个代表大洲的整数。对于海洋省份，必须保持为 0，而对于湖泊_可以_保持为 0。  
这里为每个省份单独定义了地形。这_不会_改变省份的外观，只改变分配的地形，从而改变海战或陆战。对于湖泊省份，地形必须是"lakes"，而对于海洋省份必须是"ocean"。  
在这里，沿海状态用于海洋和陆地省份。对于陆地省份，它意味着与海洋省份（不是湖泊）接壤；对于海洋省份，它意味着与陆地省份接壤。这用于确定例如哪些地方可以建海军基地、哪些地方不能。如果位图和省份定义在省份是否沿海的问题上不一致（例如，一个陆地省份被指定为非沿海，但仍然与海洋省份接壤，反之亦然），则以位图的结果为准。这确实意味着没有必要在省份定义中指定沿海状态，它是 1.11 之前的遗留物。

**省份 ID 应该按顺序排列**。虽然省份 ID 的间隙不一定会导致游戏崩溃，但会产生另一个问题：间隙之后的每个省份都会继承按 ID 排列的下一个省份的属性（除了位图上的颜色）。例如，如果省份 23 不存在，省份 24 将继承省份 25 的地形、类型（land|sea|lake）、沿海状态和大洲，而省份 25 又会从省份 26 复制，依此类推。这即使不崩溃也会导致非常意外的行为，所以最好不要有任何省份 ID 间隙：如果要删除一个省份，必须有另一个省份填补该空缺，例如按 ID 排列的最后一个省份。

（图片）

钢铁雄心 IV 对省份显示有限制。同时可显示的省份边界不能超过 65536 条，超出后整数溢出会导致游戏引擎停止显示任何更多的边界。在游戏中，这通常在约 21000 个省份时达到。换句话说，**省份数量应保持较低**，原版约 13000-14000 个省份是值得追求的数量。更多省份会导致游戏运行更慢、更不稳定。如果一个省份以不连续的方式多次与另一个省份接壤，如附图所示，每一次都算作一条独立的边界。

这些不连续的岛屿省份如果彼此距离过大、过于分散（_过于_不连续），也可能导致游戏崩溃。常规省份不必担心这一点，但如果两个省份意外共享一种颜色，就可能发生这种情况。  
根据 definition.csv，陆地省份与海洋或湖泊省份之间也不会绘制省份边界。如果有某个位置或一系列位置省份不工作，也可能是 definition.csv 中省份类型分配错误造成的。

此外，这些错误很常见：

  * "Map invalid X crossing. Please fix pixels at coords"（地图无效 X 交叉，请修复坐标处的像素）：四个省份共享一个公共角。游戏会连接左下和右上的省份，但这种情况让玩家感到困惑，应避免。请记住，地图在水平方向上是循环的，所以这个 X 交叉可能就在省份位图的边缘。错误信息坐标中使用的坐标系是更传统的从左到右、再从下到上的方式，而不是游戏在其他情况下使用的坐标系。
  * "Province X has TOO LARGE BOX. Perhaps pixels are spread around the world in provinces.bmp"（省份 X 的框太大，或许像素散布在 provinces.bmp 中的世界各地）：该省份的宽度/高度超过整个地图宽度/高度的 1/8。这可能表明两个省份意外地共享一种颜色，但也可能只是一个很大的省份。游戏对大省份的处理不稳定，应避免。
  * "Province X has only N pixels"（省份 X 只有 N 个像素）：该省份的像素数不超过 NGraphics.MINIMUM_PROVINCE_SIZE_IN_PIXELS（默认 8）。这很可能太小，玩家难以正常使用。
  * "Prov 12345 has no continent"（省份 12345 没有大洲）：该错误有两个常见原因。
    * 该省份是陆地省份，但其大洲 ID 仍为 0。**这也包括自动生成的省份定义**，即当发现定义文件中不存在的颜色时生成的。如果该省份不在常规的 provinces.csv 文件中，请检查游戏在用户目录中创建的"fixed"变体。
    * 省份条目不是以 Windows 风格的 CRLF 行尾结束。如果每个省份的大洲都坏了，这表示文件中的换行不正确（Unix LF 格式是常见原因，某些程序会更改 .csv 文件中的换行）。转换为 CRLF 的具体过程取决于文本编辑器。例如：在 Visual Studio Code 中通过底部状态栏更改；在 Notepad++ 中通过编辑（Edit）菜单中的"EOL conversion"更改；在 Sublime Text 中通过视图（View）菜单中的"Line endings"更改。
  * "Bitmap and province definition disagree on whether or not province 12345 is coastal. Bitmap adjacency result will be prefered."（位图和省份定义在省份 12345 是否沿海的问题上不一致，将以位图邻接结果为准。）：此错误有这些常见原因：
    * 正如错误所述：该省份是沿海的陆地省份，但在 `/Hearts of Iron IV/map/definition.csv` 中被标记为非沿海，反之亦然。请相应调整省份定义。
    * 文件前面某处缺少一个省份 ID，导致后面文件中的省份定义与其预期颜色错位。如果此类错误数量很大，且省份定义与沿海状态相符，通常就是这种情况。看起来损坏的地形地图模式（terrain map mode）也可能是此问题的迹象。可以通过比较省份 ID 与其定义所在行号之间的差值来检查：如果省份 0 的定义存在，行号和省份 ID 的差值必须为 1。
  * "Province X has no pixels in provinces.bmp"（省份 X 在 provinces.bmp 中没有像素）：该错误有两个常见原因，取决于位图中是否有任何该颜色的像素（可以用图像编辑器中的颜色选择工具检查）。
    * 省份定义中的颜色在 provinces.bmp 中没有像素。这可能表明省份使用了错误的颜色，或者省份已被擦除但条目仍然存在。请确保游戏生成的"fixed"定义中没有可能原本就是这个无像素省份的新省份；否则，删除该省份定义，并用另一个省份定义填补空缺，因为省份 ID 不能有间隙。
    * 位图上有几个省份使用相同的颜色。如果是这种情况，游戏会将这种颜色的每个像素只分配给其中一个省份，导致另一个省份没有像素。如果它们打算是分开的，更改第二个省份的颜色；否则删除其中一个定义，同时确保有另一个省份定义填补空缺。这通常（但不是必然）会与另一个省份的 TOO LARGE BOX 错误同时出现。

### 大洲

大洲在 `/Hearts of Iron IV/map/continent.txt` 文件的 `continents = { ... }` 表中定义。大洲在游戏中有多种用途：

  * 用于[以州为作用域的触发条件](</Conditions#is_on_continent> "Conditions")检查大洲。
  * 用于为各种 AI 策略分配 [AI 区域](</AI_modding#AI_areas> "AI modding")。
  * 用于在没有为国家定义特定肖像时，为国家分配[一组随机生成的肖像](</Portrait_modding> "Portrait modding")。

大洲块是一个简单的大洲列表。ID 按定义的顺序分配。

所有陆地省份都必须属于一个大洲，以避免错误。大洲在 `/Hearts of Iron IV/map/definition.csv` 中分配，也可以通过 nudger 的数据库菜单应用。大洲不需要遵循州边界。  
原版游戏存在以下大洲：

ID  | 内部名称  | 本地化名称  | 说明   
---|---|---|---  
1  | europe  | Europe（欧洲）  | 包括塞浦路斯。欧洲与亚洲的边界沿乌拉尔山脉延伸，将卡尔梅克（Kalmykia）和阿尔汉格尔斯克（Archangelsk）等州一分为二。   
2  | north_america  | North America（北美洲）  | 错误地包括阿尔巴尼亚北伊庇鲁斯（North Epirus）的 2 个省份。   
3  | south_america  | South America（南美洲）  | 这也包括中美洲（一直到危地马拉/伯利兹，含这两个国家）和加勒比地区。   
4  | australia  | Australia（澳大利亚）  | 这_仅_包括澳大利亚大陆、新西兰，以及巴布亚、塔斯马尼亚、俾斯麦、所罗门群岛和法属波利尼西亚的岛屿和群岛。其余太平洋岛屿属于亚洲。   
5  | africa  | Africa（非洲）  | 苏伊士运河被视为边界：整个西奈州都在中东。   
6  | asia  | Asia（亚洲）  | 与中东的边界切开了赫拉特（Herat）和俾路支（Baluchistan）州，将其一分为二，而其他州几乎完全包含在其中一个之中。   
7  | middle_east  | Middle East（中东）  | 与欧洲在高加索山脉的边界将阿布哈兹（Abkhazia）、卡巴尔达-巴尔卡尔（Kabardino-Balkaria）、北奥塞梯（North Ossetia）、阿塞拜疆和伊斯坦布尔一分为二。其余完全包含在其中一个之中。   

## 地形

游戏中有两种主要类型的地形：图形地形（graphical）和省级地形（provincial）。两种地形类型都在 `/Hearts of Iron IV/common/terrain/*.txt` 文件中定义。  
省级地形在 `/Hearts of Iron IV/map/definition.csv` 中为每个陆地省份分配，海洋省份则在战略区域中分配。这不会以任何方式改变图形外观（除了"简单地形"（simple terrain）地图模式），而是为省份分配修正（modifier）以及陆战或海战的细节。  
图形地形在 `/Hearts of Iron IV/map/terrain.bmp` 中分配给地图本身。该文件是 8 位索引图像，与省份位图具有相同的尺寸。这纯粹是地图的视觉外观，不会以任何实际方式改变地图。但是，nudger 中的数据库部分有一个设置，可以根据图形地形自动生成省级地形。

以下是原版游戏地形类型的表格。  
由于游戏根据色图 ID 决定地形，图形地形中的颜色可以改成任何值，只要色图 ID（ID 列中指定的）相同，文件的处理就没有区别，所以这里的颜色只是原版游戏使用的颜色。图形地形表中的"地形类型"（Terrain type）指的是 nudger 生成的省级地形类型。图形地形表中的"外观"（Appearance）是指图集（atlas）文件中指定的一段以完全不透明显示：实际上，图集文件具有透明度，因此地形的某些部分比其他部分更可见。

图形地形：  
展开 ID | 颜色 | 外观 | 地形类型 | 说明   
---|---|---|---|---  
0  | (86, 124, 27)  |  | plains  |   
1  | (0, 86, 6)  |  | forest  | 通常用于茂密的森林。   
2  | (112, 74, 31)  |  | hills  |   
3  | (206, 169, 99)  |  | desert  |   
4  | (6, 200, 11)  |  | forest  | 通常用于稀疏的森林。   
5  | (255, 0, 24)  |  | plains  | 通常用于农田。   
6  | (134, 84, 30)  |  | mountain  |   
7  | (252, 255, 0)  |  | desert  |   
8  | (73, 59, 15)  |  | desert  |   
9  | (75, 147, 174)  |  | marsh  |   
10  | (174, 0, 255)  |  | mountain  |   
11  | (92, 83, 76)  |  | mountain  |   
12  | (255, 0, 240)  |  | desert  |   
13  | (240, 255, 0)  |  | urban  | 自动生成城市模型。   
14  | (55, 90, 220)  |  | lakes  | 游戏中从未使用，似乎指向一个无效的纹理。相反，湖泊使用的是海洋地形。   
15  | (8, 31, 130)  |  | ocean  |   
16  | (255, 255, 255)  |  | mountain  | 永久被雪覆盖，与使用相同外观的其他图形地形不同。   
17  | (132, 255, 0)  |  | hills  |   
18  | (255, 126, 0)  |  | mountain  | 原版游戏从未使用。   
19  | (114, 137, 105)  |  | plains  | 永久被雪覆盖，与使用相同外观的其他图形地形不同。   
20  | (58, 131, 82)  |  | mountain  |   
21  | (255, 0, 127)  |  | jungle  |   
22  | (0, 82, 82)  |  | jungle  | 原版游戏从未使用。   
27  | (243, 199, 147)  |  | mountain  |   
31  | (27, 27, 27)  |  | mountain  | 原版游戏从未使用。   

省级地形：  
展开 内部名称 | 本地化名称 | 地形类型 | 说明   
---|---|---|---  
unknown  | Unknown（未知）  | land  | 新省份的默认地形。   
forest  | Forest（森林）  | land  | 84 最优战斗宽度，-15% 师攻击力。   
hills  | Hills（丘陵）  | land  | 80 最优战斗宽度，-25% 师攻击力。   
mountain  | Mountain（山地）  | land  | 75 最优战斗宽度，-50% 师攻击力。   
plains  | Plains（平原）  | land  | 90 最优战斗宽度。   
urban  | Urban（城市）  | land  | 96 最优战斗宽度，-30% 师攻击力。   
jungle  | Jungle（丛林）  | land  | 84 最优战斗宽度，-30% 师攻击力。   
marsh  | Marsh（沼泽）  | land  | 78 最优战斗宽度，-40% 师攻击力。   
desert  | Desert（沙漠）  | land  | 90 最优战斗宽度。   
ocean  | Ocean（海洋）  | sea  | 海洋省份的默认地形。   
lakes  | Lakes（湖泊）  | sea  | 湖泊省份的默认地形。   
water_fjords  | Fjords and Archipelagos（峡湾与群岛）  | naval  | 使战列巡洋舰、战列舰、重型巡洋舰和航空母舰的可用性降低（-20% 攻击、防御和移动），使海军更难被侦测（-20% 可见度），并从阵型中移除 15%。   
water_shallow_sea  | Shallow Sea（浅海）  | naval  | 使潜艇更容易被侦测（+100% 可见度），并从阵型中移除 5%。   
water_deep_ocean  | Deep Oceans（深海）  | naval  | 使驱逐舰的攻击、移动和防御降低 20%；轻巡洋舰的攻击、移动和防御降低 10%；使潜艇更难被侦测（-15% 可见度），但速度降低 25%。命中水雷的几率减半。   

### 省级地形

省级地形类型在 `/Hearts of Iron IV/common/terrain/*.txt` 文件的 `categories = { ... }` 块中定义。每个地形是一个代码块，块名会被用作地形的名称，例如下面会创建 my_terrain_1 和 my_terrain_2 两个地形。

    categories = {
        my_terrain_1 = {
            <...>
        }
        my_terrain_2 = {
            <...>
        }
    }

以下参数可以放入地形中：

  * `color = { 100 120 140 }` 在"简化地形"（Simplified terrain）地图模式中为地形分配一种颜色。这是一个 RGB 值，每个值在 0 到 255 范围内。
  * `sound_type = forest` 分配地形播放的环境音效。这似乎未被使用。
  * `movement_cost = 1.0` 分配这种地形的移动速度。值越高意味着师或舰船移动越慢。
  * `units = { ... }` 分配以单位为作用域的修正，会应用到战斗时该省份内的每个陆军师或舰船，例如 `attack = 0.1` 或 `defence = -0.1`。
  * `my_subunit = { ... }` 用于让这些修正只应用到特定的子单位，其中的 `units = { ... }` 用于战斗，其余用于战斗之外。例如：

    carrier = {
        units = {
            attack = -0.2
        }
        navy_fuel_consumption_factor = 0.2
    }

  * 此外，地形本身也是一个修正块，允许在其中使用任何省份[修正](</Modifiers> "Modifiers")。其中包括许多与战斗相关和以州为作用域的修正。
  * 如果设置了 `is_water = yes`，将把该地形标记为水体地形，使其只能用于海洋和湖泊。此外，还需要 `naval_terrain = yes` 将其标记为可以用于战略区域的海军地形。

以下参数**仅适用于陆地**地形：

  * `combat_width = 78` 分配基础战斗宽度。这么多单位可以在没有惩罚的情况下参加战斗。
  * `combat_support_width = 24` 在 combat_width _之上_分配战斗宽度。超过 combat_width 后，还可以有 combat_support_width 更多的部队参战，但会有惩罚。
  * `supply_flow_penalty_factor = 0.2` 对通过具有这种地形的省份的补给流施加惩罚。
  * `match_value = 8` 为该省份分配一个地形价值。这主要用于 [state_and_terrain_strategic_value](</Conditions#state_and_terrain_strategic_value> "Conditions") 触发条件/变量，以及为和平会议分配成本。
  * `ai_terrain_importance_factor = 8.0` 为 AI 在这种地形上分配一个战略重要性值。值越高意味着 AI 会认为该省份值得防守。
  * `buildings_max_level = { bunker = 4 }` 修改省份级别建筑的最高等级。

一些常用的[修正](</Modifiers> "Modifiers")包括：

  * `attrition = 0.1` 设置陆地省份的基础损耗。
  * `enemy_army_bonus_air_superiority_factor = -0.1` 修改由空中优势给予攻击者的加成。
  * `sickness_chance = 1.0` 使在该省份作战可能导致陆军将领生病。
  * `positioning = 0.1` 修改该海洋省份内的海军阵型。
  * `navy_visibility = -0.2` 修改该海洋省份内海军的可见度。

可以使用地形 ID 作为本地化键添加[本地化](</Localisation> "Localisation")：

     my_terrain_1: "My terrain"
     my_terrain_1_desc: ""

对于陆地省份，有 2 个精灵图（sprite）：常规（名称前加 GFX_terrain_）和冬季（末尾额外加 _winter）。冬季精灵图在省份被雪覆盖时出现，常规精灵图正常出现。对于 `my_terrain_1`，它们如下：

    spriteType = {
        name = GFX_terrain_my_terrain_1
        textureFile = gfx/interface/terrains/terrain_my_terrain_1.dds
    }
    spriteType = {
        name = GFX_terrain_my_terrain_1_winter
        textureFile = gfx/interface/terrains/terrain_my_terrain_1_winter.dds
    }

对于海军地形，有 10 个精灵图：常规、雨、暴风雨、雪和暴风雪，白天和夜晚各一套。

展开 名为 `my_naval_terrain_1` 的海军地形的 sprite 定义示例  
---  
    spriteType = {
        name = GFX_terrain_my_naval_terrain_1_day
        textureFile = gfx/interface/terrains/terrain_my_naval_terrain_1_day.dds
    }
    spriteType = {
        name = GFX_terrain_my_naval_terrain_1_day_rain
        textureFile = gfx/interface/terrains/terrain_my_naval_terrain_1_day_rain.dds
    }
    spriteType = {
        name = GFX_terrain_my_naval_terrain_1_day_storm
        textureFile = gfx/interface/terrains/terrain_my_naval_terrain_1_day_storm.dds
    }
    spriteType = {
        name = GFX_terrain_my_naval_terrain_1_day_snow
        textureFile = gfx/interface/terrains/terrain_my_naval_terrain_1_day_snow.dds
    }
    spriteType = {
        name = GFX_terrain_my_naval_terrain_1_day_snow_storm
        textureFile = gfx/interface/terrains/terrain_my_naval_terrain_1_day_snow_storm.dds
    }
    spriteType = {
        name = GFX_terrain_my_naval_terrain_1_night
        textureFile = gfx/interface/terrains/terrain_my_naval_terrain_1_night.dds
    }
    spriteType = {
        name = GFX_terrain_my_naval_terrain_1_night_rain
        textureFile = gfx/interface/terrains/terrain_my_naval_terrain_1_night_rain.dds
    }
    spriteType = {
        name = GFX_terrain_my_naval_terrain_1_night_storm
        textureFile = gfx/interface/terrains/terrain_my_naval_terrain_1_night_storm.dds
    }
    spriteType = {
        name = GFX_terrain_my_naval_terrain_1_night_snow
        textureFile = gfx/interface/terrains/terrain_my_naval_terrain_1_night_snow.dds
    }
    spriteType = {
        name = GFX_terrain_my_naval_terrain_1_night_snow_storm
        textureFile = gfx/interface/terrains/terrain_my_naval_terrain_1_night_snow_storm.dds
    }

### 图形地形

图形地形在 `/Hearts of Iron IV/common/terrain/*.txt` 文件的 `terrain = { ... }` 块中定义。每种图形地形类型都是该总块中的一个独立块，块名无关紧要，可以重复。示例定义包括：

    terrain = {
        my_terrain = {
            type = urban
            color = { 23 }
            texture = 1
            spawn_city = yes
        }
        my_terrain = { type = plains color = { 24 } texture = 2 }
    }

在这里，`type = urban` 告诉 nudger 在自动生成省份地形时，会将哪种省级地形类型分配给这种图形地形；`color = { ... }` 是用于图形纹理的色图索引列表；`texture = 1` 分配图集定义，从 0 开始。  
可选参数有 `spawn_city = yes`，会自动生成城市模型；以及 `perm_snow = yes`，使指定区域永久被雪覆盖。

图集文件是 `/Hearts of Iron IV/map/terrain/atlas0.dds` 和 `/Hearts of Iron IV/map/terrain/atlas_normal0.dds`。它们必须是正方形。默认情况下，每个都是 4x4[5] 的瓦片（tile）地图，其中每个瓦片为 512x512 像素。[6]  
瓦片按从左到右、从上到下的顺序排列，从 0 开始，如下表所示。

0  | 1  | 2  | 3   
---|---|---|---  
4  | 5  | 6  | 7   
8  | 9  | 10  | 11   
12  | 13  | 14  | 15   

这种顺序意味着，例如，图形定义中的 `texture = 11` 在默认的 4x4 排列下会选中最右侧、中间偏下位置的瓦片。  
atlas0 是常规纹理贴图，用于分配在地形上的纹理；atlas_normal0 是[法线贴图](<http://en.wikipedia.org/wiki/Normal_mapping> "wp:Normal mapping")，用于分配垂直于纹理上每个点的向量，在给地图着色时使用。

文件 atlas1 和 atlas2，以及 atlas_normal1 和 atlas_normal2 用于更远的缩放级别：当镜头拉得更远或使用不同的图形设置时，游戏使用较低质量的纹理以节省性能。atlas1 的每个维度都是 atlas0 的一半大小，atlas2 的每个维度是 atlas0 的四分之一大小。

为了让游戏读取文件，必须生成 mipmap，并且必须使用 DXT5 作为压缩算法。

## 高度图

`/Hearts of Iron IV/map/heightmap.bmp` 用于确定地图上某个位置的高度。最小值（或纯黑）对应 Y 轴高度 0，而最大值（或纯白）对应 Y 轴高度 25.5。  
海平面默认设置为高度 9.5[3]，因此低于值 95（在 0 到 255 的范围内）的任何值都将显示为水下，而高于 95 的所有值都将显示在水面之上。水面始终保持恒定高度。

应优先创建像素值之间的平滑过渡，以产生更平滑的过渡效果。除了高度图之外，法线贴图也有助于平滑。

高度图与省份位图具有相同的图像尺寸，并保存为 8 位灰度/索引灰度图像。

## 法线贴图

`/Hearts of Iron IV/map/world_normal.bmp` 是一张以 24 位 RGB 格式保存的[法线贴图](<http://en.wikipedia.org/wiki/Normal_mapping> "wp:Normal mapping")，决定地图 3D 渲染中每个像素的确切斜率。它用于光照计算。  
红色通道决定向量的 X 值，范围从 -1 到 1：值为 0 时尽可能指向左（西），值为 255 时尽可能指向右（东）。  
绿色通道决定向量的 Y 值，范围从 -1 到 1：值为 0 时尽可能指向下（南），值为 255 时尽可能指向上（北）。  
蓝色通道决定向量的 Z 值，范围从 0 到 -1：值 128 对应 0，表示不指向观察者，而是根据 X 和 Y 值垂直指向；值 255 对应 -1，表示尽可能指向观察者。

有几种工具可以用来从高度图生成法线贴图。其中包括：

  * 适用于 Photoshop 的 NVIDIA Texture Tools Exporter  
打开高度图并将图像模式更改为 RGB，保存副本菜单 -> 选择 NVIDIA 导出器的 DDS 格式。在 Image Type 中选择"Normal Map : Tangent Space"。在"Height Source"中选择"Max RGB"或"Average RGB"，在"Height Generation"中勾选"Wrap"和"Invert Y"。"Filter Type"可以随意更改，查看预览以了解效果。"Scale"是改变地图整体观感的最重要因素，因为它控制法线贴图的强度。Scale 越大，地图越"凹凸"。数值较低时，生成的地图是空的。将"Alpha Field"设置为"Unchanged"，并勾选"Normalize"。然后打开保存的 DDS 文件并导出为 BMP 格式。
  * NVIDIA Texture Tools 独立应用程序  
与 Photoshop 插件中的界面工作方式相同。
  * 在旧版本的 Photoshop CC 中：Filter -> 3D -> Generate Normal Map。  
Photoshop 的 3D 功能于 2021 年停止使用，并于 2024 年完全移除。最后一个完整可用的版本是 2021 v22.2。
  * 在 GIMP 中，使用法线贴图插件  
下载插件，打开高度图，将图像模式更改为 RGB，Filter -> Map -> Normal，并反转 Y 轴。

## 河流

（图片：这是河流在位图中外观的示例。每个像素已被放大为 50x50 像素的正方形，河流从右向左流动。）

`/Hearts of Iron IV/map/rivers.bmp` 是一个 8 位索引位图文件，决定河流的位置。河流_必须_正好一个像素宽，并且只能沿正交方向延伸：像素不能对角连接。由于河流被视为 1 级铁路[7]，特别长的河流会导致游戏变慢或运行不稳定。河流图是 8 位索引位图，与省份位图具有相同的尺寸。

要正确渲染，每条河流必须有且只有一个（默认为绿色的）起点标记。在这种情况下，河流被视为单个连续的河流像素块：通过红色流入源（flow-in）或黄色流出源（flow-out）连接的那些与主流算作同一条河流。**只有河流的"主支"应有绿色源头像素，任何通过红色流入源连接到它的支流都不应有。**

**rivers.bmp** 中使用的颜色：索引 | 颜色 | 功能   
---|---|---  
0  | (0, 255, 0)  | 河流的源头   
1  | (255, 0, 0)  | 流入源。用于将多条"源头"路径合并为一条河流。   
2  | (255, 252, 0)  | 流出源。用于从一条河流向外分支。   
3  | (0, 225, 255)  | 纹理最窄的河流。   
4  | (0, 200, 255)  | 纹理窄的河流。   
5  | (0, 150, 255)  |   
6  | (0, 100, 255)  | 纹理宽的河流。   
7  | (0, 0, 255)  |   
8  | (0, 0, 225)  |   
9  | (0, 0, 200)  |   
10  | (0, 0, 150)  |   
11  | (0, 0, 100)  | 纹理最宽的河流。   

默认情况下，索引 0[8] 到 6（含）[9] 在游戏机制中被视为小河，索引到 11（含）[10] 为大河。文件中任何其他索引的像素在游戏中不会被读取，并作为"注释"使用，通常用于标示陆地省份的轮廓，以便更容易放置河流。

如果连接两个省份中心的[路径](</Terrain#Paths> "Terrain")与至少一个河流像素重叠，则视为河流渡口。如果它与多个不同类型的河流像素相交，渡口类型由实现决定。为避免玩家困惑，省份路径应清晰切割河流或远离河流。

可能遇到的一个错误是 `MAP_ERROR: Palette in rivers.bmp is probably not correct`（rivers.bmp 中的调色板可能不正确）。这个错误_完全可以忽略_：rivers.bmp 文件无论如何都会被加载，与其他地图错误不同，在没有调试模式的情况下它不会阻止游戏加载。  
此错误由 GIMP 引起：在 Photoshop 中编辑不会产生此错误。默认情况下，DIB 头被设置为说明色图有 0 种颜色，尽管色图实际上仍然包含 256 种颜色。这是为了确保游戏不花时间读取 BMP 文件中的颜色，而是直接跳到位图本身。GIMP 反而将 DIB 头设置为说明调色板中有 256 种颜色，这是游戏没有预料到的。这无法在 GIMP 内部修复，但假设河流位图在其他方面是正确的（以 8 位索引模式和 BITMAPINFOHEADER 保存），也可以通过用十六进制编辑器打开河流位图并更改两个值来修复：地址 `00 00 00 2F` 和 `00 00 00 33` 都应为 `00`，而不是 GIMP 设置的 `01`。

## 树木

_另见：[Entity modding（实体修改）](</Entity_modding> "Entity modding")_

`/Hearts of Iron IV/map/trees.bmp` 是一个 8 位索引位图，决定树木模型在地图上的位置。地图的分辨率决定树木的密度，默认每条边是省份位图的 75/256。

该地图决定 pdxmesh 对象的位置。例如，ID 为 14 的精灵图会导致名为 `mapobject_14` 的 pdxmesh 在该位置生成：

    pdxmesh = {
        name = "mapobject_14"
        file = "gfx/models/mapitems/trees/my_tree.mesh"
        scale = 0.5
    }

此定义位于 `/Hearts of Iron IV/gfx/entities/*.gfx` 文件的 `objectTypes = { ... }` 块中。默认情况下，原版游戏存在这些树木类型：

树木列表：  
色图 ID | 颜色 | 树木类型/密度   
---|---|---  
2  | (30, 139, 109)  | 稀疏棕榈树（Sparse Palm）   
3  | (18, 100, 78)  | 密集棕榈树（Dense Palm）   
5  | (76, 156, 51)  | 稀疏森林（Sparse Forest）   
6  | (47, 120, 24)  | 密集森林（Dense Forest）   
11  | (255, 255, 0)  | 无法通行的丛林（Impassable Jungle）   
28  | (150, 0, 255)  | 稀疏丛林（Sparse Jungle）   
29  | (88, 0, 138)  | 密集丛林（Dense Jungle）   

## 彩色贴图

彩色贴图（colourmap）文件定义应用于地图的整体色调。没有彩色贴图文件，所有陆地都会呈现相同的整体颜色，无论地形类型如何。它们应保存为 .DDS 格式，使用 8.8.8.8 ARGB 32 位配置，不带 mipmap。

### 水体

`/Hearts of Iron IV/map/terrain/colormap_water_0.dds` 用于为_水体_着色。它的每个维度都是省份位图的一半。水体彩色贴图 1 和 2 同理：它们相同，但每个维度都是上一级的一半。这是出于性能原因，使游戏在拉远镜头或使用不同图形设置时使用较低质量的纹理。

### RGB 与城市灯光

`/Hearts of Iron IV/map/terrain/colormap_rgb_cityemissivemask_a.dds` 有两个用途。RGB 通道定义地图的默认着色，会被地形修改。在更改地形或高度图时，此彩色贴图也应更新，以在视觉上反映变化。阿尔法通道用于夜间城市灯光：透明度越高，夜间灯光越强。该文件应使用省份位图垂直和水平分辨率的一半。  
如果阿尔法通道与 RGB 通道分开，编辑这个彩色贴图会容易得多，因为它们的用途不同。分离过程取决于图像编辑器。

  * 在 GIMP 中，通过添加一个选中"Transfer layer's alpha channel"（转移图层的阿尔法通道）设置的图层蒙版来完成，这允许通过编辑蒙版来编辑阿尔法通道，通过编辑现在不透明的图层来编辑 RGB 通道。
  * 在 Photoshop 中，通过使用通道（Channels）选项卡完成。正在编辑的通道是同时被选中并设置为可见的那个。要分别可视化每个通道上的编辑效果，一个通道必须同时被选中并设置为可见，另一个必须设置为不可见。使用 NVIDIA 插件打开 dds 文件时，必须勾选"Load Alpha as Channel Instead of Transparency"（将阿尔法作为通道而非透明度加载）框。
  * **Paint.net 无法用于编辑此彩色贴图**，因为它不允许分别使用 RGB 和阿尔法通道。这些通道无法单独可视化，编辑会同时应用于所有通道，而不是允许分别编辑每个通道。

## 建筑

_另见：[Nudger § 建筑](</Nudger#Buildings> "Nudger")_

自[补丁 1.15](</Patch_1.15> "Patch 1.15") 以来，map 文件夹中建筑的主要文件只有一个：`/Hearts of Iron IV/map/buildings.txt`，它主要决定每个州中建筑模型的位置。该文件中的条目定义如下（如未指定，假设数字最多有 2 位小数）：

    State ID (integer); building ID (string); X position; Y position; Z position; Rotation; Adjacent sea province (integer)

  * State ID 定义建筑所在的州。即使是省级建筑，这也是州的 ID，而不是省份的 ID。相反，省级建筑在每个州有多个条目，游戏使用 XYZ 位置来判断它属于哪个省份。
  * Building ID 定义放置的是哪个模型。这不仅包括每种建筑，还包括以 floating_harbor 表示的浮港。
  * X、Y 和 Z 位置使用坐标系表示建筑模型在地图上的位置。
  * Rotation 以弧度为单位。旋转 0 会使建筑模型指向与模型本身相同的方向，正值会使其逆时针旋转，负值会使其顺时针旋转。与 0 相同的完整旋转等于数字 π 乘以 2，约为 6.28。
  * 邻接海洋省份（Adjacent sea province）只对海军基地和浮港有必要定义，以便让游戏知道舰船或运输船队可以从哪个海洋省份进入其所在的省份。如果建筑类型不是海军基地，应保持为 0。

最好在 nudger 的建筑部分生成建筑模型，而不是手动填写。但请注意，如果现有的 `/Hearts of Iron IV/map/buildings.txt` 文件完全为空，游戏会崩溃。只要文件不完全为空，文件的内容无关紧要。

**如果此文件中缺少某个海军基地或浮港的定义，游戏会崩溃**，一旦某个拥有它的省份被 AI 评估或尝试用作海军基地。这表现为在游戏进行数小时后崩溃，最后读取的文件是 [client_ping 或 hourly_ping](</Troubleshooting#Crash_data_log> "Troubleshooting")，关闭 AI 可以修复。这是因为建筑定义不仅用于海军基地的模型，还用于分配港口通向的海洋省份。如果没有定义，游戏无法评估海军会被放置的位置，导致无限循环消耗 RAM 和 CPU，最终崩溃。

## 单位模型与胜利点位置

_另见：[Nudger § 单位](</Nudger#Units> "Nudger")_

`/Hearts of Iron IV/map/unitstacks.txt` 文件决定每个省份内单位模型和胜利点的位置。这通过 nudger 中的`单位`（Unit）菜单编辑。  
特别是，它决定省份中如果存在胜利点时，胜利点在地图上的位置。是否存在胜利点、如何称呼以及价值多少，由[州历史文件和本地化](</State_modding#History> "State modding")决定。

文件中的条目具有以下格式：

    Province ID; Type; X position; Y position; Z position; Rotation; Offset

  * Province ID 指定模型面向哪个省份。
  * Rotation 以弧度为单位，0 是默认状态，正值使其逆时针旋转。
  * offset 用于动画，使省份内单位的动画不会同时发生，而是有延迟。
  * Type 是 0 到 38 的整数，指定用途。

unitstacks 类型列表：  
展开 ID  | 内部名称  | 说明   
---|---|---  
0  | Standstill  | 静止   
1  | Moving 0  | 移动 0；不同的数字代表不同的所需旋转级别   
2  | Moving 1  | 移动 1；不同的数字代表不同的所需旋转级别   
3  | Moving 2  | 移动 2；不同的数字代表不同的所需旋转级别   
4  | Moving 3  | 移动 3；不同的数字代表不同的所需旋转级别   
5  | Moving 4  | 移动 4；不同的数字代表不同的所需旋转级别   
6  | Moving 5  | 移动 5；不同的数字代表不同的所需旋转级别   
7  | Moving 6  | 移动 6；不同的数字代表不同的所需旋转级别   
8  | Moving 7  | 移动 7；不同的数字代表不同的所需旋转级别   
9  | Attacking  | 攻击   
10  | Defending  | 防御   
11  | Disembarck 0  | 下船 0；不同的数字代表不同的所需旋转级别   
12  | Disembarck 1  | 下船 1；不同的数字代表不同的所需旋转级别   
13  | Disembarck 2  | 下船 2；不同的数字代表不同的所需旋转级别   
14  | Disembarck 3  | 下船 3；不同的数字代表不同的所需旋转级别   
15  | Disembarck 4  | 下船 4；不同的数字代表不同的所需旋转级别   
16  | Disembarck 5  | 下船 5；不同的数字代表不同的所需旋转级别   
17  | Disembarck 6  | 下船 6；不同的数字代表不同的所需旋转级别   
18  | Disembarck 7  | 下船 7；不同的数字代表不同的所需旋转级别   
19  | Ship in port  | 港内舰船   
20  | Ship in port moving  | 港内移动的舰船   
21  | Standstill RG  | 静止 RG；RG 代表 regrouping（重新集结）   
22  | Moving 0 RG  | 移动 0 RG；不同的数字代表不同的所需旋转级别。RG 代表 regrouping（重新集结）   
23  | Moving 1 RG  | 移动 1 RG；不同的数字代表不同的所需旋转级别。RG 代表 regrouping（重新集结）   
24  | Moving 2 RG  | 移动 2 RG；不同的数字代表不同的所需旋转级别。RG 代表 regrouping（重新集结）   
25  | Moving 3 RG  | 移动 3 RG；不同的数字代表不同的所需旋转级别。RG 代表 regrouping（重新集结）   
26  | Moving 4 RG  | 移动 4 RG；不同的数字代表不同的所需旋转级别。RG 代表 regrouping（重新集结）   
27  | Moving 5 RG  | 移动 5 RG；不同的数字代表不同的所需旋转级别。RG 代表 regrouping（重新集结）   
28  | Moving 6 RG  | 移动 6 RG；不同的数字代表不同的所需旋转级别。RG 代表 regrouping（重新集结）   
29  | Moving 7 RG  | 移动 7 RG；不同的数字代表不同的所需旋转级别。RG 代表 regrouping（重新集结）   
30  | Disembarck 0 RG  | 下船 0 RG；不同的数字代表不同的所需旋转级别。RG 代表 regrouping（重新集结）   
31  | Disembarck 1 RG  | 下船 1 RG；不同的数字代表不同的所需旋转级别。RG 代表 regrouping（重新集结）   
32  | Disembarck 2 RG  | 下船 2 RG；不同的数字代表不同的所需旋转级别。RG 代表 regrouping（重新集结）   
33  | Disembarck 3 RG  | 下船 3 RG；不同的数字代表不同的所需旋转级别。RG 代表 regrouping（重新集结）   
34  | Disembarck 4 RG  | 下船 4 RG；不同的数字代表不同的所需旋转级别。RG 代表 regrouping（重新集结）   
35  | Disembarck 5 RG  | 下船 5 RG；不同的数字代表不同的所需旋转级别。RG 代表 regrouping（重新集结）   
36  | Disembarck 6 RG  | 下船 6 RG；不同的数字代表不同的所需旋转级别。RG 代表 regrouping（重新集结）   
37  | Disembarck 7 RG  | 下船 7 RG；不同的数字代表不同的所需旋转级别。RG 代表 regrouping（重新集结）   
38  | Victory point  | 胜利点   

## 邻接

邻接文件是 `/Hearts of Iron IV/map/adjacencies.csv`。它决定省份边界之间的关系，允许在不直接相邻的省份之间创建边界（如海峡渡口），阻止两个直接相邻省份之间的边界（使其不可通行），或以其他方式设置使穿越边界受限的邻接规则（如直布罗陀海峡）。

邻接使用以下格式：

    Start province ID;End province ID;Adjacency type;Through province ID;Starting X position;Starting Y position;Ending X position;Ending Y position;Adjacency rule;Comment

例如，以下都是有效的邻接：

    6891;3838;sea;5579;-1;-1;-1;-1;;Sardinia-Corsica
    10910;12807;impassable;-1;-1;-1;-1;-1;;Himalayas
    # Comment
    3314;6336;sea;2752;2885;1578;2890;1581;;Afsluitdijk

邻接有 2 种主要类型：sea（海洋）和 impassable（不可通行）。'impassable' 完全阻止两个省份之间的连接，而 'sea' 在省份之间创建有条件的边界（使用或不使用邻接规则），不需要这些省份有直接边界。如果未指定类型，则默认为 sea。海洋连接必须在两个相同类型的省份之间：sea 或 land。如果连接在两个陆地省份之间，它们不能直接接壤。  
由于 impassable 类型不能"穿过"（through）一个省份，没有用于图形的起点或终点位置，也不能设置邻接规则，这些字段应保持未设置。做法是将邻接规则字段完全留空，其余字段保持为 -1。  
'Through'（经过）标记一个作为 sea 类型邻接通路（gateway）的省份。如果两个省份不直接接壤，则_必须_定义一个经过省份。敌人对该省份的控制（例如在两个陆地省份之间的海洋省份中的敌方舰船）将阻止该邻接被使用。此外，在这种情况下可以定义一个邻接规则应用于这些省份。

X 和 Y 位置决定两个陆地省份之间海峡渡口所创建的红线的起点和终点。它们使用 3D 坐标系中的 X 和 Z 坐标。如果设置为 -1，则位置将自动计算，即起始和结束省份的中点。

文件的最后一行必须是 `-1;-1;-1;-1;-1;-1;-1;-1;-1`。这是文件应停止读取的指示符，文件中任何更靠后的邻接条目都会被忽略。即使文件在其他方面是空的，这一行也应该存在，以避免加载时挂起。

### 邻接规则

邻接规则位于 `/Hearts of Iron IV/map/adjacency_rules.txt`，是建立更复杂规则以确定谁能访问指定邻接（无论是海峡还是运河）的方法。要建立邻接规则，必须先在 `/Hearts of Iron IV/map/adjacency.csv` 中指定其名称。**此文件必须以 UTF-8 编码**，且不带字节顺序标记（BOM）。

接下来是确定每种关系类型有什么访问权限。友方（friend）是任何作为臣属国、被授予军事通行权或与指定邻接的控制国处于同一派系的国家。竞争（contested）是指两个国家通过控制 required_provinces 中的不同省份来竞争该邻接。敌方（enemy）是与控制国交战的任何国家，中立（neutral）是没有任何特定关系的国家。每种关系都应指定军队是否可以通行（transports）、海军是否可以通行、潜艇是否可以通行，以及是否可以使用它来输送贸易。

之后是必需省份（required_provinces）：这些指定一个国家必须控制哪些省份才能成为该邻接的所有者。所有省份不需要位于同一个州或来自同一个国家。此字段中必须至少指定两个省份。

`is_disabled` 是一个为试图使用该邻接规则的国家评估的触发条件块，若为真则完全阻止其使用。`tooltip = localisation_key` 用于分配本地化键。

`icon` 指定邻接的图标在海军视图中显示在哪个省份之上。这应该是一个海洋省份。

`offset` 指定图标从指定为图标的省份中心开始移动的图形位置（按 { X 值 Z 值 Y 值 } 的顺序）。

（图片）

展开 邻接规则示例   
---  
    adjacency_rule =
    {
    	name = "BOSPHORUS_STRAIT"
        
    	contested =
    	{
    		army = no
    		navy = no
    		submarine = no
    		trade = no
    	}
    	enemy =
    	{
    		army = no
    		navy = no
    		submarine = no
    		trade = no
    	}
    	friend =
    	{
    		army = yes
    		navy = yes
    		submarine = yes
    		trade = yes
    	}
    	neutral =
    	{
    		army = no
    		navy = no
    		submarine = no
    		trade = yes
    	}
    	
    	required_provinces = { 9833 11829 }
    
    	is_disabled = {
    		has_country_flag = BOSPHORUS_STRAIT_BLOCKED_FOR_COUNTRY
    		tooltip = bosporus_strait_blocked_tt
    	}
    	
    	icon = 9833
    	offset = { -2 0 -9 }
    }

## 补给

_另见：[Nudger § 补给](</Nudger#Supply> "Nudger")_

补给节点和铁路的初始位置分别在 `/Hearts of Iron IV/map/supply_nodes.txt` 和 `/Hearts of Iron IV/map/railways.txt` 中定义。**无效的定义会导致崩溃**，例如在尝试打开单人游戏或尝试在 nudger 中打开'补给'（Supply）部分时。这种情况下的无效定义是指跨越不存在或无州省份的定义，或非常不连续的铁路定义。  
建议使用 nudger 的补给部分来分配补给节点和铁路。

补给节点文件中的条目格式如下，不带分号：

    Level; Province

level 表示补给节点的等级。默认情况下，补给节点的最高等级为 1[11]，因此这里限制为 1。  
province 表示补给节点所在省份的 ID。示例条目是 `1 1234`

铁路文件中的条目格式如下，不带分号：

    Level; Amount of provinces; List of provinces

level 表示铁路的等级。默认情况下，最高不超过 5。[12]  
省份数量表示铁路经过多少个省份。  
省份列表是以空白字符分隔的省份 ID 列表，铁路经过这些省份。  
一个有效的铁路定义是：`4 4 693 1444 12 11`

### 补给区域（适用于 1.11 之前的版本）

**注意：随着 1.11 和 No Step Back 的发布，补给区域（supply area）已被弃用，取而代之的是通过 supply_nodes.txt 和 railways.txt 定义的初始后勤/补给系统；请参阅上一节。有关如何将地图从 1.10 更新到 1.11 的更多信息，请参阅[这篇文章](<https://www.reddit.com/r/hoi4modding/comments/r2876d/updating_custom_map_mods_to_work_with_nsb/>)。**

所有州都必须与一个补给区域相关联。每个补给区域可以容纳任意数量的州，每个州应该只属于一个补给区域。

补给区域的格式如下：

    supply_area={
    	id=1
    	name="SUPPLYAREA_1"
    	value=12
    	states={
    		5 85 
    	}
    }

## 环境物体

_另见：[Entity modding（实体修改）](</Entity_modding> "Entity modding")_

`/Hearts of Iron IV/map/ambient_object.txt` 文件用于定义地图上始终生成的外观 3D 物体，例如地图边框或风。  
每个环境物体是文件中一个单独的 `type = { ... }` 定义。以下参数可以放入环境物体定义中：

  * `type = object_type` 引用在 `/Hearts of Iron IV/gfx/entities/*.asset` 文件中定义的实体。
  * `use_animation = no` 设置环境物体是否有应使用的动画。
  * `time_duration = 300` 设置动画持续的时间。
  * `scale = 100.0` 设置环境物体的大小。默认大小为 1。
  * `always_visible = yes`，如果设置，强制环境物体永久可见，例如边框。
  * `object = { ... }` 决定实体的每个实例。同一个环境物体中可以存在多个。具体来说，会用到这些参数：
    * `name = my_entity_name` 是物体的名称。它不必是唯一的，并且只在 nudger 中显示。
    * `position = { ... }` 是物体的 XYZ 位置。
    * `rotation = { ... }` 是物体按 XYZ 轴的旋转。

这是一个环境物体定义的示例：

    type = {
        type = my_entity
        use_animation = no
        scale = 1000
        always_visible = yes
        object = {
            name = my_entity_name
    		position={
    			420.090 10.000 382.670 
    		}
    		rotation={
    			0.000 0.000 0.000 
    		}
        }
        object = {
            name = my_entity_name
    		position={
    			1234.890 10.000 407.440 
    		}
    		rotation={
    			0.000 0.000 0.000 
    		}
        }
    }

nudger 可以编辑现有的环境物体，但无法创建新的：必须先手动创建它们。

## 参考资料

  1. ↑ 例如，打开原版游戏的 `heightmap.bmp` 就能看到这一点：Photoshop 会将其读取为索引颜色模式图像，带有默认的灰度颜色表，任何用 GIMP 灰度模式保存的图像也会发生同样的情况。使用 sGray 颜色配置文件以灰度模式保存图像也会产生相同的结果：保存的文件实际上将是索引模式，带有灰度颜色表。然而，在灰度模式下使用任何其他颜色配置文件都会导致文件损坏。
  2. ↑ Paint.net 会自动生成色图：[https://forums.getpaint.net/topic/10989-bitmap-colormap-editing/?do=findComment&comment=181320](<https://forums.getpaint.net/topic/10989-bitmap-colormap-editing/?do=findComment&comment=181320>)
  3. ↑ 跳转至： 3.0 3.1 `/Hearts of Iron IV/gfx/FX/constants.fxh` 中的 `static const float WATER_HEIGHT = 9.5f;`
  4. ↑ [HoI 4 - map/definition.csv in user dir is used without validation](<https://forum.paradoxplaza.com/forum/index.php?threads/1153223> "forum:1153223")
  5. ↑ `/Hearts of Iron IV/gfx/FX/constants.fxh` 中的 `static const float MAP_NUM_TILES = 4.0f;`
  6. ↑ `/Hearts of Iron IV/gfx/FX/constants.fxh` 中的 `static const float TEXELS_PER_TILE = 512.0f;`
  7. ↑ [Defines](</Defines> "Defines") 中的 NDefines.NSupply.RIVER_RAILWAY_LEVEL = 1
  8. ↑ [Defines](</Defines> "Defines") 中的 NDefines.NMilitary.RIVER_SMALL_START_INDEX = 0
  9. ↑ [Defines](</Defines> "Defines") 中的 NDefines.NMilitary.RIVER_SMALL_STOP_INDEX = 6
  10. ↑ [Defines](</Defines> "Defines") 中的 NDefines.NMilitary.RIVER_LARGE_STOP_INDEX = 11
  11. ↑ 如 `/Hearts of Iron IV/common/buildings/00_buildings.txt` 中所定义
  12. ↑ [Defines](</Defines> "Defines") 中的 NDefines.NSupply.MAX_RAILWAY_LEVEL = 5

编辑 [Defines](</Defines> "Defines") 时，请务必使用覆盖文件（override file）而不是复制整个文件，因为复制整个文件可能导致游戏崩溃——当添加新的 defines 时就会发生这种情况，即使在"次要"更新中也可能发生。

**[Modding（修改）](</Modding> "Modding")**

文档  | [Effects](</Effect> "Effect") • [Triggers](</Conditions> "Conditions") • [Defines](</Defines> "Defines") • [Modifiers](</Modifiers> "Modifiers") • [List of modifiers](</List_of_modifiers> "List of modifiers") • [Scopes](</Scopes> "Scopes") • [Localisation](</Localisation> "Localisation") • [On actions](</On_actions> "On actions") • [Data structures](</Data_structures> "Data structures") ([Flags](</Data_structures#Flags> "Data structures"), [Event targets](</Data_structures#Event_targets> "Data structures"), [Country tag aliases](</Data_structures#Country_tag_aliases> "Data structures"), [Variables](</Data_structures#Variables> "Data structures"), [Arrays](</Data_structures#Arrays> "Data structures"))   
---|---  
脚本  | [Achievements](</Achievement_modding> "Achievement modding") • [AI](</AI_modding> "AI modding") • [AI focuses](</AI_focuses> "AI focuses") • [Autonomous states](</Autonomy_state_modding> "Autonomy state modding") • [Balances of power](</Balance_of_power_modding> "Balance of power modding") • [Bookmarks/Scenarios](</Bookmark_modding> "Bookmark modding") ([Game rules](</Bookmark_modding#Game_rules> "Bookmark modding")) • [Buildings](</Building_modding> "Building modding") • [Characters and traits](</Character_modding> "Character modding") • [Cosmetic tags](</Cosmetic_tag_modding> "Cosmetic tag modding") • [Countries](</Country_creation> "Country creation") • [Divisions](</Division_modding> "Division modding") • [Decisions](</Decision_modding> "Decision modding") • [Doctrines](</Doctrine_modding> "Doctrine modding") • [Equipment](</Equipment_modding> "Equipment modding") • [Events](</Event_modding> "Event modding") • [Factions](</Faction_modding> "Faction modding") • [Ideas](</Idea_modding> "Idea modding") • [Ideologies](</Ideology_modding> "Ideology modding") • [Military industrial organizations](</Military_industrial_organization_modding> "Military industrial organization modding") • [National focuses](</National_focus_modding> "National focus modding") • [Resources](</Resources_modding> "Resources modding") • [Scripted GUI](</Scripted_GUI_modding> "Scripted GUI modding") • [Technologies and doctrines](</Technology_modding> "Technology modding") • [Units](</Unit_modding> "Unit modding")  
---|---  
地图  | Map • [States](</State_modding> "State modding") • [Supply areas](</Supply_areas_modding> "Supply areas modding") • [Strategic regions](</Strategic_region_modding> "Strategic region modding")  
---|---  
图形  | [Interface](</Interface_modding> "Interface modding") • [Graphical assets](</Graphical_asset_modding> "Graphical asset modding") • [Entities](</Entity_modding> "Entity modding") • [Posteffects](</Posteffect_modding> "Posteffect modding") • [Particles](</Particle_modding> "Particle modding") • [Fonts](</Font_modding> "Font modding")  
---|---  
外观  | [Portraits](</Portrait_modding> "Portrait modding") • [Namelists](</Namelist_modding> "Namelist modding") • [Music](</Music_modding> "Music modding") • [Sound](</Sound_modding> "Sound modding")  
---|---  
其他  | [Console commands](</Console_commands> "Console commands") • [Troubleshooting](</Troubleshooting> "Troubleshooting") • [Mod structure](</Mod_structure> "Mod structure") • [Mods](</Mods> "Mods") • [Nudger](</Nudger> "Nudger")  
---|---
