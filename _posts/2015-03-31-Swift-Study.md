---
layout: post
title: 学习Swift
---

####新特性： 
#####基础类型：
* 常用基本类型 Int  Double  Float Turple 类型都是结构体了。它们可以和类一样拥有自己的方法，但是在进行表达式的类型转换的时候，swift中必须显示转换
* 对于option主要是用于状态的判断,它不能直接用于运算，如果没有初始值会自动为nil。表现方式如下：  

	“​var​ ​serverResponseCode​: ​Int​? = ​404”
		   
* 主要作用：nil 可以适用于任何option类型和类，我们可以在if或者其他的状态变化中用nil来做判断 ，如下：
* 
    ```swift
	​let​ ​convertedNumber​ = ​possibleNumber​.​toInt​()
	 if​ ​convertedNumber​ != ​nil​ {
	​			 ​println​(​"convertedNumber contains some integer value."​)
	​ }
	​		 // prints "convertedNumber contains some integer value.”
	可以通过optional变量后面加！来告诉编译器已经确认它必定不为nil，如果为nil编译器会报错
	```
	

* 基础操作：
  * %可以用于浮点型  “a = (b × some multiplier) + remainder”
  * a..<b  和  a…b 符号的加入 半开运算符比较实用于0 开始的数组，如下：

    ```swift
	“​let​ ​names​ = [​"Anna"​, ​"Alex"​, ​"Brian"​, ​"Jack"​]
	​let​ ​count​ = ​names​.​count
	​for​ ​i​ ​in​ ​0​..<​count​ {
	​	​println​(​"Person ​\(​i​ + ​1​)​ is called ​\(​names​[​i​])​"​)
	​}
	​// Person 1 is called Anna
	​// Person 2 is called Alex
	​// Person 3 is called Brian
	​// Person 4 is called Jack”
	```

*  === 和 !== 运算符来判断是否是同一个变量
   = 没有任何返回值
     
    ```swift
	​if​ ​x​ = ​y​ {
	​	​// this is not valid, because x = y does not return a value
	​}
	```

* ?? 空合并运算符， a ?? b 等价于 “a​ != ​nil​ ? ​a​! : ​b“ 。需要注意的是 b 必须和 a 中的值是一个类型的	
在逻辑运算符中可以 直接 if a && b || c || d 这样连续，但是在oc中是不可以的。


#####字符串：
* 字符串在swift中是值类型，而并非指针类型。它在传值的时候会被拷贝。(因为String 式结构体，结构体和类的区别就是传值的时候结构体是拷贝的	
* \+ 运算符加入
* \\() 在字符串中填写表达式进行字符串的插入
* countElements可以计算出字符串的个数，能够正确兼容Unicode，在object-C length 方法在计算中文的时候一个字length是2


#####集合：
######Array:
* 在swift中集合是又类型的。不论是显示(Array<SomeType>)还是隐式生成。例如一定你第一个插入了Int 你不能再插入非Int的类型了。而它也带了一个好处：不是只能插入类。他们都是结构体
       
    ```swift
	Array :
	Array显示定义以及初始化：
	 “var​ ​shoppingList​: [​String​] = [​"Eggs"​, ​"Milk"​]”
	 “​var​ ​threeDoubles​ = [​Double​](​count​: ​3​, ​repeatedValue​: ​0.0​)”
	 var​ ​someInts​ = [​Int​]()
	 ```

* 用+=向Array中添加元素
* Array之间可以 + 相加
* 用a[1] = b 改变Array元素的值 或者用 a[1…3] = [b,c,d] 形式来进行连续赋值
* Array遍历：

    ```swift
	for​ (​index​, ​value​) ​in​ ​enumerate​(​shoppingList​) {
	​	​println​(​"Item ​\(​index​ + ​1​)​: ​\(​value​)​"​)
	​}
	```

######Dictinary:
* hash 值是由 == 来决定的
* swift中使用“[key 1: value 1, key 2: value 2, key 3: value 3]”来定义dictionary。 而不是Object-c 中的{key1 : value1 , key2 : value2 } ，
* a[b] = c形式进行赋值
* 遍历：

    ```swift
	for​ (​airportCode​, ​airportName​) ​in​ ​airports​ {
	​	​println​(​"​\(​airportCode​)​: ​\(​airportName​)​"​)
	​}
	```


#####控制：
* Turple作为判断条件的时候比较特殊，具体看教程
* switch 中 可以默认不用写break，他会自己break。 break的作用作于你想提前break。 fallthrough关键字能够让switch判断继续向下走而不是break了,他不会判断下个case的条件，而是直接执行下个case里面的代码
* 可以用如下进行复杂的流程控制，循环和switch嵌套的话，continue、  ， 用来控制作用于那个（这个功能非常有用)

		“label name: while condition {
			statements
		}”

#####函数：
* func 可以返回Turple 类型，也就是返回多值
* func 可以带外部名称 ，函数内部必须用 localParameterName  调用函数的时候用 externalParameterName 。 当两者名字一样的时候直接在名字前面加#

    ```swift
	func​ ​someFunction​(​externalParameterName​ ​localParameterName​: ​Int​) {
	​	​// function body goes here, and can use localParameterName
	​	​// to refer to the argument value for that parameter
	​}
	```

