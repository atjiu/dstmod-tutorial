>  声明：本篇文章由`五年一班 勿言`整理

## 概述

class是面向对象的重要一环，在饥荒关于其的实现中，表是其数据结构的载体，对于表来说，函数传参是传递引用的，你在函数中通过表引用做出的修改是影响本身的。

实现一个类得解决两个方面的问题，一个是对象的实例化，一个是类继承

饥荒对于类的实现依赖两个重要特性，一个是元表，一个是闭包，我假设阅读此文的人了解元表和它`__index`,`__newindex`,`__call`这三个元函数，了解闭包的具体含义

实现实例化，可以借助元表的`__call`元函数，这个函数会使得一个表可以像函数一样调用使用

实现继承，函数的继承就是一个浅拷贝，元素的继承通过实例化时调用父表的初始化函数

饥荒的类实现同时实现了数据代理，通过`__index`和`__newindex`进行数据的劫持和重定向

这里只是概述一下，如果你看不太明白，下面我会给出详细的解释，本文建议上下文综合理解

饥荒关于类的实现，文件位于`scripts/class.lua`中

## Class函数的调用实例

因为类有继承和非继承两种构造，Class函数也有两种调用形态

**继承构造**

在UI部分，都是继承构造，不论是什么UI类，都是Widget派生而来，拿一个简单的Text举个例子

```lua
local Widget = require "widgets/widget"
local Text = Class(Widget, function(self, font, size, text, colour)
    Widget._ctor(self, "Text") --注意这一行，这是继承实现的重要一环

    self.inst.entity:AddTextWidget()
    self.inst.TextWidget:SetFont(font)
    self.font = font
	self:SetSize(size)
    self:SetColour(colour or { 1, 1, 1, 1 })
    if text ~= nil then
        self:SetString(text)
    end
end)
```

这个调用传入了两个参数，一个Widget表作为父表，一个匿名函数，这个匿名函数就是初始化对象数据所要调用的
匿名函数的内部有一个父表的_ctor函数调用，这个_ctor就是Widget表声明部分传入的匿名函数，通过调用父表的初始化匿名函数，达到元素继承的目的

**非继承构造**

在除开UI的其它地方，几乎都是这种形式，典型的就是components里，拿一个简单的举例子

```lua
local Eater = Class(function(self, inst)
    self.inst = inst

    self.eater = false
    self.strongstomach = false
    self.preferseating = { FOODGROUP.OMNI }
    self.caneat = { FOODGROUP.OMNI }
    self.oneatfn = nil
    self.lasteattime = nil
    self.ignoresspoilage = false
    self.eatwholestack = false
    self.healthabsorption = 1
    self.hungerabsorption = 1
    self.sanityabsorption = 1
end,
nil,
{
    caneat = oncaneat,
})
```

这个调用传入了三个参数，第一个是一个匿名函数，效果也是初始化对象的数据；第二个是nil；第三个是一个表，这个表关系着数据代理，我们后面拓展的时候再谈

## 核心实现

看完了调用，我们现在来看一看Class函数最核心的实现
这是类的核心实现部分(数据代理不包括在内，所以第三个形参省去了)

```lua
function Class(base,_ctor)
    local c={}
    --部分1
    if not _ctor and type(base)=="function" then
        _ctor=base
        base=nil
    elseif type(base) == 'table' then
        for k,v in pairs(base) do
            c[k]=v
        end
    end
    --部分2
    c.__index=c
    c._ctor=_ctor
    --部分3
    local mt={}
    mt.__call=function(class_tbl,...)
        local obj={}
        setmetatable(obj,c)
        if c._ctor then
            c._ctor(obj,...)
        end
        return obj
    end
    setmetatable(c,mt)
    return c
end
```

Class函数里有三个最核心的表，一个c，一个mt，一个obj；先看部分3，在函数末尾，Class函数将mt表设置为了c的元表，然后返回了c,mt表中有`__call`元函数，`__call`元函数返回obj表

```lua
local Con = Class(...)  --调用Class获取内部返回的c表
local obj_table = Con() --__call元函数的设置使得c表可以像函数一般调用，这个调用过程就是执行_call元函数的过程，拿到返回的obj表
local obj_s_table = Con()  --每次这种调用都会产生一个新的obj表返回，这就是实例化的过程
```

---------

再来看部分1，上面提到，Class有两种调用方式，部分1的if就是分别处理两种构造

```lua
function Class(base,_ctor)

if not _ctor and type(base)=="function" then --这里对应非继承构造，调用的时候，base是匿名函数，__ctor是nil
    _ctor=base --这里将__ctor设置成了匿名函数，统一下文匿名函数用_ctor表示
    base=nil
elseif type(base) == 'table' then --这里对应继承构造，base就是父表，_ctor就是匿名函数
    for k,v in pairs(base) do --这里遍历父表是为了对父表的函数做一个浅拷贝，实现方法的继承
        c[k]=v
    end
end
```

执行完部分1,`_ctor`指向匿名函数，如果是继承构造，那么子表现在已经继承了父表的所有方法

------------

再来看部分2，部分2比较简单，将c表的`__index`键指向自己,在`__call`内，会将c设置成obj的元表，在定义函数的时候，我们定义的函数都是c表的，那么通过设置元表，将`__index`指向自己，那么obj实例表就可以使用c表的函数了

然后是保存匿名函数到c表中，以`_ctor`为键值，这样`__call`元函数产生实例表obj的时候可以调用`_ctor`，对obj内的元素进行初始化。

顺带提一句，在调用`__call`元函数的时候，总是会默认会把对应的c表作为第一个实参，形参名为`class_tbl`，后面三个点的形参在lua里是不定长参数，你调用Class函数传入的匿名函数,即`_ctor`在`__call`内调用时接收的参数，第一个参数就是生成的实例表obj,后面就是你自定义任意数量的参数。

