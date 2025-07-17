- 配置
	- 配置文件路径（docker内部）： /opt/cassandra/conf/cassandra.yaml
	- 开启用户认证
		- `sed -i 's/user_defined_functions_enabled: false/user_defined_functions_enabled: true/g' cassandra.yaml`
	- 开发自定义函数
		- `sed -i 's/authenticator: AllowAllAuthenticator/authenticator: PasswordAuthenticator/g' cassandra.yaml`
- docker启动
	- ```
	  docker run --name cassandra -p 9042:9042 -e CASSANDRA_USER=cassandra -e CASSANDRA_PASSWORD=cassandra -v 目录:/var/lib/cassandra -d cassandra:5.0
	  ```
	- 连接信息
		- ```
		  账号： cassandra/cassandra
		  默认库： system
		  ```