* func 可以带可变参数 … ,可变参数放到最后一个
* 参数默认是常量，你不可以改变它。 你也可以设置变量，但是他是本身copy的一个新变量。 如果你想在函数里面更改传入参数的值，那么在定义参数的时候加关键字inout . 注意：在swift中参数的传递已经不区分深复制和浅复制了，只有没有加inout，都不会修改原来传递的值。而object-c中如果传入的是对象或者集合都是可以改变原值的。swift让用户显示调用，更加安全。
* 函数可以作为变量使用，可以作为参数传入函数，可以作为返回值

		“var​ ​mathFunction​: (​Int​, ​Int​) -> ​Int​ = ​addTwoInts”
		
* 函数可以嵌套定义。就是函数里面定义函数，这是一种特殊形式的闭包

#####闭包：
* 闭包无名称函数

    ```swift
	{ (parameters) -> return type in
		statements
	}
	```
	
* 它可以根据上下文来判断参数和返回值的类型，可以简化

```swift
​reversed​ = ​sorted​(​names​, { (​s1​: ​String​, ​s2​: ​String​) -> ​Bool​ ​in​ ​return​ ​s1​ > ​s2​ } )
```
		
简化成
```swift
	​reversed​ = ​sorted​(​names​, { ​s1​, ​s2​ ​in​ ​return​ ​s1​ > ​s2​ } )
```
单个表达式的闭包可以自动return，我们可以省略return 
```swift
​reversed​ = ​sorted​(​names​, { ​s1​, ​s2​ ​in​ ​s1​ > ​s2​ } )
```
闭包提供了一个速记的参数形式$1 $2 $3 ,分别代表第一个，第二个，第三个参数
```swift
reversed​ = ​sorted​(​names​, { ​$0​ > ​$1​ } )
```
由于String 实现了 > 方法，所以简化为
```swift
​reversed​ = ​sorted​(​names​, >)
```
尾部闭包，主要作用于让函数体比较大的闭包方便显示
```swift
reversed​ = ​sorted​(​names​) { ​$0​ > ​$1​ }
```
* 闭包捕获上下午的值是引用，而不是copy

#####枚举：
swift中枚举不会自定赋0、1、2 ...的默认值，首字母必须大写

```swift
​enum​ ​CompassPoint​ {
​	​case​ ​North
​	​case​ ​South
​	​case​ ​East
​	​case​ ​West
​}
```swift
		
或者

```swift
enum​ ​Planet​ {
​	​case​ ​Mercury​, ​Venus​, ​Earth​, ​Mars​, ​Jupiter​, ​Saturn​, ​Uranus​, ​Neptune
​}
```

* 每一个enum的成员都可以是其他的任何类型，它可以让使用者存储附加信息。例如：

```swift
enum​ ​Barcode​ {
​	​case​ ​UPCA​(​Int​, ​Int​, ​Int​, ​Int​)
​	​case​ ​QRCode​(​String​)
​}
```
	
* 可以通过raw value 来获取enum的值，它返回的是optional值，raw value 为了让使用者和c 模式一样，直接通过 1，2，3，4...来比较. 例如

		“​let​ ​possiblePlanet​ = ​Planet​(​rawValue​: ​7​)”


#####类和结构体：
* 类比结构体多的功能：
  * 继承
  * 类型转换 列入  a = (String *) 
  * Deinitializers释放实例占有的资源
  * 引用数可以大于1 。 （结构体是copy，所以只有1）
  * 对于Stuct 可以，直接用成员初始化，而class不行
  
```swift
“struct​ ​Resolution​ {
​	​var​ ​width​ = ​0
​	​var​ ​height​ = ​0
​}
let​ ​vga​ = ​Resolution​(​width​: ​640​, ​height​: ​480​)
```

* 特性：
成员变量中可以用 lazy 关键字进行修饰，它表示延迟加载，只有当第一次调用该成员变量的时候，该成员变量才会被创建。

```swift
lazy​ ​var​ ​importer​ = ​DataImporter​()
```

计算属性格式 ，set函数没有参数，默认有个值 newValue可以提供使用：

```swift
struct​ ​AlternativeRect​ {
​	​var​ ​origin​ = ​Point​()
​	​var​ ​size​ = ​Size​()
​	​var​ ​center​: ​Point​ {
​		​get​ {
​			​let​ ​centerX​ = ​origin​.​x​ + (​size​.​width​ / ​2​)
​			​let​ ​centerY​ = ​origin​.​y​ + (​size​.​height​ / ​2​)
​			​return​ ​Point​(​x​: ​centerX​, ​y​: ​centerY​)
​		}
​		​set​ {
​			​origin​.​x​ = ​newValue​.​x​ - (​size​.​width​ / ​2​)
​			​origin​.​y​ = ​newValue​.​y​ - (​size​.​height​ / ​2​)
​		}
​	}
​}
```

* 只读属性直接在计算属性的基础上，写函数体然后return就可以了
* 属性监听，willSet , didSet 。 在willSet 中可以用newValue来获取新值，在didSet中可以用oldValue获取旧值，如果在didSet中给该属性赋值，他会替换刚刚付的值

```swift
class​ ​StepCounter​ {
​	​var​ ​totalSteps​: ​Int​ = ​0​ {
​		​willSet​(​newTotalSteps​) {
​			​println​(​"About to set totalSteps to ​\(​newTotalSteps​)​"​)
​		}
​		​didSet​ {
​			​if​ ​totalSteps​ > ​oldValue​  {
​				​println​(​"Added ​\(​totalSteps​ - ​oldValue​)​ steps"​)
​			}
​		}
​	}
​}
```

* 类型成员变量，也就是object-c中的类静态变量。用static 关键字修饰，如果在类里面用class 修饰
    
    ```swift
	static​ ​var​ ​storedTypeProperty​ = ​"Some value.
	```
		
* 类，结构体和枚举类型都可以定义方法

