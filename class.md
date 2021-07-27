在饥荒里要想创建一个 component, widget, prefab等，都需要用到Class函数，下面是Class的源码

```lua
function Class(base, _ctor, props)
    local c = {}    -- a new class instance
    local c_inherited = {}
	if not _ctor and type(base) == 'function' then
        _ctor = base
        base = nil
    elseif type(base) == 'table' then
        -- our new class is a shallow copy of the base class!
		-- while at it also store our inherited members so we can get rid of them
		-- while monkey patching for the hot reload
		-- if our class redefined a function peronally the function pointed to by our member is not the in in our inherited
		-- table
        for i,v in pairs(base) do
            c[i] = v
            c_inherited[i] = v
        end
        c._base = base
    end

    -- the class will be the metatable for all its objects,
    -- and they will look up their methods in it.
    if props ~= nil then
        c.__index = __index
        c.__newindex = __newindex
    else
        c.__index = c
    end

    -- expose a constructor which can be called by <classname>(<args>)
    local mt = {}

    if TrackClassInstances == true and CWD~=nil then
        if ClassTrackingTable == nil then
            ClassTrackingTable = {}
        end
        ClassTrackingTable[mt] = {}
		local dataroot = "@"..CWD.."\\"
        local tablemt = {}
        setmetatable(ClassTrackingTable[mt], tablemt)
        tablemt.__mode = "k"         -- now the instancetracker has weak keys

        local source = "**unknown**"
        if _ctor then
  			-- what is the file this ctor was created in?

			local info = debug.getinfo(_ctor, "S")
			-- strip the drive letter
			-- convert / to \\
			source = info.source
			source = string.gsub(source, "/", "\\")
			source = string.gsub(source, dataroot, "")
			local path = source

			local file = io.open(path, "r")
			if file ~= nil then
				local count = 1
   				for i in file:lines() do
					if count == info.linedefined then
						source = i
						-- okay, this line is a class definition
						-- so it's [local] name = Class etc
						-- take everything before the =
						local equalsPos = string.find(source,"=")
						if equalsPos then
							source = string.sub(source,1,equalsPos-1)
						end
						-- remove trailing and leading whitespace
						source = source:gsub("^%s*(.-)%s*$", "%1")
						-- do we start with local? if so, strip it
						if string.find(source,"local ") ~= nil then
							source = string.sub(source,7)
						end
						-- trim again, because there may be multiple spaces
						source = source:gsub("^%s*(.-)%s*$", "%1")
						break
					end
					count = count + 1
				end
				file:close()
			end
		end

		mt.__call = function(class_tbl, ...)
			local obj = {}
			if props ~= nil then
				obj._ = { _ = { nil, __dummy } }
				for k, v in pairs(props) do
					obj._[k] = { nil, v }
				end
			end
			setmetatable(obj, c)
			ClassTrackingTable[mt][obj] = source
			if c._ctor then
				c._ctor(obj, ...)
			end
			return obj
		end
	else
		mt.__call = function(class_tbl, ...)
			local obj = {}
			if props ~= nil then
				obj._ = { _ = { nil, __dummy } }
				for k, v in pairs(props) do
					obj._[k] = { nil, v }
				end
			end
			setmetatable(obj, c)
			if c._ctor then
			   c._ctor(obj, ...)
			end
			return obj
		end
	end

    c._ctor = _ctor
    c.is_a = function(self, klass)
        local m = getmetatable(self)
        while m do
            if m == klass then return true end
            m = m._base
        end
        return false
    end
    setmetatable(c, mt)
    ClassRegistry[c] = c_inherited
--    local count = 0
--    for i,v in pairs(ClassRegistry) do
--        count = count + 1
--    end
--    if string.split then
--        print("ClassRegistry size : "..tostring(count))
--    end
    return c
end
```

可以看到Class函数有三个参数

- base 定义类的原函数，可以传function,也可以传table
- _ctor 构造方法
- props 默认初始化的数据

一般创建一个类，_ctor 对象都是给个nil，但props可能会有值，用于初始化一些属性或者方法的，当然这些方法也可以放在base里定义，只不过base会显的特别长罢了

比如 health.lua 组件里的定义

```lua
local Health = Class(function(self, inst)
    self.inst = inst
    self.maxhealth = 100
    self.minhealth = 0
    self.currenthealth = self.maxhealth
    self.invincible = false

    --V2C: Not used at all, but who knows about MODs?
    --     Save memory instead by making nil default to true
    --self.vulnerabletoheatdamage = true
    -----

    self.takingfiredamage = false
    self.takingfiredamagetime = 0
    --self.takingfiredamagelow = nil
    self.fire_damage_scale = 1
    self.externalfiredamagemultipliers = SourceModifierList(inst)
    self.fire_timestart = 1
    self.firedamageinlastsecond = 0
    self.firedamagecaptimer = 0
    self.nofadeout = false
    self.penalty = 0

    self.absorb = 0 -- DEPRECATED, please use externalabsorbmodifiers instead
    self.playerabsorb = 0 -- DEPRECATED, please use externalabsorbmodifiers instead

    self.externalabsorbmodifiers = SourceModifierList(inst, 0, SourceModifierList.additive)

    self.destroytime = nil
    self.canmurder = true
    self.canheal = true
end,
nil,
{
    maxhealth = onmaxhealth,
    currenthealth = oncurrenthealth,
    takingfiredamage = ontakingfiredamage,
    takingfiredamagelow = ontakingfiredamagelow,
    penalty = onpenalty,
    canmurder = oncanmurder,
    canheal = oncanheal,
    invincible = oninvincible,
})
```

就是把很多方法放在了外面去定义，然后通过props传入到Class里进行base里定义字段的初始化
