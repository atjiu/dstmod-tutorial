上一篇里介绍的 `modutil.lua` 里定义的函数，在modmain.lua里用是不需要`GLOBAL`来调用的

但有些对象就必须要用`GLOBAL`来调用，分别都有哪些呢？这里来罗列一下

> 是否需要GLOBAL来调用的在 mods.lua 里有定义，在env里声明的都是可以直接用的，不需要加GLOBAL，其它就需要加上GLOBAL才能访问了，env声明部分代码如下

```lua
local env =
{
    -- lua
    pairs = pairs,
    ipairs = ipairs,
    print = print,
    math = math,
    table = table,
    type = type,
    string = string,
    tostring = tostring,
    require = require,
    Class = Class,

    -- runtime
    TUNING=TUNING,

    -- worldgen
    LEVELCATEGORY = LEVELCATEGORY,
    GROUND = GROUND,
    LOCKS = LOCKS,
    KEYS = KEYS,
    LEVELTYPE = LEVELTYPE,

    -- utility
    GLOBAL = _G,
    modname = modname,
    MODROOT = MODS_ROOT..modname.."/",
}
```

被 `global()` 包装的对象经过查找在 `main.lua` 里，分别有以下这些

> 这些对象在饥荒源码里找不到在哪定义的，所以下面罗列的属性及方法都是全局搜索出来的

**以下列出的属性和方法使用 属性：GLOBAL.TheCamera.distance 来调用，方法 GLOBAL.TheCamera:GetDownVec()**

## TheCamera

游戏摄像头对象

**属性**

- targetpos 获取目标的xyz坐标
- headingtarget
- targetoffset
- heading
- distance
- distancegain

**方法**

- GetDownVec()
- SetDownVec()
- Update(dt)
- Snap()
- GetRightVec()
- SetRightVec()
- SetDefault()
- SetOffset()
- GetGains()
- SetGains(1.5, heading_gain, distance_gain) 设置视野范围的，三个参数，游戏内在上船的时候视野会变大，那部分有相应的代码
- SetOffset(x,y,z)
- SetDistance(dt)
- GetDistance()
- GetHeading()
- ZoomOut(zoom)
- ZoomIn(zoom)
- SetHeadingTarget(rotamount)
- GetHeadingTarget()
- AddListener()
- RemoveListener()
- GetPitchDownVec()
- CanControl()
- SetControllable(bool)
- IsControllable()
- Shake() 屏幕摇晃，四个参数
- SetOnUpdateFn()
- Apply()
- PushScreenHOffset(self, SCREEN_OFFSET) 屏幕水平方向位移，在打开游戏内礼物那部分有相关代码
- PopScreenHOffset(self) 与上一个方法对应，这个方法是复位屏幕的位移

## ShadowManager

游戏中阴暗管理对象

**属性**

没找到相关的属性

**方法**

- SetTexture("images/shadow.tex")

## RoadManager

游戏中路径管理对象

**属性**

没找到相关的属性

**方法**

- BeginRoad()
- AddControlPoint()
- SetStripEffect()
- SetStripTextures()
- GenerateVB()
- GenerateQuadTree()
- IsOnRoad(x,y,z)

## EnvelopeManager

**属性**

没找到相关的属性

**方法**

- AddColourEnvelope()
- AddVector2Envelope()

## PostProcessor

滤镜处理对象

**属性**

没找到相关的属性

**方法**

- SetColourCubeData()
- SetColourCubeLerp()
- SetBloomEnabled()
- SetDistortionEnabled()
- AddTextureSampler()
- SetTextureSamplerState()
- SetTextureSamplerFilter()
- AddUniformVariable()
- SetColourModifier()
- AddSamplerEffect()
- AddSampler()
- SetColourCubeSamplerEffect()
- AddPostProcessEffect()
- SetEffectUniformVariables()
- SetBloomSamplerParams()
- SetPostProcessEffectBefore()
- EnablePostProcessEffect()
- SetDistortionFactor()
- SetOverlayBlend()
- SetLunacyEnabled()
- SetDistortionRadii()
- SetDistortionEffectTime()
- SetLunacyIntensity()
- SetMoonPulseParams()
- SetMoonPulseGradingParams()
- IsBloomEnabled()
- IsDistortionEnabled()

## FontManager

字体管理对象

**属性**

没找到相关的属性

**方法**

