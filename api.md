下面这些api被定义在 `modutil.lua` 文件里

## AddCustomizeGroup(category, name, text, desc, atlas, order)

## RemoveCustomizeGroup(category, name)

## AddCustomizeItem(category, group, name, itemsettings)

## RemoveCustomizeItem(category, name)

## GetCustomizeDescription(description)

## AddLevelPreInit(levelid, fn)

> 这个api里的level是世界的意思，比如地上是森林世界，地下是洞穴世界

在世界(levelid)初始化之前执行fn

fn参数是level的实例

## AddLevelPreInitAny(fn)

在任意世界初始化之前执行fn

fn参数是level的实例

## AddTaskSetPreInit(tasksetname, fn)

地图相关api，对taskset集进行操作

比如饥荒内置的两个taskset，一个是default,一个是classic

对应就是创建地图时的生物群落

## AddTaskSetPreInitAny(fn)

## AddTaskPreInit(taskname, fn)

在任务(taskname)执行之前先执行fn

fn参数为task对象

## AddRoomPreInit(roomname, fn)

地图相关api，对room的操作，在游戏里room就是最小的区域，很多room组成task，很多task组成level

## AddLocation(arg1, ...)

## AddLevel(arg1, arg2, ...)

添加level(世界)，比如游戏内置的 forest 就是地上，cave 就是洞穴

## AddTaskSet(arg1, ...)

## AddTask(arg1, ...)

## AddRoom(arg1, ...)

## AddStartLocation(arg1, ...)

## GetModConfigData(optionname, get_local_config)

获取modinfo.lua里配置的值

get_local_config参数相当于默认值

## AddGamePostInit(fn)

在游戏初始化之后执行fn

## AddSimPostInit(fn)

Sim: simulator 简写，模拟器的意思，在饥荒里可以理解为世界或者存档

在模拟器（引擎）初始化之后执行fn

## AddGlobalClassPostConstruct(package, classname, fn)

## AddClassPostConstruct(package, fn)

## AddAction(id, str, fn)

添加动作

id可以是动作名，也可以是动作本身，换句话说，id可以是字符串，也可以是table

如果id是字符串，那么这个api自动创建一个Action()并把id, str, fn给传进去初始化好，然后把动作添加到全局动作`Actions`里面去

## AddComponentAction(actiontype, component, fn)

给组件添加动作

## AddPopup(id)

## AddMinimapAtlas(atlaspath)

添加小地图上的贴图

## AddStategraphActionHandler(stategraph, handler)

给状态图(stategraph)添加处理程序(handler)

handler参数有两个(Action(), function(inst, action))

## AddStategraphState(stategraph, state)

给状态图(stategraph)添加新的状态(state)

state参数是一个table，结构如下

```lua
State{
    name = "idle",
    tags = {"idle", "canrotate"},

    onenter = function(inst, pushanim) end,

    timeline = {
        TimeEvent(5*FRAMES, function(inst) inst.didalertnoise = nil end),
    },

    ontimeout = function(inst) end,
},
```

## AddStategraphEvent(stategraph, event)

给状态图(stategraph)添加新的事件(event)

event参数是一个对象，定义结构如下

```lua
EventHandler("attacked", function(inst)
    if not inst.components.health:IsDead() and not inst.sg:HasStateTag("attack") then
        inst.sg:GoToState("hit")
    end
end),
```

## AddModShadersInit(fn)

## AddModShadersSortAndEnable(fn)

## AddStategraphPostInit(stategraph, fn)

在状态图(stategraph)初始化之后执行方法fn

fn的参数就是stategraph的实例

## AddComponentPostInit(component, fn)

在组件初始化之后执行fn

fn的参数就是component实例

## AddPrefabPostInitAny(fn)

## AddPlayerPostInit(fn)

玩家初始化后的修改api

## AddPrefabPostInit(prefab, fn)

在预制体(prefab)初始化之后执行fn

fn参数就是prefab的实例

## AddBrainPostInit(brain, fn)

在大脑(brain)初始化之后执行fn

fn参数就是brain的实例

## AddIngredientValues(names, tags, cancook, candry)

给原料(names)添加属性

