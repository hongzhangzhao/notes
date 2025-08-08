


```bash
rpm -qf /usr/lib64/libldap.so.2   # 查看库版本
rpm -aV  # 检查依赖项
ldconfig -p | grep libldap   # 检查依赖项
ldd /usr/libexec/sudo/sudoers.so 和 ldd /usr/lib64/libldap-2.4.so.2   检查其它依赖项
```