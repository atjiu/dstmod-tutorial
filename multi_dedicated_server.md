## 前言

这篇介绍一下多层世界搭建的方法

在搭建多层之前首先也要将steamcmd安装好，可参见上一篇 [SteamCMD创建专服](https://atjiu.github.io/dstmod-tutorial/#/steamcmd_dedicated_server)

这篇就以三台服务器为例，两个地面世界，一个洞穴世界

| 服务器 | ip              | 身份         | 公网ip |
|-----|-----------------|------------|------|
| 清风服 | 123.123.123.123 | 主世界(地面) | 需要   |
| 碎梦服 | 123.123.123.124 | 从世界(地面) | 不需要 |
| 火鸡服 | 123.123.123.125 | 从世界(洞穴) | 不需要 |

需要借用的mod：[多层世界选择器](https://steamcommunity.com/sharedfiles/filedetails/?id=1438233888)

## 主世界

首先看一下存档结构

![](images/20221119091751.png)

其中被选中的两个文件就是开多层要修改配置的文件

首先 `cluster.ini`

主要修改的就是

`bind_ip` 和 `master_ip`

- `bind_ip` 写死就是 `0.0.0.0`
- `master_ip` 就是当前服务器的公网ip

完整配置如下

```ini
[GAMEPLAY]
game_mode = endless
max_players = 11
pvp = false
pause_when_empty = true


[NETWORK]
lan_only_cluster = false
cluster_password =
cluster_description = 主世界：清风抚月，从地面：碎梦樱雪，从洞穴：火鸡正餐
cluster_name = 清风服
offline_cluster = false
cluster_language = zh


[MISC]
console_enabled = true


[SHARD]
shard_enabled = true
bind_ip = 0.0.0.0
master_ip = 123.123.123.123
master_port = 10888
cluster_key = defaultPass
```

其次是 `server.ini`

主要修改的就是

- `server_port` 服务的端口，这个保持默认就好
- 
