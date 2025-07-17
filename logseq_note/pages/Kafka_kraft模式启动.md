- Kraft模式启动
	- 编辑配置文件
		- ```
		  cd ./kafka/config/kraft
		  cp server.properties server.properties.bak
		  
		  vi server.properties
		  
		  log.dirs=./kafka/kraft-combined-logs  # 实际使用绝对路径
		  advertised.listeners=PLAINTEXT://本机真实IP:9092  # 允许外部访问
		  ```
	- 生成UUID
		- ```
		  ./bin/kafka-storage.sh random-uuid  # 45FsNTJXSR-yQstzKxJpew
		  ./bin/kafka-storage.sh  format -t 45FsNTJXSR-yQstzKxJpew -c ./config/kraft/server.properties
		  ```
	- 启动
		- ```
		  ./bin/kafka-server-start.sh -daemon ./config/kraft/server.properties
		  ```