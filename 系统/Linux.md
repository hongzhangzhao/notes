

# 1 字体安装

> https://juejin.cn/post/7152120152744525832

```
- 安装字体命令 dnf install fontconfig
- 使用这个命令查看已安装字体: fc-list, fc-list :lang=zh (查看中文字体)
- 创建一个目录用来保存字体: mkdir -p /usr/share/fonts/my_fonts
- 在win10系统`C:\Windows\Fonts`查找字体, 并将字体上传到linux目录
- 安装字体索引指令 dnf install mkfontscale
- 进入字体目录中, 执行 mkfontscale
- 执行 fc-list 查看字体安装成功
```


# 2 设置时间

```shell
timedatectl  # 查看时间和时区
timedatectl set-timezone Asia/Shanghai  # 设置时区
timedatectl set-local-rtc 0  # RTC time = Universal time
timedatectl set-time "2024-08-06 08:21:00"  # 设置系统本地时间
date -s "2022-10-01 12:00:00"  # 也可以设置本地时间
date -R  # 查看本地时间
```


# 3 systemctl 命令

```
systemctl start 是有超时机制的，如果启动的脚本无法结束，到了超时时间，将会中断。

修改超时时间：编辑/etc/systemd/system.conf下的DefaultTimeoutStartSec=

如果脚本中执行java程序需要在结尾追加`>> /dev/null 2>&1 &`，否则脚本无法结束会触发超时

还有，在成功start后，docker容器会自动关闭，这个需要在脚本结尾追加`tail -f /dev/null`让脚本超时，这样就不会执行关闭容器的操作了
```


# 4 域名解析

```shell
vi /etc/resolv.conf

nameserver 8.8.8.8
nameserver 8.8.4.4
```


# 5 rpm

## 5.1 dnf

```shell
# 不安装，仅下载包
dnf install --downloadonly <package-name> --downloaddir=<save_dir>

# 下载指定版本
dnf install --downloadonly **libwbclient-4.18.5-1.oe2309.x86_64** --downloaddir=/home/temp5

# 查看已经安装的包
rpm -qa | grep <package-name>

# 卸载
rpm -e --nodeps <package-name>

```



# 6 欧拉 网卡操作


```shell
ip link show
ip link set dev [eth0] down
ip link set dev [eth0] up
```


