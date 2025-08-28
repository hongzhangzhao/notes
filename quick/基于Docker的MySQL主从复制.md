#mysql/主从复制 #docker

# 基于Docker的MySQL主从复制


简介
- 实验基于mysql-5.7.44，实现方式 File + Position
- mysql-8.0可以使用GTID方式
- File + Position 和 GTID 之间有什么区别

---

配置文件

- 主库配置
```sh
[mysqld]
server-id       = 1
log-bin         = mysql-bin
binlog_format   = mixed
expire_logs_days= 7
max_binlog_size = 100M
# 可选：跳过一些复制错误（如1062主键重复错误）
slave_skip_errors = 1062

character-set-server = utf8mb4
collation-server = utf8mb4_unicode_ci

[client]
default-character-set = utf8mb4

[mysql]
default-character-set = utf8mb4

```

- 从库配置
```sh
[mysqld]
server-id       = 2
relay-log       = mysql-relay-bin
# log-slave-updates = 1
read_only       = 1

character-set-server = utf8mb4
collation-server = utf8mb4_unicode_ci

[client]
default-character-set = utf8mb4

[mysql]
default-character-set = utf8mb4

```

---

主库创建复制账号

```sh
docker exec -it <主库> mysql -uroot -p<密码>

CREATE USER 'repl'@'%' IDENTIFIED BY 'Repl@1234';
GRANT REPLICATION SLAVE ON *.* TO 'repl'@'%';
FLUSH PRIVILEGES;
```

---

查看 File 与 Position

```
FLUSH TABLES WITH READ LOCK;
SHOW MASTER STATUS;
UNLOCK TABLES;
```

---

从库关联主库

```sh
docker exec -it <从库> mysql -uroot -p<密码>

CHANGE MASTER TO
  MASTER_HOST='ip',
  MASTER_USER='repl',
  MASTER_PASSWORD='Repl@1234',
  MASTER_PORT=3306,
  MASTER_LOG_FILE='mysql-bin.000003',
  MASTER_LOG_POS=154,
  MASTER_SSL=0;

```

---

启动从库复制（登录从库）

```
START SLAVE;
```


---

查看配置是否生效

```
SHOW SLAVE STATUS\G
```

关注这两个参数是否为 yes
- Slave_IO_Running
- Slave_SQL_Running

---

验证数据复制
- 主库插入数据，查看从库是否存在同样的数据

---

容器启动

```sh
docker run -d \
--name mysql \
--ulimit nofile=65535:65535 --ulimit nproc=65535:65535 \
-e MYSQL_ROOT_PASSWORD=123456 \
-e TZ=Asia/Shanghai \
-v /root/mysql/my.cnf:/etc/mysql/conf.d/my.cnf \
-v /root/mysql/data:/var/lib/mysql \
-p 43306:3306 \
mysql:5.7.44
```
