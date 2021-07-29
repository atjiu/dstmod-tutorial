prefab 预制体，饥荒游戏里所有的东西都是prefab（比如人物，花，鸟，鱼，树等等）

## 定义

先看看`Prefab()`函数的定义

```lua
Prefab = Class( function(self, name, fn, assets, deps, force_path_search)
    self.name = string.sub(name, string.find(name, "[^/]*$"))  --remove any legacy path on the name
    self.desc = ""
    self.fn = fn
    self.assets = assets or {}
    self.deps = deps or {}
    self.force_path_search = force_path_search or false

    if PREFAB_SKINS[self.name] ~= nil then
		for _,prefab_skin in pairs(PREFAB_SKINS[self.name]) do
			table.insert( self.deps, prefab_skin )
		end
    end
end)
```

- name 预制体名，在控制台里通过 `c_give()` 生成物品的时候要传的就是这个名字
- fn 初始化函数，在里面定义各种属性，方法等
- assets 材质，预制体用到的动画、贴图、音乐等
- deps 直接传prefabs变量即可，在main.lua里klei给定义好了这个变量，就是为了兼容乱七八糟的mod里的写法的`prefabs = nil -- this is here so mods dont crash because one of our prefab scripts missed the local and a number of mods were erroneously abusing it`
- force_path_search 直译强制路径搜索

## 创建

在案例中的添加食谱那篇里有创建预制体的例子，代码如下，贴图可以去那篇里跟着做一下

```lua
-- 加载贴图，动画
local assets = {Asset("ANIM", "anim/pidan.zip"), Asset("IMAGE", "images/pidan.tex"), Asset("ATLAS", "images/pidan.xml")}

function fn()
    local assetname = "pidan"

    local inst = CreateEntity() -- 创建实体
    inst.entity:AddTransform() -- 添加xyz形变对象
    inst.entity:AddAnimState() -- 添加动画状态

    MakeInventoryPhysics(inst)

    inst.AnimState:SetBank(assetname) -- 地上动画
    inst.AnimState:SetBuild(assetname) -- 材质包，就是anim里的zip包
    inst.AnimState:PlayAnimation("idle") -- 默认播放哪个动画

    MakeInventoryFloatable(inst)
    --------------------------------------------------------------------------
    if not TheWorld.ismastersim then
        return inst
    end
    --------------------------------------------------------------------------
    inst:AddComponent("inspectable") -- 可检查组件
    inst:AddComponent("inventoryitem") -- 物品组件

    inst.components.inventoryitem.atlasname = "images/pidan.xml" -- 在背包里的贴图

    inst:AddComponent("edible") -- 可食物组件
    inst.components.edible.foodtype = FOODTYPE.MEAT

    inst:AddComponent("perishable") -- 可腐烂的组件
    inst.components.perishable:SetPerishTime(TUNING.PERISH_SUPERSLOW)
    inst.components.perishable:StartPerishing()
    inst.components.perishable.onperishreplacement = "spoiled_food" -- 腐烂后变成腐烂食物

    inst.components.edible.hungervalue = TUNING.CALORIES_SMALL
    inst.components.edible.healthvalue = TUNING.CALORIES_TINY
    inst.components.edible.sanityvalue = TUNING.SANITY_HUGE

    inst:AddComponent("stackable") -- 可堆叠
    inst.components.stackable.maxsize = TUNING.STACK_SIZE_SMALLITEM

    MakeHauntableLaunch(inst)

    return inst
end

return Prefab("pidan", fn, assets, prefabs)
```

创建例子中的几个方法 `MakeInventoryPhysics(inst)` `MakeInventoryFloatable(inst)` `MakeHauntableLaunch(inst)`

这些函数是在 `standardcomponents.lua` 里定义的