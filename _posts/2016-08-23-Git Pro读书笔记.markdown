---
layout: post
title: Git Pro读书笔记
date: 2016-08-23 21:29:14.000000000 +08:00
---

> [Git book 地址](https://git-scm.com/book/zh/v2)

##基础概念
  1. 版本控制：版本控制是一种记录一个或若干文件内容变化，以便将来查阅特定版本修订情况的系统。
  2. 集中化的版本控制系统（SVN）：有一个单一的集中管理的服务器，保存所有文件的修订版本，而协同工作的人们都通过客户端连到这台服务器，取出最新的文件或者提交更新。
这么做最显而易见的缺点是中央服务器的单点故障。如果宕机一小时，那么在这一小时内，谁都无法提交更新，也就无法协同工作。如果中心数据库所在的磁盘发生损坏，又没有做恰当备份，毫无疑问你将丢失所有数据——包括项目的整个变更历史，只剩下人们在各自机器上保留的单独快照。本地版本控制系统也存在类似问题，只要整个项目的历史记录被保存在单一位置，就有丢失所有历史更新记录的风险。
  3. 分布式版本控制系统：客户端并不只提取最新版本的文件快照，而是把代码仓库完整地镜像下来。 这么一来，任何一处协同工作用的服务器发生故障，事后都可以用任何一个镜像出来的本地仓库恢复。 因为每一次的克隆操作，实际上都是一次对代码仓库的完整备份。
  4. Git 有三种状态，你的文件可能处于其中之一：已提交（committed）、已修改（modified）和已暂存（staged）。 
  ![工作目录、暂存区域以及 Git 仓库](https://git-scm.com/book/en/v2/book/01-introduction/images/areas.png)
    - 已提交表示数据已经安全的保存在本地数据库中。 
    - 已修改表示修改了文件，但还没保存到数据库中。
    - 已暂存表示对一个已修改文件的当前版本做了标记，使之包含在下次提交的快照中。

##安装Git
  1. 在Mac上安装
    1. Mavericks （10.9） 或更高版本的系统中，在 Terminal 里尝试首次运行 git 命令即可。 如果没有安装过命令行开发者工具，将会提示你安装。
    2. 如果你想安装更新的版本，可以使用二进制安装程序。 官方维护的 OSX Git 安装程序可以在 Git 官方网站下载，网址为 http://git-scm.com/download/mac。
  2. 在 Windows 上安装
    1. 在 Windows 上安装 Git 也有几种安装方法。 官方版本可以在 Git 官方网站下载。 打开 http://git-scm.com/download/win，下载会自动开始。 要注意这是一个名为 Git for Windows的项目（也叫做 msysGit），和 Git 是分别独立的项目；更多信息请访问 http://msysgit.github.io/。
    2. 另一个简单的方法是安装 GitHub for Windows。 该安装程序包含图形化和命令行版本的 Git。 它也能支持 Powershell，提供了稳定的凭证缓存和健全的 CRLF 设置。 稍后我们会对这方面有更多了解，现在只要一句话就够了，这些都是你所需要的。 你可以在 GitHub for Windows 网站下载，网址为 http://windows.github.com。
  3. 在 Linux 上安装
如果你想在 Linux 上用二进制安装程序来安装 Git，可以使用发行版包含的基础软件包管理工具来安装。 如果以 Fedora 上为例，你可以使用 yum：
  `$ sudo yum install git`
如果你在基于 Debian 的发行版上，请尝试用 apt-get：
  `$ sudo apt-get install git`
要了解更多选择，Git 官方网站上有在各种 Unix 风格的系统上安装步骤，网址为 http://git-scm.com/download/linux。

  4. 从源码安装
    如果你想从源码安装 Git，需要安装 Git 依赖的库：curl、zlib、openssl、expat，还有libiconv。 如果你的系统上有 yum （如 Fedora）或者 apt-get（如基于 Debian 的系统），可以使用以下命令之一来安装最小化的依赖包来编译和安装 Git 的二进制版：
```
  $ sudo yum install curl-devel expat-devel gettext-devel \
    openssl-devel zlib-devel
  $ sudo apt-get install libcurl4-gnutls-dev libexpat1-dev gettext \
    libz-dev libssl-dev
    
    //为了能够添加更多格式的文档（如 doc, html, info），你需要安装以下的依赖包：
    
  $ sudo yum install asciidoc xmlto docbook2x
  $ sudo apt-get install asciidoc xmlto docbook2x
```
    当你安装好所有的必要依赖，你可以继续从几个地方来取得最新发布版本的 tar 包。 你可以从 Kernel.org 网站获取，网址为 https://www.kernel.org/pub/software/scm/git，或从 GitHub 网站上的镜像来获得，网址为 https://github.com/git/git/releases。 通常在 GitHub 上的是最新版本，但 kernel.org 上包含有文件下载签名，如果你想验证下载正确性的话会用到。

  接着，编译并安装：
  ```
  $ tar -zxf git-2.0.0.tar.gz
  $ cd git-2.0.0
  $ make configure
  $ ./configure --prefix=/usr
  $ make all doc info
  $ sudo make install install-doc install-html install-info
  
    //完成后，你可以使用 Git 来获取 Git 的升级：
  $ git clone git://git.kernel.org/pub/scm/git/git.git
  ```
  
##Git配置
Git 自带一个 git config 的工具来帮助设置控制 Git 外观和行为的配置变量。 这些变量存储在三个不同的位置：
  1. /etc/gitconfig 文件: 包含系统上每一个用户及他们仓库的通用配置。 如果使用带有 --system 选项的 git config 时，它会从此文件读写配置变量。
  2. ~/.gitconfig 或 ~/.config/git/config 或 对应项目的.git/config  对应的是：系统、全局、本地
  3. 当前使用仓库的 Git 目录中的 config 文件（就是 .git/config）：针对该仓库。

用户信息:
当安装完 Git 应该做的第一件事就是设置你的用户名称与邮件地址。 这样做很重要，因为每一个 Git 的提交都会使用这些信息，并且它会写入到你的每一次提交中，
```
$ git config (--global/local) user.name "Sheldon"
$ git config (--global/local) user.email "lishaoyouqy@126.com"
```

##获取 Git 仓库
有两种取得 Git 项目仓库的方法。 第一种是在现有项目或目录下导入所有文件到 Git 中； 第二种是从一个服务器克隆一个现有的 Git 仓库。
1. 在现有目录中初始化仓库，并与远程仓库关联
```
git init
git remote add origin [远程仓库地址]
git add .
git commit -m 'initial project version'
git push -u origin master
```
2. 克隆现有的仓库
当你执行 git clone 命令的时候，默认配置下远程 Git 仓库中的每一个文件的每一个版本都将被拉取下来。
`git clone [url] `
Git 支持多种数据传输协议。 如 https:// 协议，不过你也可以使用 git:// 协议或者使用 SSH 传输协议，比如 user@server:path/to/repo.git 

##Git基础
1. 文件状态
  - 已跟踪：被纳入了版本控制的文件，在上一次快照中有它们的记录，在工作一段时间后，它们的状态可能处于未修改，已修改或已放入暂存区。
  - 未跟踪：除已跟踪文件以外的所有其它文件都属于未跟踪文件，它们既不存在于上次快照的记录中，也没有放入暂存区。

![文件状态](https://git-scm.com/book/en/v2/book/02-git-basics/images/lifecycle.png)

2. 忽略文件(.gitignore)
文件 .gitignore 的格式规范如下：
  - 所有空行或者以 ＃ 开头的行都会被 Git 忽略。
  - 可以使用标准的 glob 模式匹配。
  - 匹配模式可以以（/）开头防止递归。
  - 匹配模式可以以（/）结尾指定目录。
  - 要忽略指定模式以外的文件或目录，可以在模式前加上惊叹号（!）取反。

所谓的 glob 模式是指 shell 所使用的简化了的正则表达式。 星号匹配零个或多个任意字符；[abc] 匹配任何一个列在方括号中的字符（这个例子要么匹配一个 a，要么匹配一个 b，要么匹配一个 c）；问号（?）只匹配一个任意字符；如果在方括号中使用短划线分隔两个字符，表示所有在这两个字符范围内的都可以匹配（比如 [0-9] 表示匹配所有 0 到 9 的数字）。 使用两个星号表示匹配任意中间目录，比如a/**/z 可以匹配 a/z, a/b/z 或 a/b/c/z等。

TIP
GitHub 有一个十分详细的针对数十种项目及语言的 .gitignore 文件模板，你可以在[https://www.gitignore.io/](https://www.gitignore.io/) 找到它.

3. 查看已暂存和未暂存的修改
`git diff`此命令比较的是工作目录中当前文件和暂存区域快照之间的差异， 也就是修改之后还没有暂存起来的变化内容。
`git diff --cached`查看已暂存的将要添加到下次提交里的内容

4. 提交更新
`git commit`这种方式会启动文本编辑器以便输入本次提交的说明。 (默认会启用 shell 的环境变量 $EDITOR 所指定的软件，一般都是 vim 或 emacs。当然也可以按照 起步 介绍的方式，使用 git config --global core.editor 命令设定你喜欢的编辑软件。）

5. 移除文件
`git rm`从 Git 中移除某个文件，就必须要从已跟踪文件清单中移除（确切地说，是从暂存区域移除），然后提交。并连带从工作目录中删除指定的文件，这样以后就不会出现在未跟踪文件清单中了。如果删除之前修改过并且已经放到暂存区域的话，则必须要用强制删除选项 -f（译注：即 force 的首字母）。 

*忘记添加 .gitignore 文件，把不需要的文件添加到暂存区时*
`$ git rm --cached xxxxx`

6. 远程仓库
`git fetch`这个命令会访问远程仓库，从中拉取所有你还没有的数据。 执行完成后，你将会拥有那个远程仓库中所有分支的引用，可以随时合并或查看。
注意：命令会将数据拉取到你的本地仓库 - 它并不会自动合并或修改你当前的工作
git pull 在大多数情况下它的含义是一个 git fetch 紧接着一个 git merge 命令

git push [remote-name] [branch-name]
删除分支：git push origin --delete serverfix

7. 合并分支
`git merge`做了合并，若合并的分支有冲突的话，但是没有自动地创建一个新的合并提交。
此命令会把两个分支的最新快照（C3 和 C4）以及二者最近的共同祖先（C2）进行三方合并，合并的结果是生成一个新的快照（并提交）。
![merge](https://git-scm.com/book/en/v2/book/03-git-branching/images/basic-merging-2.png)

`git rebase`在 C4 中引入的补丁和修改，然后在 C3 的基础上再应用一次。此命令将提交到某一分支上的所有修改都移至另一分支上
![rebase](https://git-scm.com/book/en/v2/book/03-git-branching/images/basic-rebase-2.png)
```
$ git checkout experiment
$ git rebase master
First, rewinding head to replay your work on top of it...
Applying: added staged command
```
它的原理是首先找到这两个分支（即当前分支 experiment、变基操作的目标基底分支 master）的最近共同祖先 C2，然后对比当前分支相对于该祖先的历次提交，提取相应的修改并存为临时文件，然后将当前分支指向目标基底 C3, 最后以此将之前另存为临时文件的修改依序应用。（译注：写明了 commit id，以便理解，下同）
![将 C4 中的修改变基到 C3 上](https://git-scm.com/book/en/v2/book/03-git-branching/images/basic-rebase-3.png)

现在回到 master 分支，进行一次快进合并。
```
$ git checkout master
$ git merge experiment
```
![master 分支的快进合并](https://git-scm.com/book/en/v2/book/03-git-branching/images/basic-rebase-4.png)

*这两种整合方法的最终结果没有任何区别，但是rebase使得提交历史更加整洁。你在查看一个经过变基的分支的历史记录时会发现，尽管实际的开发工作是并行的，但它们看上去就像是先后串行的一样，提交历史是一条直线没有分叉。*
https://git-scm.com/book/zh/v2/Git-%E5%88%86%E6%94%AF-%E5%8F%98%E5%9F%BA

`git merge --abort` 尝试回复到运行前的状态


8. 查找
“从提交历史或者工作目录中查找一个字符串或者正则表达式”
`git grep -n  xxxx`

“使用 --count 选项来使 Git 输出概述的信息，仅仅包括哪些文件包含匹配以及每个文件包含了多少个匹配”

日志搜索
`git log -S azureColor`  搜索出更改azureColor的commit

9. 清除
“filter-branch 是一个可能会用来擦洗整个提交历史的工具。”
`$ git filter-branch --tree-filter 'rm -f passwords.txt' HEAD`


###something else
 - [Git的环境搭建与初步使用](http://www.jianshu.com/p/bd3f03be0495)
 - [Git命令进阶篇](http://www.jianshu.com/p/d0848698ad58)


