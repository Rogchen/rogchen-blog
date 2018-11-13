---
title: 让hexo的首页只显示文章的部分内容而不是全部

tags:
	[hexo]
---

	Hexo 的 Next 主题默认是首页显示你每篇文章的全文内容，那么要如何设置只显示部分呢？

我们通过下面的三种方式实现:

## 第一种方法
---
用文本编辑器打开 themes/ 目录下的对应的主题的theme文件夹下的 _config.yml 文件，找到这段代码，如果没有则新建，可能不同的主题会不支持这种方法：

```
1 # Automatically Excerpt. Not recommend.
2 # Please use <!-- more --> in the post to control excerpt accurately.
3 auto_excerpt:
4   enable: false
5   length: 150
```
把 enable 的 false 改成 true 就行了，然后 length 是设定文章预览的文本长度。
<!--more-->
修改后重启 hexo 就ok了。

* 效果：修改 _config.yml 文件的效果是会格式化你文章的样式，直接把文字挤在一起显示，最后会有 …。

## 第二种方法
--- 
  在你写 md 文章的时候，可以在内容中加上 <\!--more--\>，这样首页和列表页展示的文章内容就是 <!--more--> 之前的文字，而之后的就不会显示了。
  上面两种方式展示出来的效果是不一样的。

* 效果：加上 <\!--more--\>展示出来的就是你原本文章的样式，最后不会有…。

## 第三种方法
---

在文章的 front-matter 中添加 description，并提供文章摘录
```
1 title: 让hexo的首页只显示文章的部分内容而不是全部
2 id: set-hexo-show-more-button-on-index
3 date: 2017-09-30 11:01:40
4 tags:
5    - blog
6    - hexo
```
但是使用这种方式生成的描述信息在文章的详情页是不再显示的。

## 总结
---
各种方式展示的效果各有好处，第二种方法保留了样式而且可以自行选择显示哪些内容来预览，推荐使用此方法，第一种方法显示的每篇文章的预览都是一样的高度，第三种则需要在文章的[front-matter]{https://hexo.io/docs/front-matter.html}里面添加。

参考链接：
* [http://www.5isjyx.com/coding/201704/nextreadthefulltext.html](http://www.5isjyx.com/coding/201704/nextreadthefulltext.html)
* [http://theme-next.iissnan.com/faqs.html](http://theme-next.iissnan.com/faqs.html)
