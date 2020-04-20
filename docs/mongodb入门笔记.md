### 启动 mongodb 服务

打开 mongodb 的安装目录，执行 `mongod` 即可。mac下面要执行 `sudo mongod`。



### mongodb常用命令

* 创建数据库/切换数据库 —  `use databasename` ,该命令在没有数据库的情况下会创建数据库。
* 查看所有数据库—`show dbs`
* 删除数据库—`db.dropDatabase()`
* 创建集合—`db.createCollection(collectionname)`
* 删除集合— `db.colletionname.drop()`
* 



### mongodb 导出csv

`mongoexport -d dbname -c collectionname -o file --type json/csv -f field`

举个栗子：

`mongoexport -d lottery -c prizes -o prizelist --type csv -f _id,type,icon,rate,name,amount,ison`

* -d ：数据库名

* -c ：collection名

* -o ：输出的文件名

* --type ： 输出的格式，默认为json

* -f ：输出的字段，如果-type为csv，则需要加上-f 字段名