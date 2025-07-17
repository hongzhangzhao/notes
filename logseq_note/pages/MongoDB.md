- Linux二进制安装
  collapsed:: true
	- 下载安装包：mongodb-linux-x86_64-rhel70-4.4.28.tgz
	- ```
	  #!/bin/bash
	  
	  CUR_DIR=$(dirname "$(readlink -f "$0")")
	  echo -e "mongo dir: $CUR_DIR"
	  
	  cd $CUR_DIR
	  
	  tar -zxf mongodb-linux-x86_64-rhel70-4.4.28.tgz
	  mv mongodb-linux-x86_64-rhel70-4.4.28 mongodb
	  
	  cd mongodb
	  mkdir data
	  mkdir logs
	  mkdir conf
	  
	  cd logs
	  touch mongo.log
	  cd -
	  
	  cd conf
	  touch mongo.conf
	  
	  echo $"dbpath=$CUR_DIR/mongodb/data
	  logpath=$CUR_DIR/mongodb/logs/mongo.log
	  logappend=true
	  journal=true
	  quiet=true
	  port=27018
	  bind_ip=0.0.0.0
	  fork=true
	  auth=true  # 启用认证
	  " > mongo.conf
	  
	  cd -
	  
	  # bin/mongod -f conf/mongo.conf  # 启动 mongo
	  ```
- 连接mongo
  collapsed:: true
	- ```
	  bin/mongo --port 27018  # 指定端口连接
	  bin/mongo --port 27018 -u "dsp" -p "123456" --authenticationDatabase "dsp"  # 认证连接
	  bin/mongo --port 27018 -u "admin" -p "123456" --authenticationDatabase "admin"  # 认证连接
	  
	  # 远程连接
	  bin/mongo --host ip地址 --port 远程端口
	  bin/mongo -u 用户名 -p 密码 --authenticationDatabase 数据库名 --host IP地址 --port 远程端口
	  ```
- 创建用户
  collapsed:: true
	- ```
	  # 创建管理员用户
	  use admin
	  db.createUser({ user: "admin", pwd: "123456", roles: [{ role: "root", db: "admin" }] })
	  
	  #创建普通用户
	  use test
	  db.createUser({user: "test",pwd: "123456",roles: [{role: "readWrite", db: "test"}]})
	  
	  use dsp
	  db.createUser({user: "dsp",pwd: "123456",roles: [{ role: "readWrite", db: "dsp" }]}
	  )
	  ```
- 基本使用
  collapsed:: true
	- ```
	  db.表名.insertOne({ name: "John", age: "18", sex: "man" })  # 插入数据  
	  db.getUsers()  # 查看用户
	  db.version()  # 查看版本
	  db.表名.find().limit(5)  # 查询表数据
	  ```
- 数据导入导出
  collapsed:: true
	- ```
	  # 导入数据
	  mongoimport --host 127.0.0.1 --port 27018 --username 用户名 --db 库名 --collection 表名 --file 要导入的文件 --jsonArray
	  ```