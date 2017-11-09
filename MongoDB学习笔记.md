>NoSQL(NoSQL = Not Only SQL)，意即"不仅仅是SQL".

## MongoDB简介和安装

MongoDB 是由C++语言编写的，是一个基于分布式文件存储的开源数据库系统。

MongoDB 将数据存储为一个文档，数据结构由键值(key=>value)对组成。

MongoDB 文档类似于 JSON 对象。字段值可以包含其他文档，数组及文档数组。

### MongoDB特点

- MongoDB的提供了一个面向文档存储，操作起来比较简单和容易。

- 你可以在MongoDB记录中设置任何属性的索引 (如：FirstName="Sameer",Address="8 Gandhi Road")来实现更快的排序。

- 你可以通过本地或者网络创建数据镜像，这使得MongoDB有更强的扩展性。

- 如果负载的增加（需要更多的存储空间和更强的处理能力） ，它可以分布在计算机网络中的其他节点上这就是所谓的分片。

- Mongo支持丰富的查询表达式。查询指令使用JSON形式的标记，可轻易查询文档中内嵌的对象及数组。

- MongoDb 使用update()命令可以实现替换完成的文档（数据）或者一些指定的数据字段 。

- Mongodb中的Map/reduce主要是用来对数据进行批量处理和聚合操作。
Map和Reduce。Map函数调用emit(key,value)遍历集合中所有的记录，将key与value传给Reduce函数进行处理。

- Map函数和Reduce函数是使用Javascript编写的，并可以通过db.runCommand或mapreduce命令来执行MapReduce操作。

- GridFS是MongoDB中的一个内置功能，可以用于存放大量小文件。

- MongoDB允许在服务端执行脚本，可以用Javascript编写某个函数，直接在服务端执行，也可以把函数的定义存储在服务端，下次直接调用即可。

- MongoDB支持各种编程语言:RUBY，PYTHON，JAVA，C++，PHP，C#等多种语言。

- MongoDB安装简单。

### MongoDB下载
>下载地址：[https://www.mongodb.com/download-center#community](https://www.mongodb.com/download-center#community)

>最新版不支持32位系统和Windows xp系统

### 安装

#### Windows安装
Windows 直接双击安装，默认是安装在C盘，可以点击`Custom`进行修改。

MongoDB将数据目录存储在`db`目录下，但是这个数据目录不会主动创建，所以我们在安装完成后需要创建它

>请注意，数据目录应该放在根目录下(C:\ 或者 D:\)。例如默认安装在C盘的，就要在`C:\`创建一个data的目录然后在`data`目录里创建`db`目录。

然后就在`MongoDB`目录的`bin`目录中执行`mongod.exe`文件
```
mongod --dbpath c:\data\db
```

>可以通过`CMD`命令窗口进入到`bin`目录然后执行命令

成功了会看到类似这样的信息：

![MongoDB start](http://ovv4v0gcw.bkt.clouddn.com/mongodbstart01.png)

可以通过打开`bin`目录下的`mongo.exe`运行MongoDB Shell，MongoDB自带的交互式`Javascript shell`,用来对MongoDB进行操作和管理的交互式环境。
当你进入mongoDB后台后，它默认会链接到 test 文档（数据库）

#### Linux平台安装

去官网下载安装包，然后解压并移动到要安装的目录下，如(/usr/local/mongodb),然后其他的就跟Windows类似，新建/data/db目录，然后进入mongodb的`bin`目录下运行。

```
#在var目录下新建/data/db
mkdir -p /var/data/db
#进入bin目录
cd /usr/local/mongodb/bin
#加上& 后台运行命令, 加上--dbpath指定数据库目录路径，然后就启动MongoDB
./mongod --dbpath /var/data/db &

#进入MongoDB后台Shell,bin目录下运行以下命令
./mongo
```

>可以把MongoDB的bin目录加入到PATH路径中。

>MongoDB 提供了简单的web界面，要想使用此功能，只需启动的时候加上 `--rest`,如`./mongod --dbpath /var/data/db/ --rest`

>web界面的端口比服务端口多`1000`，默认mongodb运行的端口为`27017`,所以默认的web界面端口为`28017`.访问地址为：`http://ip:28017`



## MongoDB使用

### MongoDB 概念解析

在mongodb中基本的概念是文档、集合、数据库。

![MongoDB 概念](http://ovv4v0gcw.bkt.clouddn.com/mongodbuse01.png)

#### 数据库
一个mongodb中可以建立多个数据库。

MongoDB的默认数据库为`db`，该数据库存储在data目录中。

MongoDB的单个实例可以容纳多个独立的数据库，每一个都有自己的集合和权限，不同的数据库也放置在不同的文件中。

`show dbs`命令可以显示所有数据的列表。

执行`db`命令可以显示当前数据库对象或集合。

运行`use`命令，可以连接到一个指定的数据库。

数据库也通过名字来标识。数据库名可以是满足以下条件的任意`UTF-8`字符串。
- 不能是空字符串（"")。
- 不得含有' '（空格)、.、$、/、\和\0 (空字符)。
- 应全部小写。
- 最多64字节。
有一些数据库名是保留的，可以直接访问这些有特殊作用的数据库。
- admin： 从权限的角度来看，这是"root"数据库。要是将一个用户添加到这个数据库，这个用户自动继承所有数据库的权限。一些特定的服务器端命令也只能从这个数据库运行，比如列出所有的数据库或者关闭服务器。
- local: 这个数据永远不会被复制，可以用来存储限于本地单台服务器的任意集合
- config: 当Mongo用于分片设置时，config数据库在内部使用，用于保存分片的相关信息。


