---
published: true
layout: post
title: "MongoDB 常用操作备忘"
date: 2017-05-08
---

🌲 **version 3.4**

### 数据库相关操作

- 查询所有数据库
`show dbs`

- 查询当前数据库
`db`

- 创建/切换数据库
`use <database>`

### 集合 增删改查基础操作

#### 插入文档

- 插入单条文档
`db.collection.insertOne(document)`

- 插入多条文档
`db.collection.insertMany(documents array)`

#### 查询文档

- 查询所有文档
`db.collection.find(query, projection)`  

query相当于sql的where条件，projection指定那些列显示(1)和不显示(0)

🌰示例：

```
db.col.find({status: 'A'}, {_id: 0, name: 1, age: 1})
相当于
select name, age from col where status = 'A'
```
- 查询单条文档
`db.collection.findOne(query, projection)`

- 查询嵌套字段
`db.collection.find({ "field.nestedField": "value" })`

#### 更新文档

- 更新单条文档
`db.collection.updateOne(<filter>, <update>, <options>)`    

🌰示例：

```
db.col.updateOne(
   { name : 'Lee' }, 
   { $set: { age : 28 } },
   { upsert: true }
)

```
`upsert`默认值false, 若设置为 true, 则 `filter`无法匹配任何文档时会创建一条新文档。

`Update Operators` $set, $unset, $rename 

- 更新多条文档
`db.collection.updateMany(<filter>, <update>, <options>)`

- 替换文档
`db.collection.replaceOne(<filter>, <replacement>, <options>)`  
注：replacement不能使用 update operators , 是全部替换。

#### 删除文档
- 删除单条文档
`db.collection.deleteOne()`

- 删除多条文档
`db.collection.deleteMany()`

### 常用操作符

#### 比较操作符

- **$eq** (=) equal
- **$gt** (>) greater than
- **$gte** (>=) greater than or equal
- **$lt** (<) less than
- **$lte** (<=) less than or equal
- **$ne** (!=) not equal
- **$in** in an array
- **$nin** not in an array

🌰示例：

```
db.col.find({age: {$gte: 10, $lte: 20}})
相当于
select * from col where age >= 10 and age <= 20
```

#### 逻辑操作符
- **$or**
- **$and**
- **$not**
- **$nor**

🌰示例：

```
db.col.find({$or: [{key1: value1}, {key2:value2}]})

```

#### 数组操作符
- **$all**
- **$elemMatch**
- **$size**

🌰示例：

```
db.col.find({ scores: { $elemMatch: { $gte: 60, $lt: 80 } } })

```
