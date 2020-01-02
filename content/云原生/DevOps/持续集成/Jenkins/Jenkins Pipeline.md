---
title: "Jenkins Pipeline"
date: 2019-12-15T20:50:40+08:00
draft: true
#pre: "<b>1. </b>"
weight: 50
---

https://blog.csdn.net/diantun00/article/details/81075007

Jenkins Pipeline的总体介绍
1.Jenkins Pipeline 的核心概念

Pipeline，简而言之，就是一套运行于Jenkins上的工作流框架，将原本独立运行于单个或者多个节点的任务连接起来，实现单个任务难以完成的复杂流程编排与可视化。

Pipeline是Jenkins2.X的最核心的特性，帮助Jenkins实现从CI到CD与DevOps的转变

Pipeline是一组插件，让Jenkins可以实现持续交付管道的落地和实施。

持续交付管道（CD Pipeline）是将软件从版本控制阶段到交付给用户或客户的完整过程的自动化表现。软件的每一次更改（提交到源代码管理系统）都要经过一个复杂的过程才能被发布。

Pipeline提供了一组可扩展的工具，通过Pipeline Domain Specific Language（DSL）syntax可以达到Pipeline as Code（Jenkinsfile存储在项目的源代码库）的目的。

Stage：阶段，一个Pipeline可以划分成若干个Stage，每个Stage代表一组操作，例如：“Build”，“Test”，“Deploy”。

注意，Stage是一个逻辑分组的概念，可以跨多个Node

Node：节点，一个Node就是一个Jenkins节点，或者是Master，或者是Agent，是执行Step的具体运行环境。

Step：步骤，Step是最基本的操作单元，小到创建一个目录，大到构建一个Docker镜像，由各类Jenklins Plugin提供，例如：sh ‘make’

Pipeline五大特性

代码:Pipeline以代码的形式实现，通常被检入源代码控制，使团队能够编辑、审查和迭代其CD流程。
可持续性：Jenklins重启或者中断后都不会影响Pipeline Job。
停顿：Pipeline可以选择停止并等待任工输入或批准，然后再继续Pipeline运行。
多功能：Pipeline支持现实世界的复杂CD要求，包括fork/join子进程，循环和并行执行工作的能力
可扩展：Pipeline插件支持其DSL的自定义扩展以及与其他插件集成的多个选项。
Pipeline和Freestyle的区别
