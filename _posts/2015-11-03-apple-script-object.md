layout: post
title: AppleScript（二）脚本对象
---

脚本对象是用户结合方法和数据自定义的对象。脚本对象支持继承。

####定义脚本对象
定义脚本对象以`script`开始，关键字`end`结尾，两者之前可以定义属性，方法和内嵌的脚本对象和其他的语句

```applescript
script variableName

    [ ( property | prop ) parent : parentSpecifier ]

    [ ( property | prop ) propertyLabel : initialValue ]...

    [ handlerDefinition ]...

    [ statement ]...

end [ script ]
```

* variableName 变量名
* parentSpecifier 父对象,通常是另一个脚本对象。
* propertyLabel 属性
* initialValue  属性的初始值
* handlerDefinition 定义方法
* statement 任意 AppleScript语句

实例：

```applescript
script John
    property HowManyTimes : 0
 
    to sayHello to someone
        set HowManyTimes to HowManyTimes + 1
        return "Hello " & someone
    end sayHello
 
end script
```

以上定义了一个名为John的脚本对象，它包含HowManyTimes属性和sayHello方法。你可以用`tell`语句发送sayHello命令给John这个脚本对象。

```applescript
tell John to sayHello to "Herb" --result: "Hello Herb"
```

####初始化脚本对象
初始化脚本对象，创建实例。

```applescript
on makePoint(x, y)
    script thePoint
        property xCoordinate:x
        property yCoordinate:y
    end script
    return thePoint
end makePoint
 
set myPoint to makePoint(10,20)
get xCoordinate of myPoint  --result: 10
get yCoordinate of myPoint  --result: 20
```

当执行makePoint命令的时候AppleScript实例化thePoint脚本对象。当调用makePoint后，myPoint指向了这个脚本对象。makePoint方法的参数x ,y成为了脚本对象的本地变量。x初始化为10,y为20.

####发送命令到脚本对象
可以用`tell`语句去给脚本对象发送命令。例如，发送两个sayHello给John

```applescript
tell John
    sayHello to "Herb"
    sayHello to "Grace"
end tell
```
一个脚本对象定义可以包括隐式运行的处理程序，由那些任何处理或嵌套脚本对象之外的所有的可执行语句，或它可包括显式运行的处理程序开头上运行，但它可能不包含两个 - 例如一个脚本不能编译。如果脚本没有运行的处理程序（例如，作为处理程序库，在保存和处理程序的加载库中描述的脚本），执行脚本什么都不做。然而，在发送一个明确的运行命令将导致错误.

```applescript
script John
    property HowManyTimes : 0
    to sayHello to someone
        set HowManyTimes to HowManyTimes + 1
        return "Hello " & someone
    end sayHello
    display dialog "John received the run command"
end script
 
tell John to run
```

可以用下面的任何一个给John发送命令

```applescript
John's sayHello to "Jake" --result: "Hello Jake"
sayHello of John to "Jake" --result: "Hello Jake"
```

####脚本库
保存在Script Libraries文件夹中脚本对象可以被其他脚本使用。Libraries可以让你分享和重用方法。脚本库定义的脚本对象，一个客户端脚本可以再引用，然后发送命令给它。

```applescript
script "My Library"
```
AppleScript会在Script Library中搜索这个变量。并且不像`load script`，以上做法可以在客户端生命周期中一直存在并且共享(单例),所以你不需要把它保存到变量当中。

```applescript
property name : "Counter"
property nextNumberProperty : 0
on nextNumber()
    set my nextNumberProperty to my nextNumberProperty + 1
    return my nextNumberProperty
end nextNumber
```

运行：

```applescript
tell script "Counter" to log its nextNumber() -- logs "1"
tell script "Counter" to log its nextNumber() -- logs "2"
```

####继承
继承特征和其他语言一样，基本上都符合object-c的继承规则！
实例：

```applescript
script Alex
    on sayHello()
        return "Hello, " & getName()
    end sayHello
    on getName()
        return "Alex"
    end getName
end script
 
script AlexJunior
    property parent : Alex
    on getName()
        return "Alex Jr"
    end getName
end script
 
-- Sample calls to handlers in the script objects:
tell Alex to sayHello() --result: "Hello, Alex"
tell AlexJunior to sayHello() --result: "Hello, Alex Jr."
 
tell Alex to getName() --result: "Alex"
tell AlexJunior to getName() --result: "Alex Jr"
```

调用`continue`来调用父类的方法,和object-c的super类似。例如：

```applescript
script Hugh
    on identify()
        me
    end identify
end script
script Andrea
    property parent : Hugh
    on identify()
        continue identify()
    end identify
end script
 
tell Hugh to identify()
--result: «script Hugh»
 
tell Andrea to identify()
--result: «script Andrea»
```
