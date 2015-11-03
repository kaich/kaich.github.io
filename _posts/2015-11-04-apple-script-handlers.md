---
layout: post
title: AppleScript（三）处理程序
---

处理程序帮助脚本开发者重用他们的代码，一个处理程序是一系列的语句的集合。处理程序也就是我们所熟知函数，字例程或者方法。

####处理程序基础
处理程序是可以通过名称调用语句的集合。处理程序是，在多个地方进行相同的动作脚本很有用。您可以包执行特定任务作为处理报表，给它一个描述性的名称，并在脚本的任何地方调用它。这使得脚本短且容易维护。   
一个脚本可以包含一个或多个处理程序。但是你不能在处理程序中定义另一个处理程序。   
处理程序可以用`return`语句来返回一个值并且结束这个处理程序。

#####定义一个简单的处理程序
下面的处理程序中，处理程序的名字是rock，它的参数是around the clock，around是标签，而clock是参数名。

```applescript
on rock around the clock
    display dialog (clock as text)
end rock
```
可以就像写英语那样调用它：

```applescript
rock around the current date -- call handler to display current date
```

处理程序可以没有参数。为了表明一个处理程序没有参数，你必须在程序程序名称之后加一对括号。

```applescript
on helloWorld()
    display dialog "Hello World"
end
 
helloWorld() -- Call the handler
```

#####处理程序和参数标签
处理程序和标签参数可以有直接参数（参数不带标签）。直接参数有个例外，它必须紧跟在处理程序名称之后，标签参数可以以任何顺序出现。包含参数在`given`,`with`和`without`字句之中(可以任意多个)  

findNumbers处理程序用一个专门的表情`given`定义了一个标签是given rounding的参数。

```applescript
to findNumbers of numberList above minLimit given rounding:roundBoolean
        set resultList to {}
        repeat with i from 1 to (count items of numberList)
            set x to item i of numberList
            if roundBoolean then -- round the number
                -- Use copy so original list isn’t modified.
                copy (round x) to x
            end if
            if x > minLimit then
                set end of resultList to x
            end if
        end repeat
        return resultList
end findNumbers
```

调用:

```applescript
set myList to {2, 5, 19.75, 99, 1}
findNumbers of myList above 19 given rounding:true
    --result: {20, 99}
findNumbers of myList above 19 given rounding:false
    --result: {19.75, 99}
```

也可以用`with`或者`without`来指定rounding的参数是true或者false。AppleScript自动把given rounding:true转换成with rounding，把given rounding:false转换成without rounding。

```applescript
findNumbers of {5.1, 20.1, 20.5, 33} above 20 with rounding
    --result: {33}
 
findNumbers of {5.1, 20.1, 20.5, 33.7} above 20 without rounding
    --result: {20.1, 20.5, 33.7}
```

另一个使用标签参数的例子：

```applescript
to check for yourNumber from startRange thru endRange
    if startRange ≤ yourNumber and yourNumber ≤ endRange then
        display dialog "Congratulations! Your number is included."
    end if
end check
```
调用：

```applescript
check for 8 from 7 thru 10 -- call the handler
```

#####带位置参数的处理程序
定义一个带位置参数的处理程序，你必须在调用的时候按照定义的顺序去调用。而标签却不一定要按照顺序。这和ruby或者其他程序的方法定义是一致的。

```applescript
on minimumValue(x, y)
    if x < y then
        return x
    else
        return y
    end if
end minimumValue
 
-- To call minimumValue:
minimumValue(5, 105) --result: 5
```
#####带图案位置参数的处理程序
你可以定义一个处理程序，当调用的时候要匹配位置参数定义的模式。下面定义了一个单独的参数，这个参数是包含两个元素的序列（list）。

```applescript
on displayPoint({x, y})
    display dialog ("x = " & x & ", y = " & y)
end displayPoint
 
-- Calling the handler:
set testPoint to {3, 8}
displayPoint(testPoint)
```

