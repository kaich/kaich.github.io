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
</div>