没找到相关的方法

## MapLayerManager

地图层管理对象

**属性**

没找到相关的属性

**方法**

- SetSampleStyle()
- CreateRenderLayer()
- SetPrimaryColor()
- SetSecondaryColor()
- SetSecondaryColorDusk()
- SetMinimapColor()

## Roads

**属性**

没找到相关的属性

**方法**

没找到相关的方法

## TheFrontEnd

*整理方法的时候大致猜出来的* 场景管理对象（打开饥荒界面，选择存档界面，配置游戏界面等）

**属性**

- screenroot
- widget_editor
- _showalleventservers
- match_results
- fadedir
- fadecb
- screenstack
- fade_type
- consoletext
- lastx
- lasty
- tracking_mouse
- MotdManager

**方法**

- GetIsOfflineMode() 获取是否是离线模式
- SetOfflineMode() 设置游戏模式（离线，在线）
- PushScreen() 加载下一个场景
- GetActiveScreen() 获取当前场景
- EnableWidgetDebugging()
- EnableEntityDebugging()
- Fade() 场景切换的特效（渐进，渐出等）
- PopScreen() 退出场景
- OnSaveLoadError() 进入游戏失败时保存错误信息的方法
- ShowScreen() 显示场景
- GetGraphicsOptions() 获取显示选项（这个应该是游戏设置里图像的配置项）
- ClearScreens() 清除所有场景
- SetFadeLevel()
- GetFadeLevel()
- GetSound() 获取背景音乐（不是进入存档游戏的音乐，而是打开饥荒的音乐）
- StopTrackingMouse() 停止鼠标跟踪
- OnControl()
- OnMouseMove() 鼠标移动时事件
- OnMouseButton() 鼠标按键事件
- ShowSavingIndicator() 显示存档列表
- HideSavingIndicator() 隐藏存档列表
- SendScreenEvent() 发送场景事件
- GetScreenStackSize() 获取栈里场景的数量（说白了就是拿到目前加载的场景数量）
- GetAccountManager() 获取玩家帐号管理对象
- LockFocus()
- GetFocusWidget()
- ShowConsoleLog() 显示控制台日志
- HideConsoleLog() 隐藏控制台日志
- GetHUDScale() 获取界面上组件显示的缩放大小
- FadeBack()

## TheWorld

游戏世界对象

这个对象应该是写mod最常用的了，至少 `TheWorld.ismastersim` 这个属性非常常见

**属性**

- Map 世界地图对象
- components 世界对象上的所有组件，比如沙尘暴，植物重生等
- ismastersim 判断是否是服务器
- __announcementtask 公告任务
- topology
- net
- auto_teleport_players 自动传送玩家
- minimap 小地图
- state 世界状态，比如季节，温度，天气等
- generated
- speechdisabled
- isdeactivated
- GetMvpAwards
- GetAwardedWxp
- GetPlayerStatistics 玩家统计数据
- GetMatchOutcome
- Pathfinder
- worldprefab
- has_ocean 判断是否有海洋（应该是更新海洋那个版本留下来兼容旧档的一个属性）
- meta
- GroundCreep

**方法**

- PushEvent() 世界事件
- DoTaskInTime() 定时任务 延迟执行
- DoPeriodicTask() 定时任务 循环执行
- PostInit()
- ListenForEvent() 监听事件
- GetMvpAwards()
- GetAwardedWxp()
- GetPlayerStatistics()
- GetMatchOutcome()
- IsValid() 判断世界是否有效，世界有效则允许客户端连接进来
- AddTag()
- HasTag() 判断世界是否有某个标签，比如 HasTag("cave") 意思是判断世界是否有洞穴
- checkwaveyjonestarget()
- reservewaveyjonestarget()
- AddComponent()

## ThePlayer

玩家对象

**属性**

- HUD
- player_classified 玩家私有属性，在服务端可以拿到所有玩家的信息，但客机里，每个玩家就只能拿到自己的信息了，区别于replica，在饥荒组件里有不少组件是以 `_replica` 结尾的，可以理解为对所属组件的一个复制，这个复制文件里的所有方法，属性在客机里是都可以获取到的
- shownothightlight
- Transform 变量，上面有玩家在世界上的位置信息，以及玩家的大小，长宽，缩放等等
- components 玩家身上的组件
- replica 玩家复制品，可从上面获取一些三围相关的数据
- entity 玩家实体
- PushEvent 推送事件
- prefab 玩家的预制体
- userid 玩家的klei帐号id

