## 前言

b站上有好多视频介绍创建专服的，其中绝大部分都用到了一个脚本 `go.sh`，如果你能用 `go.sh` 创建好专服，这篇文章就不用看了

如果你用不好，或者喜欢折腾，不想用人家封装好的脚本，那么就可以看看这篇文章。

这篇文章会从下载steamcmd开始到安装steamcmd，安装dst，启动专服结束，跟着一步一步操作，大概率能成功

## 安装 steamcmd

steam官方给了steamcmd的安装方法，详见 https://developer.valvesoftware.com/wiki/SteamCMD#Manually

我这用的是ubuntu，就以ubuntu为例

steam linux版本是32位的，所以要先安装32位支持库

```bash
$ sudo apt-get install lib32gcc1
```

下载 steamcmd 并解压

```bash
# 先找一个目录，我这用的是Downloads目录
$ cd ~/Downloads
$ curl -sqL "https://steamcdn-a.akamaihd.net/client/installer/steamcmd_linux.tar.gz" | tar zxvf -
```

安装 steamcmd

```bash
$ ./steamcmd.sh
```

## 安装dst

当安装好 steamcmd 后，会进入steamcmd环境，会发现终端的前缀变成了 `Steam>`，这时候要先登录steam，才能进行下载游戏

不需要登录自己的帐号，用匿名登录就可以

```bash
Steam> login anonymous
```

当输出 `Waiting for user info...OK` 时，说明登录成功了

安装dst

```bash
Steam> app_update 343050 validate
```

> 303450是 dontstarve_dedicated_server 的id

安装成功后，可以在 `~/.steam/steam/steamapps/common` 找到，文件夹名是 `Don't Starve Together Dedicated Server`

## 启动专服

进到 `~/.steam/steam/steamapps/common／Don＼'t＼ Starve＼ Together＼ Dedicated＼ Server／bin64` 目录下

运行如下命令启动主世界

```bash
$ ./dontstarve_dedicated_server_nullrenderer_x64 -console -cluster Cluster_1 -shard Master
```

运行如下命令启动洞穴

```bash
$ ./dontstarve_dedicated_server_nullrenderer_x64 -console -cluster Cluster_1 -shard Caves
```

当看见两个终端上都出来 `Sim paused` 是，说明专服启动好了

> 如果出现 `TOKEN xxx` 的异常时，可以参见这篇文章来操作一下 https://steamcommunity.com/workshop/filedetails/discussion/2591636844/3195865512455929225/

专服的存档位置在 `~/.klei/DoNotStarveTogether/`

刚启动脚本写的是 `Cluster_1` 所以 `~/.klei/DoNotStarveTogether/Cluster_1` 就是存档目录了

## 配置专服

最好的方法是在本机上创建一个存档，把世界和mod配置都弄好，生成世界，然后退出，将存档文件夹上传到服务器，直接覆盖掉 Cluster_1 文件夹就可以了

不过有时候换存档需要微调一下配置

打开 `Cluster_1` 文件夹

- `adminlist.txt` 是管理员文件，里面配置的是klei官网的用户id，一行一个
- `whitelist.txt` 白名单
- `blocklist.txt` 黑名单
- `cluster.ini` 专服的配置信息，里面可以设置服务器的名字，密码，人数，游戏类型等等
- `Master` 主世界的存档文件夹
- `Caves` 洞穴的存档文件夹

世界配置在 `Master/leveldataoverride.lua` 和 `Caves/leveldataoverride.lua` 里，它俩不能互相覆盖

模组配置在 `Master/modoverrides.lua` 和 `Caves/modoverrides.lua` 里，它俩可以互相覆盖，只配置一个就行

## 常见问题

### 为什么我mod没有生效?

专服存档里配置了mod，但进游戏发现没有生效，那是还有一个地方没有配置

打开 `~/.steam/steam/steamapps/common／Don＼'t＼ Starve＼ Together＼ Dedicated＼ Server／mods/dedicated_server_mods_setup.lua`

两种方式配置你要开启的mod，一个是使用 `ServerModSetup("mod id")` 另一个是 `ServerModCollectionSetup("合集id")`

这里面把要添加的mod都加上后保存一下，再次启动服务，mod就生效了

### 终端关了服务就停了怎么办?

可以在linux服务器上装一个 `screen`

```bash
$ sudo apt install -y screen
```

创建 screen

```bash
$ screen -S dst-master
```

在里面运行启动专服的命令，然后按 `Ctrl+A+D`

同理洞穴的服务也是一样

打开正在运行的 screen

```bash
screen -r dst-master
```

### 饥荒游戏本体更新了怎么办？

重新运行一下 `./steamcmd.sh` ，匿名登录，再次运行 `app_update 343050 validate` 即可