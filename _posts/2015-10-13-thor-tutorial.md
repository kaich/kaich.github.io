---
layout: post
title: thor 教程
---

之前一直写ruby的脚本程序，对于参数的解析都是利用程序自己进行一步步的判断。后来想着这么写不是办法，绝对有通用的库来解析这个。然后在写`模板工程`的时候就正式的用到了thor来解析参数。感觉thor是非常简单易用的。在学习thor的过程中阅读了逛网的教程，感觉非常易懂，于是打算翻译过来，以供日后参阅。

> 参考： [thor官方网站](http://whatisthor.com)

#####Thor是一个强大的构建命令行的工具，他被用在Bundler，Vagrant，Rails和其他的里面。

#####开始
一个简单的thor类要暴漏一系列的字命令出来，就像git或者bundler一样。在一个Thor类里面，公有方法（public methods）就是命令。

```ruby 
class MyCLI < Thor
  desc "hello NAME", "say hello to NAME"
  def hello(name)
    puts "Hello #{name}"
  end
end
```

你可以以`MyCLI.start(ARGV)`来启动CLI。通常你在gem包的bin文件夹下可执行文件中可以这样做。   
如果传递给`start`的参数为空，Thor将会打印出关于这个类的帮助。    
通过余下的教程，我假定当前文件夹下有cli文件内容如下：

```ruby 
require "thor"
 
class MyCLI < Thor
  # contents of the Thor class
end
 
MyCLI.start(ARGV)

```

Thor会自动利用可执行文件的名称在生成的帮助之中。

```ruby 
$ ruby ./cli
 
Tasks:
  cli hello NAME   # say hello to NAME
  cli help [TASK]  # Describe available tasks or one specific task
```

如果带参数执行`hello`任务,他会调用你的方法：

```ruby 
$ ruby ./cli hello Yehuda
Hello Yehuda
```

如果你不带参数去执行他，Thor将自动打印出有用的错误信息：

```ruby 
$ ruby ./cli hello
"hello" was called incorrectly. Call as "test.rb hello NAME".
```

你也可以用Ruby的可选参数（optional arguments ）去让CLI也参数可选：

```ruby 
class MyCLI < Thor
  desc "hello NAME", "say hello to NAME"
  def hello(name, from=nil)
    puts "from: #{from}" if from
    puts "Hello #{name}"
  end
end
```

当你执行他：

```ruby 
$ ruby ./cli hello "Yehuda Katz"
Hello Yehuda Katz
 
$ ruby ./cli hello "Yehuda Katz" "Carl Lerche"
from: Carl Lerche
Hello Yehuda Katz
```

这种方式在某些情况下是非常游泳的，在通常情况下你想要使用Unix方式的可选项。

#####长描述
默认我们会用`desc`去提供简短的任务描述。

```ruby 
$ ruby ./cli help hello
Usage:
  test.rb hello NAME
 
say hello to NAME
```

在很多情况下，你如果要提供一个长的描述，你可以使用`long_desc`来指定你的长描述。

```ruby
class MyCLI < Thor
  desc "hello NAME", "say hello to NAME"
  long_desc <<-LONGDESC
    `cli hello` will print out a message to a person of your
    choosing.
 
    You can optionally specify a second parameter, which will print
    out a from message as well.
 
    > $ cli hello "Yehuda Katz" "Carl Lerche"
 
    > from: Carl Lerche
  LONGDESC
  def hello(name, from=nil)
    puts "from: #{from}" if from
    puts "Hello #{name}"
  end
end
```

默认情况下，长描述会将多行的描述最终包装成一行。如果你要断行可以在行首使用`\x5`来实现断行。

```ruby
class MyCLI < Thor
  desc "hello NAME", "say hello to NAME"
  long_desc <<-LONGDESC
    `cli hello` will print out a message to a person of your
    choosing.
 
    You can optionally specify a second parameter, which will print
    out a from message as well.
 
    > $ cli hello "Yehuda Katz" "Carl Lerche"
    \x5> from: Carl Lerche
  LONGDESC
  def hello(name, from=nil)
    puts "from: #{from}" if from
    puts "Hello #{name}"
  end
end
```
在很多情况下，你会为了让CLI简短和可读性而将长描述保存在单独的文件里面。你可以使用`File.read`来获取文件的内容。

#####选项和标志
Thor让我们很容易指定options and flags作为Thor command的数据。

```ruby
class MyCLI < Thor
  desc "hello NAME", "say hello to NAME"
  option :from
  def hello(name)
    puts "from: #{options[:from]}" if options[:from]
    puts "Hello #{name}"
  end
end
```

现在你的用户可以指定`from`选项来作为标识：

```ruby
$ ruby ./cli hello --from "Carl Lerche" Yehuda
from: Carl Lerche
Hello Yehuda
 
$ ruby ./cli hello Yehuda --from "Carl Lerche"
from: Carl Lerche
Hello Yehuda
 
$ ruby ./cli hello Yehuda --from="Carl Lerche"
from: Carl Lerche
Hello Yehuda
```

默认选项(options)是字符串，但是你可以给任何选项指定备用的类型：

```ruby
class MyCLI < Thor
  option :from
  option :yell, :type => :boolean
  desc "hello NAME", "say hello to NAME"
  def hello(name)
    output = []
    output << "from: #{options[:from]}" if options[:from]
    output << "Hello #{name}"
    output = output.join("\n")
    puts options[:yell] ? output.upcase : output
  end
end
```

现在你可以获得如下输出：

```ruby 
$ ./cli hello --yell Yehuda --from "Carl Lerche"
FROM: CARL LERCHE
HELLO YEHUDA
 
$ ./cli hello Yehuda --from "Carl Lerche" --yell
FROM: CARL LERCHE
HELLO YEHUDA
```

你也可以指定一个选项为`required`

```ruby
class MyCLI < Thor
  option :from, :required => true
  option :yell, :type => :boolean
  desc "hello NAME", "say hello to NAME"
  def hello(name)
    output = []
    output << "from: #{options[:from]}" if options[:from]
    output << "Hello #{name}"
    output = output.join("\n")
    puts options[:yell] ? output.upcase : output
  end
end
```

现在你可以尝试不带有`required`标识的选项：

```ruby
$ ./cli hello Yehuda
No value provided for required options '--from'
```

下面列出所有的选项的可选子字段：
* :desc:选项的描述。当使用`cli help hello`打印所有命令使用信息的时候，描述会出现在选项之后。
* :banner:选项简短的描述，在使用描述中会打印出来。默认情况下他是flag的大写(form=FROM)
* :required:指明选项是必须的
* :default:指定选项的默认值。一个选项不可以同时有:required和:default
* :type: :string , :hash , :array , :numeric 或者 :boolean
* :aliases: 选项的别名。通常是为了让选项有一个简短的别名，方便使用

如果你只是想指定选项的类型，你可以同时指定几个选项，你可以重写之前的例子：

```ruby
class MyCLI < Thor
  desc "hello NAME", "say hello to NAME"
  options :from => :required, :yell => :boolean
  def hello(name)
    output = []
    output << "from: #{options[:from]}" if options[:from]
    output << "Hello #{name}"
    output = output.join("\n")
    puts options[:yell] ? output.upcase : output
  end
end
```
总之，你可以指定`:required`作为类型，那么选项就会是必须的:string类型。

#####类选项
你可以通过`class_option`指定一个选项作用于整个类.class_option和option的使用方式基本一样。只不过class_option作用于整个类的所有命令。

```ruby
class MyCLI < Thor
  class_option :verbose, :type => :boolean
 
  desc "hello NAME", "say hello to NAME"
  options :from => :required, :yell => :boolean
  def hello(name)
    puts "> saying hello" if options[:verbose]
    output = []
    output << "from: #{options[:from]}" if options[:from]
    output << "Hello #{name}"
    output = output.join("\n")
    puts options[:yell] ? output.upcase : output
    puts "> done saying hello" if options[:verbose]
  end
 
  desc "goodbye", "say goodbye to the world"
  def goodbye
    puts "> saying goodbye" if options[:verbose]
    puts "Goodbye World"
    puts "> done saying goodbye" if options[:verbose]
  end
end
```

#####字命令
当你的CLI变得越来越复杂，你或许想要指定一个命令有一系列的子命令。例如`git remote`命令，它包含 `add`,`rename`,`rm`,`prune`,`set-head`等等。	
在Thor中，你可以很简单的创建新的Thor类去表示子命令和指向它的父类。让我们看看怎么实现`git remote`,例子很简单:

```ruby
module GitCLI
  class Remote < Thor
    desc "add <name> <url>", "Adds a remote named <name> for the repository at <url>"
    long_desc <<-LONGDESC
      Adds a remote named <name> for the repository at <url>. The command git fetch <name> can then be used to create and update
      remote-tracking branches <name>/<branch>.
 
      With -f option, git fetch <name> is run immediately after the remote information is set up.
 
      With --tags option, git fetch <name> imports every tag from the remote repository.
 
      With --no-tags option, git fetch <name> does not import tags from the remote repository.
 
      With -t <branch> option, instead of the default glob refspec for the remote to track all branches under $GIT_DIR/remotes/<name>/, a
      refspec to track only <branch> is created. You can give more than one -t <branch> to track multiple branches without grabbing all
      branches.
 
      With -m <master> option, $GIT_DIR/remotes/<name>/HEAD is set up to point at remote's <master> branch. See also the set-head
      command.
 
      When a fetch mirror is created with --mirror=fetch, the refs will not be stored in the refs/remotes/ namespace, but rather
      everything in refs/ on the remote will be directly mirrored into refs/ in the local repository. This option only makes sense in
      bare repositories, because a fetch would overwrite any local commits.
 
      When a push mirror is created with --mirror=push, then git push will always behave as if --mirror was passed.
    LONGDESC
    option :t, :banner => "<branch>"
    option :m, :banner => "<master>"
    options :f => :boolean, :tags => :boolean, :mirror => :string
    def add(name, url)
      # implement git remote add
    end
 
    desc "rename <old> <new>", "Rename the remote named <old> to <new>"
    def rename(old, new)
    end
  end
 
  class Git < Thor
    desc "fetch <repository> [<refspec>...]", "Download objects and refs from another repository"
    options :all => :boolean, :multiple => :boolean
    option :append, :type => :boolean, :aliases => :a
    def fetch(respository, *refspec)
      # implement git fetch here
    end
 
    desc "remote SUBCOMMAND ...ARGS", "manage set of tracked repositories"
    subcommand "remote", Remote
  end
end
```

你可以通过`parent_options`在子命令中访问父命令的选项