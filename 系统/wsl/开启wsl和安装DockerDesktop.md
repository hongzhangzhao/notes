

```sh
# 启用 WSL 和虚拟机平台，执行完重启一下
dism.exe /online /enable-feature /featurename:Microsoft-Hyper-V /all /norestart
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart

# 安装 WSL 2 和默认 Ubuntu 发行版
wsl --install

# 设置默认使用 WSL 2
wsl --set-default-version 2
```

wsl --install 会自动完成一切（执行多个步骤），还会下载一个发行版
- 但是它有时下载会很慢，这个时候可以选择手动安装
- 下载WSL内核更新包： 
	- 手动安装 [https://aka.ms/wsl2kernel](https://aka.ms/wsl2kernel)
	- 自动安装 wsl --update
- 下载最新版WSL：
	- https://github.com/microsoft/WSL



