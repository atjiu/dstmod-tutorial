> 最近玩游戏尝试去掉了传送mod，结果虫洞没一个在正经位置的，只好删了原版的，自己造几对

没写mod，在控制台里运行四行代码即可造一对

1. 在游戏里打开控制台，走到你想让虫洞生成的地方，输入 `worm1 = c_spawn("wormhole")` 回车，鼠标指向的地方就是虫洞会被生成的地方
2. 再次打开控制台，输入 `worm2 = c_spawn("wormhole")` 回车
3. 还是控制台，输入 `worm1.components.teleporter:Target(worm2)` 回车，将虫洞2关联到1上
4. 控制台，输入 `worm2.components.teleporter:Target(worm1)` 回车，将虫洞1关联到2上

这四步做完后，就可以跳了

注意：

- 直接复制粘贴的话，代码是在本地运行的，需要再次按下 ctrl 键切换回远程。
- 变量前不能加上 `local`
