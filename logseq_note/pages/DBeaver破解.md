- 破解程序项目地址
	- https://github.com/hongzhangzhao/dbeaver-agent
- Java版本： jdk 17
- 生成激活码
	- ```
	  java -cp libs\*;dbeaver-agent-1.0-SNAPSHOT-jar-with-dependencies.jar dev.misakacloud.dbee.License
	  ```
- 破解程序放到DBeaver目录下
	- dbeaver-agent-1.0-SNAPSHOT-jar-with-dependencies.jar 修改名称为 dbeaver-agent.jar
	- 将它放在dbeaver目录下, 随后将路径添加到配置文件中 "dbeaver.ini"
- 配置文件
	- 指定Java路径
		- ```
		  -vm
		  C:\Users\hongz\Desktop\apps\zulu17.50.19-ca-jdk17.0.11-win_x64\bin
		  ```
	- 指定破解程序路径
		- ```
		  -vmargs
		  -javaagent:C:\Users\hongz\Desktop\apps\dbeaver-ee-24.0.0-win32.win32.x86_64\dbeaver\dbeaver-agent.jar
		  ```