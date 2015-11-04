---
layout: post
title: AppleScript（四）类
---

类是一类用于共享特征的对象。 AppleScript的定义在AppleScript的脚本中常用的对象，如别名，布尔值，整数，文本等类。

在脚本中的每个对象是一特定类的一个实例，并具有相同的属性（包括类属性），可以包含同种元素，和支持相同类型的操作和强制转换的作为该类型的其他对象。对象被AppleScript的类型的实例可以在任何地方使用在脚本中的，它们不需要是在一个指定的应用程序内`tell`块，。

编写脚本的应用程序也可以定义自己的类，如窗口和文件，其中通常含有基于许多本章中所描述的基本的AppleScript类的属性和元素。脚本获得在定义它们的应用程序的上下文中这些对象。更多参考[Scripting Interface Guidelines](http://developer.apple.com/technotes/tn2002/tn2106.html)

#####alias
持久性的指向一个存在的文件系统中的文件，文件夹或者声音。

######alias对象的属性：

* class

 	* Access:	read only
 	* Class:	class
 	
 	 The class identifier for the object. The value is always alias.
 	 
* POSIX path

 	* Access:	read only
 	* Class:	text
 	
 	The POSIX-style path to the object.
 	
######强制转换支持：
alias -> text
alias -> list

######实例：

```applescript
set zApp to choose application as alias -- (then choose Finder.app)
--result: alias "Leopard:System:Library:CoreServices:Finder.app:"
class of zApp --result: alias
zApp as text --result: "Leopard:System:Library:CoreServices:Finder.app:"
zApp as list --result: {alias "Leopard:System:Library:CoreServices:Finder.app:"}
```

POSIX path 属性获取指向文件的路径（POSIX）:

```applescript
POSIX path of zApp --result: "/System/Library/CoreServices/Finder.app/"
```

注意：你只能指向已存在的文件或者文件夹。


#####application
本地程序或者可用的远端程序。

下面描述了`application`对象所有的属性。但是根据不同的程序可能还有一些附加的属性，这看是什么程序。

######application对象的属性：

* class
 	* Access:	read only
 	* Class:	class
 	
 	The class identifier for the object. The value is always application.
 	
* frontmost

 	* Access:	read only
 	* Class:	boolean
 	
 	Is the application frontmost?
 	
 	Starting in AppleScript 2.0, accessing an application’s frontmost property returns a Boolean value without launching the application or sending it an event.
 	
 	The value of frontmost for background-only applications, UI element applications such as System Events, and applications that are not running is always false.
 	
* id
 	* Access:	read only
 	* Class:	text
 	
 	The application’s bundle identifier (the default) or its four-character signature code. (New in AppleScript 2.0.)
 	
 	For example, the bundle identifier for the TextEdit application is "com.apple.TextEdit". Its four-character signature code is 'ttxt'. If you ask for an application object’s id property, you will get the bundle identifier version, unless the application does not have a bundle identifier and does have a signature code.
 	
* name
 	* Access:	read only
 	* Class:	text
 	
 	The application’s name.
 	
 	Starting in AppleScript 2.0, accessing an application’s name property returns the application name as text without launching the application or sending it an event.
 	
* running
 	* Access:	read only
 	* Class:	boolean
 	
 	Is the application running? (New in AppleScript 2.0.)
 	
 	Accessing an application’s running property returns a Boolean value without launching the application or sending it an event.
 	
 	You can also ask the System Events utility application whether an application is running. While it requires more lines in your script to do so, that option is available in earlier versions of the Mac OS.
 	
* version
 	* Access:	read only
 	* Class:	text
 	
 	The application’s version.
 	
 	Starting in AppleScript 2.0, accessing this property returns the application version as text without launching the application or sending it an event.
 	
######强制转换：
 application -> list
 
######实例：
 你确定你当前电脑上面的应用是否在运行（在远端电脑上不会有任何作用）
 
 ```applescript
 tell application "iTunes" -- doesn't automatically launch app
    if it is running then
        pause
    end if
end tell
```

也可以用如下的方式:

```applescript
if application "iTunes" is running
    tell application "iTunes" to pause
end if
```

下面的语句分别通过签名，bundle id 和 POSIX路径来指定应用：

```applescript
application id "ttxt"
application id "com.apple.TextEdit"
application "/Applications/TextEdit.app"
```

你可以通过`tell`语句来指定远端应用。详情[“Remote Applications”](https://developer.apple.com/library/mac/documentation/AppleScript/Conceptual/AppleScriptLangGuide/conceptual/ASLR_fundamentals.html#//apple_ref/doc/uid/TP40000983-CH218-SW9)

######特别注意：
Starting in OS X v10.5, there are several changes in application behavior

* 应用程序启动隐藏
	Applescript如果要发送命令个应用的时候会启动这个应用。但是它通常是可见的。但是现在AppleScript启动应用默认是不可见的。如果你需要让他可见请使用`activate`.
* 延迟加载
	当运行脚本，只有要向应用发送命令时候才会启动它。那么就有可能脚本内指向了一个不存在的应用。之前的版本的AppleScript会在脚本运行之前定位这个应用。
* 应用自动点位
	在`tell`块中一个对象指向了一个应用，在每次运行的时候都会重新取值。
	

#####boolean
逻辑判断值， `true`或者`false`.

######boolean对象的属性
* class

 	* Access:	read only
 	* Class:	class
 	
 	The class identifier for the object. The value is always boolean.
	
######运算符：

boolean可以结合`and`, `or`, `not`, `&`, `=`, 和 `≠`来运算。同样可以用`is equal to`,`is not equal to`,`equals`和其他的。

* `=`运算符：如果两边都返回相同的就返回true，否则返回false。(`≠`与之相反)
* `and`和`or`运算符：和其他语言一样，`and`两边都为true就返回true，`or`有一边为true就返回true。
* `not`运算符：取反，如果true就返回false。反之亦然。
* `&`运算符：创建一个包含两边bool值得数组。

```applescript
true & false --result: {true, false}
```
######实例：

```applescript
true
false
paragraphCount > 2
``

#####class
对象或者值的class
所有的类都有这个属性，它的值是一个标识符。

######class对象的属性：

* class
 	* Access:	read only
 	* Class:	class
 	
 	The class identifier for the object. The value of this property is always class.
 
######操作符：
它可以利用`&`,`=`,`≠`和`as`来进行运算。

可以利用`as`来进行强制转换。以下把text 转换为 real：

```applescript
"1.5" as real --result: 1.5
```

######强制转换：
class -> list	
class -> text

######实例：

```applescript
class of text --result: class
class of integer --result: class
class of true --result: boolean
```

#####constant
有预定义值的常量

常数一般用于枚举类型。您不能定义在脚本中的常数;常数可以仅通过应用程序和由AppleScript的定义。更多参考[Constants in AppleScript](https://developer.apple.com/library/mac/documentation/AppleScript/Conceptual/AppleScriptLangGuide/conceptual/ASLR_fundamentals.html#//apple_ref/doc/uid/TP40000983-CH218-BAJBDEJI)

######constant对象的属性：

* class
 	* Access:	read-only
 	* Class:	class
 	
 	The class identifier for the object. The value of this property is always constant.
 	
######操作符：	
 `&`, `=`, `≠`, 和 `as`
 
######实例：
 
 除了由AppleScript的定义的常量，应用程序通常定义用于命令参数或属性值枚举类型。例如，iTunes的搜索命令定义了这些常数的指定搜索areaIn除了由AppleScript的定义的常量，应用程序通常定义用于命令参数或属性值枚举类型。例如，iTunes的搜索命令定义这些常数用于指定搜索区域
 
 ```applescript
considering punctuation but ignoring hyphens and white space
    "bet-the farm," = "BetTheFarm," --result: true
end considering
class of hyphens --result: constant
 ```
 
#####date
 一个星期的第几天，日期和时间。

获取当前时间用`current date`:

```applescript
set theDate to current date
--result: "Friday, November 9, 2007 11:35:50 AM"
```
可以通过它的属性获取`date`的不同的部分。

######date对象的属性：	

* class
 	* Access:	read only
 	* Class:	class
 	
 	The class identifier for the object. The value of this property is always date.
 	
* day
 	* Access:	read/write
 	* Class:	integer
 	
 	Specifies the day of the month of a date object.
 	
* weekday
 	* Access:	read only
 	* Class:	constant
 	
 	Specifies the day of the week of a date object, with one of these constants: Monday, Tuesday, Wednesday, Thursday, Friday, Saturday, or Sunday.
 	
* month
 	* Access:	read/write
 	* Class:	constant
 	
 	Specifies the month of the year of a date object, with one of the constants January, February, March, April, May, June, July, August, September, October, November, or December.
 	
* year
 	* Access:	read/write
 	* Class:	integer
 	
 	Specifies the year of a date object; for example, 2004.
 	
* time
 	* Access:	read/write
 	* Class:	integer
 	
 	Specifies the number of seconds since midnight of a date object; for example, 2700 is equivalent to 12:45 AM (2700 / 60 seconds = 45 minutes).
 	
* date string
 	* Access:	read only
 	* Class:	text
 	
 	A text object that specifies the date portion of a date object; for example, "Friday, November 9, 2007".
 	
 	To obtain a compact version of the date, use short date string . For example, short date string of (current date) --result: "1/27/08".
 	
* time string
 	* Access:	read only
 	* Class:	text
 	
 	A text object that specifies the time portion of a date object; for example, "3:20:24 PM".
 	
######操作符：
`&`, `+`, `–`, `=`, `≠`, `>`, `≥`, `<`, `≤`, `comes before`, `comes after`, and `as`.

######date的加减操作：

```applescript
date + timeDifference
--result: date
date - date
--result: timeDifference
date - timeDifference
--result: date
```
timeDifference 是`intege`值，单位是秒。可以用如下的常量：

```applescript
minutes
    60
hours
    60 * minutes
days
    24 * hours
weeks
    7 * days
```
例如：

```applescript
date "Friday, November 9, 2007" + 4 * days + 3 * hours + 2 *  minutes
--result: date "Tuesday, November 13, 2007 3:02:00 AM"
```

######强制装换：
date -> list
date -> text

######实例：

```applescript
date "8/9/2007, 17:06"
     --result: date "Thursday, August 9, 2007 5:06:00 PM"
date "7/16/70"
     --result: date "Wednesday, July 16, 2070 12:00:00 AM"
date "12:06" -- specifies a time on the current date
     --result: date "Friday, November 9, 2007 12:06:00 PM"
date "Sunday, December 12, 1954 12:06 pm"
     --result: date "Sunday, December 12, 1954 12:06:00 PM"
```

如果你要相对于一个日期指定时间：

```applescript
date "2:30 am" of date "Jan 1, 2008"
    --result: date "Tuesday, January 1, 2008 2:30:00 AM"
date "2:30 am" of date "Sun Jan 27, 2008"
    --result: date "Sunday, January 27, 2008 2:30:00 AM"
date "Nov 19, 2007" relative to date "3PM"
    --result: date "Monday, November 19, 2007 3:00:00 PM"
date "1:30 pm" in date "April 1, 2008"
    --result: date "Tuesday, April 1, 2008 1:30:00 PM"
```

要格式化日期可以如下：

```applescript
set myDate to date "3/4/2008"
```


#####file
文件系统中文件，文件夹或者卷的引用。和`alias`有相同的属性。不一样的是，它可以指向不存在的。

######强制转换：	
file -> text
file -> list

实例：

```applescript
set fp to open for access file "Leopard:Users:myUser:NewFile"
close access fp
```

#####integer
整数

######integer的属性：

* class
 	* Access:	read-only
 	* Class:	class
 	
 	The class identifier for the object. The value of this property is always integer.
 	
######操作符：	
 `+`, `-`, `*`, `÷` (或者 `/`), `div`, `mod`, `^`, `=`, `≠`, `>`, `≥`, `<`, 和 `≤`
 
######实例：	
和其他的语言没太大区别

```applescript
set myCount to 7 as number
class of myCount --result: integer
```

#####list
有序集合。里面的每一项可以属于任何类。空list可以用{}.

######list的属性：

* class
 	* Access:	read-only
 	* Class:	class
 	
 	The class identifier for the object. The value of this property is always list.
 	
* length
 	* Access:	read only
 	* Class:	integer
 	
 	Specifies he number of items in the list.
 	
* rest
 	* Access:	read only
 	* Class:	list
 	
 	A list containing all items in the list except the first item.
 	
* reverse
 	* Access:	read only
 	* Class:	list
 	
 	A list containing all items in the list, but in the opposite order.
 	
数组里面的元素：
* item
  `item`来取list里面的元素：
  
  ```applescript
  item 2 of {"soup", 2, "nuts"} --result: 2
  ```
  
######操作符：
`&`, `=`, `≠`, `starts with`, `ends with`, `contains`, 和 `is contained by`.
  
命令：	
你可以用count命令来获取数组中有多少元素：
```applescript
count {"a", "b", "c", 1, 2, 3} --result: 6
length of {"a", "b", "c", 1, 2, 3} --result: 6
```

######强制转换：	
AppleScript支持只有一个元素的数组转换成任何类。也可以任意的数组转化成`text`。

```applescript
{5, "George", 11.43, "Bill"} as text --result: "5George11.43Bill"
```

######实例：	

```applescript
class of {"this", "is", "a", "list"} --result: list
item 3 of {"this", "is", "a", "list"} --result: "a"
items 2 thru 3 of {"soup", 2, "nuts"} --result: {2, "nuts"}
{"This"} & {"is", "a", "list"} --result: {"This", "is", "a", "list"}
```

```applescript
set numItems to 4000
set t to (time of (current date)) --Start timing
repeat with n from 1 to numItems
    item n of bigList
end repeat
set total to (time of (current date)) - t --End timing
```

