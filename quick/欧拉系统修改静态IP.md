#欧拉 #系统

# 欧拉系统修改静态IP

编辑网卡配置文件

- vi /etc/sysconfig/network-scripts/ifcfg-ens33

重启网络服务

- nmcli con reload
- nmcli con up ens33

重启网络服务方式二

- systemctl restart NetworkManager
