- 编译安装
	- ```
	  mkdir -p /usr/local/nginx
	  cd /usr/local/nginx
	  
	  wget -c http://iso.sqlfans.cn/linux/zlib-1.2.11.tar.gz
	  wget -c http://iso.sqlfans.cn/linux/pcre-8.44.tar.gz
	  wget -c http://iso.sqlfans.cn/linux/openssl-1.1.1g.tar.gz
	  wget -c http://iso.sqlfans.cn/linux/nginx-1.25.0.tar.gz
	  
	  
	  wget https://www.openssl.org/source/openssl-1.0.2s.tar.gz
	  wget https://ftp.pcre.org/pub/pcre/pcre-8.43.tar.gz
	  wget https://zlib.net/zlib-1.2.11.tar.gz
	  
	  
	  tar -zxvf openssl-1.1.1g.tar.gz
	  tar -zxvf pcre-8.44.tar.gz
	  tar -zxvf zlib-1.2.11.tar.gz
	  
	  tar -zxvf nginx-1.25.0.tar.gz
	  cd /usr/local/nginx/nginx-1.25.0
	  
	  
	  apt-get install gcc make libpcre3 libpcre3-dev zlib1g-dev g++
	  
	  
	  ./configure \
	     --with-openssl=../openssl-1.1.1g \
	     --with-pcre=../pcre-8.44 \
	     --with-zlib=../zlib-1.2.11 \
	     --with-pcre-jit --user=root \
	     --prefix=/usr/local/nginx \
	     --with-http_ssl_module \
	     --with-http_v2_module
	  
	  
	  make && make install
	  
	  
	  
	  删除无用文件：
	  
	  
	  rm -rf openssl-1.1.1g.tar.gz openssl-1.1.1g
	  rm -rf pcre-8.44.tar.gz pcre-8.44
	  rm -rf zlib-1.2.11.tar.gz zlib-1.2.11
	  rm -rf nginx-1.25.0.tar.gz nginx-1.25.0
	  
	  
	  打包： tar -czvf nginx-green.tar.gz nginx/
	  
	  
	  /usr/local/nginx/sbin/nginx            	-- 启动
	  /usr/local/nginx/sbin/nginx -s stop		-- 快速停止
	  /usr/local/nginx/sbin/nginx -s quit		-- 优雅关闭,在退出前完成已经接受的连接请求
	  /usr/local/nginx/sbin/nginx -s reload	-- 重新加载配置
	  /usr/local/nginx/sbin/nginx -v 			-- 测试是否安装成功
	  
	  
	  
	  nginx: /usr/sbin/nginx /usr/lib/nginx /etc/nginx /usr/share/nginx /usr/local/openresty/nginx/sbin/nginx
	  
	  
	  cp /usr/sbin/nginx /usr/sbin/nginx.bak
	  rm -rf /usr/sbin/nginx
	  ln -s /usr/local/nginx/sbin/nginx /usr/sbin/nginx
	  ```