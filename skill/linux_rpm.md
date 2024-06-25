

# 1 dnf

## 1.1 不安装，仅下载包

```shell
dnf install --downloadonly <package-name> --downloaddir=<save_dir>
```

## 1.2 下载指定版本

dnf install --downloadonly **libwbclient-4.18.5-1.oe2309.x86_64** --downloaddir=/home/temp5


# 2 查看已经安装的包

`rpm -qa | grep <package-name>`

# 3 卸载

`rpm -e --nodeps <package-name>`








