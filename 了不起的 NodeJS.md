# Part 1 安装和概念  

## 安装  
### Windows  
安装包  

### Linux  
编译安装  

### REPL  
JavaScript 代码可直接执行  

### 执行文件  
`node jsfile.js`  

### NPM  
安装:  
```  
mkdir my-project/  
cd my-project/  
npm install colors  
```  
自定义:  
`npm init`  
安装 CLI 工具:  
`npm install -g express`  
浏览:  
```  
npm search realtime  
npm view socket.io  
```  
帮助:  
`npm help`  

- - -  
## JavaScript 概览  
`JavaScript 语言精粹`  
### 基础  
```  
typeof null == 'object'  
typeof [] == 'object'  
```  
```  
try {  
    //...  
} catch (exception) {  
    //...  
}  
```  

### V8 中的 JavaScript  
`Object.hasOwbProperty()` -- 检查是否为 key  
`Array.isArray()` -- 检查是否为 Array  
`Array.forEach(Function)` -- 列表循环
`Array.filter(Function)` -- 列表过滤  
`Array.map(Function)` -- 列表映射  
`reduce, reduceRight, lastIndexOf`  
`String.trim()` -- 移除字符串首尾空字符  
`JSON.stringify` -- json 解码  
`JSON.parse` -- json 编码  
`var.bind({var: value})` -- 改变 this 的引用  
`Function.name = ?` -- 非标准函数命名  
`__proto__` -- 方便定义继承链  
`__defineSetter/Getter__` -- 定义和访问属性  

- - -  
## 阻塞和非阻塞 IO  
### 能力越大责任越大  
**共享状态的并发**  
Be Careful:  
> * 回调函数如何修改当前内存中变量  
> * 错误处理是否会潜在地修改状态而导致整个进程不可用  

**Node采用一个长期运行的进程**  

时间轮询:  
Node 1. 注册 => 不停询问事件是否分发 => 未分发: 继续执行  
                                 => 已分发: 回调函数被调用  

**Node 并发也实现了时间轮询**  
文件描述符是抽象的句柄, 存有对打开文件/socket/管道等的引用.  
本质上: Node 接受从浏览器 HTTP 请求, 底层 TCP 分配文件描述符. 之后若客户端发送数据, Node 接受文件描述符上的通知, 除法回调函数.  

**Node 是单线程的**  
**Node 高并发**  
> * 调用堆栈  
> * 在调用堆栈执行非常快的情况下, 同一时刻你无需处理多个请求  

**错误处理**  
**Node 依托在一个拥有大量共享状态的大进程中**  
如果某个回调发生错误, 整个进程都会遭殃  
**错误处理中, 每一步都非常重要**  

- - -  
## Node 中的 JavaScript  
### global 对象  
`global`对象, 类似 window, 任何global对象上的属性都可以被全局访问到  
`process`对象, 所有全局执行上下文的内容都在 process 对象中  
实用:  
* setTimeout  
* setImmediate  
* console  

### 模块系统  
require, module, exports  
绝对模块: 内部 node_modules 查找  
相对模块: 相对工作目录, 加上 ./  

### 暴露API  
default: 每个模块暴露空对象  
```  
exports.name = 'name';  
exports.data = 'this is some data';  
exports.getPrivate = function () {
    // ...  
}  
```  

### 事件  
`EventEmitter`  
```  
var EventEmitter = require('events').EventEmitter, a = new EventEmitter;  
a.on('event', function() {
    // ...  
});
a.emit('event');  
```  
```  
var EventEmitter = process.EventEmitter, MyClass = function() {};  
MyClass.prototype.__proto__ = EventEmitter.prototype;  
```  
**Node通常不会直接返回数据, 采用事件分发来传递数据**  

### buffer  
**buffer 表示一个固定内存分配的全局对象**  
**对数据进行编码转换**  
**在 Node 中绝大部分数据 IO 操作API 都用 buffer 来接受和返回数据**
- - -  
# Node 的重要 API  
```  
var fs = require('fs'), 
    stdin = process.stdin  
    stdout = process.stdout  
    


fs.readdir(process.cwd(), function (err, files) {
    console.log(' ');
    if (!files.length) {
        return console.log('  \033[31m No files to show!\033[39\n');
    }
    
    console.log('  Select which file or directory you wang to see\n');
    var stats    = [];
    var filename = "";
    
    function file (i) {
        filename = files[i];
        fs.stat(__dirname + '/' + filename, function (err, stat) {
            stats[i] = stat;
            
            if (stat.isDirectory()) {
                console.log('   ' + i + '  \033[36m' + filename + '/\033[39m');
            } else {
                console.log('   ' + i + '  \033[90m' + filename + '\033[39m');
            }
        
            if (++i == files.length) {
                read();
            } else {
                file(i);
            }
        })
    }

    function read () {
        console.log(' ');
        stdout.write('\033[33mEnter your choice: \033[39m');
        stdin.resume();
        stdin.setEncoding('utf-8');
        stdin.on('data', option);
    }

    function option (data) {    
        if (!files[Number(data)]) {
            stdout.write('  \033[31mEnter your choice: \033[39m');  
        } else {
            stdin.pause();
            if (stats[Number(data)].isDirectory()) {
                fs.readdir(__dirname + '/' + filename, function (err, files) {
                    console.log('');
                    console.log('   (' + file.length + ' files)');
                    files.forEach(function (file) {
                        console.log('   -   ' + file);
                    });
                });
            } else {
                fs.readFile(__dirname + '/' + filename, 'utf-8', function (err, data) {
                console.log(' ');
                console.log('\033[90m' + data.replace(/(.*)/g, '    $1') + '\033[39m');  
            });
        }
    }
}
    
    file(0);
})
```  

