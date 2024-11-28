


# 1 MySQL

启动 mysql
```
docker run -d \
--name mysql5.7 \
-e MYSQL_ROOT_PASSWORD=123456 \
-e TZ=Asia/Shanghai \
-v /x/my.cnf:/etc/my.cnf \
-v /x/data:/var/lib/mysql \
-v /x/init/:/docker-entrypoint-initdb.d/ \
-p 3309:3306 \
mysql:5.7
```
docker的mysql会有占用内存过大的问题（16G），run的时候需要设置ulimits参数，配置例子是基于compose的yaml文件
```
ulimits:
    nproc: 65535
    nofile:
        soft: 65535
        hard: 65535

--ulimit nofile=65535:65535 --ulimit nproc=65535:65535
```

配置文件 my.cnf
```
[mysqld]
character-set-server=utf8
collation-server=utf8_general_ci
skip-character-set-client-handshake=1
max_allowed_packet=256M
[client]
default-character-set=utf8
[mysql]
default-character-set=utf8
```

# 2 Oracle11g

获取镜像:
```
docker pull oracleinanutshell/oracle-xe-11g
docker save -o oracle11gxe.tar oracleinanutshell/oracle-xe-11g:latest
docker load < oracle11gxe.tar
```


启动 oracle
```
docker run -d \
--name oracle11-xe \
-e ORACLE_ALLOW_REMOTE=true \
-v /x/data:/home/oracle/app/oracle/oradata \
-v /x/init:/docker-entrypoint-initdb.d \
-p 1521:1521 \
oracleinanutshell/oracle-xe-11g:latest
```

登录信息
```
服务名/SID: XE
用户： system
密码： oracle
```

操作数据库: 模式代表一个用户也代表一个库
```
创建用户（模式）： CREATE USER your_username IDENTIFIED BY your_password;
赋予权限： GRANT CONNECT, RESOURCE,DBA TO your_username;
删除模式： DROP USER your_username CASCADE;
```
# 3 sqlserver2019

获取镜像
```
下载镜像： docker pull mcr.microsoft.com/mssql/server:2019-latest
保存镜像： docker save -o sqlserver2019.tar mcr.microsoft.com/mssql/server:2019-latest
加载镜像： docker load < sqlserver2019.tar
```

启动sqlserver，sqlserver容器内的用户不是root，所以挂载的数据目录需要赋予权限
```
chmod 777 /x/data
firewall-cmd --add-port=1433/tcp --permanent
firewall-cmd --reload

docker run -d \
--name sql-server-2019 \
-e ACCEPT_EULA=Y \
-e MSSQL_PID=Express \  # 不设置这个也行
-e MSSQL_SA_PASSWORD=SA@12345 \
-v /x/data:/var/opt/mssql \
-p 1433:1433 \
mcr.microsoft.com/mssql/server:2019-latest
```

登录信息
```
用户： SA
密码： 见 MSSQL_SA_PASSWORD
```

操作数据库
```
/opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P "SA@12345"  # 登录数据库

Select @@version
GO
CREATE DATABASE TestDB;
SELECT Name from sys.databases;
GO
USE TestDB;
CREATE TABLE dbo.Inventory (id INT, name NVARCHAR(50),quantity INT );
INSERT INTO dbo.Inventory VALUES (1, 'banana', 150);
INSERT INTO dbo.Inventory VALUES (2, 'orange', 154);
GO
SELECT * FROM dbo.Inventory;
GO
QUIT

删除数据库：
DROP DATABASE <db_name>;
```

初始化数据
```
# 将初始化数据复制到容器中（宿主机）
docker cp /x/init/scan-sqlserver.sql 容器ID:/opt/scan-sqlserver.sql

进入容器，执行初始化（容器内）
/opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P "你的密码" -i /opt/scan-sqlserver.sql
```


# 4 PostgreSQL 11

获取镜像
```
下载镜像： docker pull postgres:11
保存镜像： docker save -o postgres.tar postgres:11
加载镜像： docker load < postgres.tar
```

启动postgre
```
docker run -d --name postgres \
-e POSTGRES_PASSWORD=Abc1234% \
-e ALLOW_IP_RANGE=0.0.0.0/0 \
-v /x/data:/var/lib/postgresql/data \
-v /x/init:/docker-entrypoint-initdb.d \
-p 5432:5432 \
postgres:11
```

登录信息
```
Database: postgres
用户： postgres
密码： 见 POSTGRES_PASSWORD
```

操作数据库
```
登录： psql -U your_username -d your_database
创建模式： CREATE SCHEMA your_schema;
查询模式： SELECT schema_name FROM information_schema.schemata;
删除模式： DROP SCHEMA your_schema CASCADE;
```

# 5 mongo 4.0

获取镜像
```
下载镜像： docker pull mongo:4.4.0
保存镜像： docker save -o mongo.tar mongo:4.4.0
加载镜像： docker load < mongo.tar
```

启动 mongo
```
无密码登录启动：
docker run -d --name mongodb \
-v /x/mongodb:/data/db \
-p 27017:27017 \
mongo:4.4.0

有密码登录启动：
docker run -d --name mongodb \
-e MONGO_INITDB_ROOT_USERNAME=admin \
-e MONGO_INITDB_ROOT_PASSWORD=123456 \
-v /x/data:/data/db \
-v /x/init:/docker-entrypoint-initdb.d \
-p 27017:27017 \
mongo:4.4.0 mongod --auth
```

