## 文档型数据库

> 是以文档为单位的数据库, 内部数据结构是 bson(和 json 很像)

### 与传统型数据库的不同

- 与传统型数据库最大的不同, 是 mongodb 表下的文档都可以有自己的结构, 独特的属性和值
- 传统型的数据库必须要有指定的列, 每个列的值也一定是一种类型

### mongo 命令

#### 服务器命令

- mongo: 客户端程序, 相当于 mysql
- mongod: 服务端程序, 相当于 mysqld
    - 开启服务 `./bin/mongod --dbpath ./data --logpath ./log.log`
    - mongo 启动的时候非常占磁盘空间, 大约是 3-4G 左右, 可以用 --smallfiles 选项启动, 会尽量占用较小空间
- mongodump: 整体数据库导出, 相当于 mysqldump
- mongoexport: 导出 csv 或 json
- mongorestore: 数据库导入
- mongos: 路由器(分片时用)

#### 基本命令

- show dbs: 显示数据库列表
- use dbname: 选择数据库, 若没有此库, 会`隐式创建一个数据库`
- show tables / show collections: 显示数据表列表
    - system.indexes: 索引信息
    - system.users: 用户信息
- db.help(): 帮助
- db.createCollection(): 创建一个集合

#### CURD

- db.collName.drop(): 删除集合
- db.collName.insert(pipeline, options): 传入 json 数组即是向集合插入多条数据, 传入 json 即是插入一条数据
- db.collName.remove(pipeline, options): 删除集合中的数据, 不传就是删除所有, 若有条件, 会删除匹配的数据
- db.collName.update(pipeline, data, options): 更新集合中的数据, 但是直接更新会将新值直接替代旧值
    - data 对象中的关键字 `db.<collName>.update(pipeline, {$set:{col:"value"},$unset:{col:"value"},$incr:{col:""},$rename:{col:"newCol"}}, options)`
        - $set: 修改某列的值
        - $setOnInsert: 当 upsert 为 true 的时候, 并且发生了 insert 的时候, 补充的字段
        - $unset: 删除匹配的某列
        - $rename: 修改某列的值
        - $incr: 增长某列的值
    - options
        - {multi: true}: 默认只更改单行, 如果想更改多行, 可以使用此选项
        - {upsert: true}: 若匹配到, 直接修改, 若没有匹配到, 直接插入一行
- db.collName.find(pipeline, options): 查找集合中的数据, 不传就是所有
    - pipeline(where 和 regex 少用, 因为效率不够高)
        - {attrName: value}: 相当于 `where attrName = value`,这里的 value 是强类型的, **2 和 '2' 不相等**
        - {attrName: {$ne: value}} : 相当于 `where attrName <> value`
        - {attrName: {$in: value}} : 相当于 `where attrName in value`
        - {attrName: {$nin: value}} : 相当于 `where attrName not in value`
        - {attrName: {$gt: value}} : 相当于 `where attrName > value`
        - {attrName: {$lt: value}} : 相当于 `where attrName < value`
        - {attrName: {$gte: value}} : 相当于 `where attrName >= value`
        - {attrName: {$lte: value}} : 相当于 `where attrName <= value`
        - {attrName: {$exists: 1}} : 查找出有此列的所有文档
        - {attrName: {$mod: \[5, 1]}} : 查找出此列对 5 取模余 1 的所有文档
        - {attrName: {$all: []}} : 指定的值在文档中都应该查询到
        - {attrName: {$type: 1}} : 查找出此列匹配类型的所有文档
            - double: 1
            - string: 2
            - object: 3
            - array: 4
            - binary data: 5
            - undefined: 6
            - object id: 7
            - boolean: 8
            - date: 9
            - null: 10
            - regex: 11
            - javascript: 13
            - symbol: 14
        - {attrName: {$regex: /^value*/}} : 使用正则表达式匹配文档
        - {$or: \[{attrName: {$lte: value1}, {attrName: {$gte: value2}}]} : 相当于 `where attrName <= value1 or attrName >= value2`
        - {$and: \[{attrName: {$lte: value1}, {attrName: {$gte: value2}}]} : 相当于 `where attrName <= value1 and attrName >= value2`
        - {$nor: \[{attrName: {$lte: value1}, {attrName: {$gte: value2}}]} : 列举的条件都不成立, 相当于 `where not (attrName <= value1 or attrName >= value2)`
        - {$where: 'this.attrName>200 && this.attrName<500'} : 可以使用 where 代替负责的 pipeline
    - options
        - {attrName: 1}: 查询所有文档中的 attrName 属性, 相当于 select attrName from ...
        - {_id: 0}: 不查询 _id 属性














