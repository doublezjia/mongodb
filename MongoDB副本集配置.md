>环境：Centos6.8

### 说明

运行节点
```
192.168.106.132 主节点
192.168.106.142 从节点
```

>为了实验方便，所以仲裁节点(Arb)也在192.168.106.142上.
>
>仲裁节点的作用是协调leader选举，监测系统运行状态，提供节点互相通讯的数据信息。


### 运行

在主节点上运行
```
 mongod --replSet test --dbpath /var/data/db/ --port 27017 --oplogSize 1024 &
```

在从节点上运行
```
mongod --replSet test --dbpath /var/data/db/ --port 27017 --oplogSize 1024 &

#换了一个端口做仲裁节点，这个可以用另一台服务器来做
mongod --replSet test --dbpath /var/data/arb/ --port 27018 --oplogSize 1024 &
```
>上面的命令中，`test`是副本集的名称，每个节点必须相同，`–dbpath`指定数据库储存路径，`–port`指定侦听端口，`–oplogSize`指定数据同步之前的缓存的空间大小，暂时指定`1G`，因为要在后台运行，所以加上`&`.
>
>这里的命令是在LINUX shell中运行。

### 配置

在主节点的服务器登录 MongoDB shell
```
mongo localhost:27017
```

声名`config`变量

```
config = {_id: "test", members: []}
```

注意_id和副本集启动的共享名称一致。下面来逐步添加节点的数据信息

```
config.members.push({_id:0,host:'192.168.106.132:27017'})
config.members.push({_id:1,host:'192.168.106.142:27017'})
config.members.push({_id:2,host:'192.168.106.142:27018',arbiterOnly:true})
```
>也可以使用rs.add和rs.addArb函数来实现同样的操作。然后需要用这个表作为参数初始化副本集

然后执行
```
rs.initiate(config)
```
>返回ok为1表示初始化成功

成功之后可以通过`rs.isMaster()`
```
test:PRIMARY> rs.isMaster()
{
	"hosts" : [
		"192.168.106.132:27017",
		"192.168.106.142:27017"
	],
	"arbiters" : [
		"192.168.106.142:27018"
	],
	"setName" : "test",
	"setVersion" : 1,
	"ismaster" : true,
	"secondary" : false,
	"primary" : "192.168.106.132:27017",
	"me" : "192.168.106.132:27017",
	"electionId" : ObjectId("7fffffff0000000000000003"),
	"lastWrite" : {
		"opTime" : {
			"ts" : Timestamp(1494182990, 1),
			"t" : NumberLong(3)
		},
		"lastWriteDate" : ISODate("2017-05-07T18:49:50Z")
	},
	"maxBsonObjectSize" : 16777216,
	"maxMessageSizeBytes" : 48000000,
	"maxWriteBatchSize" : 1000,
	"localTime" : ISODate("2017-05-07T18:49:52.360Z"),
	"maxWireVersion" : 5,
	"minWireVersion" : 0,
	"readOnly" : false,
	"ok" : 1
}

```
>可以看到ismaster是true，secondary为false，hosts有2个实例，arbiter有1个元素，primary关键key表示了主节点。

>查看详细信息可以通过`rs.status()`查看，查看当前副本集的配置表`rs.conf()`

### 测试

可以通过在主节点上添加数据，然后断开主节点，观察从节点是否切换过来。数据是否同步过来。然后把主节点重新运行，断掉从节点，再观察。


>观察状态可以通过`rs.status()`或者`rs.isMaster()`命令。
>
>切换成功，可以看到从节点shell中名称从`SECONDARY`变为`PRIMARY`
>
>以上配置中都在MongoDB shell中运行。



### 配置副本集的其他配置参数

节点和初始化高级参数

- standard 常规节点:参与投票有可能成为活跃节点
- passive 副本节点:参与投票,但是不能成为活跃节点
- arbiter 仲裁节点:只是参与投票不复制节点也不能成为活跃节点

高级参数

- Priority 0到1000之间 ,0代表是副本节点 ,1到1000是常规节点
- arbiterOnly : true 仲裁节点