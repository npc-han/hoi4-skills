# 钢铁雄心4地图细化与Nudge教程 P6-新建地块

> 来源：B站专栏（URL: https://www.bilibili.com/read/readlist/rl545833）| 抓取 2026-08-08 | 图片已省略，仅文字

进行province细化的第一步是对原版地图中，地块的划分进行拆解

这一期将会着重介绍在原版地格划分基础上，拆分出新的地格

Province细化（一） -新建Province

P5介绍了有关province的定义，这一期会具体介绍如何新建一个province

首先去原版文件下找到provinces.bmp，复制一份

用一款画图软件打开它（推荐PS、GIMP）

这里我用的是PS

找到你需要细化的位置，用放大镜工具放大（这里以拆分东西柏林为例）

柏林（6521）是这个地块

小建议：如果你使用的是VS Code，你可以下载一个钢丝mod制作的插件

在拓展中搜HOI4第一个就是

在设置中添加钢丝的安装路径

然后在最上方查看-&gt;命令面板-&gt;预览世界地图

可以帮助你更快地完成地图编辑

（这个模式下还可以切换成State地图，直接打开mod下的State进行文件修改）

回到正题，选择铅笔工具，将流量设置为1像素，在颜色中输入一个未被原版采用的rgb颜色（可以在definition中查找）

然后再map下涂出需要新建的地块（精确到每个像素）

Bug提醒

下面列出常见的几种报错情况（在error.log中出现），来自wiki

将修改后的地图直接放在文档目录下，用nudge直接启动游戏

虽然在map下划分了新的地块，但是并没有将它定义为province（详情见

P5​），此时一定会报错，但nudge会将游戏读取到的新建地块添加到definition.csv中，并且在文档下生成缓存文件definition.csv.fixed.csv和definition.csv.cache

（以下为我个人使用经验：如果直接复制原版的definition.csv到文档下，修改后再启动游戏，nudge依旧会生成definition.csv.fixed.csv并且把你修改好的数据改成未修改，同时打乱一些其他province的数据，因此我建议先启动游戏让nudge先读取并生成文件）

可以看到问题主要出现在Database而不是provinces.bmp

接下来修改province数据，可以直接在definition.csv.fixed.csv中修改也可以用nudge，这里我用nudge

进入nudge，为新province设置相应数据（详情见

P5​）

修改后点Save，definition.csv.fixed.csv会自动更新数据

设置成功后，这个province就可以正常使用了

然后为新的province新建一个State并添加到战略区（有关State细化见

P1​）

然后不要忘记改buildings和units

保存，然后重新进入游戏，可以看见西柏林就从原柏林中被分出来了

可以看到西柏林能在战后被同盟国“合法”占领了

最后把生成的文件放mod下，生成文件还是在那个熟悉的位置

将definition.csv.fixed.csv重命名为definition.csv

删除definition.csv.cache

就可以正常使用了

（如果你下次还需要继续修改province，建议将两个缓存文件definition.csv.fixed.csv和definition.csv.cache复制一份，下一次使用nudge的时候可以直接放入mod下，nudge就会在这基础上新加读取到的provinces.bmp文件）

细化province的补充以下内容摘抄自wiki

最大province数量：

HOI4 有显示省份的限制（介于 19K 和 22K 之间），根据目前所知，不再显示的最后一个province似乎是21651

可显示省份的数量实际上是一个固定的数字，并受到地图上同质区域数量的影响。到目前为止，创建的最高省份数量是21939

同质区域的介绍

地图上会见到一些岛屿，如马尔代夫

尽管这些岛屿中每一个在视觉上是不同的province，但是师单位可以一次性完全占领这整个province，因此在定义上，这些岛屿虽然图像上在不同地格，但是需要被定义到同一个province中

因此上文说不建议同一个province中有过大的间隔

有关细分province的内容就介绍这么多了

后续还会详细说明如何填海造陆（将海洋改为陆地）或者化陆为洋（陆地改成海洋）

不定期更新
