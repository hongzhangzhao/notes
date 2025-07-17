- Docker 启动
	- ```
	  docker run -d --name mysql5.7 \
	  --ulimit nofile=65535:65535 --ulimit nproc=65535:65535 \
	  -e MYSQL_ROOT_PASSWORD=123456 \
	  -e TZ=Asia/Shanghai \
	  -v 文件:/etc/my.cnf \
	  -v 目录:/var/lib/mysql \
	  -v 目录:/docker-entrypoint-initdb.d/ \
	  -p 3306:3306 mysql:5.7
	  ```
	- 连接信息
		- 账户 root/123456
	- 配置文件 my.cnf
		- ```
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