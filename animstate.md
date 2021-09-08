> 声明：本篇文章通过`五年一班 冰糖`的讲解整理而成
>
> 文章中用到的工具由`五年一班 风铃䓍`提供

## 内置方法

在游戏里通过控制台输入 `for k,v in pairs(AnimState)do print(k,v)end` 打印出来的

我知道什么意思的都给写个解释了，不知道的就没写

| 方法名                         | 类型                                     | 解释                                                   |
|--------------------------------|------------------------------------------|--------------------------------------------------------|
| OverrideShade                  | function                                 |                                                        |
| SetFinalOffset                 | function                                 |                                                        |
| SetSymbolExchange              | function                                 |                                                        |
| SetOrientation                 | function                                 |                                                        |
| GetSortOrder                   | function                                 |                                                        |
| GetInheritsSortKey             | function                                 |                                                        |
| SetSortOrder                   | function                                 |                                                        |
| SetClientSideBuildOverrideFlag | function                                 |                                                        |
| SetBloomEffectHandle           | function                                 |                                                        |
| GetSkinBuild                   | function                                 |                                                        |
| SetDepthBias                   | function                                 |                                                        |
| SetDepthTestEnabled            | function                                 |                                                        |
| CompareSymbolBuilds            | function                                 |                                                        |
| ClearOverrideBuild             | function                                 |                                                        |
| Show                           | function(layername)                      | 显示图层(Layer)                                        |
| OverrideSkinSymbol             | function                                 |                                                        |
| AssignItemSkins                | function                                 |                                                        |
| SetSortWorldOffset             | function                                 |                                                        |
| OverrideItemSkinSymbol         | function                                 |                                                        |
| SetMultColour                  | function                                 |                                                        |
| SetHaunted                     | function                                 |                                                        |
| GetCurrentAnimationTime        | function                                 |                                                        |
| ClearAllOverrideSymbols        | function()                               | 清除所有覆盖通道                                       |
| SetOceanBlendParams            | function                                 |                                                        |
| PushAnimation                  | function                                 |                                                        |
| GetMultColour                  | function                                 |                                                        |
| ClearDefaultEffectHandle       | function                                 |                                                        |
| SetPercent                     | function                                 |                                                        |
| SetAddColour                   | function                                 |                                                        |
| ClearBloomEffectHandle         | function                                 |                                                        |
| SetBank                        | function(bankname)                       | spriter里动画的父级节点的名字                          |
| UsePointFiltering              | function                                 |                                                        |
| GetCurrentFacing               | function                                 |                                                        |
| SetBankAndPlayAnimation        | function                                 |                                                        |
| GetAddColour                   | function                                 |                                                        |
| HideSymbol                     | function                                 |                                                        |
| SetLayer                       | function                                 |                                                        |
| SetRayTestOnBB                 | function                                 |                                                        |
| SetWorldSpaceAmbientLightPos   | function                                 |                                                        |
| IsCurrentAnimation             | function                                 |                                                        |
| SetManualBB                    | function                                 |                                                        |
| SetHighlightColour             | function                                 |                                                        |
| Hide                           | function(layername)                      | 隐藏图层(Layer)                                        |
| ClearOverrideSymbol            | function                                 | 清除覆盖的通道                                         |
| GetSymbolOverride              | function                                 | 获取覆盖通道名                                         |
| GetCurrentAnimationLength      | function                                 |                                                        |
| Pause                          | function                                 |                                                        |
| Resume                         | function                                 |                                                        |
| SetDeltaTimeMultiplier         | function                                 |                                                        |
| SetUILightParams               | function                                 |                                                        |
| SetErosionParams               | function                                 |                                                        |
| BuildHasSymbol                 | function                                 |                                                        |
| ShowSymbol                     | function                                 |                                                        |
| ClearSymbolExchanges           | function                                 |                                                        |
| UseColourCube                  | function                                 |                                                        |
| OverrideSymbol                 | function(oldsymbol, newbuild, newsymbol) | 覆盖旧通道                                             |
| SetClientsideBuildOverride     | function                                 |                                                        |
| OverrideMultColour             | function                                 |                                                        |
| SetDefaultEffectHandle         | function                                 |                                                        |
| FastForward                    | function                                 |                                                        |
| SetBuild                       | function(buildname)                      | buildname就是scml文件的名字                            |
| SetScale                       | function                                 |                                                        |
| SetFloatParams                 | function                                 |                                                        |
| SetMultiSymbolExchange         | function                                 |                                                        |
| GetBuild                       | function                                 |                                                        |
| GetSymbolPosition              | function                                 |                                                        |
| GetCurrentAnimationFrame       | function                                 |                                                        |
| AnimDone                       | function                                 |                                                        |
| IsSymbolOverridden             | function                                 |                                                        |
| SetDepthWriteEnabled           | function                                 |                                                        |
| AddOverrideBuild               | function                                 |                                                        |
| SetInheritsSortKey             | function                                 |                                                        |
| PlayAnimation                  | function(animname, loop)                 | 播放动画，animname：动画名，loop：是否循环播放，默认是false |
| SetTime                        | function                                 |                                                        |
| SetLightOverride               | function                                 |                                                        |
| SetSkin                        | function                                 |                                                        |

