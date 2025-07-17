- Docker 启动
	- ```
	  docker pull mongo:4.4.0
	  docker save -o mongo.tar mongo:4.4.0
	  docker load < mongo.tar
	  
	  ---
	  无密码登录启动：
	  docker run -d --name mongodb \
	  -v /x/mongodb:/data/db \
	  -p 27017:27017 \
	  mongo:4.4.0
	  
	  ---
	  有密码登录启动：
	  docker run -d --name mongodb \
	  -e MONGO_INITDB_ROOT_USERNAME=admin \
	  -e MONGO_INITDB_ROOT_PASSWORD=123456 \
	  -v 目录:/data/db \
	  -v 目录:/docker-entrypoint-initdb.d \
	  -p 27017:27017 \
	  mongo:4.4.0 mongod --auth
	  ```