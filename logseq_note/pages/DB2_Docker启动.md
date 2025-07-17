- Docker 启动
	- ```
	  docker pull ibmcom/db2
	  
	  ---
	  docker run -d --name db2 --privileged=true -p 50000:50000 \
	  --ulimit nofile=65535:65535 --ulimit nproc=65535:65535 \
	  -e DB2INSTANCE=db2inst1 \
	  -e LICENSE=accept -e DB2INST1_PASSWORD=123456 \
	  -e DBNAME=testdb \
	  -v 目录:/database \
	  ibmcom/db2:11.5.8.0
	  ```
	- 登录信息
		- ```
		  DB: 见 DBNAME
		  User: 见 DB2INSTANCE
		  Password: 见 DB2INST1_PASSWORD
		  ```