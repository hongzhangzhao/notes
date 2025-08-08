

# 1 postgre

## 1.1 查询自定义函数

```sql
SELECT 
    proname AS "函数名称", 
    pg_catalog.pg_get_function_result(p.oid) AS "返回值数据类型", 
    pronargs AS "参数个数" 
FROM 
    pg_proc p 
JOIN 
    pg_namespace n ON n.oid = p.pronamespace 
WHERE 
    n.nspname = 'public'; 
```

## 1.2 查看自定义函数实现

```sql
SELECT prosrc 
FROM pg_proc 
WHERE proname = 'method name';
```



