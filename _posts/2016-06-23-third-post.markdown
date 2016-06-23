---
layout: post
title: macOS初始化的一些建议
date: 2016-06-23 17:32:24.000000000 +08:00
---

### 系统强化
1. Alfred - spotlight的代替工具
![屏幕快照 2016-02-03 17.28.07.png](quiver-image-url/B1FC679148B3EBD1CBE411A107E4D1D1.png)
mac系统的spotlight已经很强大,但Alfred可以扩展成更便捷的工具.
功能包括但不限于!不限于!限于!于!!!
* 应用程序的快捷打开
* 文件搜索
* 系统操作:如关机,休眠,截屏
* 快捷粘贴历史,常用粘贴snippets
* workflow扩展(不够用就可以自己定制,牛逼得...)

2. iTerm2 - 终端的代替工具
系统自带的终端很容易挂掉(至少我的电脑是这样),换了iTerm2后,稳定得不像样了...
系统自带的bash很原生,但用上Oh-my-zsh后,敲命令也是爽得一塌糊涂.(比如tab键补全,git有内置alias)[链接：oh-my-zsh](http://ohmyz.sh)安装命令：
```
$ sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
安装成功后,切换shell的命令：
chsh -s /bin/zsh
```

3. Manico - dock栏快速入口
安装上后,可以把dock栏隐藏,扩大屏幕展示范围,点击dock的操作可以使用快捷键:option+(1~9,Q~R等)

4. BetterZip:
*.rar打包格式的软件系统自带工具无法解压,BetterZip能弥补这个缺点

5. DaisyDisk:
硬盘储存空间扫描

6. pixave:
使用系统的"预览"打开照片无法使用'上一张''下一张'功能,pixave导入文件夹后可以支持上下一张.

7. clean My Mac
系统清理,缓存清理,软件卸载等管理系统的工具

8. cinch
类似win7,鼠标指针到屏幕边缘时自动调整所移动目标的位置大小(如:在顶部扩展至全屏,在左边扩展成左半屏)
![屏幕快照 2016-02-10 15.15.48.png](quiver-image-url/04A97C57301CDB9A53CD2439EB657F01.png)
9. NTFS扩展

### 常用软件:
1. markdown管理神器:Quiver
2. markdown利器:Mou+Day One
3. iExplorer:iphone文件管理工具
4. caffeine:防止系统休眠工具
5. istat menus: 状态栏上显示当前系统使用情况(如:网络上传下载速度,cup,内存使用情况等)
6. unclutter: 状态栏顶部加上'剪切板管理''文件管理''记事本'3个功能
![屏幕快照 2016-02-10 15.24.13.png](quiver-image-url/8D1CED17B7A43D90D257A3E72D20C86A.png)
7. 2Do:日常任务管理工具

### 实用技巧
1. 善用标记:
系统给文件夹可以标记成"红色""黄色""蓝色"等等,但用颜色去记忆这些标记分别代表什么意思,有点难,因此这功能往往被我遗忘.
但是,标记的名字是可以改的,改完以后这样的分类更一目了然了
![善用标记.png](quiver-image-url/EF2A6A539D19EE92F3A7475B1A82B590.png)

2. 触发角操作:
打开'系统偏好设置'-'桌面与屏幕保护程序'-'触发角'
![屏幕快照 2016-02-10 15.38.21.png](quiver-image-url/98D0B5FC850BAEBC61552C72C9506B46.png)

Xcode插件:
1. 代码洁癖者的福音 - ClangFormat-Xcode