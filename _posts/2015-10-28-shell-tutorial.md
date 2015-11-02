---
layout: post
title: 学习Shell
---

最近闲来无事，于是乎把shell学习的计划提前了一下。

>> Shell 是一个用C语言编写的程序，它是用户使用Linux的桥梁。Shell既是一种命令语言，又是一种程序设计语言。
Shell 是指一种应用程序，这个应用程序提供了一个界面，用户通过这个界面访问操作系统内核的服务。
Ken Thompson的sh是第一种Unix Shell，Windows Explorer是一个典型的图形界面Shell。

###shell基本语法
所有的编程语言也无非是那几个方面的内容。只要熟悉那几个方面自然也就会了。关键就是多写多熟悉。

####变量
定义变量时候，变量名不加美元符号（$）,但是在时候的时候要在变量前面加美元符号（$）.例如：

```sh
name="xiaoming"
```
`注意，变量名和等号之间不能有空格，这可能和你熟悉的所有编程语言都不一样`
至于变量名的命名习惯都是司空见惯的那几条：

* 首个字符必须为字母（a-z，A-Z）
* 中间不能带空格和特殊符号，可以使用下划线(_)
* 不能使用bash的关键字

使用变量：

```sh
name="xiaoming"
echo $name
```

只读变量：在变量前面加`readonly`关键字

```sh
myUrl="http://see.xidian.edu.cn/cpp/shell/"
readonly myUrl
myUrl="http://see.xidian.edu.cn/cpp/danpianji/"
```

删除变量: 在变量前面调用`unset`方法

```sh
my_name="xiaohua"
unset $my_name
echo $my_name
```

变量类型

运行shell时，会同时存在三种变量：	

1) 局部变量

局部变量在脚本或命令中定义，仅在当前shell实例中有效，其他shell启动的程序不能访问局部变量。

2) 环境变量
所有的程序，包括shell启动的程序，都能访问环境变量，有些程序需要环境变量来保证其正常运行。必要的时候shell脚本也可以定义环境变量。	

3) shell变量

shell变量是由shell程序设置的特殊变量。shell变量中有一部分是环境变量，有一部分是局部变量，这些变量保证了shell的正常运行

####Shell特殊变量
这些特殊变量我也记不住，记个大概，需要的时候去翻就行了。
特殊变量列表

* $0	当前脚本的文件名
* $n	传递给脚本或函数的参数。n 是一个数字，表示第几个参数。例如，第一个参数是$1，第二个参数是$2。
* $#	传递给脚本或函数的参数个数。
* $*	传递给脚本或函数的所有参数。
* $@	传递给脚本或函数的所有参数。被双引号(" ")包含时，与 $* 稍有不同，下面将会讲到。
* $?	上个命令的退出状态，或函数的返回值。
* $$	当前Shell进程ID。对于 Shell 脚本，就是这些脚本所在的进程ID。

#####$* 和 $@ 的区别
$* 和 $@ 都表示传递给函数或脚本的所有参数，不被双引号(" ")包含时，都以"$1" "$2" … "$n" 的形式输出所有参数。

但是当它们被双引号(" ")包含时，"$*" 会将所有的参数作为一个整体，以"$1 $2 … $n"的形式输出所有参数；"$@" 会将各个参数分开，以"$1" "$2" … "$n" 的形式输出所有参数。

```sh
echo "print each param from \$@"
for var in $@
do
    echo "$var"
done
echo "print each param from \"\$*\""
for var in "$*"
do
    echo "$var"
done
echo "print each param from \"\$@\""
for var in "$@"
do
    echo "$var"
done
```

执行`sh test.sh a b c d`会输出以下的结果：

```sh
print each param from $@
a
b
c
d
print each param from "$*"
a b c d
print each param from "$@"
a
b
c
d
```

####替换
echo -e [string]这里 -e 表示对转义字符进行替换。
下面字符都可以出现在echo中：