- names: 原料名，可以是多个，类型是table
- tags: 对原料的分类，类型是table
  - tag共有：fruit, monster, sweetener, veggie, meat, fish, egg, decoration, fat, dairy, inedible, seed, magic
  - 这个tag可以理解为某样食材的xx度，比如大肉有1的肉度，小肉有0.5的肉度，这个参数被用在定义食谱上
- cancook：判断是否可以入锅做菜
- candry：判断原料是否可风干

## AddCookerRecipe(cooker, recipe)

添加食谱

- cooker: 食物在哪个锅里做，比如 `cookpot` `portablecookpot`
- recipe：类型是table，如下

```lua
{
    test = function(cooker, names, tags) return (names.butterflywings or names.moonbutterflywings) and not tags.meat and tags.veggie and tags.veggie >= 0.5 end,
    priority = 1,
    weight = 1,
    foodtype = FOODTYPE.VEGGIE,
    health = TUNING.HEALING_MED,
    hunger = TUNING.CALORIES_LARGE,
    perishtime = TUNING.PERISH_SLOW,
    sanity = TUNING.SANITY_TINY,
    cooktime = 2,
    floater = {"small", 0.05, 0.7}
}
```

## AddModCharacter(name, gender, modes)

添加mod人物

## RemoveDefaultCharacter(name)

移除默认的人物

## AddRecipe(name, ingredients, tab, level, placer_or_more_data, min_spacing, nounlock, numtogive, builder_tag, atlas, image, testfn, product, build_mode, build_distance)

添加制作配方，比如小偷背包很难出，写个配方直接制作

参数很多，可以参照 `recipes.lua` 里官方定义的配方来添加自己想要的配方

## AddRecipeTab(rec_str, rec_sort, rec_atlas, rec_icon, rec_owner_tag, rec_crafting_station)

制作科技栏

- rec_str: 科技栏名称
- rec_sort: 排序
- rec_atlas: 材质生成的xml文件名
- rec_icon: 材质生成的tex文件名
- rec_owner_tag: 人物专属科技标签（？我还不确定）
- rec_crafting_station

## LoadPOFile(path, lang)

加载翻译文件

在mod中可以通过 `GLOBAL.LanguageTranslator.defaultlang` 来获取游戏设置的语言，然后进而加载对应语言的翻译文件即可实现mod的多语言支持

## RemapSoundEvent(name, new_name)

## AddReplicableComponent(name)

## AddModRPCHandler(namespace, name, fn)

RPC(Remote Procedure Call) 三个单词首字母缩写，意思是：远程过程调用

说白了就是服务端与客户端通信的东西，这个方法就是自定义一个RPC

- namespace: 命名空间，一般写mod名字即可，目的就是与其它mod的rpc区分开防止冲突
- name: rpc名字，在当前命名空间下的rpc名字，发送rpc时会用到
- fn: 当rpc发送时，要做的事，方法第一个参数是player，表示发出rpc的玩家是谁

与之对应的发送方法是 SendModRPCToServer()

**使用方法详见案例中的rpc用法**

## AddClientModRPCHandler(namespace, name, fn)

自定义RPC，发送方法是 SendModRPCToClient()

## AddShardModRPCHandler(namespace, name, fn)

自定义RPC，发送方法是 SendModRPCToShard()

## GetClientModRPCHandler(namespace, name)

获取mod里定义的rpc的实现方法

## GetShardModRPCHandler(namespace, name)

获取mod里定义的rpc的实现方法

## SendModRPCToServer(id_table, ...)

## SendModRPCToClient(id_table, ...)

## SendModRPCToShard(id_table, ...)

## GetModRPC(namespace, name)

获取mod里定义的rpc

## GetClientModRPC(namespace, name)

获取mod里定义的rpc

## GetShardModRPC(namespace, name)

获取mod里定义的rpc

## SetModHUDFocus(focusid, hasfocus)

## AddUserCommand(command_name, data)

添加用户命令，类似于游戏内置的 `c_save()` `c_rollback()` 这种指令

## AddVoteCommand(command_name, init_options_fn, process_result_fn, vote_timeout)

添加投票指令

## ExcludeClothingSymbolForModCharacter(name, symbol)

## RegisterInventoryItemAtlas(atlas, prefabname)