## 层，通道

动画`层`是在`anim.bin`里的，使用反编译工具将`anim.bin`转成`xml`文件，即可看到`layer`的名字

点击下载 [反编译工具](attachment/反编译anim.binv0.2.zip)

解压，配置环境变量

打开`DecodeAnimtion.bat` `EncodeAnimtion.bat` 这两个文件，将里面的`D:\SteamLibrary\steamapps\common\Don't Starve Mod Tools`这个路径换成自己电脑上的安装路径即可

随便找一个动画zip包，把里面的 `anim.bin` 给解压到当前目录下，然后双击 `DecodeAnimtion.bat` 即可反编译成 xml 文件

![](images/20210908153903.png)

打开 `anim.bin.xml` 文件，就可以看到层的名字了

![](images/20210908154028.png)

-----

通道(symbol)就是spriter里右边项目文件夹，如下图

![](images/20210908154209.png)

## 实操

> 做过人物mod的都知道，人物在idle状态的时候，是有三只手的，当在游戏中拿武器时，第三只手显示出来同时再隐藏掉一只手，这样看起来会合理的多。这里显示和隐藏图层就是用到了 `层` `通道` 相关的方法

如上反编译出来的xml里的layername，其中`pigking_arm`就是猪王的胳膊，如果想在游戏里隐藏，代码如下

```lua
AddPrefabPostInit("pigking", function(inst)
    inst.AnimState:Hide("pigking_arm")
end)
```

重新显示出来也非常简单

```lua
AddPrefabPostInit("pigking", function(inst)
    inst.AnimState:Show("pigking_arm")
end)
```

----

如果没有找到对应的layername怎么办呢？还可以对通道进行更新，比如给猪王换一个皮，我这以给猪王屁股上贴个膏药为例

首先找到猪王的屁股贴图在哪个文件夹里(pigking_torso)，然后将里面的图片替换掉即可

![](images/20210908155526.png)

然后在电脑上的某个位置创建一个文件夹，随便叫啥名都行，把换好贴图的文件夹给拷贝到新文件夹里去，也可以对原文件夹改个名

![](images/20210908155808.png)

打开spriter，在刚创建的文件夹(pigking)里创建一个项目，保存名为 `wound_pig_king.scml`，如下图

![](images/20210908160232.png)

最后把sp文件与文件夹都拷贝到exported里启动游戏打包即可

![](images/20210908160401.png)

打包好后，在mod文件夹里的anim里会找到一个名为 `wound_pig_king.zip`的文件，打开它，把里面的 `anim.bin` 给删掉即可

最后就是代码里使用了

modmain.lua
```lua
Assets = {Asset("ANIM", "anim/wound_pig_king.zip")}

AddPrefabPostInit("pigking", function(inst)
    -- 先让猪王进入睡眠状态，这样它的屁股才能显示出来
    inst.sg:GoToState("sleep")
    -- 调用覆盖通道的方法，参数有三个，第一个是旧通道名（就是文件夹名），第二个是新的build名（就是新创建的scml文件名），第三个是新的通道名（就是上面修改的文件夹名）
    inst.AnimState:OverrideSymbol("pigking_torso", "wound_pig_king", "wound_pigking_torso")

    -- 清除覆盖的通道
    -- inst.AnimState:ClearOverrideSymbol("pigking_torso")
end)
```

效果图

![](images/wound_pig_king.gif)

----

骚操作

当使用 `inst.AnimState:OverrideSymbol()` 方法试图去覆盖旧通道时，当传的第二个或者第三个参数不存在时，贴图则会消失，也是一种变相的隐藏贴图的方式
