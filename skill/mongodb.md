
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

