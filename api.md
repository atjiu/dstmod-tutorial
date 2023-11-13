下面这些api被定义在 `modutil.lua` 文件里

## AddCustomizeGroup(category, name, text, desc, atlas, order)

猜测：添加自定义分组（用于创建世界时的对于游戏自定义处）

## RemoveCustomizeGroup(category, name)

猜测：移除自定义分组（用于创建世界时的对于游戏自定义处）

## AddCustomizeItem(category, group, name, itemsettings)

猜测：添加自定义项（与AddCustomizeGroup搭配使用，组里有项）

## RemoveCustomizeItem(category, name)

猜测：移除自定义项（与AddCustomizeGroup搭配使用，组里有项）

## GetCustomizeDescription(description)

猜测：获取自定义配置的描述

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

对所有`地图任务`初始化的前置操作

## AddTaskPreInit(taskname, fn)

在`地图任务`(taskname)执行之前先执行fn

fn参数为task对象

## AddRoomPreInit(roomname, fn)

地图相关api，对room的操作，在游戏里room就是最小的区域，很多room组成task，很多task组成level

## AddLocation(arg1, ...)

## AddLevel(arg1, arg2, ...)

添加level(世界)，比如游戏内置的 forest 就是地上，cave 就是洞穴

## AddTaskSet(arg1, ...)

地图api，添加`地图任务`集合

## AddTask(arg1, ...)

地图api，添加地图任务

## AddRoom(arg1, ...)

与task结合使用，room可理解为地图上的`奇遇`布置，task可理解为地形（草原，矿区，橡树林...）

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

见 `AddClassPostConstruct` 的介绍

## AddClassPostConstruct(package, fn)

万能api，对一个类进行hook，常见用于对 `components`, `widget` 等hook

- package: 被hook的类的路径，要带上包名，如：`"components/health"` 即为对 `components/health.lua` 组件进行hook
- fn：回调函数，fn第一个参数永远都是self，即被hook类的自身，后面参数见被hook类的定义处，如health.lua的构造方法是 `local Health = Class(function(self, inst)` 有两个参数，fn就有两个参数即：`fn(self, inst)`

举例：

比如要对`"components/health"`进行hook，写法如下

```lua
AddClassPostConstruct("components/health", function(self, inst)
    -- 这里是你的逻辑
end)
```

## AddAction(id, str, fn)

添加动作

id可以是动作名，也可以是动作本身，换句话说，id可以是字符串，也可以是table

如果id是字符串，那么这个api自动创建一个Action()并把id, str, fn给传进去初始化好，然后把动作添加到全局动作`Actions`里面去

id参数为Action本身，eg.
```lua
-- 定义一个动作kan，优先级为99.
local KAN = Action({
     priority = 99,
})
-- 定义动作属性
KAN.id = "KAN_ID" -- 动作唯一标示
KAN.str = "砍" -- 动作可以执行时，显示的文字，比如你拿斧头时，鼠标移到树上会显示砍。
KAN.fn = function(act)
    -- 此处调用组件中写好的函数，传入相应参数，当触发该动作时，会执行这里代码。
    print("触发砍动作fn")
    return true
end
-- 将动作添加到动作表
AddAction(KAN)
```

id为字符串，eg.
```lua
AddAction('KAN_ID','砍',function(act)
 -- 触发砍
    return true
end)
```

## AddComponentAction(actiontype, component, fn)

给组件添加动作，决定该动作何时触发，参数：场景、组件(必须是触发的物品有的组件)、判断函数，满足相同条件是，显示优先级priority最高的，详情查看componentactions.lua

```lua
-- 定义一个动作选择器，eg.EQUIPPED装备有一个物品A，A有tool组件时触发fn
AddComponentAction("EQUIPPED", "tool", function(inst, doer,target, actions, right)
    -- function参数，inst这里是物品A,doer是动作执行者，这里是一般为玩家，target动作执行对象，actions，可触发的动作列表。right=true，是否是右键动作。
    if
    not right -- 不是右键
    doer.replica.combat ~= nil -- 有攻击组件
    and not (doer.replica.rider ~= nil and doer.replica.rider:IsRiding()) -- 动作执行者不在骑乘状态
    and not target:HasTag("wall") -- 目标不是墙
    and doer.replica.combat:CanTarget(target) -- doer可以把target作为目标
    and target.replica.combat:CanBeAttacked(doer) -- target可以被doer攻击
    and not doer.replica.combat:IsAlly(target) -- 目标不是队友
    then
        --判定如果符合kan动作触发的条件，就讲kan动作加入到可触发动作列表。
        table.insert(actions, ACTIONS.KAN_ID)
    else
        print("没通过判定")
    end
end)
```

