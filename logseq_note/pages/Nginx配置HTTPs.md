- 配置HTTPs
	- 生成证书
		- 不要使用弱哈希算法, 否则会导致该漏洞: CVE-2004-2761
		- `openssl req -x509 -nodes -newkey rsa:2048 -keyout nginx-selfsigned.key -out nginx-selfsigned.crt -sha256 -days 365`
		- ```
		  # 这是表示组织所在国家的两个字母的代码。
		  Country Name (2 letter code) [AU]:US
		  
		  # 这是组织所在的城市。
		  Locality Name (eg, city) []:New York City
		  
		  # 这是颁发证书的组织或公司的法律名称。
		  Organization Name (eg, company) [Internet Widgits Pty Ltd]:Bouncy Castles, Inc.
		  
		  # 此字段允许您指定组织内的特定部门或单元。
		  Organizational Unit Name (eg, section) []:Ministry of Water Slides
		  
		  # 这是将使用证书的服务器的完全限定域名（FQDN
		  Common Name (e.g. server FQDN or YOUR name) []:server_IP_address
		  
		  # 这是与负责服务器或证书的组织或个人相关的电子邮件地址。
		  Email Address []:admin@your_domain.com
		  ```
	- nginx.conf 配置
		- ```
		  server {
		  
		  listen 80 default_server;
		  
		  # server_name localhost;
		  
		  return 301 https://$host$request_uri;
		  
		  # rewrite ^(.*)$ https://$host$1 permanent;
		  
		  # rewrite ^(.*)$ https://${server_name}$1 permanent;
		  
		  }
		  
		  server {
		  
		  listen 443 ssl;
		  
		  # server_name _;
		  
		  # server_name localhost;
		  
		  client_max_body_size 500m;
		  
		  ssl_certificate /etc/nginx/cert/server.crt;
		  
		  ssl_certificate_key /etc/nginx/cert/server.key;
		  
		  location /api/ {
		  
		      proxy_set_header Upgrade $http_upgrade;
		      
		      proxy_set_header Connection $connection_upgrade;
		      
		      proxy_pass http://127.0.0.1:8080/api/;
		      
		      proxy_set_header Host $host;
		      
		      proxy_set_header X-Real-IP $remote_addr;
		      
		      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
		    
		    }
		  }
		  ```
	- 参考
		- https://blog.csdn.net/qq_43455906/article/details/132142712
	- 其它
		- 注意: 在配置完成后, 验证是否能通过80跳转的443, 中间通过301重定向, 但是死活跳转不过去,最后找到原因, 是因为主机防火墙没有关闭, 301 端口被拦截了, 所以浏览器一直跳不过去 443