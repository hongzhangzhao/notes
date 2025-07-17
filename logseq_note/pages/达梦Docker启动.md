- Docker 启动
	- ```
	  下载网址: https://eco.dameng.com/download/
	  安装教程: https://eco.dameng.com/document/dm/zh-cn/start/dm-install-docker.html
	  
	  ---
	  docker load -i dm8_20230808_rev197096_x86_rh6_64_single.tar
	  
	  docker run -d --name dm8_test --restart=always --privileged=true \
	  --ulimit nofile=65535:65535 --ulimit nproc=65535:65535 \
	  -e CASE_SENSITIVE=0 \
	  -e PAGE_SIZE=16 \
	  -e LD_LIBRARY_PATH=/opt/dmdbms/bin \
	  -e EXTENT_SIZE=32 \
	  -e BLANK_PAD_MODE=1 \
	  -e LOG_SIZE=1024 \
	  -e UNICODE_FLAG=1 \
	  -e LENGTH_IN_CHAR=1 \
	  -e INSTANCE_NAME=dm8_test \
	  -e TZ=Asia/Shanghai \
	  -v 目录:/opt/dmdbms/data \
	  -p 5236:5236 \
	  dm8_single:dm8_20230808_rev197096_x86_rh6_64
	  ```
	- 命令行登录
		- ```
		  # docker 内部
		  source /etc/profile
		  cd /opt/dmdbms/bin
		  ./disql SYSDBA/SYSDBA001
		  ```
	- 注意事项
		- ```
		  1.如果使用 docker 容器里面的 disql，进入容器后，先执行 source /etc/profile 防止中文乱码。
		  2.新版本 Docker 镜像中数据库默认用户名/密码为 SYSDBA/SYSDBA001。
		  ```