## AddPopup(id)

添加一个弹窗的ui，游戏内详见耕作帽右键打开的作物详情界面，模组里可参照勋章的皮肤弹窗写法

## AddMinimapAtlas(atlaspath)

添加小地图上的贴图

```lua
Assets = {
    Asset("IMAGE", "images/map_icons/map_icon.tex"), -- 小地图
    Asset("ATLAS", "images/map_icons/map_icon.xml"),
}
-- 增加小地图图标
AddMinimapAtlas("images/map_icons/map_icon.xml")
```

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

猜测：初始化mod中的着色器，这个太高端，如果想学，可参见`老王天天写bug`的这个模组 [Sakana & Chinanago](https://steamcommunity.com/sharedfiles/filedetails/?id=2860956005) 的源码，里面有着色器相关的代码

## AddModShadersSortAndEnable(fn)

猜测：对mod着色器排序和启用，详见`AddModShadersInit`的介绍

## AddStategraphPostInit(stategraph, fn)

在状态图(stategraph)初始化之后执行方法fn

fn的参数就是stategraph的实例

## AddComponentPostInit(component, fn)

在组件初始化之后执行fn

fn的参数就是component实例

```lua
-- 免疫掉san光环,sanityaura是san光环组件文件名。
AddComponentPostInit("sanityaura", function(SanityAura)
    --先获取原本的GetAura（为什么是GetAura方法呢？这是分析源码后得到的结果）
    local oldGetAura = SanityAura.GetAura
    --然后给SanityAura定义一个新的GetAura方法，参数和旧的GetAura方法一模一样。
    function SanityAura:GetAura(observer)
        --该方法里先执行旧的GetAura得到掉san或回san的数值
        local val = oldGetAura(self, observer)
        --如果光环内的对象有immune_san标签并且光环是掉san的，那么就返回0，否则返回旧的结果。
        --这样掉san就变成了掉0san，等于不掉了。就达到了免疫掉san光环的功能。
        --当然其他方式掉san还是照样掉。
        if observer:HasTag("immune_san") and val < 0 then
            return 0
        else
            return val
        end
    end
end)
```

## AddPrefabPostInitAny(fn)

在任何预制物初始化后执行。一般不用这个。

## AddPlayerPostInit(fn)

玩家初始化后的修改api

```lua
AddPlayerPostInit(function(inst)
   inst:AddTag("xxx")--给所有玩家都加一个xxx的标签
end)
```

## AddPrefabPostInit(prefab, fn)

在预制体(prefab)初始化之后执行fn

fn参数就是prefab的实例

```lua
-- 让某个物品不能被拆解魔杖分解。greenstaff是拆解魔杖代码。
if TheNet:GetIsServer() then --这段代码只能在服务端运行，所以加了这个判断
    AddPrefabPostInit("greenstaff", function(greenstaff)
        local spell = greenstaff.components.spellcaster.spell
        function NewSpellFn(staff, target)
        -- 当被分解对象代码不是武器1时才走原来的分解逻辑。
            if target.prefab ~= '武器1'  then
                spell(staff, target)
            end
        end
        greenstaff.components.spellcaster:SetSpellFn(NewSpellFn)
    end)
end

```

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

添加mod人物，参数name为人物角色代码，gender为性别，modes不用加，我也不知道它干啥的。

```lua
--增加人物代码为renwu的角色到mod人物列表的里面 性别为女性（MALE（男）, FEMALE（女）, ROBOT（机器人）, NEUTRAL（中性）, PLURAL（双性））
AddModCharacter("renwu", "MALE")
```

## RemoveDefaultCharacter(name)

移除默认的人物

## AddRecipe(name, ingredients, tab, level, placer_or_more_data, min_spacing, nounlock, numtogive, builder_tag, atlas, image, testfn, product, build_mode, build_distance)

添加制作配方，比如小偷背包很难出，写个配方直接制作

参数很多，可以参照 `recipes.lua` 里官方定义的配方来添加自己想要的配方

- name：Prefab名
- ingredients：成分表
- tab：物品栏分类
- level：科技等级
- placer：建筑物放置物，也就是制造建筑时显示的那个图像（实质上也是个Prefab）是否有placer(设置不能被绿杖拆解)
- min_spacing：最小间隔
- nounlock：是否可以离开制作台制作--远古物品只能在制作台上制作。nil则可以离开制作台
- numtogive：制作数量，若填nil则为制作1个。
- builder_tag：制作者需要拥有的Tag（标签），填nil则所有人都可以做--需要的标签（比如女武神的配方需要女武神的自有标签才可以看得到）
- atlas：制作栏图片文档路径
- image：制作栏图片文件名，当名字与Prefab名相同时，可省略。
- testfn：自定义检测函数，需要满足该函数才能制作物品，不常用。

## AddRecipeTab(rec_str, rec_sort, rec_atlas, rec_icon, rec_owner_tag, rec_crafting_station)

制作科技栏

- rec_str: 科技栏名称
- rec_sort: 排序
- rec_atlas: 材质生成的xml文件名
- rec_icon: 材质生成的tex文件名
- rec_owner_tag: 人物专属科技标签(只有拥有这个标签的人物才能有这个科技栏，比如温蒂专属药剂的制作栏)
- rec_crafting_station

```lua
-- 添加专属栏
AddRecipeTab("zhuanshu_tab", 999, "images/tool_man_tab.xml", "tool_man_tab.tex",
             "tool_man", false)--人物必须有tool_man标签才有这个专属栏。
STRINGS.TABS["zhuanshu_tab"] = "工具栏" -- 专属栏名字,鼠标悬浮在科技上后显示的文字。
```

## MakePlacer(name, bank, build, anim, onground, snap, metersnap, scale, fixedcameraoffset, facing, postinit_fn, offset, onfailedplacement)

制作放置物，比如猪房在建造时的阴影

- name：放置物的Prefab名，一般约定为原Prefab名_placer
- bank：放置物的Bank
- build：放置物的Build
- anim：放置物用于播放的动画，一般约定为idle
- onground：取值为true或false，是否设置为紧贴地面。请参考前面AnimState的内容
- snap：取值为true或false，这个参数目前无用，设置为nil即可
- metersnap：取值为true或false，与围墙有关，一般建筑物用不上，设置为nil即可。
- scale：缩放大小
- fixedcameraoffset：固定偏移
- facing：设置有几个面，参考AnimState的内容
- postinit_fn：特殊处理

## LoadPOFile(path, lang)

加载翻译文件

在mod中可以通过 `GLOBAL.LanguageTranslator.defaultlang` 来获取游戏设置的语言，然后进而加载对应语言的翻译文件即可实现mod的多语言支持

## RemapSoundEvent(name, new_name)

人物模组中会用到的api，比如给自己的人物模组添加专属音效，就需要用到这个api来初始化音效，用法参见模组[Whispy, the Agricultured](https://steamcommunity.com/sharedfiles/filedetails/?id=2978133982)

## AddReplicableComponent(name)

初始化复制组件的api，比如你的模组中自定义了一个口渴的组件 `thirsty.lua`，要想将数据同步到客机，就需要用到复制组件，关于复制组件详见：[net&replica](https://atjiu.github.io/dstmod-tutorial/#/net?id=replica%e7%bb%84%e4%bb%b6)

当使用这个api注册了 `thirsty` 组件后，就相当于告诉饥荒，`thirsty`组件有一个复制组件用于同步信息，否则使用 `inst.replica.thirsty`获取客机的`thirsty`组件对象将会是空的

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

客机调用给主机发消息用的，其中的`id_table`格式是 `MOD_RPC[namespace][name]`，也可写成 `GetModRPC(namespace, name)`

## SendModRPCToClient(id_table, ...)

给客机发消息的rpc（用的不多）

## SendModRPCToShard(id_table, ...)

多层世界中用于给其它世界发消息的rpc（用的不多）

## GetModRPC(namespace, name)

获取mod里定义的rpc，看名字可知这个api是获取`AddModRPCHandler`api里定义的rpc

## GetClientModRPC(namespace, name)

获取mod里定义的rpc，看名字可知这个api是获取`AddClientModRPCHandler`api里定义的rpc

## GetShardModRPC(namespace, name)

获取mod里定义的rpc，看名字可知这个api是获取`AddShardModRPCHandler`api里定义的rpc

## SetModHUDFocus(focusid, hasfocus)

猜测：设置模组中界面UI焦点的方法

## AddUserCommand(command_name, data)

添加用户命令，类似于游戏内置的 `c_save()` `c_rollback()` 这种指令

## AddVoteCommand(command_name, init_options_fn, process_result_fn, vote_timeout)

添加投票指令

## ExcludeClothingSymbolForModCharacter(name, symbol)

猜测：直译出来是 `排除Mod人物的服装特征`

## RegisterInventoryItemAtlas(atlas, prefabname)

注册背包内物品贴图的api，当然在食谱等一些UI上显示的贴图都是用这个api注册的贴图，常见的打开食谱不显示模组食物贴图的问题就是因为没用这个api注册一下贴图导致的





















