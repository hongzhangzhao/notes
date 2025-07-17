- Maven配置
	- ```
	  设置本地镜像库，在55行
	  <localRepository>/data/apache-maven-3.6.3/repositories</localRepository>
	   
	  在159行的标签为</mirrors>前添加如下阿里云镜像
	  <mirror>
	      <id>alimaven</id>
	      <name>aliyun maven</name>
	      <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
	      <mirrorOf>central</mirrorOf>
	  </mirror>
	  ```