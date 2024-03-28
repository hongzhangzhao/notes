
[[2024-03-20]]

# fastjson

```
// 读取JSON文件内容 
String jsonStr = FileUtils.readFileToString(new File("data.json")); 

// 解析JSON字符串为
JSONObject JSONObject jsonObject = JSON.parseObject(jsonStr); 

// 获取JSON对象中的属性 
String name = jsonObject.getString("name"); 
int age = jsonObject.getIntValue("age"); 

// 解析JSON字符串为Java对象 
User user = JSON.parseObject(jsonStr, User.class); 

// 获取Java对象中的属性 
System.out.println(user.getName()); 
System.out.println(user.getAge());
```

