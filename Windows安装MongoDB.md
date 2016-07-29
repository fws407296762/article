##概述

使用本教程在Windows系统上安装`MongoDB`

>支持的平台：
从MongoDB2.2开始，MongoDB就不支持Windows XP。请在最新的Windows版本上使用最新的MongoDB版本

>重要事项：
如果你在 Windows Server 2008 R2 or Windows 7 运行，请安装[为修复Window上内存映射文件问题](http://support.microsoft.com/kb/2731284)

##安装MongoDB

####选择你需要的MongoDB版本

Windows中有3个MongoDB版本：

**Windows Server 2008 R2 edition版**（即 2008R2仅仅只能运行在Windows Server 2008 R2、Windows 7 64-bit和新的Windows版本。这个版本可以在最新的Window平台运行最好的功能，但是不能在老版本运行。

**Windows 64位版** 可以运行在高于Windows XP的其它Windows64位版本上，包括Windows Server 2008 R2和Windows 7 64-bit。

**Windows 32位版**  可以运行在高于Windows XP的其它Windows32位版本上。32位版的MongoDB仅仅只能在老系统上进行测试和开发。并且只支持容量小于2GB的数据库。

在命令行中输入下面的命令，可以找到你正在运行的系统版本

> wmic os get osarchitecture

####下载Windows版MongoDB

可以在 [MongoDB下载页面](http://www.mongodb.org/downloads?_ga=1.205039760.1267742375.1419469730) 下载最新发布的MongoDB。保证为你的Window系统下载正确的MongoDB版本。64位的MongoDB不能运行在32位Windows上。

####安装下载文件

在Windows的资源管理器中，找到你已经下载的MongoDB mis文件，一般在`Downloads`文件夹中就可以找到。双击这个`mis`文件。安装过程就会出现在的你的屏幕上。

####将MongoDB目录移动到其它地方

为了移动MongoDB的目录，你必须用管理者身份打开命令行。例如移动目录到`C:\mongodb`:

选择 开始 > 所有程序 > 附件

在弹窗的目录中，右键单机`命令提示符`,选择`以管理员身份运行`。

执行下面的命令：

> cd \
> move C:\mongodb-win32-* C:\mongodb  也就是move 旧路径 新路径

MongoDB是独立的，没有依赖任何系统。你可以在你选择的目录中运行MongoDB。你可以在任何目录中安装MongoDB(例如：D:\test\mongodb)

##运行MongoDB

>警告：
不要让MongoDB显示在公共网络，除非是在已经授权的安全模式下。MongoDB被设计运行在可信赖的环境中，默认数据库是不显示“安全模式”

####配置MongoDB环境

MongoDB 需要一个[数据目录](http://docs.mongodb.org/manual/reference/glossary/#term-dbpath)用来存储所有的数据。MongoDB的默认数据路径是`\data\db`。可以在命令行中执行下面的命令去创建这个目录

> md \data\db

你可以在 [mongod.exe](http://docs.mongodb.org/manual/reference/program/mongod.exe/#bin.mongod.exe) 使用 *--dbpath*选项为数据文件指定一个替换的路径。例如：

> C:\mongodb\bin\mongod.exe --dbpath d:\test\mongodb\data

如果你的路径有空格，用双引号把路径包起来，例如：

>C:\mongodb\bin\mongod.exe --dbpath "d:\test\mongo db data"

####开始MongoDB

在命令提示符中运行 `mongod.exe`来开启MongoDB：

>C:\Program Files\MongoDB\bin\mongod.exe

这会启动主MongoDB数据库进程。` waiting for connections`消息在控制台中出现，就表示 `mongod.exe`进程运行成功了。

根据你的操作系统的安全等级，Windows有可能会有一个**安全性警告**弹窗出现，阻止来自`C:\Program Files\MongoDB\bin\mongod.exe`的网络功能。所有的用户都应该选择`私人网络，如家庭或者工作网络`，然后点击`允许通过`。关于MongoDB其它安全信息，可以去看看[安全文档](http://docs.mongodb.org/manual/core/security/)。

####连接MongoDB

另外打开一个命令提示符界面，通过`mongo.exe`命令去连接MongoDB.当正在连接的时候，指定的数据目录必须存在。这里提供了几个连接命令的例子。

如果你的MongoDB使用默认的数据目录，连接的时候不用指定数据目录

>C:\mongodb\bin\mongo.exe

如果你的MongoDB使用了一个不同的数据目录，在连接的时候，需要指定目录，例如：

>C:\mongodb\bin\mongod.exe --dbpath d:\test\mongodb\data

如果你的路径有空格，用双引号把路径包起来，例如：

>C:\mongodb\bin\mongod.exe --dbpath "d:\test\mongo db data"

如果你想用.NET开发应用，可以去到[C# and MongoDB](http://docs.mongodb.org/ecosystem/drivers/csharp)看看更多的信息

####开始使用MongoDB

看看[开始MongoDB之旅](http://docs.mongodb.org/manual/tutorial/getting-started/)去开始使用MongoDB,在一个开发环境中部署MongoDB之前也可以考虑去看看[产品笔记](http://docs.mongodb.org/manual/administration/production-notes/)

最后，当`mongod`正在运行的时候，按`Ctrl+C`可以去停止MongoDB

##为MongoDB配置Window服务

> 笔记：
在MongoDB 2.6.0中有一个已知的问题，[SERVER-13515](https://jira.mongodb.org/browse/SERVER-13515?_ga=1.7013298.1267742375.1419469730),不能使用这一节的内容。对于MongoDB2.6.0，使用[手动的为MongoDB创建Window服务](http://docs.mongodb.org/manual/tutorial/install-mongodb-on-windows/#manually-create-windows-service)去为MongoDB创建Window服务。