登录信息
```
用户： 见 MONGO_INITDB_ROOT_USERNAME
密码： 见 MONGO_INITDB_ROOT_PASSWORD
```

# 6 Redis

启动
```
docker run -d \
--name redis0 \
-v /x/redis.conf:/usr/local/etc/redis/redis.conf \
-v /x/data:/data \
-p 6379:6379 \
zhaohz/redis:7 \
redis-server /usr/local/etc/redis/redis.conf
```

配置文件 redis.conf
```
daemonize no
pidfile /data/redis.pid
port 6379
bind 0.0.0.0
requirepass rhzzprivacy
rdbcompression yes
dbfilename dump.rdb
dir /data
```


# 7 Gbase

获取镜像
```
下载镜像： docker pull shihd/gbase8a:1.0
```

启动镜像
```
docker run -d -p5258:5258 shihd/gbase8a:1.0


docker run -d --name gbase8a --privileged=true \
--ulimit nofile=65535:65535 --ulimit nproc=65535:65535 \
-v /volumes/gbase/data:/home/gbase/GBase/userdata \
-p 5258:5258 shihd/gbase8a:1.0


docker run -d --name gbase8a --privileged=true \
--ulimit nofile=65535:65535 --ulimit nproc=65535:65535 \
-p 5258:5258 shihd/gbase8a:1.0
```

操作数据库
```
[gbase@cc3daf935596 ~]$ gbase -V
gbase ver 8.6.2.43-R7-free.110605, for unknown-linux-gnu (x86_64) using readline 6.3
```

登录信息
```
DB: gbase
User: root
Password: root
Port: 5258
```


# 8 DB2

下载镜像： docker pull ibmcom/db2

启动容器
```
docker run -d --name db2 --privileged=true -p 50000:50000 \
--ulimit nofile=65535:65535 --ulimit nproc=65535:65535 \
-e DB2INSTANCE=db2inst1 \
-e LICENSE=accept -e DB2INST1_PASSWORD=123456 \
-e DBNAME=testdb \
-v /volumes/db2/data:/database \
ibmcom/db2:11.5.8.0
```

登录信息
```
DB: 见 DBNAME
User: 见 DB2INSTANCE
Password: 见 DB2INST1_PASSWORD
```

导入数据
```
进入容器： xxx

切换账号： su - db2inst1

创建数据库： db2 create database indust
连接数据库：db2 connect to indust

创建模式： db2 create schema test001 authorization indust

复制sql文件到容器中： docker cp scan-db2.sql db2:/database/config/db2inst1/scan-db2.sql

# 导入数据
db2 connect to indust
db2 set current schema test001
db2 -tvf /database/config/db2inst1/scan-db2.sql
db2 commit work
db2 terminate
```


# 9 达梦

下载网址: https://eco.dameng.com/download/

安装教程: https://eco.dameng.com/document/dm/zh-cn/start/dm-install-docker.html

启动容器
```
docker load -i dm8_20230808_rev197096_x86_rh6_64_single.tar

docker run -d --name dm8_test --restart=always --privileged=true \
--ulimit nofile=65535:65535 --ulimit nproc=65535:65535 \
-e CASE_SENSITIVE=0 \
-e PAGE_SIZE=16 \
-e LD_LIBRARY_PATH=/opt/dmdbms/bin \
-e EXTENT_SIZE=32 \
-e BLANK_PAD_MODE=1 \
-e LOG_SIZE=1024 \
-e UNICODE_FLAG=1 \
-e LENGTH_IN_CHAR=1 \
-e INSTANCE_NAME=dm8_test \
-e TZ=Asia/Shanghai \
-v /opt/dm8_test:/opt/dmdbms/data \
-p 5236:5236 \
dm8_single:dm8_20230808_rev197096_x86_rh6_64
```

注意事项
```
1.如果使用 docker 容器里面的 disql，进入容器后，先执行 source /etc/profile 防止中文乱码。
2.新版本 Docker 镜像中数据库默认用户名/密码为 SYSDBA/SYSDBA001。
```

命令行登录数据库
```
docker exec -it dm8_test bash
source /etc/profile
cd /opt/dmdbms/bin
./disql SYSDBA/SYSDBA001
```

springboot 配置
```
<dependency>
    <groupId>com.dm</groupId>
    <artifactId>dmjdbc8</artifactId>
    <version>1.8.0</version>
</dependency>

---

spring:
    datasource:
        master:
            driver-class-name: dm.jdbc.driver.DmDriver
            url: jdbc:dm://192.168.1.71:30236
            username: SYSDBA
            password: SYSDBA001
```

dbeaver连接
```
类名: dm.jdbc.driver.DmDriver
URL模板: jdbc:dm://{host}:{port}
默认端口: 5236
```

导入数据

```
前提条件： sql文件复制到容器中

1、进入容器
2、cd /opt/dmdbms/bin
3、执行  ./disql  ， 输入用户和密码
4、start + 脚本绝对路径
```





