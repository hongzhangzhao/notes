- Docker 启动
	- ```
	  docker pull shihd/gbase8a:1.0
	  
	  ---
	  docker run -d --name gbase8a --privileged=true \
	  --ulimit nofile=65535:65535 --ulimit nproc=65535:65535 \
	  -v 目录:/home/gbase/GBase/userdata \
	  -p 5258:5258 shihd/gbase8a:1.0
	  ```
	- 登录信息
		- ```
		  DB: gbase
		  User: root
		  Password: root
		  Port: 5258
		  ```