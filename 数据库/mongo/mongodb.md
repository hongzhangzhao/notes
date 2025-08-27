
- bin/mongod -f conf/mongo.conf  # 启动
- bin/mongo  --port 27018  # 指定端口连接
- bin/mongo --port 27018 -u "admin" -p "123456" --authenticationDatabase "admin"  # 认证连接

# 1 远程连接

```shell
mongo --host ip地址 --port 远程端口
mongo -u 用户名 -p 密码 --authenticationDatabase 数据库名 --host IP地址 --port 远程端口
```

# 2 创建管理员账户

```json
use admin
db.createUser({ user: "admin", pwd: "123456", roles: [{ role: "root", db: "admin" }] })
```

## 2.1 创建数据库和普通用户

```json
use test
db.createUser({user: "test",pwd: "123456",roles: [{role: "readWrite", db: "test"}]})
```

- 创建表的同时插入一条数据
```json
db.表名.insertOne({ name: "John", age: "18", sex: "man" })
```

# 3 其它

- db.getUsers()  # 查看用户
- db.version()  # 查看版本
- db.表名.find().limit(5)  # 查询表数据


# 4 导入数据

导入命令
```bash
mongoimport --host 127.0.0.1 --port 27018 --username 用户名 --db 库名 --collection 表名 --file 要导入的文件 --jsonArray
```

# docker 运行


创建挂载路径
- mkdir -p /opt/mongo/conf/
- mkdir -p /opt/mongo/data/
- mkdir -p /opt/mongo/logs/

编辑配置文件

vi /opt/mongo/conf/mongod.conf

配置需要给权限 chmod 644 /opt/mongo/conf/mongod.conf

yaml格式的配置文件
```yaml
# mongod.conf

# for documentation of all options, see:
#   http://docs.mongodb.org/manual/reference/configuration-options/

# Where and how to store data.
storage:
  dbPath: /var/lib/mongodb
  journal:
    enabled: true
#  engine:
#  mmapv1:
#  wiredTiger:

# where to write logging data.
systemLog:
  destination: file
  logAppend: true
  path: /var/log/mongodb/mongod.log

# network interfaces
net:
  port: 27017
  bindIp: 0.0.0.0


# how the process runs
processManagement:
  timeZoneInfo: /usr/share/zoneinfo

#security:
security:
  authorization: disabled  # enabled or disabled

#operationProfiling:

#replication:

#sharding:

## Enterprise-Only Options:

#auditLog:

#snmp:
```


创建日志文件
- touch /opt/mongo/logs/mongod.log
- chmod 777 /opt/mongo/logs/mongod.log


```sh
docker run -d --name mongo \
--privileged \
-v /opt/mongo/data:/var/lib/mongodb \
-v /opt/mongo/conf/mongod.conf:/etc/mongod.conf \
-v /opt/mongo/logs:/var/log/mongodb  \
-p 27017:27017 \
mongo:4.4.29 --config /etc/mongod.conf
```

进入容器中设置admin密码

docker exec -it mongo /bin/bash

```
mongo  # 进入mongo

##切换到admin库##
> use admin

##创建账号/密码##
db.createUser({ user: 'admin', pwd: 'admin', roles: [ { role: "userAdminAnyDatabase", db: "admin" } ] });
```
