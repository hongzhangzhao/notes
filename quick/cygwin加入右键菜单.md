

> https://zhangnai.xin/2019/01/02/open-cygwin-here/
> https://blog.csdn.net/heifan2014/article/details/78824248


# 1 编辑环境变量


```
vi C:\cygwin64\home\用户名\.bashrc

export _T=${_T//\\//}   # replace backslash to fowardslash  
export _T=${_T//\"/}    # 如果目录带中文，传入的目录会在首尾加引号，如果无中文则不会加双引号。这里删除首尾双引号，归一处理  
if [[ $_T == "" ]]; then  
    export _T="C:\cygwin64\home\用户名"    
fi  
cd "$_T"
```


# 2 修改注册表

进入 `HKEY_CLASSES_ROOT\Directory\Background\shell`

添加key： cygwin，并编辑default值为 cygwin here （右键菜单显示的名字）

在cygwin下添加key： command，并修改默认值（cygwin的启动命令）：

`c:\cygwin64\bin\mintty.exe /bin/env _T="%V/" /bin/bash -l`





