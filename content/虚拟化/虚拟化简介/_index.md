---
title: "虚拟化简介"
date: 2019-12-15T20:50:40+08:00
draft: true
#pre: "<b>1. </b>"
weight: 5
---

**1.全虚拟化（Full Virtualization)**

全虚拟化也成为原始虚拟化技术，该模型使用虚拟机协调guest操作系统和原始硬件，VMM在guest操作系统和裸硬件之间用于工作协调，一些受保护指令必须由Hypervisor（虚拟机管理程序）来捕获处理。全虚拟化的运行速度要快于硬件模拟，但是性能方面不如裸机。



![img](https://pic3.zhimg.com/80/v2-eb1328e71f229959aaf5d37f10a24786_hd.jpg)



**2.半虚拟化（Para Virtualization）**

半虚拟化是另一种类似于全虚拟化的技术，它使用Hypervisor分享存取底层的硬件，但是它的guest操作系统集成了虚拟化方面的代码。该方法无需重新编译或引起陷阱，因为操作系统自身能够与虚拟进程进行很好的协作。半虚拟化需要guest操作系统做一些修改，使guest操作系统意识到自己是处于虚拟化环境的，但是半虚拟化提供了与原操作系统相近的性能。



![img](https://pic3.zhimg.com/80/v2-f7abc225127422922bf87507e5c8353a_hd.jpg)