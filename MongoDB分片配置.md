
## MongoDB分片技术

分片技术，跟关系数据库的表分区类似，我们知道当数据量达到T级别的时候，我们的磁盘，内存就吃不消了，或者单个的mongoDB服务器已经不能满足大量的插入操作,针对这样的场景我们该如何应对。mongoDB提供的分片技术来应对这种瓶颈。 
当然分片除了解决空间不足的问题之外，还极大的提升的查询速度。

### 分片的概念

将集合进行拆分，然后将拆分的数据均摊到几个片上的一种解决方案。

分片集群结构分布

![分片集群结构分布](http://ovv4v0gcw.bkt.clouddn.com/mongodbfp01.png)


上图中主要有如下所述三个主要组件：

- Shard:
用于存储实际的数据块，实际生产环境中一个shard server角色可由几台机器组个一个replica set承担，防止主机单点故障

- Config Server:
mongod实例，存储了整个 ClusterMetadata，其中包括 chunk信息。

- Query Routers:
前端路由，客户端由此接入，且让整个集群看上去像单一数据库，前端应用可以透明使用。


### 要点

>testdb为数据库，col为集合，没有配置完前不要使用任何数据库语句。

- 打开数据分片功能,为数据库foobar打开分片功能
```
db.runCommand({"enablesharding":"testdb"})
或者
sh.enableSharding('testdb')
```

- 对集合进行分片,制定片键
```
db.runCommand({"shardcollection":"testdb.col","key":{"_id":1}})
或者
sh.shardCollection('test.db',{"_id":1})
```
- 开启路由的命令是`mongos`,并通过`--configdb`指定配置服务器,其他服务器通过`mongod`启动。

- MongodDB 3.4要求配置服务器必须也是副本集

### 配置

环境

```
路由： 192.168.106.132:27200

分片服务器1：192.168.106.142:27017

分片服务器2：192.168.106.142:27018

配置服务器1：192.168.106.132:27100

配置服务器2：192.168.106.142:27100
```

>这里是在一台服务器上用不同的端口模拟分片数据库和配置服务器


配置启动Shard Server

在192.168.106.142这个服务器上设置

```
#先在数据库存放目录下新建两个目录还有一个存放日志的log目录。
mkdir -p /var/data/shard1
mkdir -p /var/data/shard2
mkdir -p /var/data/log

#启动Shard Server
mongod --port 27017 --shardsvr --dbpath /var/data/shard1 --logpath /var/data/log/shard1.log --fork

mongod --port 27018 --shardsvr --dbpath /var/data/shard2 --logpath /var/data/log/shard2.log --fork
```

>这里要注意，`--fork`为后台运行的意思，当使用`--fork`时要指定`--logpath`,`--logappend`是追加日志的意思

配置配置服务器

在192.168.106.142和192.168.106.132服务器上设置副本集

```
#192.168.106.142服务器
mongod --port 27100 --configsvr --replSet conf --dbpath /var/data/conf/ --logpath /var/data/log/conf.log --fork

#192.168.106.132服务器
mongod --port 27100 --configsvr --replSet conf --dbpath /var/data/conf/ --logpath /var/data/log/conf.log --fork
```

登录MongoDB shell 配置副本集,登录那个服务器都可以

```
#进入配置服务器shell
mongo --port 27100

use admin

conf={
	"_id" : "conf",
	"configsvr" : true,
	"members" : [
		{
			"_id" : 0,
			"host" : "192.168.106.142:27100"
		},
		{
			"_id" : 1,
			"host" : "192.168.106.132:27100"
		}
	]
}

rs.initiate(conf)
```
>MongodDB 3.4要求配置服务器必须也是副本集




启动路由

>在192.168.106.132上面进行设置

```
#新建一个目录存放日志文件
mkdir -p /var/data/log

mongos --port 27200 --configdb conf/192.168.106.142:27100,192.168.106.132:27100 --logpath /var/data/log/mongos.log --fork
```

>这里用的命令是`mongos` ,通过`--configdb`指定配置服务器。如果启动不了，可能是mongodb的版本问题，请关注一下最新的MongoDB的文档

>MongoDB的文档:[https://docs.mongodb.com/manual/](https://docs.mongodb.com/manual/)


通过MongoDB Shell登录到mongos，然后配置，添加Shard节点。

```
#在192.168.106.132上登录27200端口的shell
mongo --port 27200

#设置
#添加节点
sh.addShard('192.168.106.142:27017')  
sh.addShard('192.168.106.142:27018')  

#查看状态
sh.status()  

#设置数据库testdb分片，设置片键为_id
sh.enableSharding('testdb')  
sh.shardCollection('testdb.col',{'_id':1})
```

这样就配置完成了。

>默认的`chunksize`大小是64M

`sh.status()`状态

![sh.status()状态](http://ovv4v0gcw.bkt.clouddn.com/mongodbshards01.png)


>参考文档：
>
>[http://blog.csdn.net/mlz_2/article/details/71703153](http://blog.csdn.net/mlz_2/article/details/71703153)
>
>[http://blog.csdn.net/canot/article/details/50739359](http://blog.csdn.net/canot/article/details/50739359)
>
>[http://www.cnblogs.com/lens/p/4833655.html](http://www.cnblogs.com/lens/p/4833655.html)




