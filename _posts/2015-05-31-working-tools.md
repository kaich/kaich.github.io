layout: post
title: 写简化工作的工具
---

####为工作写工具
在工作中，我们在写程序的工程中，遇到代码重复性很高的时候，我们可以选择重构简化代码。在其他的方面，如果总要做一些固定流程的工作，而且经常要一步步的重复操作，作为优秀的程序员，绝对是相当受不了的（因为传说优秀的程序员都很懒）。这个时候我们就可以为简化自己的工作写一些实用的校工作连简化自己的工作，以后就既轻松而且happy了。下面来看看我自己写的一些小工具。

#####fetchapp ： 查询苹果软件相关信息
由于工作需要，我们要知道苹果手机和平板上有哪些app以及它的相关信息，比如：bundle id ,版本信息。其他同事想着一个程序一个程序的下载，软后看它的相关信息（软件包解压之后可以看到）。但是我又觉得它太慢反。想到为什么不写个程序来简化它呢（因为一个个装既费时也费力）。于是我网上查了一些。查询相关方法。于是就着手写了。只想写个小工具，于是就用ruby来写（脚本方便，包管理安装都很方便）。


	安装方式是  gem install :
	
	使用格式如下 fetchapp -t  [iphone ipad mac]  -a 
	
	没有写文档，因为没有写太多的功能，这里是一个例子
	
	fetchapp -t iphone<br>
	start request .....
	request OK !
	------iTunes U------com.apple.itunesu---------
	------查找我的 iPhone------com.apple.mobileme.fmip1---------
	------iBooks------com.apple.iBooks---------
	------Podcasts------com.apple.podcasts---------
	------查找我的朋友------com.apple.mobileme.fmf1---------
	------Pages------com.apple.Pages---------
	------iMovie------com.apple.iMovie---------
	------Numbers------com.apple.Numbers---------
	------Keynote------com.apple.Keynote---------
	------GarageBand------com.apple.mobilegarageband---------
	------Apple Store------com.apple.store.Jolly---------
	------Remote------com.apple.Remote---------
	------AirPort 实用工具------com.apple.airport.mobileairportutility---------
	------iTunes Connect------com.apple.itunesconnect.mobile---------
	------WWDC------developer.apple.wwdc-Release---------
	------TestFlight------com.apple.TestFlight---------
	这是打印比较简单的信息，加-a之后，会打印比较全面一些的信息。基本用法就是这样。如果有时间，以后会加入更多一些的功能。 

以上是第一版，功能只有以上简单的查询苹果app的方式，后来于是又加了一些功能。也写了相关的用法的简单的文档。修改版本如下,可以根据app的名称和id来查询相关信息，功法如下：

    fetchapp  -h
	fetchapp -[options] value
	find apple app info!
	options: 
	name   : app name 
	type   : app type [mac iphone ipad]
	id     : app item id 
	count  : show the count of items,default is 200
	apple  : show apple's app
	all    : show detail info
	h      : help
	
	note: if you can't use both of name and id at same time.
	
如果需要，安装方式可以直接在终端下输入：`sudo gem install fetchapp`.

#####certstepper ：简化苹果证书申请流程
比较有意思的是，最近写certstepper的过程。由于工作的特殊需要要经常申请苹果证书。久而久之，就是那么几部操作，无非就是创建文件夹，粘贴复制，点击网页下一步，然后把证书和配置文件命名成合适的名称，然后移动到创建的目录。这个能够简化的流程有限，于是我想简化复制粘贴和移动配置文件的工作，当然我是用ruby来写的。在导出证书方面可能就有些麻烦了。就没有简化证书从钥匙串导出的过程。这个有时间我可以研究一下。下面来看看申请证书的具体流程：    
申请的账号信息如下:

	12313214214@163.com  //账号
	123456789            //密码
	com.example.test     //bundle id

正常步骤如下：   

1. 复制密码账号，登录开发账号
2. 把申请证书的文件放到指定文件夹下
3. 利用申请的文件创建证书导出，并放到指定文件夹下
4. 新建相应的app id
5. 利用bundle id信息创建配置文件
6. 移动配置文件到指定目录下
7. 如果有多个信息，重复1操作

写了certstepper之后，把申请的账号信息经过简化之后如下：

1. 把账号信息写到文件下，例如~/Desktop/CertProduct/certs
2. 运行certstepper ~/Desktop/CertProduct/certs
3. 然后点击enter会把账号名复制到剪切板，再点击enter就会吧密码复制到剪切板。这样就可以登录了。不用每次都打开文件，选择复制粘贴到网页
4. 创建证书，这一步没有简化
5. 创建app id。终端点击enter，名字到剪切板 ， 再点击bundle id 到剪切板
6. 创建配置文件，同理，每次enter之后都可以直接在网页相应的位置粘贴了
7. 最后一步enter直接会将下载的配置文件移动到指定文件夹下

其实步骤并没有少，只是简化了文件移动和复制粘贴的过程。但是在这个过程中。我们可以看到，我们需要在终端和网页之间来回切换。这是很麻烦的事情了。于是我想，是不是有什么方法简化这样的一个过程呢。最初想到的是 `safari extension`。可是safari extension不能直接调用本地的脚本程序。于是放弃了（其实还是有可行的方案，只是没有尝试，这里卖卖关子，等花点时间尝试出来再说）。于是又想，其实qq那样的菜单栏上面的快捷方式挺好的，于是就写了一个类似的程序。这样就解决了程序之间来回切换的工程了。
总结一下：

程序直接来还切换的解决方式：
  
1. 利用safari extension ，但是它不能直接调用本地的脚本和程序
2. 利用qq那样状态栏上面的快捷方式，就像wifi，电池状态的那种，无时无刻都显示在那。

我完成了第二种方式。
这里是certstepper 的安装方式： `sudo gem install certstepper`,那个状态菜单的程序，还没有传到网上，过两天就附上。


#####总结
总之在工作中，我们可以想到用各种各样的方式去简化我们的工作，当然你不可能只会一样东西了。不能说你坐ios就只会object c。很多东邪我们都要用平常的业余时间去学习。例如我现在觉得我其他知识层面比较匮乏，于是我买了很多计算机系统、网络方面的书籍。总之，学无止境。用我们学到的知识运用到生活和工作中。这样不容易忘记而有所成。满足感促进我们再去学习。 




