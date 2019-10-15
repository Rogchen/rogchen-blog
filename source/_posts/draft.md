
title: hexo私密功能
tags:	
	- draft
	- hexo
---

### 1.草稿相当于很多博客都有的“私密文章”功能。
* 会在source/_drafts目录下生成一个new-draft.md文件。但是这个文件不被显示在页面上，链接也访问不到。也就是说如果你想把某一篇文章移除显示，又不舍得删除，可以把它移动到_drafts目录之中。
```
$ hexo new draft "new draft"
```
如果你希望强行预览草稿，更改配置文件：
```
render_drafts: true
```
或者，如下方式启动server：
```
$ hexo server --drafts
```
下面这条命令可以把草稿变成文章，或者页面：
```
$ hexo publish [layout] <filename>
```
### 2.常用命令

<!--more-->

```
hexo new "postName" #新建文章
hexo new page "pageName" #新建页面
hexo generate #生成静态页面至public目录
hexo server #开启预览访问端口（默认端口4000，'ctrl + c'关闭server）
hexo deploy #将.deploy目录部署到GitHub
```
组合命令
```
hexo deploy -g
hexo server -g
```
简写命令
```
hexo n == hexo new
hexo g == hexo generate
hexo s == hexo server
hexo d == hexo deploy
```

### 3.搜索引擎
[屈站长]: http://www.sousuoyinqingtijiao.com/ "Optional Title Here"
* 你可以到[屈站长]提交你的站点给搜索引擎。其他内容如添加站点或页面的description，提交Sitemap，添加百度统计，Google Analytics等等，参考本文其他章节的内容，不再一一阐述。