#### 文档

文档是一组键值(key-value)对(即BSON)。MongoDB 的文档不需要设置相同的字段，并且相同的字段不需要相同的数据类型，这与关系型数据库有很大的区别，也是 MongoDB 非常突出的特点。

简单的文档例子：
```
{'site':'www.baidu.com','name':'百度'}
```

需要注意的是：

1. 文档中的键/值对是有序的。
2. 文档中的值不仅可以是在双引号里面的字符串，还可以是其他几种数据类型（甚至可以是整个嵌入的文档)。
3. MongoDB区分类型和大小写。
4. MongoDB的文档不能有重复的键。
5. 文档的键是字符串。除了少数例外情况，键可以使用任意UTF-8字符。

文档键命名规范：

1. 键不能含有`\0`(空字符)。这个字符用来表示键的结尾。
2. `.`和`$`有特别的意义，只有在特定环境下才能使用。
3. 以下划线`_`开头的键是保留的(不是严格要求的)。

#### 集合

集合就是 MongoDB 文档组，类似于 RDBMS （关系数据库管理系统：Relational Database Management System)中的表格。

集合存在于数据库中，集合没有固定的结构，这意味着你在对集合可以插入不同格式和类型的数据，但通常情况下我们插入集合的数据都会有一定的关联性。

比如，我们可以将以下不同数据结构的文档插入到集合中：

```
{"site":"www.baidu.com"}
{"site":"www.google.com","name":"Google"}
```
当第一个文档插入时，集合就会被创建。

##### 合法的集合名
- 集合名不能是空字符串`""`。

- 集合名不能含有`\0`字符（空字符)，这个字符表示集合名的结尾。

- 集合名不能以`system.`开头，这是为系统集合保留的前缀。

- 用户创建的集合名字不能含有保留字符。有些驱动程序的确支持在集合名里面包含，这是因为某些系统生成的集合中包含该字符。除非你要访问这种系统创建的集合，否则千万不要在名字里出现`$`。


##### capped collections

`Capped collections`就是固定大小的`collection`。

它有很高的性能以及队列过期的特性(过期按照插入的顺序). 有点和 "RRD" 概念类似。

