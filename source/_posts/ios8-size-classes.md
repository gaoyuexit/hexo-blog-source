title: iOS8 Size Classes初探
date: 2014-09-09 17:18:41
tags: iOS8
---

iOS8新特性，`Size Classes`，是对老式UI思路的全新抽象：把各个设备屏幕（iphone4,5,6, ipad,iwatch?）以及它们的屏幕旋转状态都抽象成屏幕Size的变化，将这些Size归纳成几个类别（Class）

<img src="https://mmbiz.qlogo.cn/mmbiz/5EEx1Jwk8kKN4lAicL1VN2y32W4ejmmaz9sIa0ZszJu53F4dekibfkPLhq7lmF6UOLcC2pAHnGF5PyZ1MaRJHAlA/0" width="320">

宽（正常，任意， 紧凑），高（正常，任意， 紧凑）

3x3共9种Size，每种Size都可以设置特定的一套布局，如果不特殊指定，默认是在（宽任意，高任意）模式下设置，且其他8种布局继承它。

听过有人说，我们不用学autolayout了，直接学Size Class就一步到位了。这个说法是不对的，因为Size Class在将屏幕分类后，执行布局的还是Autolayout。

--- 

# Size Classes与Interface Builder

当然不出所料的是，Xcode6中Interface Builder对Size Class有了很强大的支持：

<img src="https://mmbiz.qlogo.cn/mmbiz/5EEx1Jwk8kKN4lAicL1VN2y32W4ejmmazWvtHou58grUl8IyOq2sYgb9iacUTL0oeYE7eWulrWF35G4D3GWQich3w/0" width="320">

启用Size Class后，IB中就会出现Size Class切换的菜单

<img src="https://mmbiz.qlogo.cn/mmbiz/5EEx1Jwk8kKN4lAicL1VN2y32W4ejmmazQ3DeTLO8z0QyicMicRkr1qSJbrpX5aO5jsKydyolqibWg7tAGDynpI5Jw/0" width="320">
我们可以切换到`wAny,hAny`模式去编辑通用的控件和布局，也可以切换到某个特定Class，立刻可以预览到变化，于是有个问题：

假如iPad和iPhone的布局有差异，老式写法是分成ipad.storyboard和iphone.storyboard来分别写，这本身就是个bug，因为大部分控件其实并没差别，新Size Class解决这个问题了木？

答案是肯定的，Size Class的方案比老式的好了几条街：
IB中某个View的出现与否，**约束的出现与否以及约束的值都是可以根据Size Class单独设置的**，也就是说现在一个storyboard是`9合1`的。
比如下面有个Label，我只希望它出现在长宽紧缩的屏幕上时（脑补iwatch），这么勾选下就可以（出现或不出现被命名为"Installed"，这个选项可以从9个Size Class中多选）
<img src="https://mmbiz.qlogo.cn/mmbiz/5EEx1Jwk8kKN4lAicL1VN2y32W4ejmmaz8fvJEaFDD7V313hKoYeyPHfnkvJA3akacdkg2r7hQLknceicpaL8kiaQ/0" width="480">

--- 

# Size Classes的xml文件改动

说到9合1的时候肯定会有疑问，这样的storyboard文件会不会很大？源文件会不会很乱导致多人开发经常冲突？

答案是不会的，源于apple对Size Class在xml中的描述方法是针对变化配置的，什么意思呢？对比下storyboard的xml源文件就知道了：
wAny,hAny模式下刚才只有一个Label的页面：
<img src="https://mmbiz.qlogo.cn/mmbiz/5EEx1Jwk8kKN4lAicL1VN2y32W4ejmmazSsj5BiaLrzLsOcowFj3zB49NTRS86NTVG5dqe0ACU8DnXov4iaWKllZg/0" width="480">

假如在wC,hC紧缩模式下设置不出现这个label时，在label的父view层级出现了下面的配置：
<img src="https://mmbiz.qlogo.cn/mmbiz/5EEx1Jwk8kKN4lAicL1VN2y32W4ejmmazaYtgIVwxibLf16xJXhicdWjfuBONNebKfgU2ICQsOEwAMjkibz6BJCTAQ/0" width="480">
所以说ib中以附加的描述字段来表示哪些元素是被哪些Size Class包含或排除的，也正因为这样的描述方式，使得新的xml格式**可以被低版本兼容**（低版本不解析这个字段，但其他字段正常解析）

--- 

# Size Classes与xcassets

既然storyboard变成了9合1，配套的`xcassets`必须也有所表示才行，xcode6后向`xcassets`中添加图片时增加了选择对应Size Classes的菜单，展开后会像下面一样：

<img src="http://ww1.sinaimg.cn/mw690/51530583gw1ek7k01ldj9j20j40k4aat.jpg" width="480">

通过符号表示确实不错 `-`对应紧缩，`*`对应Any，`+`对应宽松  
（@3x是iphone6 plus）

--- 


# 总结

总的来说，iOS对UI这块的改动是跨时代性的，Autolayout的出现使得布局的复杂度减少到了View与View的关系上，再由根View（也就是屏幕）指定frame，随后所有子View相对布局，把frame的概念归一化到根View的frame上；但有了Size Class后，根视图的frame概念也被移除了，这下整个app的UI和frame这个单词已然脱离关系，这也正是apple想要达到的目的。

Farewell，frame和那些还奋战在手写UI的iOS coder们...