* \\	反斜杠
* \a	警报，响铃
* \b	退格（删除键）
* \f	换页(FF)，将当前位置移到下页开头
* \n	换行
* \r	回车
* \t	水平制表符（tab键） 
* \v	垂直制表符

#####命令替换
用\`command\`进行命令替换,例如echo \`ls\`会输出当前目录的文件列表，而不是ls字符串。

#####变量替换
* ${var}	变量本来的值
* ${var:-word}	如果变量 var 为空或已被删除(unset)，那么返回 word，但不改变 var 的值。
* ${var:=word}	如果变量 var 为空或已被删除(unset)，那么返回 word，并将 var 的值设置为 word。
* ${var:?message}	如果变量 var 为空或已被删除(unset)，那么将消息 message 送到标准错误输出，可以用来检测变量 var 是否可以被正常赋值。若此替换出现在Shell脚本中，那么脚本将停止运行。
* ${var:+word}	如果变量 var 被定义，那么返回 word，但不改变 var 的值。
* ${!varprefix*}	匹配之前所有以varprefix开头进行声明的变量
* ${!varprefix@}	匹配之前所有以varprefix开头进行声明的变量

####运算
原生bash不支持简单的数学运算，但是可以通过其他命令来实现，例如 awk 和 expr，expr 最常用。

expr 是一款表达式计算工具，使用它能完成表达式的求值操作.

```sh
echo `expr 2 + 2`
```
会输出 4

注意：
* 表达式和运算符之间要有空格，例如 2+2 是不对的，必须写成 2 + 2，这与我们熟悉的大多数编程语言不一样。
* 完整的表达式要被 \` \` 包含.
* 乘号(*)前边必须加反斜杠(\)才能实现乘法运算；例如echo \`expr 2 \\\* 2\`

以下`a=10 b=20`

算数运算符： 	

* \+加法	\`expr $a + $b\` 结果为 30。
* \-减法	\`expr $a - $b\` 结果为 10。
* \*乘法	\`expr $a \* $b\` 结果为  200。
* /	除法	\`expr $b / $a\` 结果为 2。
* %	取余	\`expr $b % $a\` 结果为 0。
* =	赋值	a=$b 将把变量 b 的值赋给 a。
* ==	相等。用于比较两个数字，相同则返回 true。	[ $a == $b ] 返回 false。
* !=	不相等。用于比较两个数字，不相同则返回 true。

```sh
val=`expr $a \* $b`
echo "a * b : $val"
```


关系运算符：

* -eq	检测两个数是否相等，相等返回 true。	[ $a -eq $b ] 返回 true。
* -ne	检测两个数是否相等，不相等返回 true。	[ $a -ne $b ] 返回 true。
* -gt	检测左边的数是否大于右边的，如果是，则返回 true。	[ $a -gt $b ] 返回 false。
* -lt	检测左边的数是否小于右边的，如果是，则返回 true。	[ $a -lt $b ] 返回 true。
* -ge	检测左边的数是否大等于右边的，如果是，则返回 true。	[ $a -ge $b ] 返回 false。
* -le	检测左边的数是否小于等于右边的，如果是，则返回 true。	[ $a -le $b ] 返回 true。

```sh
a=10
b=20
if [ $a -eq $b ]
then
   echo "$a -eq $b : a is equal to b"
else
   echo "$a -eq $b: a is not equal to b"
fi
```

布尔运算符：

* !	非运算，表达式为 true 则返回 false，否则返回 true。	[ ! false ] 返回 true。
* -o	或运算，有一个表达式为 true 则返回 true。	[ $a -lt 20 -o $b -gt 100 ] 返回 true。
* -a	与运算，两个表达式都为 true 才返回 true。	[ $a -lt 20 -a $b -gt 100 ] 返回 false。

```sh
if [ $a -lt 100 -a $b -gt 15 ]
then
   echo "$a -lt 100 -a $b -gt 15 : returns true"