这里`__call`元函数是在Class函数内定义的，在执行Class的时候，它并没有调用。`__call`内能够拿到实际对应的c表依赖于lua函数闭包的特性，如果不是很明白闭包，请自行查阅资料。

```lua
local Con = Class(function(self,a,b,c) --__ctor的形参，形参self在实际调用时对应的就是生成的实例表
    self.key_a=a
    self.key_b=b
    self.key_c=c
end)
local obj_table = Con(1,2,3) --实例化，这里将三个参数传入
local obj_s_table = Con(3,2,1)  --同一个c表进行实例化，传入不同的参数
--最后我们产生的实例表obj_table和obj_s_table里面都会有三个元素:key_a,key_b,key_c


local Don = Class(Con,function(self,a,b,c,d) --继承构造
    Con._ctor(self,a,b,c) --调用父类的_ctor给obj表初始化，相当于继承了父类的元素，十分重要的一行
    self.key_d=d  --子类的元素
end)
local obj_t_table = Don(5,6,7,8)
--最后我们生成的实例表obj_t_table内就有四个元素
```

## 数据代理

讲到这里，class的核心实现已经完成了，接下来讲一下数据代理

数据代理，即你操作表的数据都会先经过特定的函数进行处理，这就是`__index`和`__newindex`这两个元方法所完成的事。

在`class.lua`中，定义了如下两个局部函数

如果一个c表启用了数据代理，那么c中的`__index`和`__newindex`就会被分别设置为以下对应的两个函数

```lua
--这两个函数的代码和下面的一段文字一起阅读
local function __index(t, k)
    local p = rawget(t, "_")[k]--先从obj表下的_表读取对应的键
    if p ~= nil then --如果有直接返回
        return p[1]
    end
    return getmetatable(t)[k]  --没有则去元表，即c中查找
end

local function __newindex(t, k, v)
    local p = rawget(t, "_")[k]
    if p == nil then  --如果不是被代理则直接在实例表中修改
        rawset(t, k, v)
    else --如果是被代理的，取出对应的值修改，并触发一个预设的函数
        local old = p[1]
        p[1] = v  --修改
        p[2](t, v, old)  --这里是一个函数调用
    end
end
```

上面说过,实例表obj的元表是对应的c表，那么设置了这两个元函数就意味着对实例表obj的部分数据访问会触发`__index`，全部数据修改都会触发`__newindex`

如果你要访问的键在obj表本身就有，那么是不会触发`__index`的，所以在生成obj表时，将需要代理的部分预设好即可。不是需要代理的，直接从obj本身存取。

在这两个元函数中进行数据访存要使用rawget和rawset，避免自身的数据访存触发了自身，造成无限递归。

启用了数据代理，那么每一个实例表obj中就会多一个元素：下划线`_`

`_`是一个表，被代理的元素都会在这个表中有对应的键

上面提到过，Class函数和数据代理相关是调用时的第三个参数对应的表

我们继续用Eater组件做示例(省去了举例多余的键)

```lua
local function oncaneat(self, caneat, old_caneat)--结合上面的__newindex内的p[2](t, v, old)思考，v就是新值，old就是修改前的旧值
    if old_caneat ~= nil then
        clearcaneat(self, old_caneat)
    end
    if caneat ~= nil then
        for i, v in ipairs(caneat) do
            self.inst:AddTag((type(v) == "table" and v.name or v).."_eater")
        end
    end
end

local Eater = Class(function(self, inst)
    self.inst = inst
    self.eater = false
    self.caneat = { FOODGROUP.OMNI } --需要代理的键
end,
nil,
{
    caneat = oncaneat,  --caneat是实例表中应该存在的一个键,oncaneat是上面定义的一个函数,修改这个被代理的键时，就会触发这里预设对应的函数
})
```

接下来让我们修改对应的Class函数

```lua
function Class(base,_ctor,props)--第三个参数props
    local c={}
    --部分1
    if not _ctor and type(base)=="function" then
        _ctor=base
        base=nil
    elseif type(base) == 'table' then
        for k,v in pairs(base) do
            c[k]=v
        end
    end
    --部分2
    if props ~= nil then --如果props表存在，即有需要代理的键，则替换元函数
        c.__index = __index
        c.__newindex = __newindex
    else
        c.__index = c
    end
    c._ctor=_ctor
    --部分3
    local mt={}
    mt.__call=function(class_tbl,...)
        local obj={}
        if props ~= nil then --如果需要代理，则遍历，在_表中生成对应的结构
            obj._ = { _ = { nil, __dummy } } --对_做代理，禁止外部通过键访问到真正的_表，__dummy是一个空函数,不过通过rawget rawset还是能拿到
            for k, v in pairs(props) do
                obj._[k] = { nil, v }
            end
        end
        setmetatable(obj,c)
        if c._ctor then
            c._ctor(obj,...)
        end
        return obj
    end
    setmetatable(c,mt)
    return c
end
```

```lua
--对于上面的Eater例子，实例化产生的obj表内数据部分的结构会是这样
{
    ["_"]={
        ["_"]={nil,__dummy},
        ["caneat"]={{ FOODGROUP.OMNI },oncaneat}
    },
    ["inst"]=inst,
    ["eater"]=false
}
```

## 关于数据代理的作用

显然，数据代理相当于为我们提供了一个天然的监听效果，当对应键的值被修改了，就可以立刻做出反应
在components这一块，部分带有replica的组件就是通过这种方式去同步对应的net

## 其它

class.lua内还有其它的功能，有兴趣的朋友可以自行阅读，这里只讲一下关键部分的实现。

本人认知有限，难免有错误地方，如有发现，请与我联系。

2021/12/17 by 勿言
