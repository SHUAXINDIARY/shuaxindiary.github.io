---
title: MongoDB入门学习笔记
category:
  - 踩坑
tag:
  - NodeJS
abbrlink: 841
date: 2019-11-10 19:48:21
---
# 使用前准备
1.安装mongodb [下载](http://downloads.mongodb.com/)
2.选择服务端然后对应系统版本，推荐下载msi格式(这个可以一步一步安装，比较方便)
3.下载后在安装目录里(与bing目录同级下)创建 data文件夹(存放数据库集合的)
4.通过cmd启动mongdb
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.进入bin目录

<!-- more -->
```linux
2. mongod --dbpath (这里的路径是第三部创建的data文件夹的而路径) 
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.看到控制台打印出 waiting forconnections on port 27017(默认端口) 即启动成功

5.将mongodb添加到本地服务中
&nbsp;&nbsp;&nbsp;1.在第三部的data文件中创建log文件夹
&nbsp;&nbsp;&nbsp;2.在log文件夹中创建mongodb.log文件
&nbsp;&nbsp;&nbsp;3.输入命令
```linux
mongod.exe --logpath (这里写你log文件的路径) --logappend --dbpath (这里写你data文件夹的路径) --directoryperdb --serviceName MongoDB --install
```
&nbsp;&nbsp;&nbsp;4.通过管理员启动cmd，输入
```linux
<!-- 启动mongodb服务 -->
net start MongoDB
<!-- 关闭mngodb服务 -->
net stop MongoDB
<!-- 卸载mongodb服务 -->
mongod.exe --remove --serviceName "MongoDB"
```
&nbsp;&nbsp;&nbsp;5.遇到无法启动的问题 删除data目录下的 mongod.lock和storage.bson文件重新启动即可
# 基本概念
集合:类似MySQL的表

文档:类似MySQL的元祖
<!-- more -->

mongo中的数据类型:
>Object ID： ⽂档ID
- 可以手动设置，未设置系统默认分配一个objectID(是⼀个12字节的⼗六进制数)
>String： 字符串， 最常⽤， 必须是有效的UTF-8
>Boolean： 存储⼀个布尔值， true或false
>Integer： 整数可以是32位或64位， 这取决于服务器
>Double： 存储浮点值
>Arrays： 数组或列表， 多个值存储到⼀个键
>Object： ⽤于嵌⼊式的⽂档， 即⼀个值为⼀个⽂档
>Null： 存储Null值
>Timestamp： 时间戳， 表示从1970-1-1到现在的总秒数
>Date： 存储当前⽇期或时间的UNIX时间格式
```shell
# 创建日期
new Date('2017-12-20')
```
# MongoDB基本命令
>关于数据库
```shell
# 启动MongoDB
net stop mongodb
# 进入安装目录的bin目录
# 终端启动(客户端)
mongo
# 服务端启动
mongod
# 查看当前数据
db
# 查看所有数据库
show dbs
# 切换数据库---不存在时会自动创建---如果创建了没操作，在切换时，该数据库不保存
use <dbname>
# 删除数据库
db.dropDatabase()
# 显示当前数据库状态信息
db.stats()
# 查看当前数据库版本
db.version()
# 查看当前连接数据库地址
db.getMongo()
```
>关于集合
```shell
# 查看所有集合
show collections
# 删除集合
db.<集合名>.drop()
```
>关于文档
```shell
# 插入文档  数据为对象格式  当集合不存在时直接插入会自动创建集合
db.<集合名>.insertOne({name:'shauxin',age:21});
# insertOne插入一个  insertMany插入多个
db.<集合名>.insertOne([{name:'one'},{name:'two'}]);
# 查询所有文档
db.<集合名>.find()
# 更新文档
#更新一条update或者updateOne,没有更新的字段会丢弃. 
db.<集合名>.update({name:'hr'},{name:'mnc'})
#更新一条
db.<集合名>.update({name:'hr'},{$set:{name:'hys'}})
#更新全部
db.<集合名>.update({},{$set:{gender:0}},{multi:true}) 
# 更新多个 updateMany
db.<集合名>.updateOne({name:'wade'},{$set:{age:21}})   
# 删除
# 删除第一个符合条件的
db.<集合名>.deleteOne({age:21})
# 删除所有符合条件的
db.<集合名>.deleteMany({age:21})
```
