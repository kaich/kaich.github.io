---
layout: post
title: react native 开发IOS
---

####接触 [react native](https://facebook.github.io/react-native/)
对于技术，我比较喜欢追新。看到报道大名鼎鼎的facebook(开源界的模范,发布了很多高质量的开源框架)开源了移动端跨平台的新框架react native，于是就迫不及待的开始接触它了。react native的目的是为了让前端开发任务能开发移动端（ios程序员相对于web端来说要少得多），于是就有了这样的框架。对于我们IOS程序员有没有必要去学它，看到它另外一个特性：`跨平台`,而且又一定程度的了解web端开发，出于各种各样的目的出发，学习react native何乐而不为呢！  

####我学react natvie经历
我从看到报道，从而接触react native，到写一个小的IOS应用，其中历时仅仅两周的时间，基本可以做到日常app中的基本UI布局，请求（json 和 xml，官方只介绍了json请求怎么请求），第三方包安装，使用和修改。无论是ios native开发或者使用react native开发，能达到这样的程度就基本满足日常所需了。下面是关于react native 的一段实例代码：

```js
var React = require('react-native');
var { TabBarIOS, NavigatorIOS } = React;

var App = React.createClass({
  render: function() {
    return (
      <TabBarIOS>
        <TabBarIOS.Item title="React Native" selected={true}>
          <NavigatorIOS initialRoute={{ title: 'React Native' }} />
        </TabBarIOS.Item>
      </TabBarIOS>
    );
  },
});
```
从上面一段代码可以知道大概需要学习的知识：

