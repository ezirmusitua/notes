## Chapter 1  
### 集合  
一组用途相同或类似的文档，不受严格模式的管制，文档可根据需要采取稍微不同的结构。  
### 文档  
单个实体数据  
文档中可以嵌入**子文档**  
BSON格式存储  
### 数据类型  
Double/String/Object/Array/Binary data/ObjectId/Boolean/Date/Null/Regular expression/JavaScript/Symbol/JavaScript/Int32/Timestamp/Int64/MinKey/MaxKey  
### 规划数据模型  
范式化数据：文档引用 -- 减少冗余和依赖   
反范式化数据：嵌入文档 -- 一次查找获得所有对象  
固定集合：大小固定的集合，超过覆写  
原子写入：不能有多个进程同时更新一个文档或集合  

## Chapter 2  
### Mongo Shell  
启动：mongo [dbname]  
[原生方法](https://docs.mongodb.org/manual/reference/mongo-shell/)  
**eval(expression)**  
**load(script-path)**  
**print(...)**  
**printjson(...)**  

## Chpater 3    
类似 JS：
1. 变量  
2. 数据类型  
3. 输出  
4. 运算符  
5. 循环  
6. 函数  
7. 对象  
8. 错误处理  

## Chapter 4  
[相关](https://docs.mongodb.org/manual/reference/command/nav-user-management/)  

## Chapter 5  
[相关](https://docs.mongodb.org/manual/reference/command/)  

[官方文档](https://docs.mongodb.org/manual/mongo/)  
[PyMongo](https://api.mongodb.org/python/current/)  
[node-mongodb-native](https://github.com/mongodb/node-mongodb-native)  

## Chapter 22  
[复制数据库](https://docs.mongodb.org/manual/reference/method/db.copyDatabase/)  
[重命名集合](https://docs.mongodb.org/manual/reference/command/renameCollection/)  
[创建Collection](https://docs.mongodb.org/manual/reference/method/db.createCollection/)  
[管理索引]()  
性能和诊断：  
db.stats  
db.Collection.validate  
db.runCommand({profile:1, slowns: 500})  
db.system.profile.find()  
评估查询：  
db.Collection.find().explain()  
使用top：  
db.runCommand({top:1})  
修复：  
mongod --repair --repairpath /tmp/mongodb/data  
备份：  
mongodump --host ... --out ...  
mongorestore --dbpath curdb bakdb  

## Chapter 23  
[实现副本集](http://www.codeceo.com/article/mongodb-config.html)  
[实现分片](http://www.codeceo.com/article/mongodb-sharding.html)  
[GridFS](http://docs.mongodb.org/manual/core/gridfs)  




