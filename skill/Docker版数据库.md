
[[2024-03-20]]

# MySQL

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

# Oracle11g

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

# sqlserver2019

启动sqlserver，sqlserver容器内的用户不是root，所以挂载的数据目录需要赋予权限
```
chmod 777 /x/data
firewall-cmd --add-port=1433/tcp --permanent
firewall-cmd --reload

docker run -d \
--name sql-server-2019 \
-e ACCEPT_EULA=Y \
-e MSSQL_PID=Express \
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

初始化数据
```
将初始化数据复制到容器中（宿主机）
docker cp /x/init/scan-sqlserver.sql 容器ID:/opt/scan-sqlserver.sql

进入容器，执行初始化（容器内）
/opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P "YourStrong!Passw0rd" -i /opt/scan-sqlserver.sql
```


# PostgreSQL 11

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
用户： postgres
密码： 见 POSTGRES_PASSWORD
```

# mongo 4.0

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

# Redis

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