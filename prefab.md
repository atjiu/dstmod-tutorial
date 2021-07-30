## 定义

prefab 预制体，饥荒游戏里所有的东西都是prefab（比如人物，花，鸟，鱼，树等等）

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

预制体只是一个空壳，要想让预制体有功能（比如斧头能砍树，食物能吃），就需要给它们身上添加上相应的组件，给预制体添加组件方法就是 `AddComponent()` 相应的移除组件就是 `RemoveComponent()`

这些预制体上的方法或者属性都被定义在 `entityscript.lua` 文件里

预制体上默认已经有的属性有如下这些

```lua
EntityScript = Class(function(self, entity)
    self.entity = entity
    self.components = {}
    self.lower_components_shadow = {}
    self.GUID = entity:GetGUID()
    self.spawntime = GetTime()
    self.persists = true
    self.inlimbo = false
    self.name = nil

    self.data = nil
    self.listeners = nil
    self.updatecomponents = nil
    self.actioncomponents = {}
    self.inherentactions = nil
    self.inherentsceneaction = nil
    self.inherentscenealtaction = nil
    self.event_listeners = nil
    self.event_listening = nil
    self.worldstatewatching = nil
    self.pendingtasks = nil
    self.children = nil

    self.actionreplica = nil
    self.replica = Replica(self)
end)
```

方法有下面这些

- GetSaveRecord()
- Hide() 隐藏
- Show() 显示
- IsInLimbo()
- ForceOutOfLimbo()
- RemoveFromScene() 从屏幕中移除
- ReturnToScene() 回到屏幕中
- __tostring()
- AddInherentAction() 添加固有动作
- RemoveInherentAction() 移除固有运用
- GetTimeAlive() 获取存在时间
- StartUpdatingComponent() 开始更新组件
- StopUpdatingComponent() 停止更新组件
- StopUpdatingComponent_Deferred() 延时停止更新组件
- StartWallUpdatingComponent()
- StopWallUpdatingComponent()
- GetComponentName() 获取组件名
- AddTag() 添加标签
- RemoveTag() 移除标签
- HasTag() 判断是否有标签
- AddComponent() 添加组件
- RemoveComponent() 移除组件
- GetBasicDisplayName() 获取基础名
- GetAdjectivedName() 获取形容名
- GetDisplayName() 获取显示名
- GetIsWet() 判断是否是潮湿状态
- GetSkinBuild() 获取皮肤
- GetSkinName() 获取皮肤名
- SetPrefabName() 设置预制体名
- SetPrefabNameOverride()
- SetDeployExtraSpacing() 设置放置间隔
- SetTerraformExtraSpacing() 设置地形间隔
- SetGroundTargetBlockerRadius() 设置地面目标不可放置间隔
- SpawnChild()
- RemoveChild()
- AddChild()
- GetBrainString() 获取大脑名
- GetDebugString()
- KillTasks() 杀掉所有任务
- StartThread() 开始线程
- RunScript() 运行脚本
- RestartBrain() 重启大脑
- StopBrain() 停止大脑
- SetBrain() 设置大脑（比如一些怪物的行为）
- SetStateGraph() 设置状态图
- ClearStateGraph() 清除状态图
- ListenForEvent() 监听事件
- RemoveListener() 移除事件
- RemoveEventCallback() 移除事件回调
- RemoveAllEventCallbacks() 移除所有事件回调
- WatchWorldState() 观察世界状态，常用于判断世界季节等
- StopWatchingWorldState() 停止观察世界状态
- StopAllWatchingWorldStates() 停止观察所有世界状态
- PushEvent() 推送事件
- SetPhysicsRadiusOverride()
- GetPhysicsRadius()
- GetPosition() 获取世界坐标
- GetRotation() 获取旋转
- GetAngleToPoint()
- GetPositionAdjacentTo() 获取相邻于xx的坐标
- ForceFacePoint()
- FacePoint()
- GetDistanceSqToInst()
- IsNear() 判断是否在某个预制体附近
- GetDistanceSqToPoint()
- IsNearPlayer() 判断是否在玩家附近（这个应该是在科学仪器上有用到）
- GetNearestPlayer() 获取周围玩家
- GetDistanceSqToClosestPlayer() 获取距离最近玩家的距离
- FaceAwayFromPoint()
- IsAsleep() 判断是否在睡觉
- CancelAllPendingTasks() 取消所有等待的任务
- DoPeriodicTask() 定时任务，周期性执行
- DoTaskInTime() 定时任务，延时执行
- GetTaskInfo() 获取任务信息
- TimeRemainingInTask()
- ResumeTask() 继续任务
- ClearBufferedAction() 清除动作
- PreviewBufferedAction() 预览动作
- PerformPreviewBufferedAction()
- PushBufferedAction() 推送动作
- PerformBufferedAction()
- GetBufferedAction() 获取动作
- OnBuilt() 判断是否在创建
- Remove() 移除
- IsValid() 判断是否有效
- OnUsedAsItem()
- CanDoAction() 是否可以开始动作
- IsOnValidGround() 是否在有效的地面上
- IsOnPassablePoint() 是否在可能的位置
- IsOnOcean() 是否在海洋上
- GetCurrentPlatform() 获取当前所处平台
- GetCurrentTileType() 获取当前地皮类型
- PutBackOnGround()
- GetPersistData()
- LoadPostPass()
- SetPersistData()
- GetAdjective()
- SetInherentSceneAction()
- SetInherentSceneAltAction()
- LongUpdate()
- SetClientSideInventoryImageOverride()
- HasClientSideInventoryImageOverrides()
- GetClientSideInventoryImageOverride()
- SetClientSideInventoryImageOverrideFlag()
- IsInLight() 是否在灯光内
- IsLightGreaterThan()

