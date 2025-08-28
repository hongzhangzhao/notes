#欧拉 #系统

# 欧拉系统为普通用户设置sudoers

通过root用户执行这个命令 `usermod -aG wheel 用户名`

openEuler 默认在 /etc/sudoers 中配置了 wheel 组，组成员可通过 sudo 执行所有命令。
