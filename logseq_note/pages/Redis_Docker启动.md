- Docker 启动
	- ```
	  docker run -d \
	  --name redis0 \
	  -v 文件:/usr/local/etc/redis/redis.conf \
	  -v 目录:/data \
	  -p 6379:6379 \
	  redis:7 \
	  redis-server /usr/local/etc/redis/redis.conf
	  ```
	- 配置文件
		- ```
		  daemonize no
		  pidfile /data/redis.pid
		  port 6379
		  bind 0.0.0.0
		  requirepass 密码
		  rdbcompression yes
		  dbfilename dump.rdb
		  dir /data
		  ```