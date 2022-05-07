---
title: K8S 学习记录
date: 2022-05-07 22:32:36
author: dFaruiy
tags: K8S
top: true
toc: true
categories: K8S
keywords: K8S 5G
---

# K8S 

## K8S 网络
```
网络名字空间(network namespace)是Linux内核提供的一项非常重要的功能，它是网络虚拟化技术的基础，也是Kubernetes和以Docker为代表的容器技术在实现它们各自的网络时所依赖的基础。所以，要理解Kubernetes的网络工作原理，首先要从network namespace入手。

在Linux内核里，每个network namespace都有它自己的网络设置，比如像：网络接口(network interfaces)，路由表(routing tables)等。我们利用network namespace，可以把不同的网络设置彼此隔离开来。当运行多个Docker容器的时候，Docker会在每个容器内部创建相应的network namespace，从而实现不同容器之间的网络隔离。

但是光有隔离还不行，因为容器还要和外界进行网络联通，所以除了network namespace以外，另一个重要的概念是网桥(network bridge)。它是由Linux内核提供的一种链路层设备，用于在不同网段之间转发数据包。Docker就是利用网桥来实现容器和外界之间的通信的。默认情况下，Docker服务会在它所在的机器上创建一个名为docker0的网桥。下面我们就先从docker0入手，通过一系列动手实验，了解网桥在容器之间进行通信时所起的作用。
```

### Network bridge

### Network namespace