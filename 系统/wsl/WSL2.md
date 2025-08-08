

# 1 安装过程

- 管理员身份运行 CMD
- wsl --update
- wsl --set-default-version 2  设置wsl版本, 设为2版
- wsl --install --web-download  安装子系统(一般默认安装Ubuntu)
	- 国内为了下载快一点, 使用 --web-download 参数
- wsl --list --online  查看发行版
- wsl --install Ubuntu-24.04 --web-download  指定安装发行版
- wsl --list -v  显示已经安装的子系统
- wsl --set-default <子系统名称>  设置默认子系统
- wsl -d <子系统名称>  启动子系统, 或是直接打开终端
- wsl --unregister <子系统名称>  卸载子系统
- wsl --export <子系统名称> xxx.tar  导出子系统
- wsl --import <子系统名称> xxx.tar  导入子系统, 可以指定子系统位置


# 2 文件共享

- 子系统调用windows上的程序
	- notepad.exe xxx.txt  打开记事本.
	- explorer.exe .   调用资源管理器, 打开当前目录.

- windows上调用linux命令
	- Get-ChildItem | wsl grep xxx
		- Get-ChildItem类似 ll 命令, 后面可以拼接linux命令对文件过滤
		- dir | wsl grep xxx 一样的效果

# 3 wslg

- linux中带UI的程序, 通过windows窗口打开
- 原理是RDP远程桌面协议


# 4 显卡直通

- nvidia-smi  查看显卡信息, 已经配置好了 CUDA
	- nvcc无法配置, 需要单独编译?

# 5 kali-linux kex

- sudo apt install kali-win-kex  安装
- kex --esm --ip --sound  运行, 通过远程桌面的方式连接到linux系统

- ubuntu 远程桌面连接?


# 6 wsl 高级配置

- 配置文件
	- wsl.conf    linux里的配置, 对某个子系统生效
	- .wslconfig  windows上的全局配置, 对所有子系统生效

- 8秒规则
	- 配置完成后, 需要执行 wsl --shutdown 关闭子系统, 8秒后重启生效

- 配置 systemd
	- sudo vim /etc/wsl.conf
    ```
    [boot]
    systemd=true
    ```

- 全局配置
	- 配置子系统和windows使用同样的IP
	- 在用户目录下创建 .wslconfig 文件
    ```
    [wsl2]
    networkingMode=mirrored
    ```

# 7 错误

## 7.1 解析服务器的名称或地址

- 修改 hosts

```shell
185.199.108.133 raw.githubusercontent.com
185.199.109.133 raw.githubusercontent.com
185.199.110.133 raw.githubusercontent.com
185.199.111.133 raw.githubusercontent.com
```

- 刷新 dns

```shell
ipconfig /flushdns  # windows use
```



# 8 安装 docker

- apt update && apt upgrade -y  # 更新一下
- apt install iptables  # 安装 iptables
- update-alternatives --config iptables  # 选择 iptables-legacy
- 启动 docker (二进制)

