组件就相当于预制体的属性，一个预制体有多少属性（功能）就相当于它有多少个组件在身上挂着

这篇就来介绍一下定义一个组件的方法

> 我这选的例子是定义一个等级组件，每吃一样东西，就能让人物升一级，每升一级，就能增加当前最大血量的10%的血量，这么一个功能的组件

先放上完整代码

scripts/components/level.lua
```lua
-- 初始化组件
local Level = Class(function(self, inst)
    self.inst = inst
    self.level = 0
    self.maxlevel = 10
end, nil, {})

-- 获取当前人物等级
function Level:GetLevel()
    return self.level
end

-- 升级
function Level:IncrLevel(count)
    if self.level >= self.maxlevel then
        return
    end
    self.level = self.level + count
    self.onlevelupfn(self.inst)
end

-- 升级时回调方法
function Level:OnLevelUp(fn)
    self.onlevelupfn = fn
end

-- 重置等级
function Level:ResetLevel()
    self.level = 0
    self.onlevelupfn(self.inst)
end

-- 保存游戏时将当前人物等级保存下来
function Level:OnSave()
    return {
        level = self.level,
        maxlevel = self.maxlevel
    }
end

-- 加载游戏时将保存的等级加载进来
function Level:OnLoad(data)
    if data.level then
        self.level = data.level
    end
    if data.maxlevel then
        self.maxlevel = data.maxlevel
    end

    -- 根据当前等级初去处理升级后要做的事
    self.onlevelupfn(self.inst)
end

return Level
```

在modmain.lua里通过api给所有玩家都添加上这个等级组件，然后再监听两个事件，一个吃，一个死亡

当吃东西的时候，给人物升级，当人物死亡的时候，重置人物等级

```lua
local function OnLevelUp(inst)
    local health_percent = inst.components.health:GetPercent()

    local maxhealth = inst.components.health.maxhealth
    inst.components.health.maxhealth = math.floor(maxhealth * (1 + inst.components.level:GetLevel() * 0.1))

    inst.components.health:SetPercent(health_percent)
end

AddPlayerPostInit(function(inst)
    if not inst.components.level then
        -- 给人物添加上组件level
        inst:AddComponent("level")
        -- 设置人物升级时的回调函数
        inst.components.level:OnLevelUp(OnLevelUp)
        -- 监听吃东西事件
        inst:ListenForEvent("oneat", function(inst, data)
            inst.components.level:IncrLevel(1)
            inst.components.talker:Say("Level: " .. tostring(inst.components.level:GetLevel()))
        end)
        -- 监听死亡事件
        inst:ListenForEvent("death", function(inst, data)
            inst.components.level:ResetLevel()
        end)
    end
end)
```

------

**定义组件时的Class()方法的三个参数分别指什么？**

在 [高级-Class](https://tomoya92.github.io/dstmod-tutorial/#/class) 中有Class函数源码的介绍

**事件回调是怎么工作的？**

给人物身上添加上level组件时候顺便调用 inst.components.level:OnLevelUp(OnLevelUp) 将自定义的实现方法 `OnLevelUp` 当参数传进去，然后在组件里将自定义的实现赋值给level实例上的 `onlevelupfn` 属性上

此时level的self上就有一个属性 `onlevelupfn` 用来处理升级的时候要做的一些操作

剩下的就是在合适的位置调用一下这个方法即可，比如吃完东西会升级，升级的方法里要调用一下。加载的时候要将人物等级给初始化回去，等级有变化了，也要调用一下，这样就实现了只要等级有变化，相应的`OnLevelUp`方法就会被调用的原因

**scripts/components下的lua文件不需要在modmain里require或者import吗？**

不像scripts/prefabs下的文件那样，定义了还要在modmain里通过PrefabFiles = {} 来引入，**scripts/components下面的文件是不需要引入的，直接用即可**
