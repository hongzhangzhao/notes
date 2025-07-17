- fnm版本管理
	- ```
	  https://github.com/Schniz/fnm 下载解压后, 配置环境变量
	  notepad $profile  # 用这个命令查看环境变量文件
	  $profile  # 如果没有, 打印这个变量, 输出文件路径
	  C:\Users\lenovo\Documents\WindowsPowerShell\Microsoft.PowerShell_profile.ps1  # 新建文件
	  fnm env --use-on-cd --shell power-shell | Out-String | Invoke-Expression  # 添加到文件中
	  fnm list  # 查看下载了哪些版本
	  fnm --version  # 查看fnm的版本
	  fnm -h  # 帮助信息
	  fnm list-remote  # 查看有哪些版本可下载
	  fnm install <版本> --node-dist-mirror https://npmmirror.com/mirrors/node  # (版本不要v, 可以不用指定小版本)
	  fnm use <版本>   # 切换版本
	  fnm default <版本>  # 设置默认版本
	  fnm uninstall <版本>  # 卸载版本
	  fnm env  # 查看环境变量
	  ```
	- 镜像配置
		- ```
		  https://npmmirror.com/  # 查看镜像地址
		  
		  # 在windows中新建环境变量, fnm就会使用这个镜像地址来下载
		  FNM_NODE_DIST_MIRROR  # 变量名
		  https://npmmirror.com/mirrors/node  # 变量值
		  ```
- 错误
	- 无法加载文件
		- ```
		  # 访问地址, 找到解决命令, 并执行
		  - https:/go.microsoft.com/fwlink/?LinkID=135170  # 网址
		  - Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser  # 命令
		  ```