else
   echo "$a -lt 100 -a $b -gt 15 : returns false"
fi
```


字符串运算符:

* =	检测两个字符串是否相等，相等返回 true。	[ $a = $b ] 返回 false。
* !=	检测两个字符串是否相等，不相等返回 true。	[ $a != $b ] 返回 true。
* -z	检测字符串长度是否为0，为0返回 true。	[ -z $a ] 返回 false。
* -n	检测字符串长度是否为0，不为0返回 true。	[ -z $a ] 返回 true。
* str	检测字符串是否为空，不为空返回 true。	[ $a ] 返回 true。

```sh
if [ -z $a ]
then
   echo "-z $a : string length is zero"
else
   echo "-z $a : string length is not zero"
fi
```


文件测试运算符:

* -b file	检测文件是否是块设备文件，如果是，则返回 true。	[ -b $file ] 返回 false。
* -c file	检测文件是否是字符设备文件，如果是，则返回 true。	[ -b $file ] 返回 false。
* -d file	检测文件是否是目录，如果是，则返回 true。	[ -d $file ] 返回 false。
* -f file	检测文件是否是普通文件（既不是目录，也不是设备文件），如果是，则返回 true。	[ -f $file ] 返回 true。
* -g file	检测文件是否设置了 SGID 位，如果是，则返回 true。	[ -g $file ] 返回 false。
* -k file	检测文件是否设置了粘着位(Sticky Bit)，如果是，则返回 true。	[ -k $file ] 返回 false。
* -p file	检测文件是否是具名管道，如果是，则返回 true。	[ -p $file ] 返回 false。
* -u file	检测文件是否设置了 SUID 位，如果是，则返回 true。	[ -u $file ] 返回 false。
* -r file	检测文件是否可读，如果是，则返回 true。	[ -r $file ] 返回 true。
* -w file	检测文件是否可写，如果是，则返回 true。	[ -w $file ] 返回 true。
* -x file	检测文件是否可执行，如果是，则返回 true。	[ -x $file ] 返回 true。
* -s file	检测文件是否为空（文件大小是否大于0），不为空返回 true。	[ -s $file ] 返回 true。
* -e file	检测文件（包括目录）是否存在，如果是，则返回 true。	[ -e $file ] 返回 true。

```sh
file="/var/www/tutorialspoint/unix/test.sh"
if [ -r $file ]
then
   echo "File has read access"
else
   echo "File does not have read access"
