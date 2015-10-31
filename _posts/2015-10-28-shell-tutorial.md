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

```shell
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

关系运算符：

* -eq	检测两个数是否相等，相等返回 true。	[ $a -eq $b ] 返回 true。
* -ne	检测两个数是否相等，不相等返回 true。	[ $a -ne $b ] 返回 true。
* -gt	检测左边的数是否大于右边的，如果是，则返回 true。	[ $a -gt $b ] 返回 false。
* -lt	检测左边的数是否小于右边的，如果是，则返回 true。	[ $a -lt $b ] 返回 true。
* -ge	检测左边的数是否大等于右边的，如果是，则返回 true。	[ $a -ge $b ] 返回 false。
* -le	检测左边的数是否小于等于右边的，如果是，则返回 true。	[ $a -le $b ] 返回 true。

布尔运算符：

* !	非运算，表达式为 true 则返回 false，否则返回 true。	[ ! false ] 返回 true。
* -o	或运算，有一个表达式为 true 则返回 true。	[ $a -lt 20 -o $b -gt 100 ] 返回 true。
* -a	与运算，两个表达式都为 true 才返回 true。	[ $a -lt 20 -a $b -gt 100 ] 返回 false。

字符串运算符:

* =	检测两个字符串是否相等，相等返回 true。	[ $a = $b ] 返回 false。
* !=	检测两个字符串是否相等，不相等返回 true。	[ $a != $b ] 返回 true。
* -z	检测字符串长度是否为0，为0返回 true。	[ -z $a ] 返回 false。
* -n	检测字符串长度是否为0，不为0返回 true。	[ -z $a ] 返回 true。
* str	检测字符串是否为空，不为空返回 true。	[ $a ] 返回 true。

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