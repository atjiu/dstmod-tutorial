## 写在前面

各位mod开发者们，是不是或多或少都会碰到如下场景

```lua
local function foo()
    print("foo!")
end

local function bar()
    print("bar!")
end

local function onxxxfn()
    print("onxxxfn start")
    foo()
    bar()
    print("onxxxfn end")
end

function fn()
    inst.components.xxx.onxxxfn = onxxxfn
end
```

然后想将bar()里的实现给改了，比如上面例子中是输出 `print("bar!")` 现在想将它改为 `print("bar?")`

一般能想到的就是把 `foo()` `bar()` `onxxxfn()` 都给复制一下，然后对 `inst.components.xxx.onxxxfn` 重新赋值，也就是说 `onxxxfn()` 这个方法里有多少方法，就需要复制多个份出来进行修改

很显然这样做非常不友好，当官方对其中某个方法一做改动，本来跟要修改的目标方法没关系的，但也容易导致游戏崩溃或者mod失效

## debug方法介绍

debug里提供了不少用于调试的方法，其中有两个方法可以实现上述功能

- getupvalue()
- setupvalue()

我初见这两个方法的时候，完全不知道是啥意思，听大佬解释也没明白啥意思，在菜鸟教程里是这样解释的

| fn | 描述 |
| ---| --- |
| getupvalue(f, up) | 此函数返回函数 f 的第 up 个上值的名字和值。 如果该函数没有那个上值，返回 nil 。以 '(' （开括号）打头的变量名表示没有名字的变量 （去除了调试信息的代码块）。 |
| setupvalue(f, up, value) | 这个函数将 value 设为函数 f 的第 up 个上值。 如果函数没有那个上值，返回 nil 否则，返回该上值的名字。|

**里面讲的`上值`其实就是在被查找函数中调用的其它函数** 与它对应的，还有两个函数

- getlocal()
- setlocal()

看个例子

```lua
local function onxxxfn()
    foo() -- upvalue
    bar() -- upvalue
    local _innerfn = function()
        print("balabala")
    end
    _innerfn() -- local
end
```

## 实现功能

针对上面描述的问题，用upvalue来解决的思路就是先用 getupvalue() 找到要被修改的方法，然后将其修改后再通过 setupvalue() 方法给重新赋值一下即可完成

```lua
local function foo()
    print("foo!")
end

local function bar()
    print("bar!")
end

local function onxxxfn()
    print("onxxxfn start")
    foo()
    bar()
    print("onxxxfn end")
end

-- function fn()
--     inst.components.xxx.onxxxfn = onxxxfn
-- end

print("============================")
onxxxfn() -- 原始函数执行一下，看看输出结果
print("============================")

local i = 0
local _value
local _name = ''
while _name ~= 'bar' do -- while循环找一下bar()函数的位置
    i = i + 1
    _name, _value = debug.getupvalue(onxxxfn, i)
end

print(i, _name, _value) -- 将找到的位置信息打印出来

debug.setupvalue(onxxxfn, i, function() -- 通过 setupvalue()函数将原bar()函数的内容给修改成自己想要的
    print("bar?")
end)

print("============================")
onxxxfn() -- 修改后再执行一下，看看输出结果
print("============================")
```

执行结果如下

```log
============================
onxxxfn start
foo!
bar!
onxxxfn end
============================
3       bar     function: 0000000000b5d800
============================
onxxxfn start
foo!
bar?
onxxxfn end
============================
```