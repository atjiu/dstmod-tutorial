## 前言

在 Q&A 里已经介绍过 rpc 与 replica 的关系 [传送门](https://tomoya92.github.io/dstmod-tutorial/#/qa?id=%e4%bb%a5replica%e7%bb%93%e5%b0%be%e7%9a%84%e7%bb%84%e4%bb%b6%e4%b8%8erpc%e6%9c%89%e4%bb%80%e4%b9%88%e5%8c%ba%e5%88%ab)

replica 翻译过来就是 `复制品` 的意思，顾名思义就是给客机获取数据用的，因为组件一般都是在主机处添加处理的，在客机里是拿不到组件上的数据的，除非有replica组件才能从它身上获取

举个例子

一般获取组件上属性或者调用方法的写法都是 `inst.components.mycomponent.fieldname` 如果是客机的话，直接从components上取是取不到值的，如果关联了replica就可以这样来取 `inst.replica.mycomponent.fieldname`

## 编写组件

我这里以我写的一个mod为例，定义一个位置组件 dest

```lua
local Dest = Class(function(self, inst)
    self.inst = inst
    self.dest = {}
    self.needupdate = true
end, nil, {})

function Dest:AddDest(data)
    -- todo(添加新位置到 self.dest里去)
end

function Dest:GetDest()
    return self.dest
end

return Dest
```

这样就可以在主机上通过 `inst.components.dest:GetDest()` 来获取到所有的位置信息了。

**但这仅限于在主机上获取，在客机上是取不到的，这时候就要写replica组件**

## replica组件

> replica组件的命名方式是固定写法，都是原组件名+`_replica`.lua的形式

定义一个复制组件 `dest_replica.lua`

```lua
local function OnDestDirty(self, inst)
    self.dest = inst.replica.dest._dest:value()
end

-- 定义与主组件是一样的
local Dest = Class(function(self, inst)
    self.inst = inst
    -- 定义一个字符串的通信变量
    -- 三个参数分别是：当前组件所挂实例的GUID，唯一的一个名字不能重复写什么都行，事件名
    self._dest = net_string(inst.GUID, "dest._dest", "destdirty")

    if not TheWorld.ismastersim then
        -- 这里做个判断，只在客机里监听这个 destdirty 事件，destdirty事件就是上面定义的字符串通信变量的事件名
        inst:ListenForEvent("destdirty", function(inst) OnDestDirty(self, inst) end)
    end
end)

-- 这个方法是给主组件用的，用于将主组件里的数据传过来在复制组件里同步信息用
function Dest:SetDest(dest)
    if TheWorld.ismastersim then
        -- 通信变量只要调用 set() 方法，即自动触发定义时指定的第三个参数（也就是发一个事件出去）
        self._dest:set(dest)
    end
end

return Dest
```

上面提到了一个方法 `SetDest()` 是给主组件用的，那么主组件里就要来调用一下，时机就是数据有变动时

```lua
local Dest = Class(function(self, inst)
    self.inst = inst
    self.dest = {}
    self.needupdate = true
end, nil, {})

function Dest:AddDest(data)
    -- todo(添加新位置到 self.dest里去)
    table.insert(self.dest, data)
    -- 同步数据到复制组件里
    if self.inst.replica.dest then
        -- 在这调用复制组件里的方法，将 self.dest 数据给传过去
        self.inst.replica.dest:SetDest(self.dest)
    end
end

function Dest:GetDest()
    return self.dest
end

return Dest
```

## 流程

1. 主机组件里有数据变化时，会调用复制组件里的 `SetDest()` 方法将数据同步过去
2. 在 `SetDest()` 方法里会调用复制组件里定义的通信变量 `_dest` 的 `set()` 方法，因为只要调用 `set()` 方法，就会发出事件
3. 调完 `set()` 方法，事件 `destdirty` 也被发出了，然后客机里通过监听 `destdirty` 事件的触发，从而调用 `OnDestDirty()` 方法，将通信变量 `_dest` 里set的值给取出来，然后放在自身的 `self.dest` 属性上
4. 到此，客机就可以通过 `inst.replica.dest.dest` 的方式来获取组件里存的位置信息了

**最后，也是最重要的一点，写了replica组件，一定要用 AddReplicableComponent("dest") 函数注册一下，否则是不会生效的**

## 总结

通信变量有很多类型

- net_bool            通信变量类型为bool类型
- net_string          通信变量类型为string类型
- net_entity
- net_ushortint
- net_byte
- net_tinybyte
- net_smallbyte
- net_float
- net_event
- net_smallbytearray

我一般就用bool,string两个类型的，甚至bool类型都可以不用，string就够用了

---
net变量调用set()时会触发事件，但当set的值没有发生变化时，事件是不会触发的

相比于rpc就不同了，rpc是只要发出，就一定会触发，它不会判断值是否发生变化
---

虽然客机上拿不到主机上的组件上的属性值，但主机上可以拿到挂在复制组件上的通信变量，所以客机从复制组件上拿的数据都是被动接收的，这就导致了主给客同步数据是很方便的，但主没给客同步数据，客机又想要数据怎么办？就要借助rpc了，客机发个rpc给主机，主机接收到rpc数据后，再同步数据到复制组件里，然后客机就能拿到值了

**因为rpc，net这些东东都属于网络请求，所以全都是异步的操作，切记不要在刚发完rpc就去replica里取数据，往往是取不到的**
