GDB调试工具
===


## 如何安装GDB

```
apt update && apt install gdb
```

## 如何让调试信息更易读

安装符号包，将调试信息转成语言符号

## 如何安装符号包

- 打开 ddebs 仓库（一次性操作）
```sh
# 添加官方 ddebs 源
echo "deb http://ddebs.ubuntu.com $(lsb_release -cs) main restricted universe multiverse
deb http://ddebs.ubuntu.com $(lsb_release -cs)-updates main restricted universe multiverse
deb http://ddebs.ubuntu.com $(lsb_release -cs)-security main restricted universe multiverse" | \
tee /etc/apt/sources.list.d/ddebs.list

# 导入 GPG key
sudo apt install ubuntu-dbgsym-keyring
sudo apt update
```

- 安装符号包
```sh
sudo apt install \
  libc6-dbg \
  libstdc++6-dbgsym \
  gcc-11-dbgsym g++-11-dbgsym \
  libgcc-s1-dbgsym
```

## 如何使用gdb调试

- 编译程序的时候需要加 -g
```
g++ -g -O0 -Wall tinytetris-commented.cpp -lncurses -o tinytetris_debug
```

- 参数说明
  - `-g`：包含调试信息
  - `-O0`：关闭优化，便于调试
  - `-Wall`：显示所有警告
  - `-lncurses`：链接 ncurses 库

- 启动程序的时候使用gdb
  - gdb ./xxx(编译后的可运行程序)

## 遇到异常退出的程序如何debug

- 生成core文件
  - core文件是什么
- 调试的时候指定core文件

## 运行中的程序如何调试

- 先查看进程的PID, 通过ps命令查看
- gdb运行时指定pid: gdb -p <PID>