- Dokcer 启动
	- ```
	  docker pull postgres:11
	  docker save -o postgres.tar postgres:11
	  docker load < postgres.tar
	  
	  docker run -d --name postgres \
	  -e POSTGRES_PASSWORD=Abc1234% \
	  -e ALLOW_IP_RANGE=0.0.0.0/0 \
	  -v 目录:/var/lib/postgresql/data \
	  -v 目录:/docker-entrypoint-initdb.d \
	  -p 5432:5432 \
	  postgres:11
	  ```
	- 登录信息
		- ```
		  默认库: postgres
		  用户： postgres
		  密码： Abc1234%
		  ```