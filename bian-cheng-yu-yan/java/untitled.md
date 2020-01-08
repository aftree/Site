# 关于Java

[java发展史](https://mp.weixin.qq.com/s?__biz=MzAxOTc0NzExNg==&mid=2665513103&idx=1&sn=56d832072a92e3f41f5e6796676993e0&scene=21#wechat_redirect)

概念 Java EE，Java 平台企业版（Java Platform Enterprise Edition），之前称为Java 2 Platform, Enterprise Edition \(J2EE\)，2018年3月更名为 Jakarta EE\(这个名称应该还没有得到群众认可\)。狭义的 Java EE 是 Sun 公司为企业级应用推出的标准平台，用来开发B/S架构软件，可以说是一个框架，也可以说是一种规范。

广义的 Java EE 包含各种框架，其中最重要的就是 Spring 全家桶。Spring 诞生之初是为了改进 Java EE 开发的体验，后来逐渐成为了 Java Web 开发的实际标准。后面的文章里，会对 Spring 进行进一步的说明。

JavaEE 与 JavaSE 的区别与联系 JavaEE 是在 JavaSE 的基础上构建的，是对 JavaSE 的扩展，增加了一些更加便捷的应用框架。

除了 EE 和 SE，还有为移动端而生的 JavaME，但目前应用不算广泛。三者的关系可以用下图概括：

JavaEE主要技术 JavaEE 号称有十三种核心技术。它们分别是：JDBC、JNDI、EJB、RMI、Servlet、JSP、XML、JMS、Java IDL、JTS、JTA、JavaMail和JAF。

简单介绍下需要重点关注的技术。

JDBC Java 数据库连接，（Java Database Connectivity，JDBC）是 Java 语言中用来规范客户端程序如何来访问数据库的应用程序接口，提供了诸如查询和更新数据库中数据的方法。

JNDI Java 命名和目录接口（Java Naming and Directory Interface，JNDI），是 Java 的一个目录服务应用程序界面（API），它提供一个目录系统，并将服务名称与对象关联起来，从而使得开发人员在开发过程中可以使用名称来访问对象。

EJB 企业级 JavaBean（Enterprise JavaBean, EJB）是一个用来构筑企业级应用的服务器端可被管理组件。不过这个东西在 Spring 问世后基本凉凉了，知道是什么就行。

Servlet Servlet（Server Applet），是用 Java 编写的服务器端程序。其主要功能在于交互式地浏览和修改数据，生成动态 Web 内容。

狭义的 Servlet 是指 Java 语言实现的一个接口，广义的 Servlet 是指任何实现了这个 Servlet 接口的类，一般情况下，人们将 Servlet 理解为后者。

JSP JSP（全称JavaServer Pages）是由 Sun 公司主导创建的一种动态网页技术标准。JSP 部署于网络服务器上，可以响应客户端发送的请求，并根据请求内容动态地生成 HTML、XML 或其他格式文档的 Web 网页，然后返回给请求者。

JavaEE框架 JavaEE 拥有广泛市场的原因之一就是可以使用多种框架来使开发变得简单。对于框架的选择多种多样，目前比较常见的框架组合有 SSH和SSM。在后面的章节中会作详细介绍。另外Spring本身也提供了多种层次的框架供选择，可以到Spring官网了解详情。

Spring： [https://spring.io/](https://spring.io/)

SSH Structs + Spring + Hibernate

SSM Spring +SpringMVC + MyBatis

JavaEE 学习路径 一般来讲，初学者应该遵循以下路径

Servlet -&gt; JSP -&gt; Spring -&gt; 组合框架

Servlet 和 JSP 在日后的开发中虽然很少直接应用，但却是各种框架的基础，应该放在开始去了解。这两部分也并不难，相信经过了 JavaSE 的洗礼，只需要进行短期的学习，知道它们都是什么，就可以投入实践中了。

至于上面提到的其它内容，在实践中遇到了再去了解也不妨，现在也只能在一些老旧的应用中看到这些东西了。JDBC 可以稍微重点关注一下，其它的知道有这么个词就差不多啦。

推荐一个学习 Java 的良心网站，里面有 Java 全栈知识，在每个阶段都设置了练习内容，谁用谁知道——

「How2J 的 Java 教程」

这个系列的文章我会逐步更新，主要是希望帮助大家对 Java Web 开发建立一个大概的认识，感兴趣的小伙伴可以戳下面的连接——

JavaEE 从入门到放弃（二）：一文读懂 Servlet JavaEE 从入门到放弃（三）：为什么要用 JSP？ JavaEE 从入门到放弃（四）：MVC 模式 JavaEE 从入门到放弃（五）：如何理解 Spring JavaEE 从入门到放弃（六）：框架的选择 （持续更新中）

另外我用 Vue + Spring Boot 做了一个前后端分离的小项目，并做了一下实践教程，有兴趣的小伙伴可以戳以下链接——

Vue + Spring Boot 项目实战

在学习中遇到问题，或者想与我交流学习经验，可以给我发送邮件，地址是：evan\_nightly@163.com

有兴趣听我吹牛扯皮的同学可以关注我的公号，只讲故事，不谈技术。

———————————————— 版权声明：本文为CSDN博主「neuf\_soleil」的原创文章，遵循 CC 4.0 BY-SA 版权协议，转载请附上原文出处链接及本声明。 原文链接：[https://blog.csdn.net/Neuf\_Soleil/article/details/80962686](https://blog.csdn.net/Neuf_Soleil/article/details/80962686)

1. 引言

听说过 Java EE 吗？那关于 Java 2 EE 、J2EE 或者现在的 Jakarta EE，你又是否有所耳闻呢？实际上，这些各异的术语描述的都是相同的东西：由 Java SE 扩展出的一系列企业规范。

在本篇短文中，我们将讲述 Java EE 的发展史。

1. 历史

在 Java 的第一个版本中，Java 企业扩展还只是核心 JDK 的一部分（译者注：核心 JDK 通常指 Java SE） 。然而到了 1999 年，Java 企业扩展已经被剥离出 Java SE，成为了 Java 2 的一部分，这也意味着 J2EE，或者说Java 2 平台企业版（Java 2 Platform Enterprise Edition）的诞生。J2EE 这个称呼一直维持到2006年。

2006 年发布的 Java 5，J2EE 被重命名为 Java EE，或者说 Java 平台企业版（Java Platform Enterprise Edition）。这次改名后的称呼一直延续到 了 2017 年的 9 月。那年发生了一件重大的事，Oracle 决定将 Java EE 捐赠给 Eclipse 基金会（但 Java仍然属于 Oracle）。

1. 转变原因

事实上，因为 Oracle 拥有 “Java” 商标权。按照法律要求，Eclipse 基金会需要对 Java EE 进行更名。

经过社区的投票选择，Java EE 被更名为 Jakarta EE。从某种意义上来说，Java EE 依然叫 JEE。（译者注： 将 Java EE 首字母缩写也可简称为 JEE）。

不过这仍然是个正在进行的故事，还未完全尘埃落定。

举个例子，虽然 Oracle 开源了 Java 源代码，但却并未开源所有的文档。关于这个问题，因为涉及到一些法律事宜，导致开源一些文档（例如与 JMS、EJB相关的）非常棘手，至今仍有许多争议。

现在还无法得知新的 Eclipse 基金会文档是否能够参考原文档。

同样令人奇怪的是 Eclipse 基金会不能使用 javax 的命名空间来创建新的 Java 包，但是可以在现有包的下面创建新的类和子类。

转变阶段也意味着对 Jakarta EE 添加规范的新流程。为了更好地理解这一点，让我们快速看一下 Oracle 添加规范的流程以及 Eclipse 基金会相应做出的改变。

4.未来

在过去，为了将一个特性添加进 “EE”（译者注：原文作者为了避免 Jakarta EE 历史名字的混杂性，使用“EE”来代指全部的版本，下同），我们需要 3 样东西 ：规范、参考实现与测试。社区里的任何人都可以提交这 3 样东西，之后执行委员会将会决定何时将它们整合进 Java 语言中。

为了更好地理解添加规范的旧流程，让我们进一步了解 JSRs、Glassfish 和 TCK是什么 ，以及它们是如何整合新特性的。

我们也将一睹在未来可以预期的事。

4.1 JCP 以及现在的 EFSP

在过去，产生EE 新特性的流程被称为 JCP（Java Community Process）。

Java SE 现在仍然采用 JCP。但是由于 EE 的所有权已经从 Oracle 移交至 Eclipse 基金会，EE 已经有了新的流程，这个流程是Eclipse 开发流程\([https://www.eclipse.org/projects/dev\_process\)的扩展，与](https://www.eclipse.org/projects/dev_process%29的扩展，与) Java SE 的流程互不干扰，我们称之为 EFSP（Eclipse Foundation Specification Process）。

尽管 JCP 与 EFSP 之间有一些大的差异，但大都围绕着“透明、公开、集体负责和供应商中立”这几条准则展开。例如，EFSP 的组织者设想的合作工作团体是供应商中立的，认证流程是自助服务的，组织的运作与管理是精英化的。

4.2 JSRs

在 JCP 中，为 EE 添加新特性的第一步是创建一个 JSR（Java Specification Request）。JSR 有点类似于一个 EE 特性的接口。JCP 执行委员会会核准一个完整的 JSR，然后相应的 JSR 贡献者会编写代码，使其在社区内生效。

JSR-339 或者 JAX-RS 对于阐述上面的流程是一个好例子。JAX-RS 最初于 2011 年提出，在2012年被 JCP 批准，最终在 2013 年得以发布。

虽然在讨论规范时，社区可以随时加入进来，但时间表明，一个实现优先（ implementation-first）的方式更利于创建能被广泛接受的特性与 API。所谓的实现优先，类似于JSR 310中的 java.time 和 Joda Time这个例子（译者注：JDK 1.8 之前 Java 关于时间的 API 很不如人意，使用广泛的是 Joda-Tme）。

因此，EFSP（Eclipse Foundation Specification Process）在其设定的目标中阐述了这个观点：“EFSP 将基于是否先进行了动手实验和编码，来判断其是否值得添加进规范中。

4.3 GlassFish

此外，JSR 作为 JCP 的一部分，需要一个参考实现。这有点类似于实现接口的类。对于那些想要创建自己的规范实现的群体，比如说兼容库的开发人员或者其他组织，参考实现都可以给予帮助。

对于 Java EE 特性，JCP 使用 Glassfish 作为参考实现。

虽然 Glassfish 的中心化简化了实现者的探索过程，但是这种中心化也要求更多的管理，并且倾向于偏袒某个供应商。

因此，EFSP 不要求参考实现，而只要求兼容的实现。简而言之，这种微妙的变化使得类似 Glassfish 之类的中心体系结构内的实现，不会被基金会无缘由地首选。

4.4 TCK

最后，JCP 要求 EE 特性需通过 TCK（Technology Compatibility Kit）的测试。

TCK 是一组验证特定 EE JSR 的测试。简而言之，为了遵循 Java EE，应用服务器需要实现所有 JSR， 并通过特定 TCK 上的所有测试。

与前述类似，Oracle虽然开源了TCK和EE jsr的源代码（译者注：但并没有开源相应的文档）。当然，未来所有的文档和 TCK 都将是开源的。

1. 总结

这些年来，Java EE 无疑前进了许多。很高兴看到它继续变化与变好。

前方之路充满坎坷，希望 Java 的转变能够平滑些。

———————————————— 版权声明：本文为CSDN博主「码农翻身」的原创文章，遵循 CC 4.0 BY-SA 版权协议，转载请附上原文出处链接及本声明。 原文链接：[https://blog.csdn.net/coderising/article/details/100021723](https://blog.csdn.net/coderising/article/details/100021723)

1、Java分类

Java分为JavaSE（Java标准版）、J2EE（Java企业版）和JavaME（Java微型版）；

JavaSE（Java Standard Edition），一般用来开发桌面应用程序，但是在开发桌面应用程序上相对VB，Delphi，VC++并没有什么优势。

JavaEE（Java Enterprise Edition），一般用于开发JavaWeb应用程序，初级的一般是用JSP（Java Server Pages）+servlet+JavaBean来开发的，对大型的网站一般是使用框架来开发的，例如struts，hibernate，spring。

JavaME（Java Micro Edition），诺基亚手机上运行的Java程序就是用这个版本开发的程序。

2、JavaWeb与J2EE的区别

J2EE是Java的企业应用开发，涵盖了B/S和C（Client客户端）/S（Server服务器），注重的是结构和框架，我们所熟知的struts2、hibernate和spring即ssh就是j2ee的一些基本框架。

JavaWeb是指Java在B（Browser浏览器）/S（Server服务器）方面的开发，做的是网络应用；

