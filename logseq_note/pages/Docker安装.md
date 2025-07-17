- 安装
	- ```
	  下载 docker 二进制
	  下载 docker-compose github
	  
	  ---
	  chmod +x docker/*
	  cp docker/* /usr/bin/
	  cp docker.service /etc/systemd/system/
	  chmod +x /etc/systemd/system/docker.service
	  
	  systemctl daemon-reload
	  systemctl start docker
	  systemctl enable docker.service
	  
	  echo 'docker installed'
	  
	  cp docker-compose  /usr/bin/docker-compose 
	  chmod +x /usr/bin/docker-compose
	  echo 'docker-compose installed'
	  ```
	- 配置自启服务
		- ```
		  [Unit]
		  Description=Docker Application Container Engine
		  Documentation=https://docs.docker.com
		  After=network-online.target firewalld.service
		  Wants=network-online.target
		  [Service]
		  Type=notify
		  # the default is not to use systemd for cgroups because the delegate issues still
		  # exists and systemd currently does not support the cgroup feature set required
		  # for containers run by docker
		  ExecStart=/usr/bin/dockerd
		  ExecReload=/bin/kill -s HUP $MAINPID
		  # Having non-zero Limit*s causes performance problems due to accounting overhead
		  # in the kernel. We recommend using cgroups to do container-local accounting.
		  LimitNOFILE=infinity
		  LimitNPROC=infinity
		  LimitCORE=infinity
		  # Uncomment TasksMax if your systemd version supports it.
		  # Only systemd 226 and above support this version.
		  #TasksMax=infinity
		  TimeoutStartSec=0
		  # set delegate yes so that systemd does not reset the cgroups of docker containers
		  Delegate=yes
		  # kill only the docker process, not all processes in the cgroup
		  KillMode=process
		  # restart the docker process if it exits prematurely
		  Restart=on-failure
		  StartLimitBurst=3
		  StartLimitInterval=60s
		   
		  [Install]
		  WantedBy=multi-user.target
		  ```
	- 配置源
		- ```
		  vi /etc/docker/daemon.json （没有这个配置文件就新建一个）
		  
		  ---
		  {
		    "registry-mirrors": [
		      "https://registry.cn-hangzhou.aliyuncs.com",
		      "https://registry.docker-cn.com",
		      "http://hub-mirror.c.163.com",
		      "https://docker.mirrors.ustc.edu.cn"
		    ]
		  }
		  
		  ---
		  systemctl daemon-reload
		  systemctl restart docker
		  ```