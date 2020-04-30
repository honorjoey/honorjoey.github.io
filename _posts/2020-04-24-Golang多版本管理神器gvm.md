---
layout:     post
title:      Golang多版本管理神器gvm
subtitle:   gvm安装和遇到的问题解决 
date:       2020-04-24
author:     HonorJoey
header-img: img/post-bg-algorithm.jpg 
catalog: true
tags:
    - Go
---

### 缘起

最近编译 GRPC 遇到了点坑，编译各种报错，经搜索调研发现用 mod 模式编译时对版本要求1.13及以上，。比如我的 go1.12.4 就无法编译，必须得 go1.13.xx 版本才能编译。为了解决这种尴尬的场景只能再在主机安装个 go1.13.xx 版本，那么有没有什么优雅的方式来实现本机多版本 Golang 版本的管理呢，能很方便的进行不同版本的切换，这也是本文的目的，推荐一款 Go多版本管理神器 gvm，用法类似 Python 的多版本管理工具 pyenv。

### 简介
gvm，即 Go Version Manager，Go 版本管理器，使用 shell 脚本开发，它可以非常轻量的切换 Go 版本。对比其他语言，通常也有类似的工具，如 NodeJS 的 NVM，Python 的 pyenv 等。在使用方法上和 Python 的多版本管理工具 pyenv 非常类似。

其实不借助类似的版本管理工具安装多个版本 Go 也是可以自己手动实现的，做法很简单，就是下载不同的 Golang 安装包，然后放置到独立的目录，使用时将 GOROOT 和 GOPATH 指向对应版本的目录即可完成版本切换。其实 gvm 原理上就是这么做的，只不过通过工具的形式将这些繁杂的手工操作封装起来，使得开发起来更加优雅，不必再为 Go 的安装、版本管理花费更多的心思。下面为 gvm 的工作原理：

![JyEYJx.png](https://s1.ax1x.com/2020/04/25/JyEYJx.png)

### 安装

```bash
bash < <(curl -s -S -L https://raw.githubusercontent.com/moovweb/gvm/master/binscripts/gvm-installer)
```

输出提示：


```
Cloning from https://github.com/moovweb/gvm.git to /Users/apple/.gvm
Created profile for existing install of Go at /usr/local/go
Installed GVM v1.0.22

Please restart your terminal session or to get started right away run
 `source /Users/apple/.gvm/scripts/gvm`
```

安装完成！

重启控制台或执行 `source $HOME/.gvm/scripts/gvm` 即可启用 `gvm`。

提醒下，不同操作系统还需要相应的依赖项要装，具体查看 项目说明 的介绍。

**安装可能遇到的坑**

```bash
$ gvm  install go1.12.10
Downloading Go source...
ERROR: Couldn't download Go source. Check the logs /Users/jim/.gvm/logs/go-download.log
```

根据提示看 log 报错

```
Cloning into '/Users/apple/.gvm/archive/go'...
error: RPC failed; curl 18 transfer closed with outstanding read data remaining
fatal: the remote end hung up unexpectedly
fatal: early EOF
fatal: index-pack failed
```

解决问题：`vim ~/.gvm/scripts/install`
修改 `GO_SOURCE_URL` 变量地址为: 
```
GO_SOURCE_URL=git://github.com/golang/go
```

### 使用

1.列出当前已安装的 Go 版本

```
gvm list
```

2.列出当前可以安装的 Go 版本

```
gvm listall
```

3.安装指定版本的 Go

```
gvm install go1.12.10
```

4.切换到指定的 Go 版本
临时切换

```
gvm use go1.12
```

永久切换

```
gvm use go1.12 --default
```

**使用遇到的问题**

当下次打开终端时，可能会出现`zsh: command not found: gvm`， 可以再在终端运行


```
source ~/.gvm/scripts/gvm
```

避免每次都要如此操作，可以


```
cat ~/.gvm/scripts/gvm
```

然后将上述文件中的内容加入`~/.bashrc`或`~/.profile`，然后使修改生效

```
cat ~/.gvm/scripts/gvm >> ~/.bashrc
source ~/.bashrc
```

或


```
cat ~/.gvm/scripts/gvm >> ~/.profile
source ~/.profile
```

### 包环境管理

gvm 除了 Go 版本的管理，还可以管理包环境，相关命令是 `pkgenv` 和 `pkgset`。如果没使用包依赖管理工具，它也是挺方便的。
演示个例子，假设我们要创建一个新的项目 blog，可提前创建相应的环境。

```
$ gvm pkgset create blog  # 创建
$ gvm pkgset use blog     # 启用
```

复制代码现在，我们通过 go get 安装的包都会默认在 blog 环境下。基于的原理是 go get 默认会把安装的放在 GOPATH 中的第一个目录下。

好了，就介绍这么多吧。有兴趣的朋友可以再研究研究。毕竟在有了 go mod 之后，这个功能以后是基本不会用了。

### gvm 目录结构

gvm 是 shell 编写，默认是安装在 `$HOME/.gvm/` 目录下。查看下它的目录结构会有助我们了解它的实现。
其中几个主要的目录，如下：

```
archive             # go 源码
bin                 # gvm 可执行文件
environments        # 不同环境的环境变量配置
scripts             # gvm 的子命令脚本
logs                # 日志信息
pkgsets             # 每个独立环境 gopath 所在路径
```

复制代码在研究了 gvm 的实现后，我们会发现，这一套思路其实也适用于其他很多工具的版本管理。如果之后再遇到同样的需求，即使我们没有现成的工具，自己实现一套也是可以的。
总结
本文从我的需求出发，引出了如何灵活地进行管理 Go 版本的话题。
以往的经验告诉我，既然其他语言都有工具实现这样的需求，Go 也应该有。搜索了下，找到了 gvm。虽说我在使用它的时候，发现了一些 bug 与体验不好的地方，但总体而言，已经足够满足我的需求。


参考资料
[https://juejin.im/post/5d848b66f265da03a7160e89](https://juejin.im/post/5d848b66f265da03a7160e89)