**方法**

- GetPosition() 获取玩家坐标信息 x,y,z
- PushEvent() 推送事件
- AddTag()
- HasTag() 判断玩家身上是否有某个标签
- IsValid() 判断玩家是否是有效的
- IsNear() 判断某个实例是否在玩家附近，比如靠近boss会播放特定的音乐
- DoTaskInTime() 定时任务，延迟执行
- GetDistanceSqToInst(inst) 获取某个实体离玩家的距离
- EnableMovementPrediction() 开启/关闭延迟补偿
- GetStormLevel() 获取风暴等级

## AllPlayers

**属性**

没找到相关的属性

**方法**

没找到相关的方法

## SERVER_TERMINATION_TIMER

**属性**

没找到相关的属性

**方法**

没找到相关的方法

## EventAchievements

成就管理，原版饥荒里没见到哪用了，不过有成就mod，估计用的就是这个对象来实现的

**属性**

没找到相关的属性

**方法**

- GetActiveAchievementsIdList() 获取成就id列表
- LoadAchievementsForEvent() 根据事件加载成就
- GetAchievementsCategoryList() 获取成就分类列表
- FindAchievementData() 查找成就信息
- IsAchievementUnlocked() 判断成就是否解锁
- GetNumAchievementsUnlocked() 获取未解锁的成就数量
- SetAchievementTempUnlocked() 临时将成就设置为未解锁
- GetAllUnlockedAchievements() 获取所有未解锁的成就
- SetActiveQuests()
- BuildFullQuestName()
- ParseFullQuestName()

## TheRecipeBook

游戏里配方管理对象

**属性**

- selected 选中的配方
- filters 过滤配方
- recipes 所有配方列表

**方法**

- Load() 加载配方
- RegisterWorld()
- GetValidRecipes() 获取可制作的配方

## TheCookbook

食谱

**属性**



**方法**

- Load() 加载食谱
- ClearNewFlags()
- Save() -- for saving filter settings 保存过滤的条件设置
- ClearFilters()
- IsNewFood() 判断是否是新食物
- GetFilter() 获取过滤条件
- SetFilter() 设置过滤条件
- ApplyOnlineProfileData()

## ThePlantRegistry

更新后的农田种植新增的对象，记录研究过的庄稼数据

**属性**

- save_enabled 是否允许保存研究过的庄稼数据

**方法**

- Load() 加载
- KnowsPlantStage() 已知的生长阶段
- KnowsFertilizer() 已知的肥料
- KnowsSeed() 已知的种子
- KnowsPlantName() 已知的庄稼名
- ClearFilters() 清除过滤条件
- GetPlantPercent() 获取庄稼生长的百分比
- HasOversizedPicture() 庄稼是否有巨大贴图
- ApplyOnlineProfileData() 加载在线数据
- SetLastSelectedCard() 设置最后的选择页
- IsAnyPlantStageKnown() 判断是否解锁庄稼生长的所有阶段

## Lavaarena_CommunityProgression

熔炉竞技场对象

**属性**

没找到相关的属性

**方法**

- Load()
- Save()
- RegisterForWorld()
- IsQueryActive()
- RequestAllData()
- GetProgressionQuerySuccessful()
- IsEverythingUnlocked()
- GetProgressionKeyBoss()
- IsLocked()
- IsNewUnlock()
- GetUnlockOrder()
- GetUnlockData()
- GetQuestQuerySuccessful()
- GetCurrentQuestData()
- GetUnlockOrderStyles()
- GetNumTotalUnlocks()
- GetProgression()
- GetLastSeenProgression()

## SaveGameIndex

保存游戏时要用到的对象，主要操作的是一个存档回档那个标签里的5个插槽（栏位）的数据

**属性**

- loaded_from_file 从本地文件加载存档信息
- current_slot 当前插槽信息
- slot_cache 插槽缓存信息
- slots 所有插槽

**方法**