`Capped collections`是高性能自动的维护对象的插入顺序。它非常适合类似记录日志的功能 和标准的`collection`不同，你必须要显式的创建一个`capped collection`， 指定一个`collection`的大小，单位是字节。`collection`的数据存储空间值提前分配的。
要注意的是指定的存储大小包含了数据库的头信息。
```
db.createCollection("mycoll", {capped:true, size:100000})
```

- 在capped collection中，你能添加新的对象。

- 能进行更新，然而，对象不会增加存储空间。如果增加，更新就会失败 。

- 数据库不允许进行删除。使用drop()方法删除collection所有的行。

- 注意: 删除之后，你必须显式的重新创建这个collection。

- 在32bit机器中，capped collection最大存储为1e9( 1X109)个字节。

##### 元数据

数据库的信息是存储在集合中。它们使用了系统的命名空间：
```
dbname.system.*
```

在MongoDB数据库中名字空间 `<dbname>.system.*`是包含多种系统信息的特殊集合(Collection)，如下:

![集合](http://ovv4v0gcw.bkt.clouddn.com/mongodbcoll01.png)

对于修改系统集合中的对象有如下限制。

在`{{system.indexes}}`插入数据，可以创建索引。但除此之外该表信息是不可变的(特殊的drop index命令将自动更新相关信息)。

`{{system.users}}`是可修改的。`{{system.profile}}`是可删除的。


##### MongoDB 数据类型

![MongoDB 数据类型](http://ovv4v0gcw.bkt.clouddn.com/mongodbtype01.png)

### Mongo 数据库操作

#### MongoDB 连接

MongoDB shell 来连接 MongoDB 服务器

标准 URI 连接语法

```
mongodb://[username:password@]host1[:port1][,host2[:port2],...[,hostN[:portN]]][/[database][?options]]
```

- mongodb:// 这是固定的格式，必须要指定。

- username:password@ 可选项，如果设置，在连接数据库服务器之后，驱动都会尝试登陆这个数据库

- host1 必须的指定至少一个host, host1 是这个URI唯一要填写的。它指定了要连接服务器的地址。如果要连接复制集，请指定多个主机地址。

- portX 可选的指定端口，如果不填，默认为27017

- /database 如果指定username:password@，连接并验证登陆指定数据库。若不指定，默认打开 test 数据库。

- ?options 是连接选项。如果不使用/database，则前面需要加上/。所有连接选项都是键值对name=value，键值对之间通过&或;（分号）隔开

标准的连接格式包含了多个选项(options)，如下所示：

![options](http://ovv4v0gcw.bkt.clouddn.com/mongodboption01.png)

实例：

连接本地数据库服务器，端口是默认的。
```
mongodb://localhost
```
使用用户名fred，密码foobar登录localhost的admin数据库。
```
mongodb://fred:foobar@localhost
```
使用用户名fred，密码foobar登录localhost的baz数据库。
```
mongodb://fred:foobar@localhost/baz
```
连接 replica pair, 服务器1为example1.com服务器2为example2。
```
mongodb://example1.com:27017,example2.com:27017
```
连接 replica set 三台服务器 (端口 27017, 27018, 和27019):
```
mongodb://localhost,localhost:27018,localhost:27019
```
连接 replica set 三台服务器, 写入操作应用在主服务器 并且分布查询到从服务器。
```
mongodb://host1,host2,host3/?slaveOk=true
```
直接连接第一个服务器，无论是replica set一部分或者主服务器或者从服务器。
```
mongodb://host1,host2,host3/?connect=direct;slaveOk=true
```
当你的连接服务器有优先级，还需要列出所有服务器，你可以使用上述连接方式。

安全模式连接到localhost:
```
mongodb://localhost/?safe=true
```
以安全模式连接到replica set，并且等待至少两个复制服务器成功写入，超时时间设置为2秒。
```
mongodb://host1,host2,host3/?safe=true;w=2;wtimeoutMS=2000
```


#### MongoDB 创建数据库

MongoDB 创建数据库的语法格式

```
use DATABASE_NAME
```

>如果数据库不存在，则创建数据库，否则切换到指定数据库。

然后可以通过`db`和`show dbs`查看

>刚创建的数据库不会显示在数据库列表中的，需要在新建的数据库中添加一些数据才可以显示。


#### MongoDB 删除数据库

MongoDB 删除数据库的语法格式

```
db.dropDatabase()
```

>删除当前数据库，默认为 test，你可以使用 db 命令查看当前数据库名。

实例：

![删除数据库](http://ovv4v0gcw.bkt.clouddn.com/mongodbremove01.png)


删除集合

集合删除语法格式

```
db.collection.drop()
```
实例：

![删除集合](http://ovv4v0gcw.bkt.clouddn.com/mongodbremove02.png)

>实例中是删除test集合


### MongoDB 文档操作

MongoDB文档的数据结构和JSON基本一样。所有存储在集合中的数据都是BSON格式。

BSON是一种类json的一种二进制形式的存储格式,简称Binary JSON。

#### MongoDB 插入文档

MongoDB 使用 insert() 或 save() 方法向集合中插入文档

```
db.COLLECTION_NAME.insert(document)
```
>`COLLECTION_NAME`为集合名，如果没有这个集合会自动创建。
>我们可以把数据定义为一个变量，然后再插入。

实例：

![插入文档](http://ovv4v0gcw.bkt.clouddn.com/mongodbinsert01.png)

>实例中把数据定义到`document`中，然后再添加。
>
>可以通过`db.COLLECTION_NAME.find()`查看数据。
>
>插入文档你也可以使用`db.COLLECTION_NAME.save(document)`命令。如果不指定 `_id`字段`save()`方法类似于`insert()`方法。如果指定`_id`字段，则会更新该`_id`的数据。

3.2 版本后还有以下几种语法可用于插入文档:

- db.collection.insertOne():向指定集合中插入一条文档数据

- db.collection.insertMany():向指定集合中插入多条文档数据

#### MongoDB 更新文档

update()方法用于更新已存在的文档。语法格式

```
db.collection.update(
   <query>,
   <update>,
   {
     upsert: <boolean>,
     multi: <boolean>,
     writeConcern: <document>
   }
)
```

参数说明：

- query : update的查询条件，类似sql update查询内where后面的。

- update : update的对象和一些更新的操作符（如$,$inc...）等，也可以理解为sql update查询内set后面的

- upsert : 可选，这个参数的意思是，如果不存在update的记录，是否插入objNew,true为插入，默认是false，不插入。

- multi : 可选，mongodb 默认是false,只更新找到的第一条记录，如果这个参数为true,就把按条件查出来多条记录全部更新。

- writeConcern :可选，抛出异常的级别。

实例：

![更新数据](http://ovv4v0gcw.bkt.clouddn.com/mongodbupdate01.png)

>实例中只修改匹配的第一条记录，如果要修改多条的，需要设置`mulit`参数为`true`

```
db.coltest.update({title:'Baidu'},{$set:{title:'百度'}},{mulit:true})
```

只更新第一条记录：
```
db.col.update( { "count" : { $gt : 1 } } , { $set : { "test2" : "OK"} } );
```
全部更新：
```
db.col.update( { "count" : { $gt : 3 } } , { $set : { "test2" : "OK"} },false,true );
```
只添加第一条：
```
db.col.update( { "count" : { $gt : 4 } } , { $set : { "test5" : "OK"} },true,false );
```
全部添加加进去:
```
db.col.update( { "count" : { $gt : 5 } } , { $set : { "test5" : "OK"} },true,true );
```
全部更新：
```
db.col.update( { "count" : { $gt : 15 } } , { $inc : { "count" : 1} },false,true );
```
只更新第一条记录：
```
db.col.update( { "count" : { $gt : 10 } } , { $inc : { "count" : 1} },false,false );
```
>在3.2版本开始，MongoDB提供以下更新集合文档的方法：
>
>db.collection.updateOne() 向指定集合更新单个文档
>
>db.collection.updateMany() 向指定集合更新多个文档


save() 方法

save() 方法通过传入的文档来替换已有文档。语法格式

```
db.collection.save(
   <document>,
   {
     writeConcern: <document>
   }
)
```

参数说明：

- document : 文档数据。

- writeConcern :可选，抛出异常的级别。

#### MongoDB 删除文档

MongoDB remove()函数是用来移除集合中的数据。

remove() 方法的基本语法格式

```
db.collection.remove(
   <query>,
   <justOne>
)
```

2.6版本以后的，语法格式如下：
```
db.collection.remove(
   <query>,
   {
     justOne: <boolean>,
     writeConcern: <document>
   }
)
```
参数说明：
- query :（可选）删除的文档的条件。

- justOne : （可选）如果设为 true 或 1，则只删除一个文档。

- writeConcern :（可选）抛出异常的级别。

>执行remove()函数前先执行find()命令来判断执行的条件是否正确，这是一个比较好的习惯。

删除title为Google的记录

```
db.coltest.remove({title:'Google'})
```

如果你只想删除第一条找到的记录可以设置 justOne 为 1
```
db.coltest.remove({'title':'Google'},1)
```

如果你想删除所有数据，可以使用以下方式（类似常规 SQL 的 truncate 命令）

```
db.col.remove({})
```

#### MongoDB 查询文档

MongoDB 查询文档使用 find() 方法。

find() 方法以非结构化的方式来显示所有文档。

MongoDB 查询数据的语法格式如下
```
db.collection.find(query, projection)
```
参数说明：

- query ：可选，使用查询操作符指定查询条件

- projection ：可选，使用投影操作符指定返回的键。查询时返回文档中所有键值， 只需省略该参数即可（默认省略）。

如果你需要以易读的方式来读取数据，可以使用`pretty()`方法
```
db.collection.find().pretty()
```

>pretty() 方法以格式化的方式来显示所有文档。
>
>除了 find() 方法之外，还有一个 findOne() 方法，它只返回一个文档。

MongoDB 与 RDBMS Where 语句比较

![查询比较](http://ovv4v0gcw.bkt.clouddn.com/mongodbsel01.png)

MongoDB AND条件

MongoDB的`find()`方法可以传入多个键(key)，每个键(key)以逗号隔开，及常规SQL的AND条件。

语法格式如下：
```
db.col.find({key1:value1, key2:value2}).pretty()
```
实例

![and查询](http://ovv4v0gcw.bkt.clouddn.com/mongodbsel02.png)

>实例中类似sql中的where语句： where URL='www.baidu.com' and title = 'Baidu'

MongoDB OR条件

MongoDB OR条件语句使用了关键字`$or`,语法格式
```
db.col.find(
   {
      $or: [
         {key1: value1}, {key2:value2}
      ]
   }
).pretty()
```

实例

![or查询](http://ovv4v0gcw.bkt.clouddn.com/mongodbsel03.png)

#### MongoDB 条件操作符

条件操作符用于比较两个表达式并从mongoDB集合中获取数据。

MongoDB中条件操作符有：

- (>) 大于 - $gt
- (<) 小于 - $lt
- (>=) 大于等于 - $gte
- (<= ) 小于等于 - $lte

例子

使用$gte查询
```
db.col.find({likes : {$gte : 100}})
```
类似于SQL中的
```
Select * from col where likes >=100;
```

使用$lt和$gt查询

```
db.col.find({likes : {$lt :200, $gt : 100}})
```
类似于SQL中的
```
Select * from col where likes>100 AND  likes<200;
```
>其他的条件操作符同理

#### MongoDB $type 操作符

`$type`操作符是基于BSON类型来检索集合中匹配的数据类型，并返回结果。

MongoDB 中可以使用的类型如下表所示

![MongoDB 中可以使用的类型](http://ovv4v0gcw.bkt.clouddn.com/mongodbtype02.png)

MongoDB 操作符`-$type`用法

获取`coltest`集合中`title`为`String`的数据

```
db.coltest.find({title:{$type:2}})
```

#### MongoDB Limit与Skip方法

##### MongoDB Limit()方法

如果你需要在MongoDB中读取指定数量的数据记录，可以使用MongoDB的Limit方法，limit()方法接受一个数字参数，该参数指定从MongoDB中读取的记录条数。

limit()方法基本语法如下
```
db.COLLECTION_NAME.find().limit(NUMBER)
```

>如果`limit()`中的括号为空则显示全部数据，如果为1则显示第一条记录，为10则显示前十条记录，如此类推。

##### MongoDB Skip()方法

使用skip()方法来跳过指定数量的数据，skip方法同样接受一个数字参数作为跳过的记录条数。

skip() 方法脚本语法格式如下

```
db.COLLECTION_NAME.find().limit(NUMBER).skip(NUMBER)
```

>如果`skip()`中的括号为空则显示全部，如果为1则跳过第一条记录，为10则跳过前十条记录，如此类推。`skip()`方法默认参数为0

>可以通过`limit()`和`skip()`实现分页，不过`skip()`只适合小的数据分页，当有几百万条数据时效率会很低。

>当查询时同时使用`sort`,`skip`,`limit`，无论位置先后，最先执行顺序`sort`再`skip`再`limit`。

#### MongoDB 排序

在MongoDB中使用使用`sort()`方法对数据进行排序，`sort()`方法可以通过参数指定排序的字段，并使用`1`和`-1`来指定排序的方式，其中`1`为升序排列，而`-1`是用于降序排列。

sort()方法基本语法如下
```
db.COLLECTION_NAME.find().sort({KEY:1})
```
如按ID降序排序
```
db.coltest.find().sort({_id:-1})
```
#### MongoDB 索引

索引通常能够极大的提高查询的效率，如果没有索引，MongoDB在读取数据时必须扫描集合中的每个文件并选取那些符合查询条件的记录。

索引是特殊的数据结构，索引存储在一个易于遍历读取的数据集合中，索引是对数据库表中一列或多列的值进行排序的一种结构

MongoDB使用`ensureIndex()`方法来创建索引

ensureIndex()方法基本语法格式如下
```
db.COLLECTION_NAME.ensureIndex({KEY:1})
```

>语法中 Key 值为你要创建的索引字段，1为指定按升序创建索引，如果你想按降序来创建索引指定为-1即可。

`ensureIndex()`方法中你也可以设置使用多个字段创建索引（关系型数据库中称作复合索引）

`ensureIndex()`接收可选参数，可选参数列表如下

![ensureIndex参数](http://ovv4v0gcw.bkt.clouddn.com/mongodbindex01.png)

如创建唯一索引，就在后面加上`unique`,值为`true`
```
db.coltest.ensureIndex({"title":1},{"unique":true})
```

查看索引

```
db.COLLECTION_NAME.getIndexes()
```

删除索引

```
db.COLLECTION_NAME.dropIndex({KEY:1})
```

#### MongoDB 聚合

MongoDB中聚合(aggregate)主要用于处理数据(诸如统计平均值,求和等)，并返回计算后的数据结果。有点类似sql语句中的`count(*)`。

MongoDB中聚合的方法使用`aggregate()`。

aggregate() 方法的基本语法格式如下

```
db.COLLECTION_NAME.aggregate(pipeline, options)
```

例如计算集合中相同URL的总数

```
db.coltest.aggregate([{$group:{_id:'$URL',num_tutorial:{$sum:1}}}])
```
![相同URL的总数](http://ovv4v0gcw.bkt.clouddn.com/mongodbaggreagte01.png)

>类似如SQL中的`select URL,count(*) from coltest group by URL`

一些聚合的表达式

![一些聚合的表达式](http://ovv4v0gcw.bkt.clouddn.com/mongodbaggreagte02.png)

>计算集合总数可以这样写`db.coltest.aggregate([{$group:{_id:null,count:{$sum:1}}}])`

上面聚合中的`$group`为管道操作符，MongoDB的聚合管道将MongoDB文档在一个管道处理完毕后将结果传递给下一个管道处理。管道操作是可以重复的。

聚合框架中常用的几个操作：

- $project：修改输入文档的结构。可以用来重命名、增加或删除域，也可以用于创建计算结果以及嵌套文档。

- $match：用于过滤数据，只输出符合条件的文档。$match使用MongoDB的标准查询操作。

- $limit：用来限制MongoDB聚合管道返回的文档数。

- $skip：在聚合管道中跳过指定数量的文档，并返回余下的文档。

- $unwind：将文档中的某一个数组类型字段拆分成多条，每条包含数组中的一个值。

- $group：将集合中的文档分组，可用于统计结果。

- $sort：将输入文档排序后输出。

- $geoNear：输出接近某一地理位置的有序文档。


## MongoDB 备份

### MongoDB 复制(副本集)

MongoDB复制是将数据同步在多个服务器的过程。复制提供了数据的冗余备份，并在多个服务器上存储数据副本，提高了数据的可用性， 并可以保证数据的安全性。复制还允许您从硬件故障和服务中断中恢复数据。

#### MongoDB复制原理

mongodb的复制至少需要两个节点。其中一个是主节点，负责处理客户端请求，其余的都是从节点，负责复制主节点上的数据。

mongodb各个节点常见的搭配方式为：一主一从、一主多从。

主节点记录在其上的所有操作oplog，从节点定期轮询主节点获取这些操作，然后对自己的数据副本执行这些操作，从而保证从节点的数据与主节点一致。

MongoDB复制结构图如下所示：

![MongoDB复制结构图](http://ovv4v0gcw.bkt.clouddn.com/mongodbcopy01.png)

>结构图中，客户端从主节点读取数据，在客户端写入数据到主节点时， 主节点与从节点进行数据交互保障数据的一致性。

副本集特征：

- N 个节点的集群

- 任何节点可作为主节点

- 所有写入操作都在主节点上

- 自动故障转移

- 自动恢复

MongoDB副本集设置

```
mongod --port "PORT" --dbpath "YOUR_DB_DATA_PATH" --replSet "REPLICA_SET_INSTANCE_NAME"
```

同一个MongoDB做主从
```
mongod --port 27017 --dbpath /var/data/db --replSet rs0
```

以上实例会启动一个名为`rs0`的MongoDB实例，其端口号为`27017`。

启动后打开命令提示框并连接上mongoDB服务。

在Mongo shell 使用命令`rs.initiate()`来启动一个新的副本集。

我们可以使用`rs.conf()`来查看副本集的配置

查看副本集状态使用`rs.status()`命令

副本集添加成员

添加副本集的成员，我们需要使用多台服务器来启动mongo服务。进入Mongo客户端，使用rs.add()方法来添加副本集的成员。

```
rs.add(HOST_NAME:PORT)
```

MongoDB中你只能通过主节点将Mongo服务添加到副本集中， 判断当前运行的Mongo服务是否为主节点可以使用命令`db.isMaster()`。

MongoDB的副本集与我们常见的主从有所不同，主从在主机宕机后所有服务将停止，而副本集在主机宕机后，副本会接管主节点成为主节点，不会出现宕机的情况。

>MongoDB副本集配置参考：[http://www.cnblogs.com/visionwang/p/3290435.html](http://www.cnblogs.com/visionwang/p/3290435.html)

### MongoDB 分片

####分片

在Mongodb里面存在另一种集群，就是分片技术,可以满足MongoDB数据量大量增长的需求。当MongoDB存储海量的数据时，一台机器可能不足以存储数据，也可能不足以提供可接受的读写吞吐量。这时，我们就可以通过在多台机器上分割数据，使得数据库系统能存储和处理更多的数据。

#### 为什么使用分片

- 复制所有的写入操作到主节点
- 延迟的敏感数据会在主节点查询
- 单个副本集限制在12个节点
- 当请求量巨大时会出现内存不足。
- 本地磁盘不足
- 垂直扩展价格昂贵

分片集群结构分布

![分片集群结构分布](http://ovv4v0gcw.bkt.clouddn.com/mongodbfp01.png)

上图中主要有如下所述三个主要组件：

- Shard:
用于存储实际的数据块，实际生产环境中一个shard server角色可由几台机器组个一个replica set承担，防止主机单点故障

- Config Server:
mongod实例，存储了整个 ClusterMetadata，其中包括 chunk信息。

- Query Routers:
前端路由，客户端由此接入，且让整个集群看上去像单一数据库，前端应用可以透明使用。

### MongoDB 备份(mongodump)与恢复(mongorestore)

#### MongoDB数据备份

在Mongodb中我们使用`mongodump`命令来备份MongoDB数据。该命令可以导出所有数据到指定目录中。

`mongodump`命令可以通过参数指定导出的数据量级转存的服务器。

mongodump命令脚本语法

```
mongodump -h dbhost -d dbname -o dbdirectory
```

参数说明：

- -h：
MongDB所在服务器地址，例如：127.0.0.1，当然也可以指定端口号：127.0.0.1:27017

- -d：
需要备份的数据库实例，例如：test

- -o：
备份的数据存放位置，例如：c:\data\dump，当然该目录需要提前建立，在备份完成后，系统自动在dump目录下建立一个test目录，这个目录里面存放该数据库实例的备份数据。

mongodump 命令可选参数列表

![mongodump 命令可选参数列表](http://ovv4v0gcw.bkt.clouddn.com/mongodbdump01.png)


#### MongoDB数据恢复

mongodb使用 mongorestore 命令来恢复备份的数据。

mongorestore命令语法

```
mongorestore -h <hostname><:port> -d dbname <path>
```

参数说明：

- --host <:port>, -h <:port>：
MongoDB所在服务器地址，默认为： localhost:27017

- --db , -d ：
需要恢复的数据库实例，例如：test，当然这个名称也可以和备份时候的不一样，比如test2

- --drop：
恢复的时候，先删除当前数据，然后恢复备份的数据。就是说，恢复后，备份后添加修改的数据都会被删除，慎用哦！

- <path>：
mongorestore 最后的一个参数，设置备份数据所在位置，例如：c:\data\dump\test。
你不能同时指定 <path> 和 --dir 选项，--dir也可以设置备份目录。

- --dir：
指定备份的目录
你不能同时指定 <path> 和 --dir 选项。

### MongoDB 监控

在你已经安装部署并允许MongoDB服务后，你必须要了解MongoDB的运行情况，并查看MongoDB的性能。这样在大流量得情况下可以很好的应对并保证MongoDB正常运作。

MongoDB中提供了mongostat 和 mongotop 两个命令来监控MongoDB的运行情况。

#### mongostat 命令

mongostat是mongodb自带的状态检测工具，在命令行下使用。它会间隔固定时间获取mongodb的当前运行状态，并输出。如果你发现数据库突然变慢或者有其他问题的话，你第一手的操作就考虑采用mongostat来查看mongo的状态。

mongostat运行界面如下

![mongostat运行界面](http://ovv4v0gcw.bkt.clouddn.com/mongodbmongostat01.png)

#### mongotop 命令

mongotop也是mongodb下的一个内置工具，mongotop提供了一个方法，用来跟踪一个MongoDB的实例，查看哪些大量的时间花费在读取和写入数据。 mongotop提供每个集合的水平的统计数据。默认情况下，mongotop返回值的每一秒。

mongotop运行界面如下

![mongotop运行界面](http://ovv4v0gcw.bkt.clouddn.com/mongodbmongotop01.png)





### 关于mongodb几个启动参数

```
--replSet 集群名

--dbpath 数据文件存储目录

-port 端口

--oplogSize 操作日志，这里设置了50M，对于实验环境足够了，生产环境一般默认就好，5%的磁盘空间，当然越大越好

--logpath 日志文件

--logappend 追加日志方式

--fork 后台运行

--smallfiles 使用小文件

--nojournal 不记录操作日志
```
