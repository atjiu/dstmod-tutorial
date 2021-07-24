当使用快捷键时，就会遇到在modmain.lua里处理玩家按了哪个键的问题

笨一点的办法就是把玩家按下的按键通过 `print()` 函数给打印出来，然后去日志文件里找，比如案例里更新世界时间的那个例子，就是我当初通过print()试出来的code值

```lua
GLOBAL.TheInput:AddKeyHandler(function(key, down)  -- 监听键盘事件
    if down then
        print(key) -- 这里会把键盘按下的每个键的code值都给打出来
    end
end)
```

在日志里找到后，再通过判断就能实现按下指定按键来执行相应的操作了，如下

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

当然饥荒源码里也给提供了常用的按键的code定义，在 `constants.lua` 有定义

```lua
KEY_TAB = 9
KEY_KP_0			= 256
KEY_KP_1			= 257
KEY_KP_2			= 258
KEY_KP_3			= 259
KEY_KP_4			= 260
KEY_KP_5			= 261
KEY_KP_6			= 262
KEY_KP_7			= 263
KEY_KP_8			= 264
KEY_KP_9			= 265
KEY_KP_PERIOD		= 266
KEY_KP_DIVIDE		= 267
KEY_KP_MULTIPLY		= 268
KEY_KP_MINUS		= 269
KEY_KP_PLUS			= 270
KEY_KP_ENTER		= 271
KEY_KP_EQUALS		= 272
KEY_MINUS = 45
KEY_EQUALS = 61
KEY_SPACE = 32
KEY_ENTER = 13
KEY_ESCAPE = 27
KEY_HOME = 278
KEY_INSERT = 277
KEY_DELETE = 127
KEY_END    = 279
KEY_PAUSE = 19
KEY_PRINT = 316
KEY_CAPSLOCK = 301
KEY_SCROLLOCK = 302
KEY_RSHIFT = 303 -- use KEY_SHIFT instead
KEY_LSHIFT = 304 -- use KEY_SHIFT instead
KEY_RCTRL = 305 -- use KEY_CTRL instead
KEY_LCTRL = 306 -- use KEY_CTRL instead
KEY_RALT = 307 -- use KEY_ALT instead
KEY_LALT = 308 -- use KEY_ALT instead
KEY_LSUPER = 311
KEY_RSUPER = 312
KEY_ALT = 400
KEY_CTRL = 401
KEY_SHIFT = 402
KEY_BACKSPACE = 8
KEY_PERIOD = 46
KEY_SLASH = 47
KEY_SEMICOLON = 59
KEY_LEFTBRACKET	= 91
KEY_BACKSLASH	= 92
KEY_RIGHTBRACKET= 93
KEY_TILDE = 96
KEY_A = 97
KEY_B = 98
KEY_C = 99
KEY_D = 100
KEY_E = 101
KEY_F = 102
KEY_G = 103
KEY_H = 104
KEY_I = 105
KEY_J = 106
KEY_K = 107
KEY_L = 108
KEY_M = 109
KEY_N = 110
KEY_O = 111
KEY_P = 112
KEY_Q = 113
KEY_R = 114
KEY_S = 115
KEY_T = 116
KEY_U = 117
KEY_V = 118
KEY_W = 119
KEY_X = 120
KEY_Y = 121
KEY_Z = 122
KEY_F1 = 282
KEY_F2 = 283
KEY_F3 = 284
KEY_F4 = 285
KEY_F5 = 286
KEY_F6 = 287
KEY_F7 = 288
KEY_F8 = 289
KEY_F9 = 290
KEY_F10 = 291
KEY_F11 = 292
KEY_F12 = 293

KEY_UP			= 273
KEY_DOWN		= 274
KEY_RIGHT		= 275
KEY_LEFT		= 276
KEY_PAGEUP		= 280
KEY_PAGEDOWN	= 281

KEY_0 = 48
KEY_1 = 49
KEY_2 = 50
KEY_3 = 51
KEY_4 = 52
KEY_5 = 53
KEY_6 = 54
KEY_7 = 55
KEY_8 = 56
KEY_9 = 57
```

然后上面的代码就可以修改成

```lua
GLOBAL.TheInput:AddKeyHandler(function(key, down)  -- 监听键盘事件
    if down then
        if key == GLOBAL.KEY_LEFTBRACKET then -- [
            SendModRPCToServer(MOD_RPC["world_time"]["scale"], -0.5)
        elseif key == GLOBAL.KEY_RIGHTBRACKET then -- ]
            SendModRPCToServer(MOD_RPC["world_time"]["scale"], 0.5)
        end
    end
end)
```