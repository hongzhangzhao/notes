- Docker 启动
	- ```
	  docker run -d \
	  --name nginx \
	  --ulimit nofile=65535:65535 --ulimit nproc=65535:65535 --privileged=true \
	  -v /opt/nginx/config/default.conf:/etc/nginx/conf.d/default.conf \
	  -v /opt/nginx/data/log:/var/log/nginx \
	  -v /opt/nginx/data/dist:/usr/local/nginx/html \
	  -p 80:80 -p 443:443 \
	  nginx:1.27.3
	  ```
- 配置文件 default.conf
	- ```
	  server {
	      listen       80;
	      server_name  localhost;
	  
	      client_max_body_size 1024m;
	  
	      location / {
	          root   /usr/local/nginx/html;
	          index  index.html index.htm;
	          try_files $uri $uri/ /index.html;
	      }
	  
	      location  ^~ /api/ {
	          proxy_pass http://127.0.0.1:5000/;
	          proxy_set_header Host   $host;
	          proxy_set_header X-Real-IP      $remote_addr;
	          proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
	          proxy_read_timeout 600;
	      }
	  
	      location /assets/ {
	          alias /data/upload/2/;
	      }
	  
	      error_page   500 502 503 504  /50x.html;
	      location = /50x.html {
	          root   html;
	      }
	  }
	  ```