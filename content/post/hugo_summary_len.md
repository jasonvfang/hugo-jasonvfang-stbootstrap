+++
banner = ""
categories = ["Hugo"]
date = "2018-06-24T16:10:51+08:00"
description = ""
images = []
menu = ""
tags = ["hugo"]
title = "Hugo summary length 限制"
+++

本文将说明如何在HUGO中对摘要进行长度限制。
<!--more-->


体验了一下HUGO静态网站生成，的确很amazing！

初次写文章时发现首页上的摘要有点显示过长了，GOOGLE了很久，有说配置summary length的属性，试过不行，
其实在官方DOC中已经写明了，请参考[这里](https://gohugo.io/variables/page/)。

只需要在需要进行分隔的地方添加一个```<!--more-->```标签即可，这样标签后面的内容则需要点Read more才可完全显示。


