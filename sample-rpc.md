只要涉及到服务器与客户端通信的地方都要用到RPC

下面用一个更新游戏世界时间速率的例子来介绍一下RPC的用法

### 客户端向服务器发送消息

使用api自定义一个RPC

```lua
-- AddModRPCHandler() 方法的前两个参数是随便填写的，与 SendModRPCToServer() 方法里的第一个参数里从MOD_RPC表里取的值的键是一一对应的
-- 一般第一个参数会写上mod名字，第二个参数是当前这个rpc的功能名
-- 第三个参数是一个函数，函数的第一个参数固定是player对象，后面的参数就是在发RPC时传的数据了，数据的类型可以为整形，浮点型，布尔型，字符型，甚至table，function都是可以的
--这里代码需要在服务端执行。
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
下面代码需要在客户端执行。

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


### 服务器向客户端发送消息。

此处与上面有所不同，需要在客户端和服务端都注册同一个名字的handler

在客户端注册
```lua
    --misaka_world一般为mod名字，一个mod所有rpchandler一般是相同的。
    --s2c_refresh rpc名字，这里代表服务器s向客户端c发送刷新消息，这样命名比较有意义一点。
if TheNet:GetIsClient() then
    AddClientModRPCHandler("misaka_world", "s2c_refresh",
                           function( str)
    print("我收到啦来自服务器的消息啦："...str)       
    end)
end
```
在服务端注册,这里仅作为注册用，不需要实现功能。
```lua
if TheNet:GetIsServer() then
    AddClientModRPCHandler("misaka_world", "s2c_refresh",
                           function(str) end)
end
```
服务器调用，发送消息str到客户端。
```lua
-- 发送时，传递到的后面三个参数，玩家ID,必须要有，str是自定义的。
SendModRPCToClient(CLIENT_MOD_RPC["misaka_world"]["s2c_refresh"],
                       player.userid, str)
```