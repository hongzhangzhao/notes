

- 构建请求体（Json格式）

```java

import com.fasterxml.jackson.core.type.TypeReference;  
import com.fasterxml.jackson.databind.JsonNode;  
import com.fasterxml.jackson.databind.ObjectMapper;  
import com.fasterxml.jackson.databind.node.ObjectNode;  


ObjectMapper jsonMapper = new ObjectMapper();  // 创建一个mapper

ObjectNode aggsNode = jsonMapper.createObjectNode();  // 通过mapper创建节点

ObjectNode fieldQuery = jsonMapper.createObjectNode();
fieldQuery.put("field", fieldName);  // 往节点中添加key-value

ObjectNode existsQuery = jsonMapper.createObjectNode();  
existsQuery.set("exists", fieldQuery);  // 添加其他节点(对象类型)用 set, 基础类型用 put

ObjectNode filterAgg = jsonMapper.createObjectNode();  
filterAgg.set("filter", existsQuery);

aggsNode.set("aggName", filterAgg);

ObjectNode requestBody = jsonMapper.createObjectNode();  
requestBody.put("size", 0);
requestBody.set("aggs", aggsNode);

String jsonRequestBody = jsonMapper.writeValueAsString(requestBody);  // 将mapper转成json字符串

/*
jsonRequestBody:
{
  "size": 0,
  "aggs": {
    "aggName": {
      "filter": {
        "exists": {
          "field": fieldName
        }
      }
    }
  }
}
*/
```

- 解析json

```java

ObjectMapper objectMapper = new ObjectMapper();  // 创建一个mapper

JsonNode rootNode = objectMapper.readTree("json字符串");  // 形成一个树结构吧

JsonNode hitsNode = rootNode.path("hits").path("hits");  // path可以定位到某个节点
!hitsNode.isArray() || hitsNode.size() == 0  // node对象有很多isXXX的方法来判断该节点的数据类型

JsonNode sourceNode = hitsNode.get(0).path("_source");  // get方法获取数组中的节点

Map<String, Object> fieldMap = objectMapper.convertValue(  // 节点下的子节点如果是字典结构，可以快速转成Map
        sourceNode,  
        new TypeReference<Map<String, Object>>() {}  
);

for (Map.Entry<String, Object> entry : fieldMap.entrySet()) {  
    String valueType = entry.getValue() != null ? entry.getValue().getClass().getSimpleName() : "null";  // 判断value的类型
  
    System.out.printf("%-20s: [类型: %-10s] %s%n",  
            entry.getKey(),  
            valueType,  
            entry.getValue().toString());  
}

node.get("index").asText("默认值");  // asText应该是将值转成String，并且可以传入一个默认值

for (JsonNode indexNode : rootNode)   // 节点也可以直接遍历（如果有多个子节点）

if (node.has("properties"))  // has可以判断该节点下是否存在某个节点（properties）

properties.fields().forEachRemaining(field -> {  // fields()将该节点下的多个子节点转成Iterator<Map.Entry<String, JsonNode>>
    String fieldName = field.getKey();  
    JsonNode fieldConfig = field.getValue();  
    String fieldType = fieldConfig.path("type").asText("未知类型");  
  
    System.out.println("字段名: " + fieldName);  
    System.out.println("类型: " + fieldType);  
    System.out.println("-----");  
});
```
