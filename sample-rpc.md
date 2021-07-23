只要涉及到服务器与客户端通信的地方都要用到RPC

下面用一个更新游戏世界时间速率的例子来介绍一下RPC的用法

使用api自定义一个RPC

```lua
AddModRPCHandler("world_time","scale", function(player, timeScale)
    local currentTimeScale = GLOBAL.TheSim:GetTimeScale()  -- 拿到当前世界时间速率
    currentTimeScale = currentTimeScale + timeScale
    if currentTimeScale <= 0 then
        currentTimeScale = 1
    end
    if currentTimeScale > 4 then
        currentTimeScale = 4
    end
    GLOBAL.TheSim:SetTimeScale(currentTimeScale)  --设置新的世界时间速率
    GLOBAL.TheNet:Announce("当前世界时间流速为: x" .. currentTimeScale)  -- 在游戏里发送匿名公告
end)
```

定义好之后，下面就是触发了，我这里选择使用按键来触发，按键分别是 `[` `]`

- `[` 减速
- `]` 加速

```lua
GLOBAL.TheInput:AddKeyHandler(function(key, down)  -- 监听键盘事件
    if down then
        if key == 91 then -- [
            SendModRPCToServer(MOD_RPC["world_time"]["scale"], -0.5)
        elseif key == 93 then -- ]
            SendModRPCToServer(MOD_RPC["world_time"]["scale"], 0.5)
        end
    end
end)
```