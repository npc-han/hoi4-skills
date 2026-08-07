# 钢铁雄心4地图细化与Nudge教程 P5-地块数据

> 来源：B站专栏（URL: https://www.bilibili.com/read/readlist/rl545833）| 抓取 2026-08-08 | 图片已省略，仅文字

终于来到province相关的细化部分了，接下来的几期内容都将围绕province和map的修改进行

这是province细化的基础部分Database

Province细化（零） -Database

P1粗略地讲了一下province的划分，将世界地图拆分为一个一个的小格，用不同的RGB颜色将地格区分开，并标注序号，province的初步划分就完成了，可以在钢丝本体文件、map下找到provinces.bmp

完成划分province之后，还需要为每个province规定不同的数据，例如province id，是什么类型，是否沿海，每个province的地形类型等，这些就在Database中规定

本期主要基于原版地图的基础上，介绍修改province的基础数据

如果你细心的话，你会发现nudge其实也可以修改Database，但是由于nudge修改的时候经常性发生闪退（所以nudge是真的不好用），所以先介绍常规的直接修改文件了

文件中修改

在原版文件map下找到definition.csv

复制到mod目录下，用文本编辑软件打开，definition.csv遵循以下书写格式

province ID，规定每个province独有的序列，province ID必须连续

rgb，每个province在provinces.bmp中对应的rgb颜色，与province ID共同定义游戏中一个province（有关每个province的定义会在后续map细化文章中详细说明）

province类型，原版将province分为三类，land（陆地，军队可直接通过），sea（海洋，海军可通过，无state归属），lake（湖泊，陆军海军均不可通过，可以参与State划分）

是否沿海，当有任意两个陆地和海洋province有相连部分时，这两个province应被定义为沿海，当某一province完全被相同类型的province包围时，则为非沿海，沿海与否影响到一些游戏机制的判定

地形类型，决定每个province的地形，不同的地形会在游戏中使用不同的纹理并且对战斗有不同的影响，原版规定的地形列在下面

图片摘抄自wiki

所属大洲，从0-7依次为，海洋（0）、欧洲（1）、北美洲（2）、南美洲（3）、大洋洲（4）、非洲（5）、亚洲（6）、中东（7）

注：每个province都必须有一个所属的大洲

你可以去map\continent.txt中修改原版大洲划分，如果你需要的话

在debug模式下找到你需要修改的province，遵循格式，依次修改就行了

使用nudge修改

下面以修改五大湖为例，演示用nudge修改Database

实测nudge如果用DirectX 11进入database会闪退，需要在启动器中切换成DirectX 9

首先进入nudge，右侧进入Database模式，先选择Type模式

点击Sea，然后左键点击一个province，就会将它的类型修改为Sea，如下

然后右侧切换为Coastal模式，点击Generate，nudge会自动将同Sea连接地块改为沿岸

注，此时可以右上方先切换nudge其他模式，如State模式，再切回Database，选择Coastal，会将沿岸province用斜线标识出来，方便修改

然后切换为Terrain模式，选择Ocean，左键点击改动的province，将地形类型改为海洋，点击Save保存，重启游戏加载修改的province设定（一定会报错，重新加载游戏是为了使nudge读取新地图数据，方便修改）

再次进入nudge

原湖泊province划分在State下，但是海域不属于任何State，接下里要更改它的归属

右上方将nudge切换为State模式，将改为海洋的province从原来的State中移除（见

P1​）点击Save保存

由于将Province分离了原State，会自动将它们设置为无战略区归属，右上方将nudge切换Strategc Reigns模式，为新province添加一个新战略区（具体见

P2​），点击Save保存

（后文会为五大湖添加联通外海的运河，这里需要对每片湖区都单独做一个海域战略区）

由于一些province由内陆变为沿岸，需要设定海军船坞、海军基地等建筑位置，右上方将nudge切换Buildings模式，点击Validate All State，然后点击Save保存

最后还需要为新建的海洋province创建舰队移动、登陆的动画，右上方将nudge切换Units模式，点击Auto in All，然后点击Save保存

可以看到这个时候就可以正常使用了

最后，由于五大湖（洋）和外海不是联通的，为了能在游戏中真实起作用，我们再为它添加一个运河（详情

P4​）

再次提醒，实现运河联通功能需要保证每个湖区都单独处于一个海域战略区中（战略区中没有陆地province）

最后，把生成的文件移动到你的mod下

还是那个位置，localisation可以用自己的

需要注意的是把红色框的文件删了，绿色框的文件重命名为definition.csv

definition.csv.cache是临时生成的缓存文件

definition.csv.fixed.csv是nudge修改后的definition.csv文件

在文档目录下，这两个文件必须同时存在原版文件才能正常读取

在自己的mod中，需要将definition.csv.fixed.csv重命名为definition.csv，加载mod时才能正确覆盖原版的文件而生效

以上就是本次全部的内容了

下一次会继续更新province的细化教程

（保证不鸽）
