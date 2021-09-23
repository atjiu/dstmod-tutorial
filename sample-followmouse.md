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

---

`widgets/widget.lua` 里还有两个方法 `FollowMouse()` `StopFollowMouse()` 我试了一下，不是太好用

不过这两个方法在所有控件里都能生效，因为它被定义在了 widget.lua 里，所有的控件都是继承这个 widget 控件，所以如果能判断到鼠标点击了某个控件然后开启跟随，鼠标松开后，停止跟随，也是可以实现相同效果的