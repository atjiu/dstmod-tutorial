实现一下 `widgets/button` 的鼠标跟随效果

![](images/button-followmouse.gif)

翻看 `widgets/button` 的时候无意看到了一个回调 `SetWhileDown()` 跟随效果就可以用它来实现了

```lua
local TEMPLATES = require "widgets/redux/templates"
AddClassPostConstruct("screens/playerhud", function(self)
    self.openbutton = self:AddChild(TEMPLATES.StandardButton(function()
        print("openbutton: ", self.openbutton:GetWorldPosition())
    end , "Open", {100, 50}))
    self.openbutton:SetVAnchor(ANCHOR_BOTTOM)
    self.openbutton:SetHAnchor(ANCHOR_LEFT)
    self.openbutton:SetScaleMode(SCALEMODE_PROPORTIONAL)
    self.openbutton:SetMaxPropUpscale(MAX_HUD_SCALE)

    self.openbutton.x = 0
    self.openbutton.y = 0

    -- 这个方法在button.lua里是被放在OnUpdate()里被调用的，没有参数
    -- 因为在它被调的时候还有一层判断，判断是否被点击了，被点击了才会调这个回调方法，所以不能把设置位置的代码直接放在 OnUpdate() 里
    self.openbutton:SetWhileDown(function()
        -- 获取鼠标坐标
        local mousepos = TheInput:GetScreenPosition()
        self.openbutton.x = mousepos.x
        self.openbutton.y = mousepos.y
    end)

    -- 在组件更新方法里更新它的位置
    local _OnUpdate = self.OnUpdate
    self.OnUpdate = function(self, dt)
        _OnUpdate(self, dt)
        self.openbutton:SetPosition(self.openbutton.x, self.openbutton.y)
    end
end)
```

--------

`widgets/widget.lua` 里还有两个方法 `FollowMouse()` `StopFollowMouse()` 配合着使用，效果要比上面那种方式更好，而且因为这两个方法是被定义在 widget 里的，所以它适用于所有的控件

通过翻 widget 的代码，又找到了一个 `OnMouseButton` 的函数，它有四个参数 `OnMouseButton(button, down, x, y)`

- button 鼠标按下的按键，鼠标左键 1000，右键是1001，在constants.lua里可以找到对应的值
- down 鼠标按下时为true，松开时为false
- x，y 当前鼠标的坐标

> 在我测试时，我用的是 TEMPLATES.StandardButton() 这个模板里的按钮，这个方法有五个参数，第一个是新增的参数，为当前按钮上的文本，后四个参数就是这默认的四个参数，在用时还是要先打印一下参数的值再做判断

用法如下

```lua
local TEMPLATES = require "widgets/redux/templates"
-- local TarnsferPanel = GLOBAL.require("screens/transferpanel")
AddClassPostConstruct("screens/playerhud", function(self)
    self.openbutton = self:AddChild(TEMPLATES.StandardButton(function()
        print("openbutton: ", self.openbutton:GetWorldPosition())
    end , "Open", {100, 50}))
    self.openbutton:SetVAnchor(ANCHOR_BOTTOM)
    self.openbutton:SetHAnchor(ANCHOR_LEFT)
    self.openbutton:SetScaleMode(SCALEMODE_PROPORTIONAL)
    self.openbutton:SetMaxPropUpscale(MAX_HUD_SCALE)
    -- self.openbutton.x = 400
    -- self.openbutton.y = 600

    self.openbutton.OnMouseButton = function(text, button, down, x, y, z, a, b, c)
        print("====", text, button, down, x, y, z, a, b, c)
        if button == MOUSEBUTTON_LEFT and down then
            print("mouse down", down)
            self.openbutton:FollowMouse() -- 判断鼠标按下时开启控件的鼠标跟随
        else
            print("mouse up", down)
            self.openbutton:StopFollowMouse() -- 判断鼠标松开时，停止控件的跟随
            self.openbutton:SetPosition(TheInput:GetScreenPosition()) -- 并把当前鼠标的位置坐标设置成控件的坐标
        end
    end
end)
```

**以上方法都没有对控件最终位置做保存，也就是说下次打开控件或者下次进游戏时还是会出现在默认设置的位置。**

-------

## 2021/11/23 liximi补充

如果直接使用官方的`FollowMouse()`函数可能会遇到出乎意料的问题。在实际情况中，我们一般会将widget作为另一个widget的子级（child），当我们未对子级widget单独设置其坐标原点时，其坐标原点就是父级widget的坐标位置。即，当我们给子级widget设置坐标SetPosition()时，设置的相对于父级widget坐标的坐标——相对位置。而如果我们使用`SetVAnchor(ANCHOR_BOTTOM)`、`SetHAnchor(ANCHOR_LEFT)`单独给子级widget设置了原点，那么子级widget就不会再以父级坐标为原点，但同时子级也不会再跟随父级的移动而移动，如果你需要整体移动某个UI，那么单独设置子级widget的原点会让你移动起来非常麻烦。

