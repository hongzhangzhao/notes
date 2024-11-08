书籍: 跟闪电侠学 netty

示例代码:

> https://github.com/Chanmoey/learn-netty-with-flash
> https://github.com/lightningMan/flash-netty


# 1 基础知识

- wireshark 抓包显示的是16进制, 一组(FF)代表一个字节, 它们之间用空格隔开. netty只能获取应用层的报文, 所以只需要关心应用层部分的信息就好.  
- java中byte表示一个字节, 它是有符号的, 范围在-128到127. 对于符号的问题需要了解下补码.  
- 数字, 字符串和浮点虽然都是二进制的形式, 但是它们机制是不同的, 计算机如何区分呢?
- TCP建立连接, 通过三次握手, 就是互相发送消息并接收回应, 最后保存连接状态, 其中SYN(同步序列编号)和ACK(确认序列编号)保证数据包的正确传输.  
- 保持连接状态, 通过心跳等机制实现. 主要形式有cookie和session id, TCP 协议会使用IP和端口作为唯一标识, 使用缓冲区保存接收和发送的数据包, 并且可以配置连接参数: 窗口大小和序列号.  
- socket 主机网口的抽象表示? 它是一个端点, 一个链路两端的端点.  
- 通信协议, 指的是二进制数据包, 协定其中每段字节是什么意思.  


当多个客户端连接成功后, 要如何保存这些连接, 并在使用的时候取出来?  
- 连接成功后, 用一个标识跟连接进行映射, 以后通过标识取出连接来传递信息
- 代理就好像是, 双方都是一个客户端, 并且和代理服务端建立连接, 服务端根据客户端的标识取出连接并转发信息(个人理解)
- 大致过程
```java
// netty如何将建立成功的连接通过一个标识保存到map中, 使用的时候通过标识取出连接传递信息?
// 通过 ChannelHandlerContext 获取连接, 并保存到Map中
// attr 可以保存并提取连接中的信息

// ChannelHandlerContext ctx 是当前通道的引用
String userId = ctx.channel().attr(AttributeKey.valueOf("id")).get();
ChannelMap.put(userId, ctx.channel());

// 将消息发送给对端
channel.writeAndFlush(msg);
```


16进制字符串与Bytebuf的转换

```java

// 示例16进制字符串
String hexString = "68656c6c6f"; // 代表 "hello"

// 将16进制字符串转换为字节数组
byte[] bytes = ByteBufUtil.decodeHexDump(hexString);

// 创建ByteBuf
ByteBuf byteBuf = Unpooled.wrappedBuffer(bytes);

// 输出结果
System.out.println(byteBuf.toString(io.netty.util.CharsetUtil.UTF_8)); // 输出: hello
```





