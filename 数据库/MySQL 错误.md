

# 1 视图查询错误

原因是在创建视图时的定义者指定的是root用户, 使用其它用户登录数据库, 并操作视图, 由于当前登录的用户和视图的定义者不匹配, 所以视图拒绝访问.

```
The user specified as a definer ('root'@'localhost') does not exist
```

