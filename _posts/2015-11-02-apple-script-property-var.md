---
layout: post
title: AppleScript（一）变量和属性
---

###变量和属性
####定义属性：

* property propertyLabel : expression
	* propertyLabel 属性名称
	* expression 属性的值
	
实例：

```applescript
property windowCount : 0
property defaultName : "Barry"
property strangeValue : (pi * 7)^2
```

当定义之后可以通过`copy`或者`set`来赋值。

属性值不会每次运行脚本的时候重置，它的值会一直保留到下次编译。

你不能在句柄中声明属性，但是你可以在句柄中访问属性。

####声明变量
可以通过`set`或`copy`来声明变量并赋值。例如：

```applescript
set circumference to pi * 3.5 --result: 10.995574287564
copy circumference to savedResult --result: 10.995574287564 (copy of 1st variable)
```
你可利用`set`和`copy`和其他语言一样用变量，表达式或者简单的值给变量赋值,他会替换到变量原本的值。如果你要只想指向已存在另一对象的本身就是用[a reference to]()

#####本地变量

* local variableName [, variableName ]…
	* variableName 变量名称
	
实例：

```applescript
local windowCount -- defines one variable
local agentName, agentNumber, agentHireDate -- defines three variables
```
你不可以在声明的时候给本地变量赋值，但是可以用`copy`和`set`进行初始化。例如给以上定义的变量初始化：

```applescript
set windowCount to 0 -- initialize to zero; an integer
set agentName to "James Smith" -- assign agent name; a text string
set agentNumber to getAgentNumber(agentName) -- call handler; an integer
copy current date to agentHireDate -- call current date command; a date
```
#####全局变量

* global variableName [, variableName ]…
	* variableName 变量名称

实例：

```applescript
global gAgentCount
global gStatementDate, gNextAgentNumber
```
和本地变量一样你可利用`set`和`copy`和其他语言一样用变量，表达式或者简单的值给变量赋值。

#####set和copy命令
`copy`对创建一个独立的副本。而set总是指向原来的值。例如：把date, list, record 和 script 这些可变对象赋值给变量的时候，`set`是浅复制，而`copy`是深赋值，这和object-c多少有些类似的。

```applescript
set myList to { 1, 2, 3 }
set yourList to myList
set item 1 of myList to 4
```
结果：
myList ：{4，2，3} ，yourList：{4，2，3}

copy赋值：

```applescript
set myList to { 1, 2, 3 }
copy yourList to myList
set item 1 of myList to 4
```
结果：
myList ：{4，2，3} ，yourList：{1，2，3}

####变量和属性的作用域

|声明类型|作用域|生命周期|
|---|:---|:---:|
| property x: 3  | Everywhere in script       | Reset when script is recompiled|
| global x       | Everywhere in script       | Reset when reinitialized in script or when script is recompiled|
| local x        | Within run handler only    | Reset when script is run|
| set x to 3     | Within run handler only    | Reset when script is run|

由于相对简单，只一个实例：

```applescript
property currentCount : 0
increment()
 
on increment()
    set currentCount to currentCount + 1
    display dialog "Count is now " & currentCount  & "."
end increment
```
由于属性只有在重新编译的时候才会重置，那么currentCount第一次裕兴的时候是1，第二次运行就是2，其他依次类推。