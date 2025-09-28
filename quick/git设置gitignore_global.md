
#git/ignore


# windows 环境配置 ignore

## global ignore

在 /home/user 目录下新建 `.gitignore_global` 文件

在 `.gitignore_global` 文件中填写需要忽略的目录或文件

将 `.gitignore_global` 配置到 Git 中： `git config --global core.excludesfile ~/.gitignore_global`

验证配置成功？ `git config --global core.excludesfile`

Linux环境步骤也是一样的！！！