在代码里要想生成一个预制体，可以使用 `SpawnPrefab()` 方法

## 创建

创建预制体要用 `mainfunctions.lua` 里的 `CreateEntity()` 创建，这个方法被挂在 `TheSim` 下

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

通过CreateEntity()方法生成的 inst 实例，在最开始会添加一些方法(来自龙飞教程 https://www.jianshu.com/p/261c62c26e99)

- Transform：变换组件，控制Prefab的位置、方向、缩放等等
- AnimState：动画组件，控制Prefab的材质(Build)，动画集合(Bank)和动画播放(Animation)
- Phiysics：物理组件，控制Prefab的物理行为，比如速度，碰撞类型等等。
- Light：光照组件，添加该组件可使得Prefab成为一个光源。
- Network：网络组件，添加与否决定了一个Prefab在主机上生成时，是否会被客户端“看”到。
- MapEntity：地图实体组件，使用该组件可以为Prefab在小地图上创建一个图标。
- SoundEmitter：声音组件，控制Prefab的声音集合和播放

创建例子中的几个方法 `MakeInventoryPhysics(inst)` `MakeInventoryFloatable(inst)` `MakeHauntableLaunch(inst)`

这些函数是在 `standardcomponents.lua` 里定义的，**这些方法绝大部分都是设置物理性质的**

- DefaultIgniteFn()
- DefaultBurnFn()
- DefaultBurntFn()
- DefaultExtinguishFn()
- DefaultBurntStructureFn()
- MakeSmallBurnable()
- MakeMediumBurnable()
- MakeLargeBurnable()
- MakeSmallPropagator()
- MakeMediumPropagator()
- MakeLargePropagator()
- MakeSmallBurnableCharacter()
- MakeMediumBurnableCharacter()
- MakeLargeBurnableCharacter()
- MakeTinyFreezableCharacter()
- MakeSmallFreezableCharacter()
- MakeMediumFreezableCharacter()
- MakeLargeFreezableCharacter()
- MakeHugeFreezableCharacter()
- MakeInventoryPhysics()
- MakeProjectilePhysics()
- MakeCharacterPhysics()
- MakeFlyingCharacterPhysics()
- MakeTinyFlyingCharacterPhysics()
- MakeGiantCharacterPhysics()
- MakeFlyingGiantCharacterPhysics()
- MakeGhostPhysics()
- MakeTinyGhostPhysics()
- ChangeToGhostPhysics()
- ChangeToCharacterPhysics()
- ChangeToObstaclePhysics()
- ChangeToWaterObstaclePhysics()
- ChangeToInventoryPhysics()
- MakeObstaclePhysics()
- MakeWaterObstaclePhysics()
- MakeSmallObstaclePhysics()
- MakeHeavyObstaclePhysics()
- MakeSmallHeavyObstaclePhysics()
- RemovePhysicsColliders()
- MakeNoGrowInWinter()
- MakeSnowCoveredPristine()
- MakeSnowCovered()
- MakeFeedableSmallLivestockPristine()
- MakeFeedableSmallLivestock()
- MakeHauntable()
- MakeHauntableLaunch()
- MakeHauntableLaunchAndSmash()
- MakeHauntableWork()
- MakeHauntableWorkAndIgnite()
- MakeHauntableFreeze()
- MakeHauntableIgnite()
- MakeHauntableLaunchAndIgnite()
- MakeHauntableChangePrefab()
- MakeHauntableLaunchOrChangePrefab()
- MakeHauntablePerish()
- MakeHauntableLaunchAndPerish()
- MakeHauntablePanic()
- MakeHauntablePanicAndIgnite()
- MakeHauntablePlayAnim()
- MakeHauntableGoToState()
- MakeHauntableGoToStateWithChanceFunction()
- MakeHauntableDropFirstItem()
- MakeHauntableLaunchAndDropFirstItem()
- AddHauntableCustomReaction()
- AddHauntableDropItemOrWork()
- ToggleOffCharacterCollisions()
- ToggleOnCharacterCollisions()
- ToggleOffAllObjectCollisions()
- ToggleOnAllObjectCollisionsAt()
- PreventCharacterCollisionsWithPlacedObjects()
- PreventTargetingOnAttacked()
- AddDefaultRippleSymbols()
- MakeInventoryFloatable()
- MakeDeployableFertilizerPristine()
- MakeDeployableFertilizer()

