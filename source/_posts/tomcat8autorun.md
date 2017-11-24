---
title: Windows下配置Tomcat8开机自动启动
categories:
  - 技术
tags:
  - 文章
date: 2017-11-24 14:00:42
---

项目中有时需要更新系统或者其他需求要重启服务器，而用黑框的Tomcat启动，每次重启后还要手动启动服务，很是繁琐，所以做成开机启动还是有必要的。接下来我会分步骤介绍如何进行配置。

<!-- more -->


## 下载Windows版的Tomcat ##
[http://tomcat.apache.org/download-80.cgi](http://tomcat.apache.org/download-80.cgi "下载tomcat8地址")
![](/img/下载Windows版的Tomcat.png)


## 配置Tomcat的自定义配置 ##
> **配置了java环境变量的可以忽略此步骤**

在tomcat的bin目录下创建setenv.bat文件，配置如下代码:

    set JRE_HOME=..\jdk1.8.0_144_x64\jre
    set JAVA_HOME=..\jdk1.8.0_144_x64
    set JSSE_HOME=..\jdk1.8.0_144_x64
    set JVM=..\jdk1.8.0_144_x64\jre\bin\server\jvm.dll
    set TITLE=网站程序-8080端口

当然需要把`jdk`目录放到跟`bin`平级的目录下
![](/img/并配置路径.png)

## 安装服务 ##

在tomcat的bin目录打开dos框，可以用快捷键`Shift`+`右键`在当前目录打开命令窗口
![](/img/在此处打开命令.png)输入如下命令:
> `service.bat install`  

当然install后可以跟你想命名的服务名称。
![](/img/正常配置情况.png)
正常情况出现如上情况就算是成功了。可以在服务里面查看到
![](/img/服务安装成功查看.png)

## 删除服务 ##

如果不小心安装错了或者目录名字换了，想重新安装时，可以先执行删除服务
    
> `service.bat remove`



## 启动服务器 ##

在`bin`目录下打开`tomcat8w.exe`这个文件，其中有启动、停止、重启等操作。
![](/img/配置启动.png)
当然也可以选择启动模式，第一个`Automatic`也就是自启
![](/img/选择配置模式.png)

### 配置无java环境变量的情况 ###

如果没有配置java的环境变量的，则需要在`tomcat8w`的弹出框中的`Java`一栏把`jvm.dll`配置进去就OK，这种方式启动需要 参考上面的步骤`配置Tomcat的自定义配置`参考图如下:
![](/img/配置没有配置java路径的情况.png)

## 查看效果 ##

启动成功如果不出什么意外的情况下，应该就可以看到如下的情况了。

![](/img/正常启动成功.png)