#####Handlers with Interleaved Parameters

这种方式和object-c定义方法的模式一样：

```applescript
on areaOfCircleWithRadius:radius
    return radius ^ 2 * pi
end areaOfCircleWithRadius:
```

实例2：

```applescript
on areaOfRectangleWithWidth:w height:h
    return w * h
end areaOfRectangleWithWidth:height:
```

#####参数说明
处理函数的参数可以带说明。例如，指定参数是一个class,可以在参数名字后面带上 as class 或者 as {class,...}.

```applescript
on factorial(x as integer)
```
它会指定x是整数，如果x调用的时候转换为整数失败。AppleScript会爆出一个适当的错误。你可以用`try`去捕获它。

在标签参数中，你可以给参数一个默认值，在参数名后面加`:literal`.

```applescript
on make new theClass with data theData : missing value
```

现在这个处理程序可以调用时不指定with data参数。那么theData会被设置成missing value。

参数既可以有指定说明又可以带默认值。

```applescript
on make new theClass with properties theProperties as record : {}
```

#####递归处理程序
处理程序内部调用自己。下面是一个阶乘的处理程序。

```applescript
on factorial(x)
    if x > 0 then
        return x * factorial(x - 1)
    else
        return 1
    end if
end factorial
 
-- To call factorial:
factorial(10)   --result: 3628800
```

#####引用传递和值传递
在一个处理程序中，每个参数就像是一个变量，提供访问传递的信息。 AppleScript以引用方式传递所有参数，这意味着，一个传递变量的处理程序，并调用者之间共享，好象该处理使用set命令的变量。只有可变对象其实是可以改变：但是，要记住一点提出在使用复制和set命令是非常重要的。

* 对于可变对象(`date`,`list`,`record`,`script`)是引用传递。如果处理程序改变了参数的值，那么原来对象的值也会改变。
* 对于所有其他类，都是采用值传递。尽管AppleScript传递的是原对象的引用，源对象不能被改变。如果处理程序给它赋新值，原来的对象不会被改变。


#####在tell语句中调用处理程序
在tell语句中调用处理程序，你必须使用`me`或者`my`指定这个处理程序是脚本的一部分而不是命令对象的一部分。

例如：minimumValue在tell中被调用了，如果不包含`me`,它将引发错误。因为程序文本编辑器（TextEdit）不能识别minimumValue命令。

```applescript
tell front document of application "TextEdit"
    minimumValue(12, 400) of me
    set paragraph 1 to result as text
end tell
--result: The handler call is successful.
```


####脚本程序中的处理程序
脚本的应用是其唯一的功能是运行与它相关的脚本的应用程序。脚本应用程序包含的处理程序，使他们能够响应命令。例如，很多脚本应用程序可以响应运行命令open命令。脚本应用程序接收时，它启动一个运行命令，每当另一个图标被丢弃其在Finder图标打开的命令。它也可以包含其它处理程序到诸如退出或打印命令作出响应。  

当保存在脚本编辑器的脚本，您可以创建从文件格式选项中选择任一应用程序或应用程序包的脚本程序。保存在一个简单的格式。

当创建一个脚本程序，你可以指定它时报包含一个启动画面。无论你在Script Editor的脚本窗口的描述窗口中写了什么，它都会先在在启动画面上。你可以指定指定脚本程序在运行完之后是否还保持打开状态。默认是运行完之后退出。

就像其他程序一样你可以通过Finder运行脚本程序。如果他包含启动画面，用户要点击运行按钮或者enter键在脚本正真运行之前。

```applescript
tell application "Finder"
    close front window
end tell
```

如果你没有指定启动画面，他将自动的运行一次，关闭最顶层的Finder窗口，然后退出。

#####run处理程序
当你运行一个脚本或启动脚本的应用程序，它的run处理程序被调用。脚本的运行处理程序以两种方式之一定义：

