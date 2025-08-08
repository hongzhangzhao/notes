

主要目的是解决与内网网段冲突, 导致ssh连接不上


```
sudo vi /etc/docker/daemon.json


{
  "default-address-pools" : [
    {
      "base" : "172.31.0.0/16",
      "size" : 24
    }
  ]
}

{
  "default-address-pools" : [
    {
      "base" : "12.11.0.0/16",
      "size" : 24
    }
  ]
}

-----

sudo systemctl stop docker
sudo systemctl daemon-reload
sudo systemctl start docker

```



错误一

```
ERROR: for log  Cannot start service log: network b3cd674d38943c91c439ea8eafc0ecc4cea6d4e0df875d930e8342f6d678d135 not found
ERROR: No containers to start
```

绑定network, 这个问题好像是在stop容器并重启时会出现

```
docker network ls  # 查询网络信息
docker network connect <network_name> <container_name>
```


