

# 1 启动自定义 agent

- --conf 指定配置目录, 这个固定写conf
- --conf-file 指定配置文件, 这个写配置文件的绝对路径
- --name 指定 agent 名称, 和配置文件中的相同

```sh
bin/flume-ng agent --conf conf --conf-file example.conf --name a1 -Dflume.root.logger=INFO,console

# 另一种写法
bin/flume-ng agent -c conf -f example.conf -n z1 -Dflume.root.logger=INFO,console

```


# 2 avro

- 一个序列化框架, 用来在sink和source间传输数据


# 3 远程调试 agent

编辑启动文件 flume-ng, 修改以下内容, 之后配置idea即可

```sh
JAVA_OPTS="-Xmx500m -Xdebug -Xrunjdwp:transport=dt_socket,address=8000,server=y,suspend=y"
```





