# bash programing cookbook(in progress)
bash编程、高级bash编程、bash编程by examples

## hello world
`vim hello.sh`, 写入内容
``` bash
#!/bin/bash
# This is a comment
echo helloworld
```

执行bash文件
``` bash
bash-3.2$ sh hello.sh
helloworld
```

执行bash的方式有多种
``` bash
bash-3.2$ ./hello.sh
zsh: permission denied: ./hello.sh
bash-3.2$ ls -al
total 8
drwxr-xr-x   3 shiming  staff    96 Mar 29 12:58 .
drwxr-xr-x+ 87 shiming  staff  2784 Mar 29 13:00 ..
-rw-r--r--   1 shiming  staff    48 Mar 29 12:58 hello.sh
bash-3.2$ chmod 751 hello.sh
bash-3.2$ ./hello.sh
helloworld
bash-3.2$ zsh hello.sh
helloworld
bash-3.2$ bash hello.sh
helloworld
bash-3.2$ source hello.sh
helloworld
```

## 重定向
编辑`hello.sh`将“helloworld”重定向到文件hello.txt

``` bash
#!/bin/bash
# this is a command
echo helloworld > hello.txt
```

``` bash
bash-3.2$ sh hello.sh
bash-3.2$ ls
hello.sh  hello.txt
bash-3.2$ cat hello.txt
helloworld
```

## 变量 & 切换目录 & 清除文件内容
在/tmp目录中新建一个log文件
```
bash-3.2$ echo "some logs" > /tmp/temp.log
bash-3.2$ cat /tmp/temp.log
some logs
```

编写一个cleanlog.sh脚本，清除log但是不删除文件
cleanlog.sh文件内容
```
#!/bin/bash
LOG_DIR=/tmp    # 定义变量
cd $LOG_DIR     # 使用变量
echo "-----before log clean-----"
cat temp.log
echo "-----cleaning-----"
cat /dev/null > temp.log
echo "log has been cleaned up"

exit

```

执行脚本
```
bash-3.2$ sh cleanlog.sh
-----before log clean-----
some logs
-----cleaning-----
log has been cleaned up
```

**tips**  
权限不够加sudo啊，可是你会发现  
`sudo cat /dev/null > /var/log/wtmp` 一样会提示权限不够，为什么呢？因为sudo只能让cat命令以sudo的权限执行，而对于>这个符号并没有sudo的权限，我们可以使用  
`sudo sh -c "cat /dev/null > /var/log/wtmp "` 让整个命令都具有sudo的权限执行  

## 特殊字符
| 符号  | 含义 |
| --- | --- |
| # | 注释 |
| ; | 命令分隔符 |
| ;; | 终止case选项 |
| . | 等价source |
| " | "STRING"将会阻止（解释）"STRING"大部分特殊的字符 |
| ' | 'STRING'将会阻止'STRING'所有特殊字符的解释 |
| / | 路径分隔 |
| \ | 转义 |
| ` | 反引号中的命令会优先执行 |
| : | 空命令、变量拓展、子串替换 |
| ? | 测试操作符 |
| $ | 变量替换、命令替换 |

### 注释 (#)

`test.sh`
```
#!/bin/bash

echo "The # here does not begin a comment."
echo 'The # here does not begin a comment.'
echo The \# here does not begin a comment.
echo The # 这里开始一个注释
echo $(( 2#101011 ))     # 数制转换（使用二进制表示），不是一个注释，双括号表示对于数字的处理
```

`sh test.sh` output
```
bash-3.2$ sh test.sh
The # here does not begin a comment.
The # here does not begin a comment.
The # here does not begin a comment.
The
43
```

### 分号 (;)
使用分号（;）可以在同一行上写两个或两个以上的命令  
``` bash
#!/bin/bash
 echo hello; echo there
 filename=ttt.sh
 if [ -e "$filename" ]; then    # 注意: "if"和"then"需要分隔，-e用于判断文件是否存在
     echo "File $filename exists."; cp $filename $filename.bak
 else
     echo "File $filename not found."; touch $filename
 fi; echo "File test complete."
```

使用双分号（;;）可以终止case选项  
``` bash
case EXPRESSION in

  PATTERN_1)
    STATEMENTS
    ;;

  PATTERN_2)
    STATEMENTS
    ;;

  PATTERN_N)
    STATEMENTS
    ;;

  *)
    STATEMENTS
    ;;
esac
```

case语句举例  
``` bash
#!/bin/bash

echo -n "Enter the name of a country: "
read COUNTRY

echo -n "The official language of $COUNTRY is "

case $COUNTRY in

  Lithuania)
    echo -n "Lithuanian"
    ;;

  Romania | Moldova)
    echo -n "Romanian"
    ;;

  Italy | "San Marino" | Switzerland | "Vatican City")
    echo -n "Italian"
    ;;

  *)
    echo -n "unknown"
    ;;
esac
```

## 引用说明
大部分的示例来自于实验楼课程：  
https://www.shiyanlou.com/courses/944
