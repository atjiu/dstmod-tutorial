## AnimState:SetBank()与AnimState:SetBuild()区别

以下总结来自龙飞整理 原文：https://www.jianshu.com/p/2cc5538236ec

> Bank是一个集合，记录着一些Animation的名字。一个entity的bank决定了它能播放哪些动画。当它试图播放一个不存在于Bank中的动画时，就会在游戏中显示为透明。
>
> Build是材质包，也就是存放在MOD文件夹或游戏的data文件夹下的anim文件夹下的那些.zip文件。可以使用同一套Bank，不同的Build来表现不同的形态。比如说人物都是统一使用名为“wilson”的Bank，但每个人物都有自己的Build，通常与人物的prefab名相同。


