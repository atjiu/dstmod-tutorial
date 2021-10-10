## 前言

料理的buff不知道klei怎么想的，把它封装成了私有的方法，玩家想自制buff，要么用原版的那几个buff，要么自己模仿着写一个

所谓的buff，还是prefab，只不过这个prefab只在服务端运行，buff结束后，这个prefab也就被移除了

有了prefab就可以做各种想做的事了

## 原版buff

| 料理 | buff名 | buff对应的prefab名 |效果| 被定义在哪个文件 |
| ---- | ----| -----|----| ----|
|糖豆| `healthregenbuff` | `healthregenbuff`|回血| `healthregenbuff.lua`|
|舒缓茶| `sweettea_buff` | `sweettea_buff`|回san| `forgetmelots.lua`|
|果冻| `buff_electricattack` | `buff_electricattack`|增加伤害|`foodbuffs.lua`|
|蓝带鱼排|`buff_moistureimmunity`|`buff_moistureimmunity`|去湿|`foodbuffs.lua`|
|调味-糖|`buff_workeffectiveness`|`buff_workeffectiveness`|增加工作效率|`foodbuffs.lua`|
|调味-辣椒|`buff_attack`|`buff_attack`|增加伤害|`foodbuffs.lua`|
|调味-盐|`buff_playerabsorption`|`buff_playerabsorption`|防御|`foodbuffs.lua`|

## 自定义buff

如果想自定义buff，可能参照 `healthregenbuff.lua` 来写

实现流程：玩家身上要被挂上 `debuffable` 组件，吃完料理后调用 `AddBuff()` 方法开启buff效果，后面就不用管了，定时都是在buff定义的prefab里完成的

比如[富贵险中求](https://steamcommunity.com/sharedfiles/filedetails/?id=2591636844)中如果被蛇咬了，会有一个中毒的buff

```lua
local function OnTick(inst, target)
    if target.components.health ~= nil and not target.components.health:IsDead() and target.components.sanity ~= nil and not target:HasTag("playerghost") then
        target.components.health:DoDelta(-TUNING.JELLYBEAN_TICK_VALUE, nil, "poisoning")
        target.components.sanity:DoDelta(-TUNING.JELLYBEAN_TICK_VALUE / 2, nil, "poisoning")
        if target._ispoisonover then
            target._ispoisonover:set(true)
            target:DoTaskInTime(0.5, function(inst)
                inst._ispoisonover:set(false)
            end)
        end
        if math.random() < 1 / 5 then
            target.components.talker:Say(TUNING.POISONHEADACHE)
        end
    else
        inst.components.debuff:Stop()
    end
end

local function OnAttached(inst, target)
    inst.entity:SetParent(target.entity)
    inst.Transform:SetPosition(0, 0, 0) -- in case of loading
    inst.task = inst:DoPeriodicTask(TUNING.JELLYBEAN_TICK_RATE * 5, OnTick, nil, target)
    inst:ListenForEvent("death", function()
        inst.components.debuff:Stop()
    end, target)
end

local function OnTimerDone(inst, data)
    if data.name == "poisonover" then
        inst.components.debuff:Stop()
    end
end

local function OnExtended(inst, target)
    inst.components.timer:StopTimer("poisonover")
    inst.components.timer:StartTimer("poisonover", TUNING.TOTAL_DAY_TIME * 3)
    inst.task:Cancel()
    inst.task = inst:DoPeriodicTask(TUNING.JELLYBEAN_TICK_RATE * 5, OnTick, nil, target)
end

local function fn()
    local inst = CreateEntity()

    if not TheWorld.ismastersim then
        -- Not meant for client!
        inst:DoTaskInTime(0, inst.Remove)

        return inst
    end

    inst.entity:AddTransform()

    --[[Non-networked entity]]
    -- inst.entity:SetCanSleep(false)
    inst.entity:Hide()
    inst.persists = false

    inst:AddTag("CLASSIFIED")

    inst:AddComponent("debuff")
    inst.components.debuff:SetAttachedFn(OnAttached)
    inst.components.debuff:SetDetachedFn(inst.Remove)
    inst.components.debuff:SetExtendedFn(OnExtended)
    inst.components.debuff.keepondespawn = true

    inst:AddComponent("timer")
    inst.components.timer:StartTimer("poisonover", TUNING.TOTAL_DAY_TIME * 3)
    inst:ListenForEvent("timerdone", OnTimerDone)

    return inst
end

return Prefab("poisondebuff", fn)
```

被蛇咬时，添加buff

```lua
local function onhitother(inst, other, damage)
    if other:HasTag("character") and other.prefab ~= "wx78" then
        if not other:HasTag("poisoning") and math.random() < .5 then
            if other.components.debuffable then
                other.components.debuffable:AddDebuff("poisondebuff", "poisondebuff")
            end
        end
    end
end
```

--------------

也可以简单点，玩家吃完后，直接开启一个定时，这种可以被用来实现一次性添加的属性，比如吃完某个料理，伤害增加50%

```lua
local function item_oneaten(inst, eater)
    if eater.components.timer and eater:HasTag("player") then -- 判断是玩家
        if eater.components.timer:TimerExists("dragoonheartattack_timer") then -- 判断定时是否存在，存在的话，就重置计时
            eater.components.timer:SetTimeLeft("dragoonheartattack_timer", 240)
        else
            eater.components.timer:StartTimer("dragoonheartattack_timer", 240) --不存在就开启一个timer来计时
            if eater.components.combat ~= nil then
                eater.components.combat.externaldamagemultipliers:SetModifier("dragoonheartattack", 1.2)--调整伤害倍率
            end
        end
    end
end
```

还在要玩家身上监听一下timerdone事件

```lua
AddPlayerPostInit(function(inst)
    if not TheWorld.ismastersim then
        return inst
    end

    if not inst.components.timer then
        inst:AddComponent("timer")
    end

    -- 刚进游戏时，如果timer还没结束，再给设置一遍
    inst:DoTaskInTime(0, function(inst)
        if inst.components.timer:TimerExists("dragoonheartattack_timer") then
            if inst.components.combat ~= nil then
                inst.components.combat.externaldamagemultipliers:SetModifier("dragoonheartattack", 1.2)
            end
        end
    end)
    -- 监听timerdone事件，定时结束时将增加的伤害给移除掉
    inst:ListenForEvent("timerdone", function(inst, data)
        if data.name == "dragoonheartattack_timer" then
            if inst.components.combat ~= nil then
                inst.components.combat.externaldamagemultipliers:RemoveModifier("dragoonheartattack")
            end
        end
    end)
end)
```
