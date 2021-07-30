## StateGraph

stategraph 直译是 状态图 的意思，类比于游戏里的状态机（注意，它不是状态机）

定义文件在 `stategraph.lua` 里

```lua
StateGraph = Class( function(self, name, states, events, defaultstate, actionhandlers) end)
```

- name sg名称，比如游戏里通用的一个sg名为wilson，在 `SGwilson.lua` 里定义的
- states sg里的所有状态
- events sg里所有的事件
- defaultstate sg的默认状态，在`SGwilson.lua`里指定的默认状态是 `idle` 表示空闲时状态
- actionhandlers sg里定义的所有动作处理器

## State

状态对象，方法定义如下

```lua
State = Class(function(self, args) end)
```

args参数是个对象，它里面有如下属性

- name 状态名
- tags 状态所属标签，没有规则，想定义成啥就定义成啥，用处是在某些状态里判断预制体当前有没有处于某个状态，比如有些动作是不能被打断的，就是通过这个标签来判断从而强制让那些不能被打断的状态执行完
- onenter 进入当前状态时事件
- onexit 退出当前状态时事件
- onupdate 进入当前状态即触发且是每帧都会被调
- ontimeout 通过 inst.sg:SetTimeout() 方法触发，类似于一个延时执行
- events 定义在当前状态里的事件，这里定义的事件都是 EventHandler() ，通过 inst.sg:PushEvent() 来触发
- timeline 当前状态执行过程中以帧为单位进行某些操作

events 对象里的EventHandler定义

```lua
-- name 是事件名
-- fn 事件执行的方法
EventHandler = Class(function(self, name, fn) end)
```

timeline 里定义的 TimeEvent

```lua
-- time 时间，单位是帧
-- fn 到时间执行的方法
TimeEvent = Class(function(self, time, fn) end)
```

## Action

动作，先看定义

```lua
Action = Class(function(self, data, instant, rmb, distance, ghost_valid, ghost_exclusive, canforce, rangecheckfn) end)
```

- data 如果是table，则后面参数就不用传了，如果不是table，则会将后面的参数当成data的属性来初始化Action
  - 大致意思就是，Action({rmb=true}) 和 Action(nil, nil, true) 是一样的
- instant 应该是瞬间完成的意思（猜的）
- rmb
- distance 距离
- ghost_valid 灵魂状态是否能用这动作
- ghost_exclusive 是否是灵魂状态的专有动作
- canforce 是否可被强制执行
- rangecheckfn 范围检查方法

游戏内置了很多的动作，它们被定义在了 `actions.lua` 里

## BufferedAction

执行动作的对象，先看定义

```lua
BufferedAction = Class(function(self, doer, target, action, invobject, pos, recipe, distance, forced, rotation) end)
```

- doer 执行者，比如人物在采摘的时候，doer就是人物实例
- target 目标，采摘时，目标就是被摘的东西
- action 动作，就是Action对象，比如采摘的动作就是 Actions.PICK
- invobject *猜测应该是代理对象*
- pos 位置
- recipe 配方
- distance 距离
- forced 强制
- rotation 旋转

BufferedAction 里有个 DO() 方法，就是用来执行动作的

```lua
function BufferedAction:Do()
    if not self:IsValid() then
        return false
    end
    local success, reason = self.action.fn(self)
    if success then
        if self.invobject ~= nil and self.invobject:IsValid() then
            self.invobject:OnUsedAsItem(self.action, self.doer, self.target)
        end
        self:Succeed()
    else
        self:Fail()
    end
    return success, reason
end
```

可以看到Do()方法里调用了Action里的fn函数，这么一看就是Action()交给BufferedAction对象来管理，啥时候要执行是通过Do()方法来触发的

## ActionHandler

动作处理器，定义

```lua
ActionHandler = Class(function(self, action, state, condition) end)
```

- action 动作对象
- state 动作关联的状态，可以是个字符串，也可以是一个函数
- condition 条件

## 流程

场景：玩家攻击。在这一场景中Action, BufferedAction, StateGraph 它们都是怎么工作的？

**准备工作**

- 通过`BufferedAction`将`ACTIONS.ATTACK`的动作给包装起来
- 接着通过`ActionHandler()`将Action与State绑定起来，如下
    ```lua
    ActionHandler(ACTIONS.ATTACK, function(inst, action)
        inst.sg.mem.localchainattack = not action.forced or nil
        if not (inst.sg:HasStateTag("attack") and action.target == inst.sg.statemem.attacktarget or inst.components.health:IsDead()) then
            local weapon = inst.components.combat ~= nil and inst.components.combat:GetWeapon() or nil
            return (weapon == nil and "attack")
                or (weapon:HasTag("blowdart") and "blowdart")
                or (weapon:HasTag("slingshot") and "slingshot_shoot")
                or (weapon:HasTag("thrown") and "throw")
                or (weapon:HasTag("propweapon") and "attack_prop_pre")
                or (weapon:HasTag("multithruster") and "multithrust_pre")
                or (weapon:HasTag("helmsplitter") and "helmsplitter_pre")
                or "attack"
        end
    end)
    ```
- 接着通过EventHandler将attacked事件初始化好 `EventHandler("attacked", function(inst, data) end)` 用于监听被攻击时的事件，用于播放对应动画，计算血量等
- 然后定义State = {name="attack"} 在 onenter 里调用攻击的动画进行播放。当然还有其它的操作

这么一遍走下来，就知道游戏里是怎么通过一个动作从而让人物去做不同的事情，播放不同的动画，以及播放不同的声音了

## API

对动作及sg进行修改的api有几个，在 [Mod中常用的API](https://tomoya92.github.io/dstmod-tutorial/#/api) 中有介绍

- AddAction(id, str, fn)
- AddStategraphActionHandler(stategraph, handler)
- AddStategraphState(stategraph, state)
- AddStategraphEvent(stategraph, event)
- AddStategraphPostInit(stategraph, fn)


