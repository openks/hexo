---
title:  mongodb相关内容学习   
date: 2016-11-17 13:21:35  
description:  mongodb安装,shell命令记录  
keywords: [前端，mongoodb]  
---


### windows下安装mongodb
下载exe文件并安装  
创建数据库文件的存放位置，比如`f:/mongodb/data/db`。  
启动mongodb服务之前需要必须创建数据库文件的存放文件夹，否则命令不会自动创建，而且不能启动成功。


进入mongodb安装文件的bin目录（默认：`C:\Program Files\MongoDB\Server\3.0\bin`），
输入如下的命令设置mongoDB的dbpath：
```
mongod --dbpath f:\mongodb\data\db
```
打开[http://localhost:27017](http://localhost:27017)查看是否启动成功

为了以后不用每次都添加`--dbpath`参数,需做如下设置
在`f:\mongodb`新建文件`mongo.config`并填写内容如下
```
dbpath=f:\mongodb\data\db
logpath=f:\mongodb\log\mongo.log
```
进入mongodb安装文件的bin目录，输入如下的命令添加mongodb配置：
```
mongod --config F:\mongodb\mongo.config  
```
如果发现新生成日志文件则表示配置成功。


```js
//安装windows服务
mongod --config f:\mongodb\mongo.config -install -serviceName "MongoDB"
//进入mongoDB shell
cd C:\Program Files\MongoDB\Server\3.0\bin
mongo
//也可以将mongo所在路径加入环境变量path中,之后就可以直接在cmd里输入mongo进而打开mongo shell
```

[mongoDB使用手册](https://docs.mongodb.com/manual/)


### mongodb部分命令
```
//启用mongo shell
mongo
//连接远程mongo shell
mongo 192.168.1.100
//显示数据库
show dbs
//切换数据库
use dbname
//显示当前正在使用的数据库
db
//显示当前数据库下面的所有集合(数据库里的数据表)
show collections
//导出test数据库到data/backup文件夹下
mongodump -d test -o data/backup  
//在要恢复的文件夹的父级目录里使用如下命令
mongorestore data/backup

//Use mongoexport to export the data: 导出test数据库下面的traffic表到traffic.json文件
mongoexport --db test --collection traffic --out traffic.json

mongoimport --db users --collection contacts --file contacts.json



//插入记录
db.collectionName.insert({"userName":"zs0","birthday":"1999-09-09","passWord":"123456","gender":0});
//返回结果入下
//WriteResult({ "nInserted" : 1 })

//插入一条记录 New in version 3.2
db.collectionName.insertOne({"userName":"zs10","birthday":"1999-09-09","passWord":"123456","gender":0});
// 返回结果如下
{
    "acknowledged" : true,
    "insertedId" : ObjectId("57e8ef4a0a3c3720c7a9650d")
}

//插入多条记录 New in version 3.2
db.collectionName.insertMany([
    {"userName":"zs3","birthday":"1999-09-09","passWord":"123456","gender":0},
    {"userName":"zs1","birthday":"1999-09-09","passWord":"123456","gender":0},
    {"userName":"zs2","birthday":"1999-09-09","passWord":"123456","gender":0}]
);
//返回值如下
{
    "acknowledged" : true,
    "insertedIds" : [
        ObjectId("57e8ef810a3c3720c7a9650e"),
        ObjectId("57e8ef810a3c3720c7a9650f"),
        ObjectId("57e8ef810a3c3720c7a96510")
    ]
}


//查找全部数据
db.collectionName.find();
//查找全部数据但显示前两条
db.collectionName.find().limit(2);
//查看查询结果数量
db.collectionName.find().count();
//美化查询结果
db.collectionName.find().pretty();
//只显示需要列(_id默认显示,如果不需要显示必须显式阻止) 类似于命令 select username, email from users
db.collectionName.find({}, {"username" : 1, "email" : 1, "_id" : 0})
//单属性匹配
db.collectionName.find({"name":"zs"});
db.collectionName.find({"user.name":"zs"});
//多属性同时匹配
db.collectionName.find({"age":20,"name":"zs"});
//正则表达式匹配 find  name startwith "z" and endWith "s"
db.collectionName.find({"name":/^z/},{"name":/s$/});
//正则表达式匹配 find  name 包含BD字符串
db.collectionName.find({"name":/BD+/});
//where条件匹配
db.collectionName.find({$where:function(){return this.name="zs"}});
//find age<23 gl:less than
db.collectionName.find({"age":{$lt:23}});
//find age>23 gt：greater than
db.collectionName.find({"age":{$gt:23}});
//find name="zs" || age=20
db.collectionName.find({$or:[{"age":20},{"name":"zs"}]});
//find age in (10,20,22)
db.collectionName.find({"age":{$in:[10,20,22]}});
//find age not in (10,20,22)
db.collectionName.find({"age":{$nin:[10,20,22]}});


//修改name=zs为new_ZS 年龄为40
db.person.update({"name":"zs"},{"name":"new_ZS","age":40});
//年龄增加20
db.person.update({"name":"zs"},{$inc:{"age":20}});
//年龄设置为33
db.person.update({"name":"zs"},{$set:{"age":33}});
//第三个参数true，表示更新条件找不到时增加一条记录
db.person.update({"name":"szs"},{$set:{"age":33}},true);
//第四个参数表示更新全部数据
db.person.update({"name":"zs"},{$inc:{"age":20}}，true,true);
//对表中所有数据增加birthday列并设置其默认值
db.person.update({},{$set:{"birthday":"1991-01-01"}},true,true);



//删除某集合下`name`字段值为`new_ZS`的记录
db.collectionName.remove({"name":"new_ZS"});
//删除某集合下的全部数据（清空表）
db.collectionName.remove({});
//删除某集合下某字段（eg：userName）不存在的记录
db.collectionName.remove( { "userName": { $exists: false } } )
//删除某集合（删除某表）
db.collectionName.drop();
```


```

//统计年龄为20的记录条数
db.person.count({"age":20});
//name不重复的记录
db.person.distinct("name");
//name字段升序排列
db.person.ensureIndex({"name":1})
db.person.ensureIndex({"name":1},{"unique":true});
//非空唯一 可以为空
db.person.ensureIndex({"name":1},{"unique":true,"sparse":true});
db.person.find({"name":"ns"}).explain();
db.person.ensureIndex({"name":1,"age":1});
//获取该集合的索引
db.person.getIndexes();
// 要删除的索引名字需通过查看得到
db.person.dropIndexes("name_1");
```
## Mac OSX 下安装MongoDB
1.把从官网上下的文件，型如：mongodb-osx-x86_64-3.4.0 解压到Applications文件夹  
2.在根目录下新建mongodb文件夹,新建db文件夹（存放数据）mongodb/data/db  
3.`sudo chown －R ./Users/zyx mongodb/data` 设置权限  
4.新建文件mongodb/etc/mongod.conf和mongodb/etc/mongod.log  
5.进入到bin目录，使用`mongod --config /Users/zyx/mongodb/etc/mongod.conf` 设置mongod 配置。  
6.如果看到waiting for connections on port 27017  
7.可以打开浏览器输入：localhost:27017,如果看到It looks like you are trying to access MongoDB over HTTP on the native
 driver port 说明连接成功了。  
8.点击终端 Commond+N 打开一个新的终端 cd 到bin目录 ./mongo 便可连接到数据库进行操作  

```
#mongodb config file
dbpath=/Users/zyx/mongodb/data/db
logpath=/Users/zyx/mongodb/etc/mongod.log
logappend = true
```

##  Mac OSX 下设置环境变量
1.打开 应用程序 -> 实用工具 -> 终端；    
2.在终端中定位到自己用户的主目录，输入： `cd ~ `；    
3.创建一个空文件，输入：`touch .bash_profile` ；    
4.编辑这个文件，输入：`open .bash_profile` ；    
5.在这个文件中输入：`export PATH=${PATH}:<文件目录> `；（将"<文件目录>"替换成自己想要的目录）     
例如：`export PATH=${PATH}:/Users/skythinking/MongoDB/mongodb-osx-x86_64-2.6.1/bin`;  
6.如果需要添加其他的环境变量例如JAVA_HOME，可以输入：`export JAVA_HOME=/Library/Java/Home` ；   
7.`source .bash_profile`重启终端，测试,这个时候就可以在用户主目录使用上面配置过的配置文件进行设置`mongod --config /Users/zyx/mongodb/etc/mongod.conf`   来启动数据库了，点击终端Commond+N打开一个新的终端，使用mongo命令来连接数据库，对数据库进行操作,比如：`show dbs` 显示所有的集合  

## Mac OSX 下设置MongoDB的开机启动

Mac 下用于初始化系统环境的关键经常是 launchd，它是内核转载成功后启动的第一个进程。
所以设置服务的开机启动要用到这个进程。采用 launchd 开机启动 需要配置一个plist文件。

开机启动分为两种：  
     1、在用户登陆前启动；( plist文件放置在目录：~/Library/LaunchDaemons )  
     2、在用户登陆后启动。( plist文件放置在目录：~/Library/LaunchAgents )

如 MongoDB 的开机启动,需要在 LaunchDaemons 或 LaunchAgents 创建一个 plist文件。  
如 org.mongodb.mongod.plist 内容如下所示：
```
<plist version="1.0">
<dict>
  <key>Label</key>
  <string>org.mongodb.mongod</string>
  <key>ProgramArguments</key>
  <array>
    <string>/Users/zyx/Applications/mongodb-osx-x86_64-3.4.0/bin/mongod</string>
    <string>-f</string>
    <string>/Users/zyx/mongodb/etc/mongod.conf</string>
  </array>
  <key>RunAtLoad</key>
  <true/>
  <key>KeepAlive</key>
  <false/>
  <key>WorkingDirectory</key>
  <string>/Users/zyx/Applications/mongodb-osx-x86_64-3.4.0</string>
  <key>StandardErrorPath</key>
  <string>/Users/zyx/mongodb/etc/output.log</string>
  <key>StandardOutPath</key>
  <string>/Users/zyx/mongodb/etc/output.log</string>
  <key>HardResourceLimits</key>
  <dict>
    <key>NumberOfFiles</key>
    <integer>1024</integer>
  </dict>
  <key>SoftResourceLimits</key>
  <dict>
    <key>NumberOfFiles</key>
    <integer>1024</integer>
  </dict>
</dict>
</plist>

```
注意：以上的几个目录需要自己按自己的安装路径设置  
plist 文件创建好后 根据自己设置执行如下命令加载到 开机启动中：

```
sudo launchctl load /Library/LaunchDaemons/org.mongodb.mongod.plist
```
或者
```
sudo launchctl load /Library/LaunchAgents/org.mongodb.mongod.plist
```

命令执行后 mongodb 将会马上启动，下次也会随开机而启动。  
可通过[http://127.0.0.1:27017](http://127.0.0.1:27017/)查看是否启动成功。
