> 声明：本篇文章通过`五年一班 冰糖`的讲解整理而成
>
> 文章中用到的工具由`五年一班 风铃䓍`提供

## 内置方法

在游戏里通过控制台输入 `for k,v in pairs(AnimState)do print(k,v)end` 打印出来的

我知道什么意思的都给写个解释了，不知道的就没写

| 方法名                         | 类型                                                  | 解释                                                                                                                                      |
|--------------------------------|-------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------|
| AddOverrideBuild               | function(buildname)                                   | 添加新build                                                                                                                               |
| AnimDone                       | function                                              | 判断动画是否播放完，返回值为bool                                                                                                           |
| AnimateWhilePaused             | function(bool)                                        | 暂停时是否也播放动画                                                                                                                      |
| AssignItemSkins                | function(userid, body, hand, legs, feet)              | 重新分配物品的外观                                                                                                                        |
| BuildHasSymbol                 | function(symbolname)                                  | 是否有该 build 下的通道                                                                                                                   |
| ClearAllOverrideSymbols        | function                                              | 清除所有覆盖通道                                                                                                                          |
| ClearBloomEffectHandle         | function                                              |                                                                                                                                           |
| ClearDefaultEffectHandle       | function                                              |                                                                                                                                           |
| ClearOverrideBuild             | function                                              | 清除覆盖的build                                                                                                                           |
| ClearOverrideSymbol            | function(symbolname)                                  | 清除覆盖的通道                                                                                                                            |
| ClearSymbolBloom               | function                                              |                                                                                                                                           |
| ClearSymbolExchanges           | function                                              |                                                                                                                                           |
| CompareSymbolBuilds            | function(defaultsymbol, symbol)                       | 比较默认通道defaultsymbol是否是symbol通道，返回值是bool，一般用来自定义idle动画                                                             |
| FastForward                    | function                                              |                                                                                                                                           |
| GetAddColour                   | function                                              | 获取叠加的颜色，返回r,g,b,a                                                                                                                |
| GetBrightness                  | function                                              |                                                                                                                                           |
| GetBuild                       | function                                              | 获取build名                                                                                                                               |
| GetCurrentAnimationFrame       | function                                              | 获取当前动画帧数                                                                                                                          |
| GetCurrentAnimationLength      | function                                              | 动画总长度，单位：秒，1 帧是 1/30 秒，0.33333 秒                                                                                              |
| GetCurrentAnimationTime        | function                                              | 获取当前动画播放的时间，GetCurrentAnimationLength获取的是动画的总长度，GetCurrentAnimationTime获取的是动画当前播放的时间（我猜的）            |
| GetCurrentFacing               | function                                              | 获取当前动画的朝向，返回0，1，2，3，分别代表什么方向就需要进游戏测试了                                                                         |
| GetHue                         | function                                              |                                                                                                                                           |
| GetInheritsSortKey             | function                                              |                                                                                                                                           |
| GetMultColour                  | function                                              | 获取叠乘的颜色，返回r,g,b,a                                                                                                                |
| GetSaturation                  | function                                              |                                                                                                                                           |
| GetSkinBuild                   | function                                              | 获取当前皮肤的build                                                                                                                       |
| GetSortOrder                   | function                                              |                                                                                                                                           |
| GetSymbolAddColour             | function                                              |                                                                                                                                           |
| GetSymbolHSB                   | function                                              |                                                                                                                                           |
| GetSymbolMultColour            | function                                              |                                                                                                                                           |
| GetSymbolOverride              | function(symbolname)                                  | 获取覆盖通道名                                                                                                                            |
| GetSymbolPosition              | function(symbolname, offset_x, offset_y, offset_z)    | 获取通道的位置坐标，四个参数，第一个是通道名，后面三个是x,y,z坐标的偏移量                                                                    |
| Hide                           | function(layername)                                   | 隐藏图层(Layer)                                                                                                                           |
| HideSymbol                     | function(symbolname)                                  | 隐藏通道                                                                                                                                  |
| IsCurrentAnimation             | function(animname)                                    | 当前是否是当前动画名                                                                                                                      |
| IsSymbolOverridden             | function                                              |                                                                                                                                           |
| OverrideBrightness             | function                                              |                                                                                                                                           |
| OverrideHue                    | function                                              |                                                                                                                                           |
| OverrideItemSkinSymbol         | function(oldsymbol, skin_build, newsymbol, guid, abc) | 参数是5个，用法与OverrideSymbol类似，最后一个参数不知道什么意思                                                                             |
| OverrideMultColour             | function(r,g,b,a)                                     | 覆盖叠乘的颜色                                                                                                                            |
| OverrideSaturation             | function                                              |                                                                                                                                           |
| OverrideShade                  | function                                              |                                                                                                                                           |
| OverrideSkinSymbol             | function(oldsymbol, skin_build, newsymbol)            | 覆盖皮肤通道，与OverrideSymbol用法一致                                                                                                     |
| OverrideSymbol                 | function(oldsymbol, newbuild, newsymbol)              | 覆盖旧通道                                                                                                                                |
| Pause                          | function                                              | 暂停动画，无参                                                                                                                             |
| PlayAnimation                  | function(animname, loop)                              | 播放动画，animname：动画名，loop：是否循环播放，默认是 false                                                                                   |
| PushAnimation                  | function(animname, loop)                              | 推动画到播放列表里，与PlayAnimation不同的是，PlayAnimation执行后动画会立即执行，但PushAnimation执行后会等当前动画执行完，然后再播放Push的动画 |
| Resume                         | function                                              | 恢复暂停的动画，无参                                                                                                                       |
| SetAddColour                   | function(r,g,b,a)                                     | 颜色叠加，几乎不受原图颜色影响，参数 0-1                                                                                                    |
| SetBank                        | function(name)                                        | Spriter 里动画的父级节点的名字                                                                                                            |
| SetBankAndPlayAnimation        | function(bankname, animname)                          | 看名字能猜到是 SetBank()与PlayAnimation()两个方法的结合                                                                                   |
| SetBloomEffectHandle           | function                                              |                                                                                                                                           |
| SetBrightness                  | function                                              |                                                                                                                                           |
| SetBuild                       | function(buildname)                                   | buildname 就是 scml 文件的名字                                                                                                            |
| SetClientSideBuildOverrideFlag | function                                              |                                                                                                                                           |
| SetClientsideBuildOverride     | function                                              |                                                                                                                                           |
| SetDefaultEffectHandle         | function                                              |                                                                                                                                           |
| SetDeltaTimeMultiplier         | function(speed)                                       | 动画播放速度（速度倍数）                                                                                                                    |
| SetDepthBias                   | function                                              |                                                                                                                                           |
| SetDepthTestEnabled            | function                                              |                                                                                                                                           |
| SetDepthWriteEnabled           | function                                              |                                                                                                                                           |
| SetErosionParams               | function                                              |                                                                                                                                           |
| SetFinalOffset                 | function                                              |                                                                                                                                           |
| SetFloatParams                 | function                                              |                                                                                                                                           |
| SetHatOffset                   | function                                              |                                                                                                                                           |
| SetHaunted                     | function                                              |                                                                                                                                           |
| SetHighlightColour             | function                                              |                                                                                                                                           |
| SetHue                         | function                                              |                                                                                                                                           |
| SetInheritsSortKey             | function                                              |                                                                                                                                           |
| SetLayer                       | function                                              |                                                                                                                                           |
| SetLightOverride               | function                                              |                                                                                                                                           |
| SetManualBB                    | function                                              |                                                                                                                                           |
| SetMultColour                  | function(r,g,b,a)                                     | 颜色叠乘，受原图颜色影响，参数 0-1                                                                                                          |
| SetMultiSymbolExchange         | function                                              |                                                                                                                                           |
| SetOceanBlendParams            | function                                              |                                                                                                                                           |
| SetOrientation                 | function(ANIM_ORIENTATION)                            | 设置动画的方向，参数从constants.lua里取ANIM_ORIENTATION                                                                                    |
| SetPercent                     | function(animname, percent)                           | 动画播放百分比，固定帧，不会动（动画名，百分比）                                                                                               |
| SetRayTestOnBB                 | function                                              |                                                                                                                                           |
| SetSaturation                  | function                                              |                                                                                                                                           |
| SetScale                       | function(x,y,z)                                       | 贴图缩放,值范围：(0-1]                                                                                                                     |
| SetSkin                        | function(build_name, def_build)                       |                                                                                                                                           |
| SetSortOrder                   | function                                              |                                                                                                                                           |
| SetSortWorldOffset             | function                                              |                                                                                                                                           |
| SetSymbolAddColour             | function                                              |                                                                                                                                           |
| SetSymbolBloom                 | function                                              |                                                                                                                                           |
| SetSymbolBrightness            | function                                              |                                                                                                                                           |
| SetSymbolExchange              | function                                              |                                                                                                                                           |
| SetSymbolHue                   | function                                              |                                                                                                                                           |
| SetSymbolLightOverride         | function                                              |                                                                                                                                           |
| SetSymbolMultColour            | function                                              |                                                                                                                                           |
| SetSymbolSaturation            | function                                              |                                                                                                                                           |
| SetTime                        | function(time)                                        | 设置当前动画从第几秒开始播放（秒）                                                                                                          |
| SetUILightParams               | function                                              |                                                                                                                                           |
| SetWorldSpaceAmbientLightPos   | function                                              |                                                                                                                                           |
| Show                           | function(layername)                                   | 显示图层(Layer)                                                                                                                           |
| ShowSymbol                     | function                                              | 显示通道，与HideSymbol对应                                                                                                                 |
| UseColourCube                  | function                                              |                                                                                                                                           |
| UsePointFiltering              | function                                              |                                                                                                                                           |

