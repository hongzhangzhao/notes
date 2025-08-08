

- 创建一个组
- 创建一个用户, -s /sbin/nologin 不允许用户通过SSH等方式登录, -g [groupname]  设置用户的初始组

```sh
groupadd [groupname]
useradd [username] -s /sbin/nologin -g [groupname]
```

- PS3可以编写一些提示信息, 需要结合select使用
- options定义了一个数组, `${options[@]}` 返回包含所有元素的列表
- select会将数组的元素和序号展示到屏幕上, 用户选择对应的序号即可

```sh
PS3='提示词: '
options=("Manage" "Audit" "HA")
select opt in ${options[@]}
do
    case $opt in
        "Manage")
            echo "1"
            break
            ;;
        "Audit")
            echo "2"
            break
            ;;
        "HA")
            echo "3"
            break
            ;;
  
        "Quit")
            exit
            ;;
        *)  # 配置任何输入
        echo invalid option;;
    esac
done
```


if 语句格式, 以下是字符串的比较

```sh
if [ "${1}" = "mysql" ] ; then

    echo "starting install mysql..."

elif [ "${1}" = "nginx" ] ; then

    echo "starting install nginx..."

else
    echo "..."

fi
```

- 获取父级路径
- 获取爷爷路径
- 获取太爷路径

```sh
dirname "$PWD"
dirname "$(dirname "$PWD")"
dirname "$(dirname "$(dirname "$PWD")")"
```


- getopts说明, 在执行脚本后面需要接 xxx.sh  -a  [参数]
- opt是输入的-a, -b, -c
- $OPTARG是输入的[参数]值

```sh
while getopts ":a:b:c" opt;
do
  case $opt in
    a)
      echo "Option a with value '$OPTARG' was given." >&2
      ;;
    b)
      echo "Option b was given." >&2
      ;;
    c)
      echo "Option c was given." >&2
      ;;
    ?)
      echo "Invalid option: -$OPTARG" >&2
      exit 1
      ;;
  esac
done
```


