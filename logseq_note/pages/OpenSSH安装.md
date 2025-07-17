- OpenSSH安装
	- 安装 telnet, 防止 ssh 失败
		- ```
		  sudo dnf -y install telnet
		  sudo systemctl enable telnet.socket
		  sudo systemctl start telnet.socket
		  sudo systemctl status telnet.socket
		  ```
	- 备份 ssh
		- ```
		  sudo mkdir /data/openssh_backup -p
		  sudo cp -ar /etc/ssh* /data/openssh_backup/
		  ```
	- 关闭安全检查
		- ```
		  sudo getenforce
		  sudo setenforce 0
		  sudo sed -i 's/^SELINUX=.*/SELINUX=disabled/' /etc/selinux/config
		  ```
	- 安装基础依赖
		- ```
		  sudo dnf -y install gcc make zlib zlib-devel openssl-devel
		  
		  # 看情况装不装 perl  openssl openssl-libs
		  ```
	- 下载与安装
		- ```
		  sudo tar -zxf openssh-9.8p1.tar.gz
		  cd openssh-9.8p1
		  sudo ./configure
		  sudo make && sudo make install
		  sudo systemctl restart sshd
		  
		  # 重新ssh登录, 查看ssh版本, ssh -V
		  ```