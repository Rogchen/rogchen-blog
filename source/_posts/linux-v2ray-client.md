title: 在linux使用v2ray进行翻墙

tags: 
	[linux,v2ray,翻墙]
---
1、因为v2ray不区分客户端和服务端唯一区分客户端跟服务端是config.json这个配置文件。这样我们就可以直接参照v2ray服务端的安装方式安装客户端。
v2ray默认有三种安装方式：脚本安装、手动安装、编译安装。我们这里使用脚本安装。

2、下载v2ray
`sudo wget https://install.direct/go.sh`
3、下载完成后执行
`sudo bash go.sh`

<!--more-->

4、最重要的一步配置config.json，脚本安装的情况下路径在/etc/v2ray/
建议直接从其它v2ray拷一份（不区分系统平台只要是客户端就可）。
5、启动
```
### 使用systemctl
sudo systemctl start v2ray
### 或者使用service
sudo service v2ray start
```
6、浏览器设置：有些浏览器居然可不需要设置，不知道为什么。firefox为例：
打开设置-选项-高级-（网络）-设置-手动配置代理（sockes：127.0.0.1,端口根据你客户端的config.json的端口这里是1070）-勾选使用socksv5代理dns。
![v2ray](https://raw.githubusercontent.com/Rogchen/rogchen-picture/master/blog-img/v2ray/firefox-setting.png)	

手动安装也很简单：解压后进入目录在v2ray同级目录移入config.json就可以。但是启动命令就变成,其它配置一样。
`/v2ray/v2ray config.json`


参考链接：
* v2ray官网：  [https://www.v2ray.com/chapter_00/install.html](https://www.v2ray.com/chapter_00/install.html)
* v2ray白话文：[https://toutyrater.github.io/prep/install.html](https://toutyrater.github.io/prep/install.html)
	