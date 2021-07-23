
或许有些玩家会觉得，直接移除，太**变态**了，如果能让玩家自己选可使用的次数，就好了，下面就来介绍一下mod的配置项的定义

## 配置

打开modinfo.lua文件，配置 configuration_options 变量

```lua
-- mod的配置项，后面介绍
configuration_options = {{
    name = "use_count",             -- 配置项名换，在modmain.lua里获取配置值时要用到
    hover = "触手尖刺使用次数",        -- 鼠标移到配置项上时所显示的信息
    options = {{                    -- 配置项目可选项
        description = "默认",        -- 可选项上显示的内容
        hover = "默认可使用100次",    -- 鼠标移动到可选项上显示的信息
        data = 100                  -- 可选项选中时的值，在modmain.lua里获取到的值就是这个数据，类型可以为整形，布尔，浮点，字符串
    }, {
        description = "200次",
        hover = "总共可使用200次",
        data = 200
    }, {
        description = "无限次",
        hover = "无耐久，随便用",
        data = 0
    }},
    default = 100                   -- 默认值，与可选项里的值匹配作为默认值
}}
```

进游戏看看效果

![](images/20210723102152.png)

## 获取

有了配置项，接下来在modmain.lua里获取，然后加以利用

通过 `GetModConfigData()` 方法来获取值

```lua
local use_count = GetModConfigData("use_count")
```

然后修改一下上一篇写的去掉触手尖刺使用次数的代码，即可实现让玩家随自己喜好来定义使用次数

```lua
local use_count = GetModConfigData("use_count")

AddPrefabPostInit("tentaclespike", function(inst)
    if inst.components.finiteuses then
        if use_count == 0 then
            inst:RemoveComponent("finiteuses")
        else
            inst.components.finiteuses:SetMaxUses(use_count)
        end
    end
end)
```
