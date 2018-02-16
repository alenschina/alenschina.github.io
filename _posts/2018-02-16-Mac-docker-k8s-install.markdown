---
layout:     post
title:      "基于 Docker for Mac 的 Kubernetes 环境部署"
subtitle:   " \"Kubernetes in Docker for Mac\""
date:       2018-02-16 20:00:00
author:     "Aaron"
header-img: "img/post-bg-2015.jpg"
catalog: false
tags:
    - Kubernetes 求生手册
---

[憋bb，我要看正文](#build)

## 前言

由于年后会转职到运维岗位，所以趁过年这几天有空，先在自己电脑上折腾一下Kubernetes的环境。

听闻最新的Docker版本已经内置支持k8s，所以（很懒的）Aaron 就决定走捷径啦，直接 Docker for Mac 走起！

<p id = "build"></p>

## 正文

### 1. 下载安装 Dockers

所以首先就是去官网下载最新版本的 Docker，Aaron 下到的是18.02.0。记得要下载 Edge 版本，Stable 版本没有内嵌 k8s。

[Get Docker](https://store.docker.com/editions/community/docker-ce-desktop-mac)

<img class="shadow" src="/img/in-post/dockerk8sinstall/k8s-download.png" width="400">

Docker 到安装就不赘述了，没有什么难度。

### 2. 安装 Kubernetes

其实在 Docker 环境里安装 k8s 本身是很简单很无脑的事情，但是天朝的局域网实在太坑人，安装 k8s需要的资源都被墙了，所以这边先讲一讲科学上网那些事。

Aaron 是用的 vps + shadowsocks，本来用来查查 Google 还挺开心的，没啥问题，但是这次无论怎么配置全局模式，Docker 的代理服务都没有办法通过 shadowsocks 去下载到 k8s 到资源。后来查阅资料才知道 Docker 的代理服务走的是 http 协议，不是 socks，所以需要一些小工具转一层代理服务。这里我们使用 polipo，它可以将 socks 转换成 http 代理。

我们可以直接使用 Homebrew 安装 polipo
```
$ brew install polipo
```

然后生成 socks 到 http 代理, proxyAddress 是你本机的 ip
```
$ polipo socksParentProxy=127.0.0.1:1080 proxyAddress=“192.168.31.109”
```

**注意：启动 polipo 后，请不要关闭它的命令窗口。**

<img class="shadow" src="/img/in-post/dockerk8sinstall/k8s-polipo.png" width="600">

现在，就可以为 Docker 配置 http 代理了。

<img class="shadow" src="/img/in-post/dockerk8sinstall/k8s-proxy.png" width="400">

配置好代理，剩下的就是之前说的无脑安装 kubernetes，这可能会花上十几分钟或者更久的时间，取决于你的网络状态，所有需要一点耐心。
安装完成后，会有 install success 的提示框。并且会显示 kubernetes 正在运行。

<img class="shadow" src="/img/in-post/dockerk8sinstall/k8s-running.png" width="400">


到此为止，我们的 Kubernetes 实际已经安装完成了。我们可以尝试使用 kubectl 控制命令。
```
$ kubectl get namespaces
```


