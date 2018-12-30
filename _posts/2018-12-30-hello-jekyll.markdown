---
layout: post
title:  "hello Jekyll"
date:   2018-12-30 00:18:23 +0700
categories: [web]
---
参考文档：[使用Github pages+jekyll搭建自己的博客（windows版）](https://www.cnblogs.com/zjjDaily/p/8695978.html)

几点注意：

1. Github生成GitHub Pages，需要一些时间，大约30分钟左右吧，吃了个饭，回来就好了。

2. 安装一些软件的时候，都是翻墙搞定的。不翻墙，走国内镜像怎么配置，没研究。

3. 离线配置，端口号，默认是4000。可以修改端口号，比如：在_config最后一行加上port:5001。

4. 日常离线测试：

   ```bash
   bundle exec jekyll s
   http://127.0.0.1:5001
   ```