- Load()
- Save()
- GetValidSlots() 获取所有有效的插槽
- GetSlotSession() 获取当前插槽的会话
- IsSlotMultiLevel() 判断是否是多世界的插槽
- GetShardIndex() 获取存档索引
- IsSlotEmpty() 是否是空插槽
- GetSlotServerData() 获取插槽保存的服务端数据
- GetCurrentMode() 获取游戏模式（冒险，无尽等）
- GetCurrentSaveSlot() 获取当前保存的插槽
- StartAdventure() 开始冒险
- StartSurvivalMode() 开始生存模式
- LoadServerEnabledModsFromSlot() 加载服务端开启的mod
- DeleteSlot() 删除当前插槽，也就是回档
- GetLastUsedSlot() 获取最后一个使用的插槽
- UpdateServerData() 更新服务端数据
- GetEnabledMods() 获取开启的mod
- LoadSlotCharacter() 加载插槽保存数据里的角色信息
- SetSlotServerData() 设置插槽里的服务端数据
- SetSlotEnabledServerMods() 设置插槽里存档里开启的服务端mod
- SetSlotGenOptions()
- GetSlotLastTimePlayed() 获取插槽最新的游戏时间
- GetSlotDay() 获取插槽里存档的天数
- GetSlotDateCreated() 获取插槽的创建日期
- GetSlotCharacter() 获取插槽里存档的人物
- GetSlotDayAndSeasonText() 获取插槽里存档的天数和季节文本信息
- GetSlotPresetText()
- GetNextNewSlot() 获取下一个新的插槽

## ShardGameIndex

游戏索引对象

**属性**

没找到相关的属性

**方法**

- GetSlot()
- Delete()
- SaveCurrent()
- Load()
- GetGenOptions()
- WriteTimeFile()
- GetSaveDataFile()
- GetSaveData()
- OnGenerateNewWorld()
- GetGameMode()
- CheckWorldFile()
- NewShardInSlot()
- SetServerShardData()
- IsEmpty()

## ShardSaveGameIndex

与上面 SaveGameIndex 基本上一样

**属性**

- slot_cache
- slots

**方法**

- Load()
- Save()
- GetValidSlots() 获取所有有效的插槽
- GetSlotSession() 获取当前插槽的会话
- IsSlotMultiLevel() 判断是否是多世界的插槽
- GetShardIndex() 获取存档索引
- IsSlotEmpty() 是否是空插槽
- GetSlotServerData() 获取插槽保存的服务端数据
- GetCurrentMode() 获取游戏模式（冒险，无尽等）
- GetCurrentSaveSlot() 获取当前保存的插槽
- StartAdventure() 开始冒险
- StartSurvivalMode() 开始生存模式
- LoadServerEnabledModsFromSlot() 加载服务端开启的mod
- DeleteSlot() 删除当前插槽，也就是回档
- GetLastUsedSlot() 获取最后一个使用的插槽
- UpdateServerData() 更新服务端数据
- GetEnabledMods() 获取开启的mod
- LoadSlotCharacter() 加载插槽保存数据里的角色信息
- SetSlotServerData() 设置插槽里的服务端数据
- SetSlotEnabledServerMods() 设置插槽里存档里开启的服务端mod
- SetSlotGenOptions()
- GetSlotLastTimePlayed() 获取插槽最新的游戏时间
- GetSlotDay() 获取插槽里存档的天数
- GetSlotDateCreated() 获取插槽的创建日期
- GetSlotCharacter() 获取插槽里存档的人物
- GetSlotDayAndSeasonText() 获取插槽里存档的天数和季节文本信息
- GetSlotPresetText()
- GetNextNewSlot() 获取下一个新的插槽

## CustomPresetManager

自定义预设管理对象

**属性**

没找到相关的属性

**方法**

- Load()
- GetPresetIDs() 获取预设id
- LoadCustomPreset() 加载自定义预设值
- PresetIDExists() 预设id是否存在
- IsValidPreset() 是否是有效预设
- IsCustomPreset() 是否是自定义预设
- SaveCustomPreset() 保存自定义预设
- MoveCustomPreset() 移动自定义预设
- DeleteCustomPreset() 删除自定义预设

## TheNet

网络对象

**属性**

没找到相关的属性

**方法**

