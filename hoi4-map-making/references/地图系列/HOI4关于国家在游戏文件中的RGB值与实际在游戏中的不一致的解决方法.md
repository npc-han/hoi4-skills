

HOI4 关于国家在游戏⽂件中的 RGB 值与实际
在游戏中的不⼀致的解决⽅法
 
前⾔  
决定国家领⼟颜⾊的⽂件是common/countries/colors.txt或者
common/countries/cosmetic.txt，具体参数是 color ，可采⽤ rgb 或者 hsv 来决定颜⾊
原因：  
游戏在从⽂件中读取到的 RGB 值的基础上，给此颜⾊的饱和度以及明度分别乘以和 ，使得实际显
⽰出来的颜⾊改变，从⽽令⼆者不⼀致
解决⽅法：  
(1). 修改 defines  
在 mod ⽂件的commom/defines创建⼀个*.lua⽂件（如果没有的话），然后输⼊
便可以将原先的 分别设置为 ，使得游戏⽂件中的 RGB 值和游戏中的完全相同
(2). 修改游戏⽂件中的 RGB 值  
设某国家期望在游戏中显⽰的颜⾊的 rgb 值组为 ，令在此基础上颜⾊⽂件中应当设定的 rgb 组
为 ，令  ，则我们有如下公式：
更⼀般的情况：  
若游戏是给⽂件中的颜⾊的饱和度以及明度分别乘以和 ，则公式应改为：
游戏⽂件中的 RGB 值转游戏中实际呈现的 RGB 值公式  
上⾯给出了游戏中实际呈现的 RGB 值转游戏⽂件中的 RGB 值的公式，只需将取倒数，令
，就可以得到它的逆推公式：
将原版的 带⼊可得：
NDefines_Graphics.NGraphics.COUNTRY_COLOR_SATURATION_MODIFIER = 1.0
NDefines_Graphics.NGraphics.COUNTRY_COLOR_BRIGHTNESS_MODIFIER = 1.0
1
2
