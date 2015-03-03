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
[多说]: http://duoshuo.com
[添加多说评论]: http://havee.me/internet/2013-07/add-duoshuo-commemt-system-into-jekyll.html
[Jekyll官网]: http://jekyllcn.com/docs/deployment-methods/
[代码高亮方式]: http://yansu.org/2013/04/22/highlight-of-jekyll.html)
[Jekyll自动部署]: http://yifanes.github.io/其他编程语言/说说我是如何部署vps%2Bjekyll%2Bgit搭建这个博客的/
[Gitolite实现自动发布]: http://segmentfault.com/blog/p_chou/1190000000455473
[Gitolite构建Git服务器]:http://www.ossxp.com/doc/git/gitolite.html
[Jekyll主题]: https://github.com/jekyll/jekyll/wiki/Sites
[google code prettify]: https://code.google.com/p/google-code-prettify/
[Disqus]: https://disqus.com
[git hook]: http://git-scm.com/book/en/v2/Customizing-Git-Git-Hooks

  
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
	
	```c
	/* hello world demo */
	#include <stdio.h>
	int main(int argc, char **argv)
	{
	    printf("Hello, World!\n");
	    return 0;
	}
	```
	
会产生如下效果

	{% highlight c %}
	/* hello world demo */
	#include <stdio.h>
	int main(int argc, char **argv)
	{
	    printf("Hello, World!\n");
	    return 0;
	}
	{% endhighlight %}

如果你这样使用在markdown编辑器中预览的时候，你会看到很丑陋的页面风格。但是在Jekyll站定中打开的时候，页面就是正常的(所以说它破坏了markdown的封装)。

至于更换代码显示的风格可以调用如下命令生成CSS。

	pygmentize -S monokai -f html > your/path/pygments.css  

然后包含到你blog的default.html中

	<link rel="stylesheet" href="/your/path/pygments.css">
	
*注意: 如果之前有包含Pygments的CSS记得去掉他，或者在包含的时候直接替换掉它*  

详细类容请参考[Jekyll语法高亮][]

######google code prettify
[google code prettify]使用方法很简单，他可以自动识别语言，不需要想pygments一样需要表明。可以镶嵌代码块的时候保持了markdown的语言格式。但是它本身提供的语言支持相比pygments要少得多，但是你可以自己写扩展。如果你有兴趣你可以尝试写。关于使用可以直接包含run_prettify.js代码

	<script src="https://google-code-prettify.googlecode.com/svn/loader/run_prettify.js"></script>
	 
或者我们直接通过下载代码到博客的目录中进行本地包含。在定义博客内容的html的body的最后包含如下脚本

```js
<link rel="stylesheet" href="/public/js/prettify/prettify.css">
<script src="/public/js/prettify/prettify.js"></script>
<script type="text/javascript">
  $(function(){
    $("pre").addClass("prettyprint linenums");
    prettyPrint();
  });
</script>
```

代码中的路径根据你自己的博客中`prettify.css`和`prettify.js`的文件路径来正确填写。

到此为止你可以良好的在你本地搭建器jekyll服务器并写博客了，以及学会了如果控制代码的高亮。整个jekyll都是用css来调整版面，代码高亮算是一个额外的内容。学会它，基本在jekyll调整版面的时候不会遇到太大的问题了。关于代码高亮我也是先试了下prettify，然后才再次使用pygments的。算是有个大概了解了。    

######评论
一般的博客，别人看了之后或多或少免不了有疑问，这样我们就需要评论系统。很多国外博客都用[Disqus][],但是他对国内的社交平台支持的不太好。因此我是使用的[多说][].下面来添加多说。关于步骤以下有详细介绍，这里就不多说了，请看传送门 ： 
[添加多说评论][]    

到目前为止已经可以在本地运行起来一个相对完整的博客了。

######发布你的博客
想要发布博客，你必须先要有一台服务器。如果有资金可以直接购买服务器。资金相对短缺就可以考虑购买vps，会相对比较便宜。域名可以在国内购买。假设有了服务器。我们就可以把你本地的博客放到服务器上面去了。这里使用的是[git hook][]来进行自动部署的。步骤这里已经很详细了就不再赘述了，在此过程中可能需要linux的相关知识辅助，传送门 ： [Jekyll自动部署][]  

还有另一种方式来进行部署，具体我还没时间研究，传送门先给出来，有时间的可以研究下： [Gitolite实现自动发布][] 、[Gitolite构建Git服务器][]    

能完成自己的blog配置的整个过程，非常感谢各位热爱分享的精神，以及众多资料的帮助。



