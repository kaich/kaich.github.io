---
layout: page
title: indexed blog
---

<div class="post">
   <h1 class="post-title">
       {{ page.title }}
   </h1>

   <span class="post-date">{{ post.date | date_to_string }}</span>

   {{ page.content }}

     <!-- 多说评论框 start -->
	<div class="ds-thread" data-thread-key={{page.id}} data-title={{page.title}} data-url={{site.baseurl}}{{page.url}}></div>
<!-- 多说评论框 end -->
<!-- 多说公共JS代码 start (一个网页只需插入一次) -->
<script type="text/javascript">
var duoshuoQuery = {short_name:"kaich"};
	(function() {
		var ds = document.createElement('script');
		ds.type = 'text/javascript';ds.async = true;
		ds.src = (document.location.protocol == 'https:' ? 'https:' : 'http:') + '//static.duoshuo.com/embed.js';
		ds.charset = 'UTF-8';
		(document.getElementsByTagName('head')[0] 
		 || document.getElementsByTagName('body')[0]).appendChild(ds);
	})();
	</script>
<!-- 多说公共JS代码 end -->
</div>
