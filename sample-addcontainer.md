> 工坊里有给二本添加回收功能的，有给猪王添加容器快速交易的，有给雪球机添加容器放置燃料的，这篇介绍一下给物品添加容器的方法
>
> 最近在玩温蒂，就给阿比盖尔添加一个容器吧，前期可以当背包使

直接上代码

```lua
GLOBAL.setmetatable(env, {
    __index = function(t, k)
        return GLOBAL.rawget(GLOBAL, k)
    end
})

local containers = require("containers")
local params = containers.params

-- 给容器对象添加一个名为 abigail 的容器，用的是坎普斯背包的配置修改的
params.abigail = {
    widget = {
        slotpos = {},
        animbank = "ui_krampusbag_2x8",
        animbuild = "ui_krampusbag_2x8",
        pos = Vector3(300, -70, 0) -- 容器显示的位置，经测试，(0,0)位置就是被添加对象的位置，比如这里是把这个容器添加到阿比盖尔身上，所以容器出现位置的原点就是阿比盖尔所在位置，左上为正，右下为负
    },
    type = "abigail", -- 容器的类型，结合下面参数 openlimit 限制整个世界里只允许有一个当前类型的容器被打开
    openlimit = 1,
    itemtestfn = function(inst, item, slot) -- 容器里可以装的物品的条件
        return not item:HasTag("_container") and not item:HasTag("bundle") and item.prefab ~= "abigail_flower"
    end
}
-- 循环容器里小格子
for y = 0, 6 do
    table.insert(params.abigail.widget.slotpos, Vector3(-162, -75 * y + 240, 0))
    table.insert(params.abigail.widget.slotpos, Vector3(-162 + 75, -75 * y + 240, 0))
end

-- 容器打开时回调
local function onopen(inst)
    inst.SoundEmitter:PlaySound("dontstarve/wilson/chest_open")
end

-- 容器关闭时回调
local function onclose(inst)
    inst.SoundEmitter:PlaySound("dontstarve/wilson/chest_close")
end

AddPrefabPostInit("abigail", function(inst)
    if not TheWorld.ismastersim then
        return inst
    end
    -- 添加容器组件
    inst:AddComponent("container")
    -- 设置容器名
    inst.components.container:WidgetSetup("abigail")
    inst.components.container.onopenfn = onopen
    inst.components.container.onclosefn = onclose

    -- 阿比盖尔死亡时掉落容器里所有的物品
    inst:ListenForEvent("death", function(inst)
        if inst.components.container then
            inst.components.container:DropEverything()
        end
    end)
end)
```