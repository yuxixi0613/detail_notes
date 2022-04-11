[TOC]

## 数据库

### [1.1数据库是什么](http://docs.lipeihua.vip/#/./NodeJS/08.mongoDB?id=_11数据库是什么)

数据库（DataBase）是按照数据结构来组织、存储和管理数据的仓库。

### [1.2为什么要使用数据库](http://docs.lipeihua.vip/#/./NodeJS/08.mongoDB?id=_12为什么要使用数据库)

我们的程序都是在内存中运行的，一旦程序运行结束或者计算机断电，程序运行中的数据都会丢失。所以我们就需要将一些程序运行的数据持久化到硬盘之中，以确保数据的安全性。而数据库就是数据持久化的最佳选择。说白了，数据库就是存储数据的仓库。

### [1.3数据库的分类](http://docs.lipeihua.vip/#/./NodeJS/08.mongoDB?id=_13数据库的分类)

#### [1.3.1 关系型数据库（RDBS）](http://docs.lipeihua.vip/#/./NodeJS/08.mongoDB?id=_131-关系型数据库（rdbs）)

代表有：MySQL、Oracle、DB2、SQL Server...

特点：关系紧密，都是表

#### [1.3.2 非关系型数据库（NoSQL）](http://docs.lipeihua.vip/#/./NodeJS/08.mongoDB?id=_132-非关系型数据库（nosql）)

代表有：MongoDB、Redis...

特点：关系不紧密，有文档，有键值对





#### [2.2.2启动MongoDB服务](http://docs.lipeihua.vip/#/./NodeJS/08.mongoDB?id=_222启动mongodb服务)

1. 添加环境变量，将MongoDB的bin目录添加到path下

[![0gEIG4.md.png](https://s1.ax1x.com/2020/10/11/0gEIG4.md.png)](https://imgchr.com/i/0gEIG4) [![0gEoRJ.md.png](https://s1.ax1x.com/2020/10/11/0gEoRJ.md.png)](https://imgchr.com/i/0gEoRJ) [![0gEHMR.png](https://s1.ax1x.com/2020/10/11/0gEHMR.png)](https://imgchr.com/i/0gEHMR)

1. 打开一个命令行窗口输入mongo启动数据库的客户端





------



## Mongoose

### 介绍

Mongoose 是一个对象文档模型（ODM）库，它对Node原生的MongoDB模块进行了进一步的优化封装，并提供了更多的功能。 官网 <http://www.mongoosejs.net/>

### 作用

使用代码操作 mongodb 数据库

### 使用流程

一、安装 mongoose

在命令行下使用 npm 或者其他包管理工具安装（cnpm  yarn）

```sh
npm install mongoose@5 --save
```

二、引入包

在运行文件中引入 mongoose

```js
var mongoose = require('mongoose');
```

三、连接数据库

```js
mongoose.connect('mongodb://127.0.0.1:27017/data');

//如果启动时遇到警告提醒， 则按照提示增加选项即可
mongoose.connect('mongodb://127.0.0.1:27017/data', {useNewUrlParser: true, useUnifiedTopology: true});
```

四、监听连接事件

```js
mongoose.connection.on('open', function () {
	
	//下面编写数据库操作代码
    
    //五、创建文档结构
    var SongSchema = new mongoose.Schema({
        title: String,  //歌名
        author: String  //歌手
    });
    
    //六、创建文档模型
    var SongModel = mongoose.model('songs', SongSchema);
    
    //七、使用模型进行文档处理（这里以增加数据为例）
    SongModel.create({title:'野狼disco',author:'宝石gem'}, function(err,data){
        if(err) throw err; //这里判断错误
        
        //下面编写创建成功后的逻辑
        // ... ...
        
        //八、关闭数据库连接（可选，代码上线之后一般不加）
        mongoose.connection.close();
    });
	
});
```

### 数据类型

文档结构可选的字段类型列表

- ==String==
- ==Number==
- Date
- Buffer
- Boolean
- Mixed   任意类型（使用 mongoose.Schema.Types.Mixed 设置）
- ObjectId
- ==Array==
- Decimal128（4.3版本后加入）

### CURD

数据库的基本操作包括四个，增加（create），删除（delete），修改（update），查（read）

#### 增加

插入一条

```js
SongModel.create({
    title:'给我一首歌的时间',
    author: 'Jay'
}, function(err, data){
    //错误
    console.log(err);
    //插入后的数据对象
    console.log(data);
});
```

批量插入

```js
SongModel.insertMany([
    {
        title:'给我一首歌的时间',
        author: 'Jay'
    },
    {
        title:'爱笑的眼睛',
        author: 'JJ Lin',
    },
    {
        title:'缘分一道桥',
        author: 'Leehom Wang'
    }
], function(err, data){
    console.log(err);
    console.log(data);
});
```

#### 删除

删除一条数据

```js
SongModel.deleteOne({_id:'5dd65f32be6401035cb5b1ed'}, function(err, data){
    console.log(err);
    console.log(data);
});
```

批量删除

```js
SongModel.deleteMany({author:'Jahn'}, function(err, data){
    console.log(err);
    console.log(data);
});
```

#### 更新

更新一条数据

```js
SongModel.updateOne({author: 'jack chen'}, {author: '成龙'}, function (err, data) {
    console.log(err);
    console.log(data);
});
```

批量更新数据

```js
SongModel.updateMany({author: 'li ming'}, {author: '黎明'}, function (err, data) {
    console.log(err);
    console.log(data);
});
```

#### 查询

查询一条数据

```js
SongModel.findOne({author: '黄安'}, function(err, data){
    console.log(err);
    console.log(data);
});
//根据 id 查询数据
SongModel.findById('5dd662b5381fc316b44ce167',function(err, data){
    console.log(err);
    console.log(data);
});
```

批量查询数据

```js
//不加条件查询
SongModel.find(function(err, data){
    console.log(err);
    console.log(data);
});
//加条件查询
SongModel.find({author: '黄安'}, function(err, data){
    console.log(err);
    console.log(data);
});
```

##### 字段筛选

```js
SongModel.find().select({_id:0,title:1}).exec(function(err,data){
    console.log(data);
});
```

##### 数据排序

```js
SongModel.find().sort({hot:1}).exec(function(err,data){
    console.log(data);
});
```

##### 数据截取

```js
SongModel.find().skip(10).limit(10).exec(function(err,data){
    console.log(data);
});
```









