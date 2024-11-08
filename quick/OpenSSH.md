

> https://segmentfault.com/a/1190000045098637
> https://www.cnblogs.com/Magiclala/p/18370213
> https://blog.csdn.net/qq_46001933/article/details/129736046
> https://blog.csdn.net/chenxingrong/article/details/140442028
> https://blog.csdn.net/u011630259/article/details/132089603
> https://www.hikunpeng.com/document/detail/zh/kunpengaccel/encryp-decryp/faq-kae/kunpengaccel_10_0020.html  升级OpenSSL


# 1 安装 OpenSSH


## 1.1 安装telnet, 防止ssh失败

```bash
sudo dnf -y install telnet
sudo systemctl enable telnet.socket
sudo systemctl start telnet.socket
sudo systemctl status telnet.socket
```

## 1.2 备份ssh

```bash
sudo mkdir /data/openssh_backup -p
sudo cp -ar /etc/ssh* /data/openssh_backup/
```

## 1.3 关闭安全检查

```bash
sudo getenforce
sudo setenforce 0
sudo sed -i 's/^SELINUX=.*/SELINUX=disabled/' /etc/selinux/config
```


## 1.4 安装基础依赖

```bash
sudo dnf -y install gcc make zlib zlib-devel openssl-devel

# 看情况装不装 perl  openssl openssl-libs
```


## 1.5 下载与安装

```bash
sudo tar -zxf openssh-9.8p1.tar.gz
cd openssh-9.8p1
sudo ./configure
sudo make && sudo make install
sudo systemctl restart sshd

# 重新ssh登录, 查看ssh版本, ssh -V
```

