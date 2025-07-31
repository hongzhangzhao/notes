

- 欧拉系统配置IPv6
```sh
# 为两台机器分别配置ipv6
sudo ip -6 addr add 2001:db8:1::10/64 dev ens32
sudo ip -6 addr add 2001:db8:1::20/64 dev ens192

ping6 2001:db8:1::20
```

这种方式只能临时生效

- docker配置ipv6网络
```json

vim /etc/docker/daemon.json
---
{
  "ipv6": true,
  "fixed-cidr-v6": "2001:db8:1::/64"
}
---
sudo systemctl daemon-reload   # 让 systemd 重新读取 unit 文件（可选但推荐）
sudo systemctl restart docker  # 重启 Docker，使 daemon.json 生效

docker info | grep -i "ipv6"  # 查看是否开启成功
```

