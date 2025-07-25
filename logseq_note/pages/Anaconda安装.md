- 安装
	- ```
	  mkdir -p /miniconda3
	  
	  wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O /miniconda3/miniconda.sh
	  
	  bash /miniconda3/miniconda.sh -b -u -p /miniconda3
	  
	  rm -rf /miniconda3/miniconda.sh
	  ```
	- 初始化
		- ```
		  /miniconda3/bin/conda init bash
		  ```
	- base环境
		- ```
		  安装完conda后，shell默认会进入base环境
		  
		  设置默认不进入base： conda config --set auto_activate_base false
		  ```
	- 换源
		- ```
		  vi ~/.condarc
		  
		  ---
		  channels:
		    - defaults
		  show_channel_urls: true
		  default_channels:
		    - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
		    - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/r
		    - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/msys2
		  custom_channels:
		    conda-forge: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
		    msys2: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
		    bioconda: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
		    menpo: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
		    pytorch: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
		    pytorch-lts: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
		    simpleitk: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
		  ```
	- 创建虚拟环境
		- ```
		  conda create -n xxx_env python=3.10
		  
		  conda activate xxx_env
		  ```
- 迁移工具包
	- 安装工具包
		- ```
		  conda install -c conda-forge conda-pack
		  ```
	- 打包虚拟环境
		- ```
		  # conda pack -n 环境名 -o 环境名.tar.gz
		  conda pack -n logad -o logad.tar.gz
		  ```