- Docker 启动
	- ```
	  docker pull oracleinanutshell/oracle-xe-11g
	  docker save -o oracle11gxe.tar oracleinanutshell/oracle-xe-11g:latest
	  docker load < oracle11gxe.tar
	  
	  docker run -d \
	  --name oracle11-xe \
	  -e ORACLE_ALLOW_REMOTE=true \
	  -v /x/data:/home/oracle/app/oracle/oradata \
	  -v /x/init:/docker-entrypoint-initdb.d \
	  -p 1521:1521 \
	  oracleinanutshell/oracle-xe-11g:latest
	  ```
	- 登录信息
		- ```
		  服务名/SID: XE
		  用户： system
		  密码： oracle
		  ```