fi
```


####注释
sh里没有多行注释，只能每一行加一个#号

* 如果在开发过程中，遇到大段的代码需要临时注释起来，过一会儿又取消注释，怎么办呢？每一行加个#符号太费力了，可以把这一段要注释的代码用一对花括号括起来，定义成一个函数，没有地方调用这个函数，这块代码就不会执行，达到了和注释一样的效果。


####字符串
字符串单双引号和其他语言类似区别在于转义。
字符串操作：

* {#string}	$string的长度	 
* ${string:position}	在$string中, 从位置$position开始提取子串
* ${string:position:length}	在$string中, 从位置$position开始提取长度为$length的子串
* ${string#substring}	从变量$string的开头, 删除最短匹配$substring的子串
* ${string##substring}	从变量$string的开头, 删除最长匹配$substring的子串
* ${string%substring}	从变量$string的结尾, 删除最短匹配$substring的子串
* ${string%%substring}	从变量$string的结尾, 删除最长匹配$substring的子串
* ${string/substring/replacement}	使用$replacement, 来代替第一个匹配的$substring
* ${string//substring/replacement}	使用$replacement, 代替所有匹配的$substring
* ${string/#substring/replacement}	如果$string的前缀匹配$substring, 那么就用* $ 
* replacement来代替匹配到的$substring
* ${string/%substring/replacement}	如果$string的后缀匹配$substring, 那么就用$replacement来代替匹配到的$substring

```sh
string="alibaba is a great company"
echo ${string:1:4} #输出lib
```

####数组
数组可以用一下方式来定义

```sh
array_name=(value0 value1 value2 value3)
```
还可以单独定义数组的各个分量：
```sh
array_name[0]=value0
```
读取数组元素值的一般格式是:
```sh
value=${array_name[2]}
```
关于Array的一些方法会和字符串的一些类似，可以试着用字符串的方法在数组上尝试使用：

获取数组长度：
```sh
length=${#array_name[@]}
```

####控制
`在shell中控制语句的作用域结尾都是该控制符单词的翻转`,例如if...fi.
#####if语句
和其他语言一样if有三种形式，如果熟悉其他语言，记住它与其他语言的语法格式区别。

* if 

```sh
if [ expression ]
then
   Statement(s) to be executed if expression is true
fi
```
* if else

```sh
if [ expression ]
then
   Statement(s) to be executed if expression is true
else
   Statement(s) to be executed if expression is not true
fi
```

*if elif

```sh
if [ expression 1 ]
then
   Statement(s) to be executed if expression 1 is true
elif [ expression 2 ]
then
   Statement(s) to be executed if expression 2 is true
elif [ expression 3 ]
then
   Statement(s) to be executed if expression 3 is true
else
   Statement(s) to be executed if no expression is true
fi
```

#####case 语句
其他语言中的swith控制。

```sh
case 值 in
模式1)
    command1
    command2
    command3
    ;;
模式2）
    command1
    command2
    command3
    ;;
*)
    command1
    command2
    command3
    ;;
esac
```
case工作方式如上所示。取值后面必须为关键字 in，每一模式必须以右括号结束。取值可以为变量或常数。匹配发现取值符合某一模式后，其间所有命令开始执行直至 ;;。;; 与其他语言中的 break 类似，意思是跳到整个 case 语句的最后。

取值将检测匹配的每一个模式。一旦模式匹配，则执行完匹配模式相应命令后不再继续其他模式。如果无一匹配模式，使用星号 * 捕获该值，再执行后面的命令。

#####for 语句
```sh
for 变量 in 列表
do
    command1
    command2
    ...
    commandN
done
```
例子：
```sh
for loop in 1 2 3 4 5
do
    echo "The value is: $loop"
done
```

#####while 语句
```sh
while command
do
   Statement(s) to be executed if command is true
done
```
例子：
while循环可用于读取键盘信息。下面的例子中，输入信息被设置为变量FILM，按<Ctrl-D>结束循环。
复制纯文本新窗口

```sh
echo 'type <CTRL-D> to terminate'
echo -n 'enter your most liked film: '
while read FILM
do
    echo "Yeah! great film the $FILM"
done
```

#####until 语句
until 循环执行一系列命令直至条件为 true 时停止。until 循环与 while 循环在处理方式上刚好相反。一般while循环优于until循环，但在某些时候，也只是极少数情况下，until 循环更加有用

```sh
until command
do
   Statement(s) to be executed until command is true
done
```
例如，使用 until 命令输出 0 ~ 9 的数字：
复制纯文本新窗口

```sh
a=0
until [ ! $a -lt 10 ]
do
   echo $a
   a=`expr $a + 1`