* 作为一个隐含的run处理程序，其中包括在脚本中的任何处理或嵌套脚本对象之外声明的所有语句。声明属性和全局变量不被认为在这种情况下，也就是说语句，它们不被认为是隐式运行处理程序的一部分
* 显式run处理程序，它是闭合的 on run ... end run.就像其他处理程序一样。

隐式run:

```applescript
sayHello()
 
on sayHello()
    display dialog "Hello"
end sayHello
```

显示run:

```applescript
on run
    sayHello()
end run
 
on sayHello()
    display dialog "Hello"
end sayHello
```

#####open处理程序
Mac应用程序，包括脚本的应用程序，无论用户何时下删除的文件，文件夹或磁盘图标上的应用程序的Finder图标，即使该应用程序已经运行都可以收到一个open命令。

例如下面的open处理程序，让所有的文件路径记录到最顶层的文本编辑器中：

```applescript
on open names
    set pathNamesString to "" -- Start with empty text string.
    repeat with i in names
        -- In this loop, you can perform operations on each dropped item.
        -- For now, just get the name and append a return character.
        set iPath to (i as text)
        set pathNamesString to pathNamesString & iPath & return
    end repeat
    -- Store list in open document, to verify what was dropped.
    tell application "TextEdit"
        set paragraph 1 of front document to pathNamesString
    end tell
    return
end open
```

Files, folders, or disks are not moved, copied, or affected in any way by merely dropping them on a script application. However, the script application’s handler can tell Finder to move, copy, or otherwise manipulate the items. For examples that work with Finder items, see Folder Actions Reference.

You can also run an open handler by sending a script application the open command. For details, see Calling a Script Application From a Script.


#####保持打开状态的程序idle和quit处理程序
默认一个脚本程序接收run或者open命令然后退出。

* 一直打开的脚本可以接受`run`和`open`命令，这运输你把脚本程序作为一个脚本服务器。当它运行能提供一系列能够被别的脚本调用的处理程序。
* 一直打开的脚本程序可以周期性的运行任务，即使在后台

######idle处理程序
如果一直打开的脚本程序包含idle处理程序，AppleScript给脚本程序发送周期性的idle命令，默认每隔30秒让它执行后台任务。

如果一个idle处理程序返回一个正数，这个数代表了处理程序调用的频率（秒），如果返回的不是一个数字，那么调用频率不会改变。你可以返回0保持默认的30秒。

例如，没五秒调用一次：

```applescript
on idle
    beep
    return 5
end idle
```

以下处理程序每分钟执行一次：

```applescript
on idle
    set x to 10
    beep
    set x to x * 6  -- The handler returns the result (60).
end idle
```

######quit处理程序
AppleScript发送quit命令给一个一直打开的程序。如果脚本包含quit处理程序，这个处理程序会在程序退出之前调用。

quit处理程序可以赋值给脚本的属性，告诉其他程序去做什么，显示一个窗口或者其他任务。如果这个处理程序包含`continue quit`语句，脚本程序默认调用`quit`然后退出。如果`quit`处理程序在执行continue quit之前返回，那么程序不会退出。

```applescript
on quit
    display dialog "Really quit?" ¬
        buttons {"No", "Quit"} default button  "Quit"
    if the button returned of the result is "Quit" then
        continue quit
    end if
    -- Without the continue statement, the application doesn't quit.
end quit
```

#####在脚本中调用脚本程序
脚本可以发送命令到脚本程序。`launch`命名后面紧跟`run`命令可以启动一个非一直打开的程序。

```applescript
launch application "NonStayOpen"
run application "NonStayOpen"
```
`launch`命令启动脚本程序而不发送一个隐含的`run`命令.当`run`命令发送到脚本程序，它处理命令，如果有必要回馈一个回复，然后退出。

打开一个非一直打开的程序，在`launch`程序后面运行`stringTest`命令。

```applescript
tell application "NonStayOpen"
    launch
    stringTest("Some example text.")
end tell
```
