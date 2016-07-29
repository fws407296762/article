本教程提供一个介绍使用 [mongo](http://docs.mongodb.org/manual/reference/program/mongo/#bin.mongo) 命令进行基础数据库操作。 [mongo](http://docs.mongodb.org/manual/reference/program/mongo/#bin.mongo)是标准`MongoDB`发行版的一部分，提供了一个完整的`JavaScript`环境，用来访问`JavaScript`语言和所有标准功能以及完整的`MongoDB`数据库接口。查看[mongo JavaScript API](http://api.mongodb.org/js/?_ga=1.207164304.2015163773.1419323680)文档以及[mongo](http://docs.mongodb.org/manual/reference/program/mongo/#bin.mongo) 命令[JavaScript方法接口](http://docs.mongodb.org/manual/reference/method/)

本教程假定你是在Linux或者是OS X操作系统上运行的MongoDB，同时，你正在运行数据库服务。MongoDB已经支持Window，同时为Window发行的版本提供了相同的操作。关于安装MongoDB和开启数据库服务，可以去看看 [安装方式](http://docs.mongodb.org/manual/installation/) 文档

###连接数据库

在这一节，你通过运行[mongod](http://docs.mongodb.org/manual/reference/program/mongod/#bin.mongod)连接数据库，并开始使用[mongo](http://docs.mongodb.org/manual/reference/program/mongo/#bin.mongo) 命令去在数据库实例中选择一个逻辑数据库以及访问帮助内容。

####连接一个[mongod](http://docs.mongodb.org/manual/reference/program/mongod/#bin.mongod)

通过系统提示，在命令行中输入[mongo](http://docs.mongodb.org/manual/reference/program/mongo/#bin.mongo)开启[mongo](http://docs.mongodb.org/manual/reference/program/mongo/#bin.mongo)，如下：

>mongo

默认情况下，[mongod](http://docs.mongodb.org/manual/reference/program/mongod/#bin.mongod)看上去一个数据库服务监听了localhost的27017端口。如果想一个服务在不通过端口或者是host，可以使用 `--port`和`--host`选项

>mongo --port 8080
>mongo --host www.a.com

#####选择数据库

开启[mongo](http://docs.mongodb.org/manual/reference/program/mongo/#bin.mongo)命令后，默认你的会话将会使用**test**数据库。在任何时间，在[mongo](http://docs.mongodb.org/manual/reference/program/mongo/#bin.mongo)中进行一下操作，将会显示当前数据库名：

> db

1、显示数据库列表，如下操作：

> show dbs

2、切换到名称为**mydb**的数据库，如下操作：

>use mydb

3、验证你的会话是**mydb**数据库的当前上下文，通过检测**db**对象的值，返回当前数据库的名字，如下：

>db

针对这里，如果你再次执行`show dbs`操作，它将不会吧`mydb`数据库算在里面。只有在你把数据插入到数据库后，MongoDB才会创建一个数据库，否则将永远不会创建。**创建一个集合和插入文档**一节讲解了插入数据的过程

*在2.4版本中**show databases也会返回一个数据库列表*

#####显示mongo帮助

你可以通过下面的操作访问`mongo`命令的帮助：

>help

此外，你可以在一些JavaScrption方法、临时对象以及`db`和`db.collection`对象中添加`.help()`方法，返回额外的帮助信息

>db.help()

##创建一个集合和插入文档

在这一章，在`mydb`数据库内，你将学会把文档插入到名字为`testData`的集合中

在一次使用的时候MongoDB将创建一个隐形的集合。在插入数据之前你不需要去创建一个集合。此外，因为MongoDB使用[dynamic(动态) schemas(结构)](http://docs.mongodb.org/manual/faq/fundamentals/#faq-schema-free)，在把你的文档插入到集合中的时候，你也不需要指定结构

1、通过`mongo`命令，下面的命令可以确实你是否在`mydb`数据库中

>db

2、如果在上一个操作中没有返回**mydb**，则可以通过下面的操作可以设置为`mydb`数据库环境：

> use mydb

3、通过使用下面的JavaScript操作顺序，可以创建名字为`j`和`k`的两个文档

> j = {name : 'mongo'}
> k = {k : 3}

4、通过下面操作顺序，插入`j`和`k`文档到`testData`集合

> db.testData.insert(j) //WriteResult({ "nInserted" : 1 })
> db.testData.instert(k) //WriteResult({ "nInserted" : 1 })

当你插入第一个文档的时候，`mongod`将同时创建`mydb`数据库和`testData`集合

5、检测`testData`集合是否存在。执行下面的操作：

> show collections

`mongo`命令将返回当前(即mydb)数据库中的集合列表。在这里，只有`testData`集合是用户数据

6、在集合上使用`find()`方法执行查询来检测`testData`集合中是否存在文档

> db.testData.find()

这个操作返回下面的结果。[Objectid](http://docs.mongodb.org/manual/reference/object-id/)  值是唯一的：

>{ "_id" : ObjectId("4c2209f9f3924d31102bd84a"), "name" : "mongo" }
>{ "_id" : ObjectId("4c2209fef3924d31102bd84b"), "x" : 3 }

所有的`MongoDB`文档中有一个`_id`字段，这个字段有唯一的值。这些操作不会明确为`_id`字段指定一个值，这样在将这个字段插入到集合前`mongo`为它创建唯一的`ObjectId`值

##使用一个循环或者一个JavaScript方法插入文档

在本文档中执行剩余的步骤，使用一个或多个步骤(首先)增加更多的文档到你的数据库,在[生成测试数据](http://docs.mongodb.org/manual/tutorial/generate-test-data/)有描述

##游标工作

当你查询一个集合的时候，MongoDB返回一个包含查询结果的`cursor（游标）`对象。`mongo`命令遍历这个游标去显示结果。而不是一次性显示所有的结果，这个命令为第一次的20个结果遍历20次，然后等待一个请求去遍历剩余的结果，使用 `enter it`去遍历结果的下一个集合。

在这一节的步骤中暂时了用其他的方式去对游标进行工作。关于游标的综合文档，可以去看看[遍历返回的游标](http://docs.mongodb.org/manual/reference/method/db.collection.find/#crud-read-cursor)。

####用一个循环遍历游标

在使用这个步骤之前，在[生成测试数据](http://docs.mongodb.org/manual/tutorial/generate-test-data/)中使用这个步骤添加一个文档到集合中。你可以随意命名你的数据库和集合，但是这个步骤将假设这个数据库命名为`test`以及集合命名为`testData`。

1、在MongoDB的JavaScript命令中，查询这个`testData`集合，并将生成的游标对象指定到`c`变量上。

> var c = db.testData.find();

2、使用`while`循环遍历变量`c`来打印全部结果:

> while(c.hasNext()) printjson(c.next())

如果这个右边有文档`hasNext()`方法将返回`true`。`next()`方法返回下一个文档。`printjson()`方法以类JSON格式显示文档。这个操作显示所有文档：

>{ "_id" : ObjectId("549ad5fd5bfc8f425d6a4ab7"), "name" : "mongo" }
{ "_id" : ObjectId("549ad6155bfc8f425d6a4ab8"), "x" : 3 }
{ "_id" : ObjectId("549c1fed708228173dbea30a"), "name" : "mongo" }
{ "_id" : ObjectId("549c1ff0708228173dbea30b"), "x" : 3 }

####对光标使用数组操作

下面的操作可以将你操作的光标作为一个数组来操作：

1、在`mongo`命令中，查询这个`testData`集合，并将生成的游标对象指定到`c`变量上。


> var c = db.testData.find()

2、使用下面的操作，在数组索引4上找到文档

> printjson( c [ 3 ] )

MongoDB返回下面的结果：

> { "_id" : ObjectId("549c1ff0708228173dbea30b"), "x" : 3 }

但你使用数组索引值存储文档到游标中式，`mongo`首先会调用`cursir.toArray()`方法，