done
```

#####break和conntinue
* break命令允许跳出所有循环（终止执行后面的所有循环）
* continue它不会跳出所有循环，仅仅跳出当前循环。

#####函数
函数可以让我们将一个复杂功能划分成若干模块，让程序结构更加清晰，代码重复利用率更高。像其他编程语言一样，Shell 也支持函数。Shell 函数必须先定义后使用。

删除函数使用`unset -f [function_name]`

* $#	传递给函数的参数个数。
* $*	显示所有传递给函数的参数。
* $@	与$*相同，但是略有区别，请查看Shell特殊变量。
* $?	函数的返回值。

```sh
function_name () {
    list of commands
    [ return value ]
}
```

例如：

```sh
number_one () {
   echo "Url_1 is http://see.xidian.edu.cn/cpp/shell/"
   number_two
}
number_two () {
   echo "Url_2 is http://see.xidian.edu.cn/cpp/u/xitong/"
}
number_one
```

#####重定向
######命令输出重定向的语法为：

* 输出重定向会覆盖文件内容,如果不希望文件内容被覆盖，可以使用 >> 追加到文件末尾

```sh
$ command > file
```

例如：

```sh
$ echo line 2 >> users
```

文档文本：
```sh
command << delimiter
    document
delimiter
```
它的作用是将两个 delimiter 之间的内容(document) 作为输入传递给 command。

######：命令输入重定向的语法为：
```sh
command < file
```
例如，计算 users 文件中的行数，可以使用下面的命令：

```sh
wc -l users
users
```
也可以将输入重定向到 users 文件：
```sh
wc -l < users
```
注意：上面两个例子的结果不同：第一个例子，会输出文件名；第二个不会，因为它仅仅知道从标准输入读取内容。

一般情况下，每个 Unix/Linux 命令运行时都会打开三个文件：

* 标准输入文件(stdin)：stdin的文件描述符为0，Unix程序默认从stdin读取数据。
* 标准输出文件(stdout)：stdout 的文件描述符为1，Unix程序默认向stdout输出数据。
* 标准错误文件(stderr)：stderr的文件描述符为2，Unix程序会向stderr流中写入错误信息。

如果希望 stderr 重定向到 file，可以这样写：
```sh
$command 2 > file
```
如果希望 stderr 追加到 file 文件末尾，可以这样写：
```sh
$command 2 >> file
```
2 表示标准错误文件(stderr)。

如果希望将 stdout 和 stderr 合并后重定向到 file，可以这样写：
```sh
$command > file 2>&1
```
或
```sh
$command >> file 2>&1
```
如果希望对 stdin 和 stdout 都重定向，可以这样写：
```sh
$command < file1 >file2
```
command 命令将 stdin 重定向到 file1，将 stdout 重定向到 file2。 

* command > file	将输出重定向到 file。
* command < file	将输入重定向到 file。
* command >> file	将输出以追加的方式重定向到 file。
* n > file	将文件描述符为 n 的文件重定向到 file。
* n >> file	将文件描述符为 n 的文件以追加的方式重定向到 file。
* n >& m	将输出文件 m 和 n 合并。
* n <& m	将输入文件 m 和 n 合并。
* << tag	将开始标记 tag 和结束标记 tag 之间的内容作为输入。

注意：

* `如果希望执行某个命令，但又不希望在屏幕上显示输出结果，那么可以将输出重定向到 /dev/null：`

```sh
$ command > /dev/null
```

#####文件包含
像其他语言一样，Shell 也可以包含外部脚本，将外部脚本的内容合并到当前脚本。

Shell 中包含脚本可以使用：
复制纯文本新窗口

```sh
. filename
```

或

```sh
source filename
```

两种方式的效果相同，简单起见，一般使用点号(.)，但是注意点号(.)和文件名中间有一空格。

例如，创建两个脚本，一个是被调用脚本 subscript.sh，内容如下：

```sh
url="http://see.xidian.edu.cn/cpp/view/2738.html"
```

一个是主文件 main.sh，内容如下：

```sh
. ./subscript.sh
echo $url
执行脚本：
$chomd +x main.sh
./main.sh
http://see.xidian.edu.cn/cpp/view/2738.html
$
```

注意：被包含脚本不需要有执行权限。


关于这篇文章大部分获取自[shell教程](http://c.biancheng.net/cpp/view/6994.html).仅供自己学习。由于原文章仅仅适合快速入门或者不求甚解的人，以后随着自己知识累计会增删该文章。