---
title: MVP设计模式-WiKi
categories:
  - 技术
tags:
  - 文章
date: 2017-11-16 16:15:11
---

### 一、介绍
Model–View–Presenter (MVP) 是 Model–View–Controller (MVC)这个体系结构模式的派生，主要用于构建[用户界面][1]。 在MVP中，Presenter承担“中间人”的功能。在MVP中，所有的表示逻辑都被推送给Presenter。
<!-- more -->
### 二、历史
MVP软件模式起源于20世纪90年代早期在Apple，IBM和Hewlett-Packard的合资企业Taligent。MVP是Taligent 基于C ++的CommonPoint环境中应用程序开发的底层编程模型。后来该模式由Taligent迁移至Java，并由Taligent首席技术官[Mike Potel在一篇论文中推广][2]。

在Taligent于1998年停止使用后，Dolphin Smalltalk的Andy Bower和Blair McGlashan改编了MVP模式，构成了他们的Smalltalk用户界面框架的基础。 2006年，微软开始将MVP整合到.NET框架中用于用户界面编程的文档和示例中。

MVP模式的演变和多种变体，包括MVP与其他设计模式（如MVC）的关系在Martin Fowler的文章和Derek Greer的另一篇文章中详细讨论。

 ### 三、概述
MVP是一种用户界面架构模式，设计用于促进自动化 单元测试，并改善表示逻辑中关注的分离：

该model是定义要在用户界面中显示或以其他方式执行的数据的界面。
该view是一个被动接口，用于显示数据（模型）并将用户命令（事件）发送给演示者，以处理该数据。
presenter作用在模型和视图。它从存储库（模型）中检索数据，并将其格式化以便在视图中显示。

### 四、使用
因本人研究的是java


> 未完待续....


  [1]: https://en.wikipedia.org/wiki/User_interface
  [2]: http://www.wildcrest.com/Potel/Portfolio/mvp.pdf
