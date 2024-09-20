
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

