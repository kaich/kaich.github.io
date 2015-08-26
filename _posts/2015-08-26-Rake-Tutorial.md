---
layout: post
title: Rake 教程
---

英文原文：[Rake Tutorial](http://jasonseifer.com/2010/04/06/rake-tutorial)

在学习rails过程中遇到了rake，查看了文档和很多网站也不太清楚rake到底是什么。直到阅读了以上文章才入门rake。于是决定翻译过来，供大家参考。

如果你用Rails开发，你一定或多或少的遇到过rake。这篇博客主要目的是让你知道rake是从何而来和如何在Rails项目中使用它。

###简史
Rake是[Jim Weirich]()的项目。它是一个构建工具。如果你对它的历史有浓厚的兴趣你可以通过["rational.rdoc"]()进行更进一步的了解。Rake本来是想让Makefile里面使用ruby语言，尽管Jim并不深信这个想法，但是它是一个很好的想法。自动构建系统需要什么？通常[Wikipedia](https://zh.wikipedia.org/zh-cn/組建自動化)会这样回答：

>历来开发者们利用构建自动化通过命令行从构建脚本里面去调用编译和链接过程。它是很容易通过命令行传入源文件从而生成最终的产物。但是当尝试按照特定的顺序去编译链接很多源文件的时候，使用命令行就不是那么好的方案了。当一个构建过程变得复杂的时候，开发者开始将围绕呼叫前置和后置动作的编译器，如检查出的版本控制，部署对象复制到测试位置。术语“构建自动化”现在包括管理前置和后置的编译和链接活动，以及编译和链接活动.

###依赖
构建和依赖相关。一个文件或者一系列的文件依赖另外的以一系列文件编译和链接或者在处理下一批之前必须的一些东西。Rake里面同样存在任务之间的依赖。把下面的代码保存到任意目录名为'Rakefile'的文件中

	directory "tmp"
	
	file "hello.tmp" => "tmp" do
	  sh "echo 'Hello' >> 'tmp/hello.tmp'"
	end
	
一个名叫“hello.tmp”的文件依赖于"tmp"文件夹。当我们运行它，首先会产生一个“tmp“文件夹，然后才会执行"hello.tmp"任务。当你运行的时候会有类似如下的输出：

	[jason@brick:~/src]$ rake hello.tmp
	(in /Users/jason/src)
	echo 'Hello' > 'tmp/hello.tmp'
	
如果你查看"hello.tmp"里面的内容，你会发现"Hello"。如果你再次运行，会发生什么呢？Rake会再次生成这个文件。因为它不会查找mp/hello.tmp，而是按照定义中的区查找"hello.tmp"。让我们重新定义下：

	 directory "tmp"
	
	 file "tmp/hello.tmp" => "tmp" do
	   sh "echo 'Hello' > 'tmp/hello.tmp'"
	 end
	 
现在如果你运行它两次会看到如下的输出：

	[jason@brick:~/src]$ rake "tmp/hello.tmp"
	 (in /Users/jason/src)
	 mkdir -p tmp
	 echo 'Hello' > 'tmp/hello.tmp'
	 [jason@brick:~/src]$ rake "tmp/hello.tmp"
	 (in /Users/jason/src)
	 
Rake现在就会知道这个文件任务已经执行过了，不会重复执行任务了。


###运行其他的任务

Rake任务可以采取的形式有先决条件和可以依赖于另一个任务。下面是早晨要做的事情：

1. 关闹钟(Turn off alarm clock)
2. 整理自己(Groom myself)
3. 煮咖啡(Make coffee)
4. 遛狗(Walk dog)

我们按照顺序做以上的几件事情，用Rake来描述如下：

	task :turn_off_alarm do
	   puts "Turned off alarm. Would have liked 5 more minutes, though."
	 end
	
	 task :groom_myself do
	   puts "Brushed teeth."
	   puts "Showered."
	   puts "Shaved."
	 end
	
	 task :make_coffee do
	   cups = ENV["COFFEE_CUPS"] || 2
	   puts "Made #{cups} cups of coffee. Shakes are gone."
	 end
	
	 task :walk_dog do
	   puts "Dog walked."
	 end
	
	 task :ready_for_the_day => [:turn_off_alarm, :groom_myself, :make_coffee, :walk_dog] do
	   puts "Ready for the day!"
	 end
	 
如果我输入`rake ready_for_the_day`，会看到如下输出:

	[jason@brick:~/src]$ rake ready_for_the_day
	 (in /Users/jason/src)
	 Turned off alarm. Would have liked 5 more minutes, though.
	 Brushed teeth.
	 Showered.
	 Shaved.
	 Made 5 cups of coffee. Shakes are gone.
	 Dog walked.
	 Ready for the day!
	 
通过运行`ready_for_the_day`任务会发现`turn_off_alarm`, `groom_myself`, `make_coffee`, `walk_dog tasks`是`ready_for_the_day`任务的先决条件，而且它完全是按照我们写的顺序执行的。你会发现我们可以传值给`make_coffee`任务。我们可以通过如下形式传值给`COFFEE_CUPS`环境变量：

	 [jason@brick:~/src]$ rake COFFEE_CUPS=5 make_coffee
	 (in /Users/jason/src)
	 Made 5 cups of coffee. Shakes are gone.

###命名空间
