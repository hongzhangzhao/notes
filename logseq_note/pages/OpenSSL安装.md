- OpenSSL 安装
	- 提示
		- libldap.so库会依赖OpenSSL, 如果编译时不开启'enable-md2', libldap无法使用, 有很多系统级别的命令都依赖这个库
	- 编译与安装
		- ```
		  sudo tar -zxf openssl-1.1.1w.tar.gz  # 去github下载
		  cd openssl-1.1.1w
		  # sudo ./config --prefix=/usr/local/openssl_1
		  sudo ./config enable-md2 --prefix=/usr/local/openssl_1
		  sudo make
		  sudo make install
		  
		  # 备份原有ssl
		  sudo mv /usr/bin/openssl /usr/bin/openssl.bak
		  sudo mv /usr/include/openssl /usr/include/openssl.bak
		  
		  # 替换新的ssl
		  sudo ln -s /usr/local/openssl_1/bin/openssl /usr/bin/openssl
		  sudo ln -s /usr/local/openssl_1/include/openssl /usr/include/openssl
		  ```
	- 更新动态库
		- ```
		  sudo vi /etc/ld.so.conf  #编辑
		  /usr/local/openssl_1/lib   # 添加这一行
		  sudo ldconfig    # 退出编辑器, 使生效
		  
		  openssl version  # 验证是否更新成功
		  
		  ---
		  # 也可以将lib目录写到一个自定义的文件中, /etc/ld.so.conf会读取这个文件
		  cd /etc/ld.so.conf.d
		  sudo touch openssl-1.1.1w.conf
		  ```
	- 让 ssl 使用新版本
		- ```
		  # sudo ldconfig  # 这一步的动作没有生效
		  
		  ldconfig -p | grep ssl  # 查看动态库
		  ldd /usr/local/openssl_1/bin/openssl  # 查看使用的动态库
		  
		  # 替换新的链接库, 替换前最好做个备份
		  cd /usr/local/openssl_1/lib
		  sudo cp libssl.so.1.1 /usr/lib64/
		  sudo cp libcrypto.so.1.1 /usr/lib64/
		  ```