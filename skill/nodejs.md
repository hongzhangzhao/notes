

# 1 fnm 版本管理


- https://github.com/Schniz/fnm  官方地址

```shell
- 下载解压后, 配置环境变量
- notepad $profile  # 用这个命令查看环境变量文件
- $profile  # 如果没有, 打印这个变量, 输出文件路径
- C:\Users\lenovo\Documents\WindowsPowerShell\Microsoft.PowerShell_profile.ps1  # 新建文件
- fnm env --use-on-cd --shell power-shell | Out-String | Invoke-Expression  # 添加到文件中
- fnm list  # 查看下载了哪些版本
- fnm --version  # 查看fnm的版本
- fnm -h  # 帮助信息
- fnm list-remote  # 查看有哪些版本可下载
- fnm install <版本> --node-dist-mirror https://npmmirror.com/mirrors/node  # (版本不要v, 可以不用指定小版本)
- fnm use <版本>   # 切换版本
- fnm default <版本>  # 设置默认版本
- fnm uninstall <版本>  # 卸载版本
- fnm env  # 查看环境变量
```


## 1.1 镜像配置


```shell
- https://npmmirror.com/  # 查看镜像地址

# 在windows中新建环境变量, fnm就会使用这个镜像地址来下载
- FNM_NODE_DIST_MIRROR  # 变量名
- https://npmmirror.com/mirrors/node  # 变量值
```


## 1.2 错误

- 无法加载文件

```shell
# 访问地址, 找到解决命令, 并执行
- https:/go.microsoft.com/fwlink/?LinkID=135170  # 网址
- Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser  # 命令
```


# 2 linux 安装(有fnm这个可以不用了)


```shell
- wget https://nodejs.org/dist/v16.9.1/node-v16.9.1-linux-x64.tar.gz
- mkdir -p /usr/local/nodejs 
- tar -xvf node-v16.9.1-linux-x64.tar.gz -C /usr/local/nodejs
- echo 'export PATH=/usr/local/nodejs/node-v16.9.1-linux-x64/bin:$PATH' >> ~/.bashrc
- source ~/.bashrc
- node -v
```


# 3 windows 安装(有fnm这个可以不用了)


## 3.1 nodejs目录下创建文件夹

- node_cache  全局缓冲路径
- node_global  全局安装路径

## 3.2 设置nodejs环境变量

- 新建变量名 NODE_PATH
- 添加到path变量中
  - `%NODE_PATH%`
  - `%NODE_PATH%\node_global`

## 3.3 配置npm全局安装路径

- `npm config set cache "%NODE_PATH%\node_cache"`
- `npm config set prefix "%NODE_PATH%\node_global"`

如果卡死或异常，删除C:\Users\用户名\.npmrc文件，重新执行

## 3.4 验证安装结果

- node -v
- npm -v

## 3.5 node-sass异常

- npm uninstall node-sass
- npm install node-sass


# 4 npm 配置国内源

- npm config set registry https://registry.npmmirror.com
- npm config get registry


