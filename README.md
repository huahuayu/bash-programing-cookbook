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
| 符号 | 含义                                             |
| ---- | ------------------------------------------------ |
| #    | 注释                                             |
| ;    | 命令分隔符                                       |
| ;;   | 终止case选项                                     |
| .    | 等价source                                       |
| "    | "STRING"将会阻止（解释）"STRING"大部分特殊的字符 |
| '    | 'STRING'将会阻止'STRING'所有特殊字符的解释       |
| /    | 路径分隔                                         |
| \    | 转义                                             |
| `    | 反引号中的命令会优先执行                         |
| :    | 空命令、变量拓展、子串替换                       |
| ?    | 测试操作符                                       |
| $    | 变量替换、命令替换                               |

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

### 点号(.)
点号等价于`source`命令  
bash 中的 source 命令用于在当前 bash 环境下读取并执行 FileName.sh 中的命令  
``` bash
$ source test.sh
Hello World
$ . test.sh
Hello World
```

### 双引号
因为空格在linux中时作为一个很典型的分隔符，比如string1=this is astring，这样执行就会报错。为了避免这个问题，因此就产生了单引号和双引号。他们的区别在于，单引号将剥夺其中的所有字符的特殊含义，而双引号中的'$'（参数替换）和'`'（命令替换）是例外。所以，两者基本上没有什么区别，除非在内容中遇到了参数替换符$和命令替换符`。

### 单引号
单引号将剥夺其中的所有字符的特殊含义  

### 斜杠
文件名路径分隔符。分隔文件名不同的部分（如/home/bozo/projects/Makefile）。也可以用来作为除法算术操作符。注意在linux中表示路径的时候，许多个/跟一个/是一样的。/home/shiyanlou等同于////home///shiyanlou

### 反斜杠
一种对单字符的引用机制。\X 将会“转义”字符X。这等价于"X"，也等价于'X'。\ 通常用来转义双引号（"）和单引号（'），这样双引号和单引号就不会被解释成特殊含义了。  

\n 表示新的一行  
\r 表示回车  
\t 表示水平制表符  
\v 表示垂直制表符  
\b 表示后退符  
\a 表示"alert"(蜂鸣或者闪烁)  
\0xx 转换为八进制的ASCII码, 等价于0xx  
" 表示引号字面的意思  
转义符也提供续行功能，也就是编写多行命令的功能。  

每一个单独行都包含一个不同的命令，但是每行结尾的转义符都会转义换行符，这样下一行会与上一行一起形成一个命令序列。  

### 反引号
反引号中的命令会优先执行  
``` bash
$ cp `mkdir back` test.sh back
$ ls
```
先创建了 back 目录，然后复制 test.sh 到 back 目录  

### 冒号
1. 在while语句中`while :` = `while true`  

脚本一  
``` bash
#!/bin/bash
#checking the load of the machine

while true    
do
    uptime
    sleep 1
done
```

脚本二  
``` bash
#!/bin/bash
#checking the load of the machine

while :
do
    uptime
    sleep 1
done
```

脚本一和二等价  

2. 在if/then中做占位符  
``` bash
#!/bin/bash

condition=5

if [ $condition -gt 0 ] #gt表示greater than，也就是大于，同样有-lt（小于），-eq（等于） 
then :   # 什么都不做，退出分支
else
    echo "$condition"
fi
```

3. `: > file.txt` 清空file.txt，等价于 `cat /dev/null > file.txt`, 如果文件不存在则创建    
4. `: >> file.txt` ，不对file.txt造成任何影响，如果file.txt不存在则创建  
5. ":"还用来在 /etc/passwd 和 $PATH 变量中做分隔符，如：  
``` bash
$ echo $PATH
/usr/local/bin:/bin:/usr/bin:/usr/X11R6/bin:/sbin:/usr/sbin:/usr/games
```
### 问好  
在一个双括号结构中，? 就是C语言的三元操作符，如：  
``` bash
 #!/bin/bash

 a=10
 (( t=a<50?8:9 ))
 echo $t
```


### 美元符号
美元符号代表变量  
``` bash
#!/bin/bash

var1=5
var2=23skidoo

echo $var1     # 5
echo $var2     # 23skidoo
```

### 括号
1. 命令组  
``` bash
#!/bin/bash

a=123
( a=321 )

echo $a #a的值为123而不是321，因为括号将判断为局部变量
```

2. 初始化数组  
数组的初始化赋值用括号括起来，且元素之间没有逗号分隔，可以整形、浮点型、字符型混写  
``` bash
#!bin/bash
a=(1 2.5 str 4 5 6)
echo ${a[2]}   # 读取数组时要把变量名和下标用大括号括起来才能识别  

```

### 大括号
1. 文件名扩展  
复制 t.txt 的内容到 t.back 中  
``` bash
#!/bin/bash

if [ ! -w 't.txt' ];
then
    touch t.txt
fi
echo 'test text' >> t.txt
cp t.{txt,back}
```

2. 代码块  
代码块，又被称为内部组，这个结构事实上创建了一个匿名函数（一个没有名字的函数）。然而，与“标准”函数不同的是，在其中声明的变量，对于脚本其他部分的代码来说还是可见的。  

``` bash
#!/bin/bash

a=123
{ a=321; }
echo "a = $a"
```

### 中括号
1. 条件测试表达式放在中括号中  
``` bash
#!/bin/bash

a=5
if [ $a -lt 10 ]
then
    echo "a: $a"
else
    echo 'a>10'
fi
```

2. 数组下标  
```
#!/bin/bash

arr=(12 22 32)
arr[0]=10
echo ${arr[0]}
```

## 条件测试表达式
https://blog.csdn.net/ooooooobh/article/details/50587640  

## read
https://www.cnblogs.com/xiangzi888/archive/2012/03/27/2420084.html  



## 引用说明
大部分的示例来自于实验楼课程：  
https://www.shiyanlou.com/courses/944
