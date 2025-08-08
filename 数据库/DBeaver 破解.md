

- 版本 24.0


# 1 项目

- https://github.com/hongzhangzhao/dbeaver-agent

## 1.1 编译

- 需要 jdk 17

```
mvn package
```

## 1.2 生成激活码

- 需要注意, 依赖jar包在多个位置用 ";" 分隔, 如果是linux则用 ":"
- 此处的libs就是项目中的

```shell
java -cp libs\*;dbeaver-agent-1.0-SNAPSHOT-jar-with-dependencies.jar dev.misakacloud.dbee.License
```

## 1.3 agent文件

- dbeaver-agent-1.0-SNAPSHOT-jar-with-dependencies.jar 修改名称为 dbeaver-agent.jar
- 将它放在dbeaver目录下, 随后将路径添加到配置文件中 "dbeaver.ini"

# 2 配置文件

## 2.1 指定jdk 17版本

在首行添加

```
-vm
C:\Users\hongz\Desktop\apps\zulu17.50.19-ca-jdk17.0.11-win_x64\bin
```

## 2.2 指定agent位置

- 在 -vmargs 下添加

```
-vmargs
-javaagent:C:\Users\hongz\Desktop\apps\dbeaver-ee-24.0.0-win32.win32.x86_64\dbeaver\dbeaver-agent.jar
```

