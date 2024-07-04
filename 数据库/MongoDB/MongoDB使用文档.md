show dbs -- 获取所有的数据库

use dbname -- 切换数据库

show collections  -- 获取所有的集合列表

db.createCollection("集合名称")  -- 创建集合

db.名称.insert({ "name":"张三" })  -- 插入信息

db.名称.find()  -- 查询集合列表所有数据

db.名称.find({ "name":"wangwu" }) -- 集合按条件查询

db.名称.findOne()  -- 集合显示第一条

db.name.findOne({"name":"ziwei"}) -- 集合按条件查询第一条

db.col.find({$or:[{"by":"菜鸟教程"},{"title": "MongoDB 教程"}]}).pretty()  -- or 查询

db.名称.drop()  -- 删除

db.文档名称.update({'name':'fengshang'},{$set:{'name':'dangkou'}})  -- 更新文档

db.文档名称.remove()  -- 删除文档

db.文档名称.remove({ "name":"dangkou" })  -- 删除指定条件文档

db.serverStatus().connections  -- MongoDB查看当前连接数



