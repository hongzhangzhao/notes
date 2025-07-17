- Docker 启动
	- ```
	  docker pull mcr.microsoft.com/mssql/server:2019-latest
	  docker save -o sqlserver2019.tar mcr.microsoft.com/mssql/server:2019-latest
	  docker load < sqlserver2019.tar
	  
	  firewall-cmd --add-port=1433/tcp --permanent
	  firewall-cmd --reload
	  
	  docker run -d \
	  --name sql-server-2019 \
	  -e ACCEPT_EULA=Y \
	  -e MSSQL_PID=Express \  # 不设置这个也行
	  -e MSSQL_SA_PASSWORD=SA@12345 \
	  -v 目录:/var/opt/mssql \  # 外挂数据目录需要赋予777权限
	  -p 1433:1433 \
	  mcr.microsoft.com/mssql/server:2019-latest
	  ```
	- 登录信息
		- ```
		  用户： SA
		  密码： SA@12345
		  ```
	- 命令行工具（Docker内部）
		- ```
		  /opt/mssql-tools/bin/sqlcmd
		  ```