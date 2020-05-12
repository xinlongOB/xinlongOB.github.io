---
title: mongodb安装以及基础操作
tags:
  - mongo
  - linux
categories: 运维
date: 2020-01-08 16:09:50
---
# 安装mongo数据库
	
	cd  /etc/yum.repos.d/
	vim   mongodb-org-3.2.repo
	[mogodb-org]
	name=MongoDB Repository
	baseurl=http://mirrors.aliyun.com/mongodb/yum/redhat/6Server/mongodb-org/3.4/x86_64/
	gpgcheck=0
	enabled=1

然后保存退出

	yum clean all    # 清除缓存
	yum install  mongod-org  -y

# 配置文件

	# mongod.conf

	# for documentation of all options, see:
	#   http://docs.mongodb.org/manual/reference/configuration-options/

	# where to write logging data.
	systemLog:
	  destination: file
	  logAppend: true
	  path: /var/log/mongodb/mongod.log     #  日志文件路径

	# Where and how to store data.
	storage:
	  dbPath: /var/lib/mongo	# 数据保存路径
	  journal:
	    enabled: true		# 是否开启
	#  engine:
	#  mmapv1:
	#  wiredTiger:
	
	# how the process runs
	processManagement:
	  fork: true  # fork and run in background
	  pidFilePath: /var/run/mongodb/mongod.pid  # location of pidfile

	# network interfaces
	net:
	  port: 27017		# 监听端口
	  bindIp: 192.168.1.163  # 允许连接的IP


	#security:
	#security:

	#  authorization: enabled

	#operationProfiling:

	#replication:

	#sharding:
	## Enterprise-Only Options

	#auditLog:

	#snmp:

# 增删改查

	mongo   IP   #  进入数据库

	show  dbs	# 查看所有库

	show tables	# 查看当前库的所有表

	use   DBNAME	# 进入数据库

	db.table.find()		# 查看表中的所有数据

	db.table.find({name : xxx})	# 查看表中name为xxx的数据

	db.table.find({name : xxx}).pretty()	# 查看表中name为xxx的数据   以json格式显示

	db.table.count()	# 统计数据行数

	db.tables.find().count()	# 统计行数   同上

	db.table.count({name : xxx})	# 统计name为xxx的行数

	db.table.update({},{$set:{name : xxx}})		# 把表中所有数据的name 改为 xxx

	db.table.update({name : xxx},{$set:{ID : 666}})		# 把name 为 xxx 的ID 改为666  （只更改匹配到的第一条数据）

	db.table.update({name : xxx},{$set:{ID : 666}},false,true)	# 把全部name 为 xxx的ID 改为666  （匹配到的所有数据）

	db.copyDatabase('old_name', 'new_name', 'localhost')	# 复制数据库

	use  DBNAME 	# 进入数据库
	db.dropDatabase()	# 删除当前所在的库

	db.table.drop()		# 删除表

	db.table.remove({})	# 删除表中所有数据

	db.table.remove({name : xxx})	# 删除表中被匹配到的第一条数据

	db.table.remove({name : xxx},false,true)	# 删除表中被匹配到的所有数据

	use DBNAME 	# 进入数据库
	db.create.table()	# 创建一个表      如果这个数据库之前不存在  创建表后会自动创建库


# 增删改查--扩展
	
	db.roles.find({"ID":{"$lte": 200,"$gte":155 },userType:41})	# 范围查询  查看ID 小于等于200  大于等于155 并且userType=41 的数据

	db.roles.find({ "name" : {$regex:/大气的.*/i}})		# 模糊查询	匹配name 包含"大气的" 数据
	
	db.towers.update({"_id" : ObjectId("5a6205e275a50f321e04b8ae")},{$set:{ "levelCustomList.1.state":2}})		# 把匹配数据的levelCustomlist的第二个字段(state) 的值改为 2

	db.oreseasons.update({"_id" : ObjectId("5ad227d8da0d2e0522930156")},{$unset:{"groups.0":''}},false, true)	# 把匹配数据的groups中第一个字段删除


# 数据库的备份以及恢复

	mongodump   -h  IP    -d  DBNAME    -o  dir	# 备份数据库

	mongorestore   -h  IP   -d    DBNAME     dir/DBNAME/	# 恢复数据库

