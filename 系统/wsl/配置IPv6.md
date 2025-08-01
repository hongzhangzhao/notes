
## windows上操作

整个过程需要使用到 Hyper-V 管理器

- 查看ipv6是否开启
```
Get-NetAdapterBinding -ComponentID ms_tcpip6
```

- 在Hyper-v上创建虚拟交换机
```sh
New-VMSwitch -Name "WSLv6Switch" -SwitchType Internal  # 创建一个交换机

Get-NetAdapter | Where-Object {$_.Name -like "*WSLv6Switch*"}  # 获取索引号 ifIndex

New-NetIPAddress -IPAddress fd00::1 -PrefixLength 64 -InterfaceIndex 42  # 给交换机配置ipv6地址，42是上一步获取的索引号

Set-NetIPInterface -InterfaceIndex 42 -Forwarding Enabled  # 启动ipv6路由
```

- 配置wsl使用该交换机
```
[wsl2]
networkingMode=bridged
vmSwitch=WSLv6Switch
ipv6=true
```

- 重启下wsl

## 在wsl内操作

- 在wsl内配置静态ipv6（ubuntu24.04）
```yml
network:
  version: 2
  ethernets:
    eth0:
      dhcp4: no
      dhcp6: no
      addresses:
        - fd00::2/64
      gateway6: fd00::1
```

创建这个文件 /etc/netplan/01-netcfg.yaml，填入配置，执行 netplan apply 应用配置


# 其他

- networkingMode=mirrored
    - 这个模式实现互通可能更简单，但是没有验证



> https://blog.csdn.net/qq_37858281/article/details/144412766
> https://yijianhao.cn/archives/wsl2-shi-yong-wai-bu-wang-luo--jing-tai-ipipv6
> https://zhuanlan.zhihu.com/p/593263088