- GetPVPEnabled() 获取pvp开启状态
- Say() 群发
- Talker() 私聊
- Kick() 踢人
- BanForTime() 禁人一段时间
- Ban() 禁人
- StartVote() 开始投票
- StopVote() 停止投票
- DiceRoll() 掷骰子
- AnnounceVoteResult() 匿名投票结果
- SendWorldRollbackRequestToServer() 发送回滚请求给服务器
- SendWorldResetRequestToServer() 发送重置世界请求给服务器
- IsDedicated() 检测是否是专服
- SystemMessage() 系统消息
- Announce() 匿名消息
- SendRemoteExecute() 发送远程指令
- GetDefaultMaxPlayers() 获取默认最大玩家数
- GetServerIsClientHosted() 获取服务端与客户端是否是一个机器
- GetClientTable()
- StartClient()
- GetNetworkStatistics() 获取网络统计
- IsOnlineMode() 是否是在线模式
- GetUserID() 获取用户id
- GetServerGameMode() 获取服务端游戏模式
- SendResumeRequestToServer() 发送继续请求给服务器
- GetIsServer() 判断是否是服务端
- GetIsClient() 判断是否是客户端
- IncrementSnapshot()
- TruncateSnapshots()
- DoneLoadingMap()
- CleanupSessionCache() 清除会话缓存
- IsDedicatedOfflineCluster() 是否是离线专服
- IsNetOverlayEnabled()
- JoinServerResponse() 加入服务器响应
- Disconnect() 断开连接
- SendSpawnRequestToServer() 发送重生请求给服务器
- DeleteUserSession() 删除玩家会话
- DeserializeUserSession() 反序列化玩家会话
- NotifyLoadingState() 通知加载状态
- GetDefaultVoteEnabled() 获取默认投票是否开启
- GetServerModNames() 获取服务端名字
- ServerModSetup() 服务端mod设置信息
- ServerModCollectionSetup() 服务端mod合集设置信息
- BeginServerModSetup() 开始服务端mod配置
- DownloadServerMods() 下载服务端mod
- SendRPCToServer()
- SendRPCToClient()
- SendRPCToShard()
- CallRPC()
- CallClientRPC()
- CallShardRPC()
- SendModRPCToServer()
- SendModRPCToClient()
- SendModRPCToShard()
- GetClientTableForUser()
- SerializeWorldSession() 序列化世界会话
- ServerModsDownloadCompleted() 服务端mod下载完成
- GetDefaultGameMode() 获取默认游戏模式
- GetDefaultPvpSetting() 获取默认pvp设置
- GetDefaultFriendsOnlyServer() 获取默认仅好友设置
- GetDefaultLANOnlyServer() 获取默认本地模式
- GetDefaultClanID()
- GetDefaultClanOnly()
- GetDefaultClanAdmins()
- SetServerTags() 设置服务器标签
- SetWorldGenData() 设置世界生成数据
- GetDefaultServerIntention()
- GetDefaultEncodeUserPath()
- GetDefaultServerName() 获取默认服务器名称
- GetDefaultServerPassword() 获取默认服务器密码
- GetDefaultServerDescription() 获取默认服务器描述
- GetDefaultServerLanguage() 获取默认服务器语言
- StartServer() 启动服务器
- TryDefaultEncodeUserPath()
- GetWorldSessionFileInClusterSlot()
- GetWorldSessionFile()
- GetItemsBranch()
- GetSessionIdentifier()
- GetCountryCode() 获取国家code
- SendSlashCmdToServer() 发送 / 命令给服务器，应该是获取饥荒命令列表的
- GetIsMasterSimulation() 获取是否是主模拟器
- SetIsMatchStarting() 设置是否开始匹配
- SetIsWorldResetting() 设置世界是否重置
- SetIsClientInWorld() 设置在世界中是否是客户端
- NotifyAuthenticationFailure() 通知授权失败
- DeserializeAllLocalUserSessions()
- IsNetIDPlatformValid()
- ViewNetProfile() 显示网络信息
- GetIsHosting() 获取是否是主机
- GetServerDescription() 获取服务器描述
- SetCheckVersionOnQuery() 在查询时设置版本号
- SearchServers() 搜索服务器
- GetServerListingReadDirty()
- IsSearchingServers() 是否在搜索服务器
- GetServerListings() 获取所有器列表
- StopSearchingServers() 停止搜索服务器
- GetServerListingFromActualIndex()
- JoinServerResponse() 加入服务器响应
- ReportListing() 报告列表
- GetChildProcessStatus() 获取子进程状态
- GetChildProcessError() 获取子进程异常
- StartCloudServerRequestProcess() 开始云端服务器请求处理
- GetCloudServerRequestState() 获取云端服务器请求状态
- CancelCloudServerRequest() 取消云端服务器请求
- SendLobbyCharacterRequestToServer()
- LoadPermissionLists() 加载权限列表，应该对应的是专服里的那个 adminlist.txt 文件
- HasPendingConnection()
- GetLanguageCode() 获取语言code
- SetBlacklist() 设置黑名单
- GetBlacklist() 获取黑名单
- GetPartyTable()
- LeaveParty()
- GetPartyChatHistory()
- JoinParty()
- PartyChat()
- GetFriendsList() 获取好友列表
- InviteToParty()
- GetAveragePing() 获取平均ping值
- SetPlayerMuted() 设置玩家静音
- TruncateSnapshotsInClusterSlot()
- ListSnapshotsInClusterSlot()
- ListSnapshots()
- GetLocalUserName() 获取本地用户名
- GetServerEvent() 获取服务端事件
- IsVoiceActive() 是否开启声音

