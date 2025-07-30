
# Low Level REST Client 方式

- 建立连接
```java

import com.rhzz.database.dbmetadata.ServerAddress;  
import com.rhzz.database.dbmetadata.SimpleUser;  
import com.rhzz.database.dbmetadata.exception.MetadataException;  
import com.rhzz.database.dbmetadata.model.*;  
import org.apache.commons.lang3.StringUtils;  
import org.apache.http.HttpHost;  
import org.apache.http.auth.AuthScope;  
import org.apache.http.auth.UsernamePasswordCredentials;  
import org.apache.http.client.CredentialsProvider;  
import org.apache.http.impl.client.BasicCredentialsProvider;  
import org.apache.http.util.EntityUtils;  
import org.elasticsearch.client.Request;  
import org.elasticsearch.client.Response;  
import org.elasticsearch.client.RestClient;

private RestClient restClient;

private void buildConnect(ServerAddress address, SimpleUser user) {  
    List<String> hosts = Arrays.asList(address.getIp()); // IP地址  
    int port = address.getPort();  // 端口 9200    
    String protocol = "http"; // http or https  
  
    HttpHost[] httpHosts = hosts.stream()  
            .map(host -> new HttpHost(host, port, protocol))  
            .toArray(HttpHost[]::new);  
  
    if (StringUtils.isNotBlank(user.getUsername())) {  // 需要认证
        final CredentialsProvider credentialsProvider = new BasicCredentialsProvider();  
        credentialsProvider.setCredentials(  
                AuthScope.ANY,  
                new UsernamePasswordCredentials(user.getUsername(), user.getPassword()) // 如 elastic        
                );  
        this.restClient = RestClient.builder(httpHosts)  
                .setRequestConfigCallback(builder ->  
                        builder.setConnectTimeout(5000) // 连接超时5秒  
                                .setSocketTimeout(60000) // 响应超时60秒  
                ).setHttpClientConfigCallback(  
                        builder -> builder.setDefaultCredentialsProvider(credentialsProvider)  
                ).build();  
    } else {  // 不需要认证
        this.restClient = RestClient.builder(httpHosts)  
                .setRequestConfigCallback(builder ->  
                        builder.setConnectTimeout(5000) // 连接超时5秒  
                                .setSocketTimeout(60000) // 响应超时60秒  
                ).build();  
    }  
}
```

- 发起请求

```java
Request request = new Request("GET", "/_cat/indices/");  
request.addParameter("format", "json");  // 添加参数，类似在URL?key=value
request.addParameter("h", "index,store.size");

request.setJsonEntity("json字符串");  // 参数请求体，类似json格式的传参，这种方式请求的方式最好设置POST

Response response = restClient.performRequest(request);  // 发起请求
int statusCode = response.getStatusLine().getStatusCode();  // 获取响应码 200

String responseBody = EntityUtils.toString(response.getEntity());  // 返回值转成String
```