将饥荒里的Class定义简化后的一个小例子，有构造方法和继承功能

```lua
function Class(base, _ctor)
    local c = {}
    if type(base) == "function" then
        _ctor = base
        base = nil
    elseif type(base) == "table" then
        for i,v in pairs(base) do
            c[i] = v
        end
    end
    c.__index = c

    local mt = {}
    mt.__call = function(class_tbl, ...)
        print(1111, class_tbl, c, ... == nil)
        local obj = {}
        setmetatable(obj, c)
        if c._ctor then
           c._ctor(obj, ...)
        end
        return obj
    end
    c._ctor = _ctor
    setmetatable(c, mt)
    return c
end

------------------------------------------------------

local A = Class(function(self)
    self.a = "A"
end)
function A:Foo()
    print("foo", self.a)
end

local B = Class(A, function(self)
    A._ctor(self)
    self.b = "B"
end)
function B:Bar()
    print("bar", A.a, self.b)
end

A()
B()
B:Foo()
B:Bar()
```