## 层，通道

动画`层`是在`anim.bin`里的，使用反编译工具将`anim.bin`转成`xml`文件，即可看到`layer`的名字

点击下载 [反编译工具](https://atjiu.github.io/dstmod-tutorial/attachment/反编译anim.binv0.2.zip)

解压，配置环境变量

打开`DecodeAnimtion.bat` `EncodeAnimtion.bat` 这两个文件，将里面的`D:\SteamLibrary\steamapps\common\Don't Starve Mod Tools`这个路径换成自己电脑上的安装路径即可

随便找一个动画 zip 包，把里面的 `anim.bin` 给解压到当前目录下，然后双击 `DecodeAnimtion.bat` 即可反编译成 xml 文件

![](images/20210908153903.png)

打开 `anim.bin.xml` 文件，就可以看到层的名字了

![](images/20210908154028.png)

---

通道(symbol)就是 spriter 里右边项目文件夹，如下图

![](images/20210908154209.png)

## 实操

> 做过人物 mod 的都知道，人物在 idle 状态的时候，是有三只手的，当在游戏中拿武器时，第三只手显示出来同时再隐藏掉一只手，这样看起来会合理的多。这里显示和隐藏图层就是用到了 `层` `通道` 相关的方法

如上反编译出来的 xml 里的 layername，其中`pigking_arm`就是猪王的胳膊，如果想在游戏里隐藏，代码如下

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

---

如果没有找到对应的 layername 怎么办呢？还可以对通道进行更新，比如给猪王换一个皮，我这以给猪王屁股上贴个膏药为例

首先找到猪王的屁股贴图在哪个文件夹里(pigking_torso)，然后将里面的图片替换掉即可

![](images/20210908155526.png)

然后在电脑上的某个位置创建一个文件夹，随便叫啥名都行，把换好贴图的文件夹给拷贝到新文件夹里去，也可以对原文件夹改个名

![](images/20210908155808.png)

打开 spriter，在刚创建的文件夹(pigking)里创建一个项目，保存名为 `wound_pig_king.scml`，如下图

![](images/20210908160232.png)

最后把 sp 文件与文件夹都拷贝到 exported 里启动游戏打包即可

![](images/20210908160401.png)

打包好后，在 mod 文件夹里的 anim 里会找到一个名为 `wound_pig_king.zip`的文件，打开它，把里面的 `anim.bin` 给删掉即可

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

---

骚操作

当使用 `inst.AnimState:OverrideSymbol()` 方法试图去覆盖旧通道时，当传的第二个或者第三个参数不存在时，贴图则会消失，也是一种变相的隐藏贴图的方式

## 人物通道

**人物的手持通道有坑，这里单独拿出来说一下**

当做一件武器时，装备到手上，手部的通道名是 `swap_object` ，替换的话，一般写法都是 `owner.AnimState:OverrideSymbol("swap_object", "swap_spear", "swap_spear")`

但是如果按照上面实操中的方法来创建新通道时，进入游戏会发现贴图丢失了

解决办法：

人物手持的贴图在制作时，添加动画的名字就不能随便写了，一定要写`BUILD`，在 spriter 里把动画做好后，打成 zip 包，再将 zip 包里的 `anim.bin` 给删掉，然后再用就能在游戏里显示贴图了

至于原理，貌似挺复杂的，我也不是太清楚，在这就不解释了 :)
