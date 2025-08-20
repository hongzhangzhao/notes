
# 1 tag

```shell
# 查看tag列表
git tag

# 创建 tag
git tag -a <name> -m "<remark>"

# 推送 tag
git push origin <tag_name>
```


# 2 修改换行符

```shell
# 换行符修改成linux标准
find . -type f  
find . -type f -exec dos2unix {} +  
```

## 2.1 最佳换行符配置

```shell
git config --global core.autocrlf input
git config --global core.safecrlf true
```


# 3 设置代理

```shell
git config --global http.proxy http://127.0.0.1:7897
git config --global https.proxy http://127.0.0.1:7897

# 删除代理
git config --global --unset http.proxy
git config --global --unset https.proxy
```


# 4 提交空文件夹

- 在空文件夹内创建`.gitkeep`文件
- 编辑`.gitignore`：

```
空文件夹/* 
!空文件夹/.gitkeep
```

## git bash 中文乱码

- 换支持中文的字体
  - 在 Git Bash 标题栏 → 右键 → Options → Text → Font

- 设置 local

```sh
echo 'export LANG=zh_CN.UTF-8' >> ~/.bashrc
echo 'export LC_ALL=zh_CN.UTF-8' >> ~/.bashrc
source ~/.bashrc
```

- 统一 Git 的编码

```sh
git config --global core.quotepath false      # 不转义非 ASCII 字符 解决 git status 中文乱码
git config --global gui.encoding utf-8        # GUI 工具用 UTF-8
git config --global i18n.commitEncoding utf-8 # 提交信息编码
git config --global i18n.logOutputEncoding utf-8
```

