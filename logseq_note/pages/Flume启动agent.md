- 启动agent
	- ```
	  bin/flume-ng agent --conf conf --conf-file example.conf --name a1 -Dflume.root.logger=INFO,console
	  
	  # 另一种写法
	  bin/flume-ng agent -c conf -f example.conf -n z1 -Dflume.root.logger=INFO,console
	  
	  ./bin/flume-ng agent -c conf -f ./conf/customSyslog.conf -n collector -Dflume.root.logger=DEBUG,console
	  ./bin/flume-ng agent -c conf -f ./conf/customSyslog.conf -n collector -Dflume.root.logger=DEBUG,LOGFILE
	  ```
	- 启动参数
		- ```
		  -Dflume.root.logger=INFO,LOGFILE,console  # LOGFILE 日志内容输出到文件中
		  -Dflume.log.file=/path/to/flume.log  # 指定日志文件路径
		  ```
	- 开启远程调试
		- ```
		  编辑启动文件 flume-ng, 修改以下内容
		  
		  JAVA_OPTS="-Xmx500m -Xdebug -Xrunjdwp:transport=dt_socket,address=8000,server=y,suspend=y"
		  ```