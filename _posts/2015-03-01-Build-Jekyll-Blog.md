---
layout: post
title: 搭建Jekyll博客
---

> 本文主要参考：
> [搭建Jekyll博客][] 、
> [Jekyll语法高亮][] 、
> [添加多说评论][] 、 
> [Jekyll官网][] 、 
> [代码高亮方式][] 、
> [Jekyll自动部署][]   
> 你也可以参考：[Gitolite实现自动发布][] 、
> [Gitolite构建Git服务器][]

[搭建Jekyll博客]: http://www.ruanyifeng.com/blog/2012/08/blogging_with_jekyll.html
[Jekyll语法高亮]: http://havee.me/internet/2013-08/support-pygments-in-jekyll.html 
[添加多说评论]: http://havee.me/internet/2013-07/add-duoshuo-commemt-system-into-jekyll.html
[Jekyll官网]: http://jekyllcn.com/docs/deployment-methods/
[代码高亮方式]: http://yansu.org/2013/04/22/highlight-of-jekyll.html)
[Jekyll自动部署]: http://yifanes.github.io/其他编程语言/说说我是如何部署vps%2Bjekyll%2Bgit搭建这个博客的/
[Gitolite实现自动发布]: http://segmentfault.com/blog/p_chou/1190000000455473
[Gitolite构建Git服务器]:http://www.ossxp.com/doc/git/gitolite.html
[Jekyll主题]: https://github.com/jekyll/jekyll/wiki/Sites


  
搭建这个网站都是陆陆续续花了些时间才把它建立起来的。由于我对网页前端，网站部署等相关知识不是很熟洗。自己也是在无尽的google把这个blog建立起来的，花了很多精力。所以希望把它写出来，避免自己忘记，也希望以后和我一样的人少走弯路。  

#####Jekyll创建博客
  
由于自己本身在学习ruby中，Jekyll是基于ruby的，所以本博客才决定用Jekyll创建。

* 安装Jekyll，创建blog。比如创建一个my-awesome-site的博客

		gem install jekyll
		jekyll new my-awesome-site
		cd my-awesome-site
		jekyll serve
		
* 创建好了博客之后就可以写博客了，在_posts文件夹下新建`年-月-日-标题.MARKUP`的文件了,然后在里面写您的博客内容，这是markdown语言(关于markdown语言这里不做介绍，语言很简单，你可以自己google了解)。

关于Jekyll的安装，创建博客，到本地运行您的博客，基本上按照[jekyll官网][]都可以很快的完成。
关于整个网站的主题风格可以在[jekyll主题]找到，你可以找到自己喜欢的加以修改变成你自己的博客风格。

#####代码高亮
######Pygments
Jekyll默认是用的`Pygments`来生成代码块高亮的，`Pygments`支持100多种语言，使用起来相对于比较方便。但是它的使用会直接破坏markdown的风格。
它使用方式如下：

	{% highlight c %}
	/* hello world demo */
	#include <stdio.h>
	int main(int argc, char **argv)
	{
	    printf("Hello, World!\n");
	    return 0;
	}
	{% endhighlight %}
	
或者这样使用
	
	```c
	/* hello world demo */
	#include <stdio.h>
	int main(int argc, char **argv)
	{
	    printf("Hello, World!\n");
	    return 0;
	}
	```
如果你这样使用在markdown编辑器中预览的时候，你会看到很丑陋的页面风格。但是在Jekyll站定中打开的时候，页面就是正常的(所以说它破坏了markdown的封装)。

至于更换代码显示的风格可以调用如下命令生成CSS。

	pygmentize -S monokai -f html > your/path/pygments.css  

，然后包含到你blog的default.html中

	<link rel="stylesheet" href="/your/path/pygments.css">
	
*注意: 如果之前有包含Pygments的CSS记得去掉他，或者在包含的时候直接替换掉它*  

详细类容请参考[Jekyll语法高亮][]

######google code prettify