- - -  
## TCP  
### 特性  
**面向连接**  
**连接/数据流有序**  
**确认/超时有效**  
**流控制**  
**拥堵控制**  

- - -  
## HTTP  
### HTTP 结构  
### 头信息  
使用 ReadStream 系统API实现连续读入  
数据块形式读入的好处:  
* 高效的内存分配  
* 数据就绪即可写入  
**将一个流转移到另一个流上**  

### 一个简单的 Web 服务器  
### 一个 Twitter Web 客户端  
### superagent  

- - -  
# Web 开发  
## Connect  
[Connect](https://github.com/senchalabs/connect)  

- - -  
## Express  
[Express](https://github.com/expressjs/express)  

- - -  
## WebSocket  
### Ajax  
**控制服务器先后请求顺序**  
WebSocket: web 底层的双向 socket, 允许用户对消息传递进行控制  

- - -  
## Socket.IO  
[Socket.io](https://github.com/socketio/socket.io)  

### 传输  
**消息基于传输**  
**支持超时**  
**基于事件传输**  
**单个连接中基于命名空间区分消息**  


- - -  
# Part 4 数据库  
## MongoDB  
### 使用: 用户认证  
连接:  
```  
var server = new mongodb.Server('127.0.0.1', 27017)  
new mongodb.Db('mydb', server).open(function (err, client) {
    if (err) {
        // ...  
    } else {
        // ...  
    }
    app.users = new mongodb.Collection(client, 'users');
    app.listen(3000, function () {
        // ...  
    });
});  
```  
创建文档:  
```  
collection.insert({key: 'value'}, function (err, docs) {
    // ...  
});
```  
查找:  
```  
collection.find({});  
```  

### Mongoose  
```  
mongoose.connect('mongodb://localhost/mydb');  
var schema = mongoose.Schema, ObjectId = Schema.ObjectId;

// Schema 只是一种简单的抽象  
// 数据交互发生在模型上  
var postSchema = new Schema({
    // ...  
});
var Post = mongoose.model('BlogPost');  
```
**运行定义嵌套文档**  
**构建引**  
**this.dirtyPaths 探测什么键被修改了**  
查询:  
* find  
* findOne  
* remove  
* update  
* count  

拓展查询:  
**若某个查询不提供回调函数, 直到调用 run 它才会执行**  

排序:  
query.sort(//...);  

选择:  
**只取部分键值**  
```  
query.find({}).select('field', ...);  
```  

限制:  
**限制查询结果数量**  
```  
query.limit(5);  
```  

跳过:  
**跳过指定数目的文档数据**  
```   
query.skip(10);  
```  

自动生成键:  
**为 ObjectId 提供 ref 属性**  
之后查询时就能自动产生相应数据  
```  
Model.find({...}).populate('author') ...  
```  

转换:  
自动类型转换  

- - -  
## MySQL  
### node-mysql  
`require('mysql');`  
初始化客户端:  
```  
var db = mysql.createClient(config);  
db.query('your query to db');  
```  

### sequelize  
[Sequelize](https://github.com/sequelize/sequelize)  
`require('sequelize');`  
初始化:  
```  
var sequelize = new Sequelize('database', 'usename', 'pwd', 'host', 'port');  
```  
定义模型:  
```  
sequelize.define('model', {
    // ...  
});  
```  
...  

- - -  
## Redis  
[Node-Redis](https://github.com/NodeRedis/node_redis)  

- - -  
# Part 5 测试  
## 代码共享  
Browser 和 NodeJs 代码共享问题(API 不一定兼容, 需要进行恰当地转换)  
[BrowserBuild](https://github.com/Automattic/browserbuild)  

- - -  
## 测试  
[Expect.js](https://github.com/Automattic/expect.js)  
[Mocha](https://github.com/mochajs/mocha)  



