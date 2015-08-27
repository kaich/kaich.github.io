---
layout: post
title: Rake 教程
---

英文原文：[Rake Tutorial](http://jasonseifer.com/2010/04/06/rake-tutorial)

在学习rails过程中遇到了rake，查看了文档和很多网站也不太清楚rake到底是什么。直到阅读了以上文章才入门rake。于是决定翻译过来，供大家参考。

如果你用Rails开发，你一定或多或少的遇到过rake。这篇博客主要目的是让你知道rake是从何而来和如何在Rails项目中使用它。

###简史
Rake是[Jim Weirich](http://onestepback.org/)的项目。它是一个构建工具。如果你对它的历史有浓厚的兴趣你可以通过["rational.rdoc"](http://rake.rubyforge.org/files/doc/rational_rdoc.html)进行更进一步的了解。Rake本来是想让Makefile里面使用ruby语言，尽管Jim并不深信这个想法，但是它是一个很好的想法。自动构建系统需要什么？通常[Wikipedia](https://zh.wikipedia.org/zh-cn/組建自動化)会这样回答：

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
Rake支持命名空间，能够让你把类似的任务都放到同一个命名空间里面，然后你通过命名空间去调用里面的任务。在Rails中，你会注意到 `db:migrate` 任务，在这个例子中`db`就是命名空间，`migrate`就是任务。用我们之间的例子，把任务都放到命名空间就是如下：

	 namespace :morning do
	   task :turn_of_alarm
	   ....
	 end

现在如果你运行`rake COFFEE_CUPS=3 morning:ready_for_the_day`，你将得到和之前相同的输出，只不过现在是3杯咖啡了（3 cups of coffee ）。

###默认任务
Rake有默认任务这一概念。它意味着如果你输入rake命令时候没有待任何参数，那么就会运行默认任务。如果我们设置默认任务是：关掉闹钟(urning off the alarm).

	 task :default => 'morning:turn_off_alarm'
	 
在命令行中输入`rake`,会得到如下输出：

	[jason@brick:~/src]$ rake
	(in /Users/jason/src)
	Turned off alarm. Would have liked 5 more minutes, though.
	
###任务描述
你可以使用`desc`关键字来描述你的任务，就和其他语言里面的文档注释效果一样。这些描述会在你运行`rake -T`获得任务列表的时候显示出来。我们来在上面的例子中加入任务描述：

	 ...
	  desc "Make coffee"
	  task :make_coffee do
	    cups = ENV["COFFEE_CUPS"] || 2
	    puts "Made #{cups} cups of coffee. Shakes are gone."
	  end
	  ...

现在在命令行中输入`rake -T`，你回获得如下输出：

	  [jason@brick:~/src]$ rake -T
	  (in /Users/jason/src)
	  rake afternoon:make_coffee      # Make afternoon coffee
	  rake morning:groom_myself       # Take care of normal hygeine tasks.
	  rake morning:make_coffee        # Make coffee
	  rake morning:ready_for_the_day  # Get ready for the day
	  rake morning:turn_off_alarm     # Turn off alarm.
	  rake morning:walk_dog           # Walk the dog
	  
在显示任务列表的时候我们也可以在后面加上额外的参数去匹配任务。例如`rake -T af`将会仅仅显示afternoon task。

###任务重定义
如果你想加点什么到已经存在的任务中该怎么办？例如要在打扮自己的时候打扮一下你的头发(tyling your hair)。你可以定义另外的一个groom_myself任务，加入这一行为:

	  namespace :morning do
		....
		task :groom_myself do
	      puts "Brushed teeth."
	      puts "Showered."
	      puts "Shaved."
	    end
	    ....
	  end
	  ...
	  namespace :morning do
	    task :groom_myself do
	      puts "Styled hair."
	    end
	  end
	  
运行任务，你将会看到：

	(in /Users/jason/src)
	  Brushed teeth.
	  Showered.
	  Shaved.
	  Styled hair.
	 
###调用任务
有的时候在一个任务里面你想调用另外的一个任务。还是用上面的例子。例如你想下午做咖啡。你只需要在早上做咖啡的基础上输出一行额外的信息：

	  namespace :afternoon do
	    task :make_coffee do
	      Rake::Task['morning:make_coffee'].invoke
	      puts "Ready for the rest of the day!"
	    end
	  end

它的输出结果如下：

	 [jason@brick:~/src]$ rake afternoon:make_coffee COFFEE_CUPS=1
	 (in /Users/jason/src)
	 Made 1 cups of coffee. Shakes are gone.
	 Ready for the rest of the day!
	 
###重构
就如上面的例子有很多代码是在别处定义好了，我们要让方法中直接调用别处定义好的代码，而不是每次都重新定义类似的代码。例如给每个快到期的用户发邮件

	  namespace :accounts do
	    desc "Email expiring accounts to let them know"
	    task :email_expiring => :environment do
	      date = ENV['from'] ? Date.parse(ENV['from']) : Date.today
	      accounts = Account.find(:all, :conditions => ["expiration_date = ?", date]
	      accounts.each do |account|
	        Notifier.deliver_account_expiration(account)
	      end
	    end
	  end

以上方法，给用户发邮件算是通用方法，别处可能会用到，于是我们可以把通用的写到一个方法里面：

	  namespace :accounts do
	    desc "Email expiring accounts to let them know"
	    task :email_expiring => :environment do
	      date = ENV['from'] ? Date.parse(ENV['from']) : Date.today
	      Account.notify_expiring(date)
	    end
	  end

这样写也更容易让你的代码更具测试性。例如我们可以很方便的测试notify_expiring方法了。

###Rails
你可以访问你的模型和依赖环境的任务构成的整个环境。例如你可以运行`run rake RAILS_ENV=staging db:migrate`.Rails自动从lib/tasks查找任务。当执行`rake -T`的时候，所有.rake扩展名里面的任务都会列出来。

###调度Rake任务
可以用[cron](https://en.wikipedia.org/wiki/Cron)调度rake任务。如果想每天晚上12:15给快过期的用户发邮件，可以这样做：

	  15 * * * * cd /data/my_app/current && /usr/bin/rake RAILS_ENV=production accounts:email_expiring
	  
这个我也没有研究过。

###派生
这里就不翻译了

* [Thor](http://github.com/wycats/thor/) is a more class based approach to solving some of the things rake does as far as actual tasks go.
* [Capistrano](http://capify.org/) is the de facto standard for deploying rails apps. Its syntax is inspired pretty heavily by Rake, but it is definitely not rake.

###Further Reading

* ["Testing Rake Tasks"](http://blog.jayfields.com/2006/11/ruby-testing-rake-tasks.html) by Jay Fields
* ["On Rake"](http://www.jbarnette.com/2009/08/27/on-rake.html) by John Barnette
* [How To: Setup RSpec, Cucumber, Webrat, RCov and Autotest on Leopard | Clayton Lengel-Zigich](http://www.claytonlz.com/index.php/2009/04/how-to-setup-rspec-cucumber-webrat-rcov-and-autotest-on-leopard/)
* [Custom Rake Tasks](http://railscasts.com/episodes/66-custom-rake-tasks) Railscast

###Useful Rake Examples
* [Sample Rakefile](http://gist.github.com/359401) from this article.
* [redis.rake](http://gist.github.com/359401) from Resque.
If you have any more useful rake examples, please let me know and I’ll add them here.

