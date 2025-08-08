


# 1 无法启动

错误描述:
```
Error response from daemon: driver failed programming external connectivity on endpoint etcd (...): (iptables failed: iptables --wait -t nat -A DOCKER -p tcp -d 0/0 --dport 2380 -j DNAT --to-destination 172.17.0.2:2380 ! -i docker0: iptables: No chain/target/match by that name.
```

解决方法:

```
sudo service docker stop
sudo rm /var/lib/docker/network/files/local-kv.db
sudo service docker start
```



# 2 端口占用

错误描述:
```
Bind for 0.0.0.0:50050 failed: port is already allocated

```

解决方法:
```
$ docker container ls
$ docker stop <容器ID>
```