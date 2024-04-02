

vi /etc/docker/daemon.json （没有这个配置文件就新建一个）

```
{
  "registry-mirrors": [
    "https://registry.docker-cn.com",
    "http://hub-mirror.c.163.com",
    "https://docker.mirrors.ustc.edu.cn"
  ]
}
```

systemctl restart docker