另外，饥荒的屏幕原点在左下角，也就是说，当你将一个widget的坐标原点设置为：

```lua
self.openbutton:SetVAnchor(ANCHOR_BOTTOM)	--设置原点到屏幕下方
self.openbutton:SetHAnchor(ANCHOR_LEFT)		--设置原点到屏幕左方，综合起来就是左下方
```

时，其原点坐标就是（0,0,0）。

综上，我不推荐各位给子级widget单独设置原点，跟随父级是最方便的。

那么如何在以父级坐标为原点的情况下，让widget跟随鼠标呢？我们只需要修改一下上面的鼠标坐标即可，这样做：

```lua
-- 获取鼠标坐标
local mousepos = TheInput:GetScreenPosition()
local _pos = (mousepos - self:GetWorldPosition())	--这里的self是你要移动的widget的父级widget
local _scale = self:GetScale()			--父级widget的全局缩放
_pos = (x = _pos.x/ _scale.x,
			y = _pos.y/ _scale.y,
			z = _pos.z/ _scale.z)
self.openbutton.x = _pos.x		--任何情况下都正确的x坐标
self.openbutton.y = _pos.y		--任何情况下都正确的y坐标
```

下面详细解释一下：

`self:GetWorldPosition()`用于获取self的屏幕坐标（绝对坐标），这里需要注意屏幕坐标和相对坐标的区别，这个函数获得的坐标的原点是（0,0,0）；而`TheInput:GetScreenPosition()`获取的坐标也是屏幕坐标，这两个坐标相减，就可以得到子级widget的相对坐标，这个坐标才是你应该在`SetPosition()`中传递的参数。

但是到这里还不够，父级的缩放也会影响最终子级widget在屏幕上的位置。`GetScale()`这个函数可以获得self的全局缩放，什么是全局缩放呢？全局缩放就是这个widget最终在屏幕上表现出来的缩放大小。父级widget的缩放会作用到子级widget上，因此每个widget的全局缩放都是其父级，及其父级的父级，及其父级的父级的父级...的缩放值相乘之后再乘以其本身的缩放值。来看一下`GetScale()`的代码能帮助你更好理解这个关系：

```lua
function Widget:GetScale()
    if self.parent ~= nil then
        local sx, sy, sz = self.inst.UITransform:GetScale()
        local scale = self.parent:GetScale()	--获取父级的全局缩放，这里其实是个递归，调用了父级的GetScale()，而父级的GetScale又会去调用爷爷级的GetScale()，逐级向上
        return Vector3(sx * scale.x, sy * scale.y, sz * scale.z)	--最终用自己的缩放值 * 父级的全局缩放值
    end
    return Vector3(self.inst.UITransform:GetScale())	--如果没有父级，就直接返回自己的缩放值
    --还要注意的是，返回值是一个vector3对象，GetScreenPosition()和GetWorldPosition()的返回值也都是vetor3对象，关于vector3的内容可以查看scripts/vector3.lua
end
```

所以这里我们先获取父级widget的全局缩放，然后再用上面得到的相对坐标除以这个缩放值。为什么这么做呢？这样设想一下：当我们有一个widget，名为儿子，儿子的相对位置为（100,100,0），其父级（名为爸爸）的坐标为（0,0,0），全局缩放为（2,2,2），即，爸爸和爸爸的所有子级都被放大了2倍，那么现在这个儿子的绝对位置是多少呢？或者说这个儿子在屏幕上的位置是多少呢？

答案是（200,200,0）。同时，因为爸爸的坐标为（0,0,0），所以儿子的相对坐标也是（200,200,0）。

但是，这个坐标是最终表现在屏幕上的坐标，实际上我们`SetPosition()`的时候用的是（100,100,0），如果我们要更改儿子的坐标，那么我们应该使用和（100,100,0）相同单位的数值。比如，我们要把儿子移动到屏幕的（300,300,0），那么我们应该给`SetPosition()`传递（150,150,0）。

因此，我们需要给上面的相对坐标除以父级的全局缩放，才能获得最终正确的坐标。

总结一下，鼠标跟随的过程实际上是通过屏幕坐标求相对坐标的过程，如果能理解UI树布局的原理，那么对此问题的理解就更容易了。

给大家留一道课后题：

> 儿子的相对位置为（100,100,0），爸爸的屏幕坐标为（220,350,0），全局缩放为（1.5，2,1），鼠标的屏幕坐标为（400,550,0），如果鼠标到儿子的距离小于等于50就算鼠标命中儿子，求：此时鼠标命中儿子了吗。
>
> （p.s.数学还是非常有用的啊）