## TheInput

玩家输入（键盘，鼠标）对象

**属性**

- overridepos

**方法**

- ControllerAttached()
- GetWorldPosition() 获取世界坐标
- GetWorldEntityUnderMouse() 获取鼠标所指实体
- IsKeyDown() 判断鼠标是否按下
- GetScreenPosition() 获取屏幕坐标
- IsDebugToggleEnabled() 是否开启了debug模式
- AddKeyHandler(function(key, down) end) 添加键盘/鼠标事件
- AddTextInputHandler(function(text) end) 添加输入文本事件
- IsControlPressed() 控制键是否被按下，非ctrl,shift,alt,这方法里面可以传任意按键的keycode
- GetControlIsMouseWheel() 判断鼠标滚轮是否在滚去
- UpdateEntitiesUnderMouse() 更新鼠标所指实体
- GetAllEntitiesUnderMouse() 获取鼠标所指所有实体
- CacheController()
- ClearCachedController()
- UpdatePosition(x,y) 更新位置信息，参数只有两个
- OnControl()
- OnMouseButton() 鼠标按键事件回调
- OnMouseMove() 鼠标移动事件回调
- OnRawKey()
- OnText()
- OnGesture() 鼠标手势回调
- OnControlMapped()
- ControllerConnected()
- EnableAllControllers()
- DisableAllControllers()
- OnUpdate()
- AddControlHandler()
- GetHUDEntityUnderMouse()
- GetAnalogControlValue() 获取类似物体的数据
- AddGeneralControlHandler() 添加常规控制事件处理
- TryRecacheController()
- EnableMouse()
- AddMoveHandler() 添加鼠标事件处理
- GetLocalizedControl()
- GetControllerID()
- PlatformUsesVirtualKeyboard()
- IsPasteKey()
- GetStringIsButtonImage()
- GetInputDevices() 获取输入设备信息
- AddControlMappingHandler()

## WorldSim

生成地图的对象

**属性**

没找到相关的属性

**方法**

- GetChildrenForSite()
- ForceConnectivity()
- RemoveLink()
- GenerateVoronoiMap()
- PointInSit()
- ResetAll()
- ReplaceSingleNonLandTiles()
- WorldGen_InitializeNodePoints()
- GetSitePolygon()
- AddExternalLink()
- ReserveTile()
- SetTileNodeId()
- ForceDisconectedCells()
- GetPointsForSite()
- GenerateSessionIdentifier()
- AddLink()
- DetectDisconnect()
- SetRoadParameters()
- GetWormholes()
- LuaPrint()
- DrawCellLine()
- DrawGroundLine()
- GetTile()
- WorldGen_VoronoiPass()
- SetTile()
- SetSiteFlags()
- AddEdgePoints()
- GetPointsForMetaMaze()
- ReserveSpace()
- GetSiteCentroid()
- GetWormholesExtra()
- GetRoad()
- GetVisualTileAtPosition()
- GetSite()
- SetImpassibleTileType()
- ClearNodeLinks()
- SetNodeType()
- GetSiteArea()
- GetRandomPointsForSite()
- GenerateBlendedMap()
- SeparateIslands()
- WorldGen_AddNewPositions()
- RunMaze()
- GetEncodedMap()
- WorldGen_Commit()
- GetWorldSize()
- AddChild()
- CreateNodeIdTileMap()
- DrawRoads()
- ConvertToTileMap()
- IsTileReserved()
- SetWorldSize()
- RunCA()