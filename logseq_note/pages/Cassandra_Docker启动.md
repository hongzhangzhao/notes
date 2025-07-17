- docker 启动
	- ```
	  docker run --name cassandra -p 9042:9042 -e CASSANDRA_USER=cassandra -e CASSANDRA_PASSWORD=cassandra -v 目录:/var/lib/cassandra -d cassandra:5.0
	  ```
	- 连接信息
		- 账号： cassandra/cassandra
		- 默认库： system