* 基础CSS知识（盒子模型），可以参照[w3c css](http://www.w3school.com.cn/css/)
* CSS flex box 的相关知识，可以参照[react-native 之布局篇](http://segmentfault.com/a/1190000002658374)
* javascrip 基础 ，参考基础知识[W3C Javascript](http://www.w3school.com.cn/js/)，更深入一点[悟透javascript](http://www.cnblogs.com/leadzen/archive/2008/02/25/1073404.html)、
* npm包管理器
* [react js](http://facebook.github.io/react/) ，因为react native 是基于react js的基础上来的，因此我们必须要先了解react js，也就是它的具体原理，例如

    * react生命周期，各个周期调用的函数，方法
    * react状态机，这是react的灵魂
    
* react native 各个组件的使用和属性，调试，发布，这个直接参考文档就够了。
* github和其他博客进一步提升自己能力

####写react native程序
工欲善其行必先利其器，react native官方推荐用的是[atom](https://atom.io),期初我想用vim打造react native的编程环境，但是太多函数不提示。对于新手来说还是atom这种带有提示的好，可以增加我们的自信心。    

下面我先分享一个小的demo程序:[react-native-announce](https://github.com/kaich/react-native-announce),可以用<pre>git clone https://github.com/kaich/react-native-announce.git</pre>
来下载下来。编译运行到你的模拟器或者设备上面。你会发现它是一个界面丑陋的列表。点击之后可以根据文字的内容动态的改变cell的高度。看到了效果，现在用atom打开index.ios.js文件，开始最关键的内容了，慢慢的解析里面为数不多的100行代码。

   ```js
   "use strict";
   ```
   
这第一行代码的意思是使用的是javascript的严格模式，也就是语法比较合乎规范的模式。这里不用深究，你也可以了解：[javascript严格模式](http://www.ruanyifeng.com/blog/2013/01/javascript_strict_mode.html)

	```js
	var React = require('react-native');
	var {
	  AppRegistry,
	  StyleSheet,
	  Text,
	  View,
	  ListView,
	  TouchableHighlight,
	} = React;
	```
	
这两行是导入需要用的组件，例如这里我们导入了：AppRegistry，StyleSheet，Text，View，ListView，TouchableHighlight这些组件。

	```js
	var url = "http://ios3.app.i4.cn/getNoticeList.xhtml?toolversion=520&model=iPhone7,1&pageno=1&idfa=149DD67B-A5BB-4EE8-A83E-BE91EBE7ECE4&idfv=1C1D819E-5D0D-4059-83A5-AB1E6F9B7C2D&openudid=667ee68f513ddd228edfc1c0eeab5f008dbb4733&osversion=8.1.3&udid=(null)&macaddress=020000000000&model=iPhone7,1&certificateid=0&bundleid=0&isAuth=1&isjail=0&authtime=1431077211&serialnumber=C39N9422G5QR&cid=200025&toolversion=520"
	```

这一行是声明了url变量，是请求的接口地址。接下来会用到，由于该请求返回的数据是xml的结构形式，但是在react native中并没有提供xml的解析库(可能是xml形式过时了)，这里我们就需要导入第三方的xml解析库了，在这个项目中我用到了xmldom库。那么怎么导入呢？这里就要用到上面说的npm包管理了。进入项目根目录，运行命令
<pre>npm install xmldom</pre>
运行成功后就会看到node_modules文件夹下出现了xmldom文件夹。下面就是导入这个库并使用了

	var DOMParser = require('xmldom').DOMParser
	
导入后就可以使用DOMParser来进行解析xml了。

接下来就到了程序的主体部分了，下面一个函数一个函数的看：

	```js
	  getInitialState: function() {
	    return {
	      datasource: new ListView.DataSource({
	        rowHasChanged: (row1, row2) => {console.log("row1 :" + row1.height + "row 2:" + row2.height);  return true},
	      }),
	      expanedCell:[],
	      data:[],
	    };
	  },
	 ```

getInitialState在组件被加载之前调用，用来初始化this.state，而整个程序的关键就是this.state这个状态机，一旦state相应值改变，可以触发你界面更新的相应逻辑。这里有如下几个值：

* datasource： ListView的数据源
* expanedCell：展开的cell数组
* data：储存请求数据


	```js
	 componentDidMount: function(){
	   this.getAnnounces();
	 }
	```

componentDidMount UI组件被加载之后调用，this.getAnnounces()自定义的方法去发送HTTP请求请求数据

	```js
	getAnnounces: function() {
	      console.log("start request") 
	      fetch(url).then(response => response.text()).then(responseText => {
		var results = new Array()
		var parser = new DOMParser()
		var doc = parser.parseFromString(responseText,"text/xml").documentElement
		var listNode = doc.getElementsByTagName("noticelist")[0]
		var list = listNode.getElementsByTagName("notice")
		for(var i=0;i<list.length;i++)
		{
		   var announce = new Object()
		   var emElement = list.item(i)
		   announce.title = emElement.getElementsByTagName("title")[0].firstChild.data
		   announce.time = emElement.getElementsByTagName("time")[0].firstChild.data
		   announce.detail = emElement.getElementsByTagName("detail")[0].firstChild.data
		   announce.height=0
	           
		   results[i]=announce
		}
	        this.state.data= results;
		this.setState({
		  datasource: this.state.datasource.cloneWithRows(results),
		})
	      });     
	  }
	```
	
fetch方法请求数据，请求到数据之后用xmldom进行解析，最后调用this.setState来触发列表的更新，显示我们请求到的内容。接下来来看主体的方法render

	```js
	  render: function() {
	    return (
	     <ListView style={styles.container}
	        dataSource={this.state.datasource}
	        renderRow={this.renderListRow}
	      />
	    );
	  }
	```

定义了一个列表，它的数据源是this.state.datasource，cell的渲染会调用this.renderListRow。数据源请求方法都已经搞定了，接下来就是cell渲染的问题了

	```js
	  renderListRow: function(announce){
		return (
	      <TouchableHighlight onPress={()=>{this.rowClick(announce)}}>
		      <View>
		        <View  style={this.rowStyle(announce)} ref='Name'>
	            <Text style={styles.title}>{announce.title}</Text>
	            <Text style={styles.time}>{announce.time}</Text>
	            <Text style={styles.detail}>{announce.detail}</Text>
	          </View>
	          <View style={styles.seprator}/>
	       </View>
		    </TouchableHighlight>
		);
	  },
	```
很简单的结构，因为需要可以点击的cell，所以cell需要一个可以点击的view最为所有子视图的父视图，这个来就是三个Text的子视图，也就是IOS中的三个UILabel，分别是title(名字)，time（时间），detail（详情），然后就是一个分割线seprator，里面style是控制里面的排版样式的。样式都有了接下来就是点击之后控制它的高度变化了

	```js
	rowClick: function(announce){
    var selectedCells = this.state.expanedCell
    if(announce.height==1) 
    {
       selectedCells.pop(announce) 
       announce.height =0;
       this.setState({
          datasource: this.state.datasource.cloneWithRows(this.state.data),
	  rowViewExpaned:selectedCells,
       });
    }
    else
    {
      selectedCells.push(announce)
       announce.height=1
       this.setState({
           datasource: this.state.datasource.cloneWithRows(this.state.data),
	  rowViewExpaned:selectedCells,
       });
    }
    }
    ```
 
 点击函数如果点击height是0就改变值为1，反之亦然。然后就是调用this.setState触发cell刷新了，高度呢，为什么没看到高度的相关内容，就在下面了。


	```js		
	  rowStyle: function(announce){
	    if(announce.height==1) 
	      return styles.rowViewExpaned
	    else 
	      return styles.rowView
	  },
	```
	
如果height是1，返回扩展cell的样式，如果height是0返回正常cell的样式。

由于整个StyleSheet的样式是非常简单的，这里就不一一说了。看代码结合文档就很容易看明白了，如果不明白可以留言,我也会把我对css的理解加进来，以便新手能更好的理解，也是记录下我的历程。这篇这是简单的留给新手阅读每个部分的功能，最基础的第三方使用方式。在接下来我会提供一整个项目，而不是一个简简单单的页面，来展示react native的强大之处。
