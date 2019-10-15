---
title: springboot使用mail群发邮件一
tags:
	[springboot,mail]
---
### 原由
  之前因为业务需要需要搭建一个邮件发送，网上一查一堆，也没什么毛病，这里主要是做个笔记。还有就是为了只会搭建个邮箱群发平台，但是查了一堆资料都是使用个账号，但是目前一些免费的账号都是有发送次数限制的。那是否是不是有多个账号进行群发呢？答案显然是有的。

### javamail介绍

　　JavaMail API 按照功能可以划分为如下三大类：

* 1. 创建和解析邮件的API
* 2. 发送邮件的API
* 3. 接收邮件的API

　　以上三种类型的API在JavaMail 中由多个类组成，但是主要有四个核心类，我们在编写程序时，记住这四个核心类，就很容易编写出Java邮件处理程序。

<!--more-->

![javamail-api](https://raw.githubusercontent.com/Rogchen/rogchen-picture/master/blog-img/javamail/javamail-api.png)
　　
* 1. Message 类:javax.mail.Message 类是创建和解析邮件的核心 API，这是一个抽象类，通常使用它的子类javax.mail.internet.MimeMessage 类。它的实例对象表示一份电子邮件。客户端程序发送邮件时，首先使用创建邮件的 JavaMail API 创建出封装了邮件数据的 Message 对象，然后把这个对象传递给邮件发送API（Transport 类） 发送。客户端程序接收邮件时，邮件接收API把接收到的邮件数据封装在Message 类的实例中，客户端程序在使用邮件解析API从这个对象中解析收到的邮件数据。

* 2. Transport 类：javax.mail.Transport 类是发送邮件的核心API 类，它的实例对象代表实现了某个邮件发送协议的邮件发送对象，例如 SMTP 协议，客户端程序创建好 Message 对象后，只需要使用邮件发送API 得到 Transport 对象，然后把 Message 对象传递给 Transport 对象，并调用它的发送方法，就可以把邮件发送给指定的 SMTP 服务器。

- 3. Store 类：javax.mail.Store 类是接收邮件的核心 API 类，它的实例对象代表实现了某个邮件接收协议的邮件接收对象，例如 POP3 协议，客户端程序接收邮件时，只需要使用邮件接收 API 得到 Store 对象，然后调用 Store 对象的接收方法，就可以从指定的 POP3 服务器获得邮件数据，并把这些邮件数据封装到表示邮件的 Message 对象中。

- 4. Session 类：javax.mail.Session 类用于定义整个应用程序所需的环境信息，以及收集客户端与邮件服务器建立网络连接的会话信息，例如邮件服务器的主机名、端口号、采用的邮件发送和接收协议等。Session 对象根据这些信息构建用于邮件收发的 Transport 和 Store 对象，以及为客户端创建 Message 对象时提供信息支持。

### java实现过程

在理解下面通过代码实现邮件的发送时，我们可以将邮件发送假想为火箭运载这卫星发送。其中Message 是卫星，Transport 是火箭，而卫星和火箭的构建都需要 Session 的帮助，这样的关系更加便于记忆。

``` 账号信息
//发件人地址
public static String senderAddress = "***@qq.com";
//收件人地址
public static String recipientAddress = "***@qq.com";
//发件人账户名
public static String senderAccount = "****@qq.com";
//发件人账户密码
public static String senderPassword = "";
```


``` 代码实现过程
//1、连接邮件服务器的参数配置
Properties props = new Properties();
//设置用户的认证方式
props.setProperty("mail.smtp.auth", "true");
//设置传输协议
props.setProperty("mail.transport.protocol", "smtp");
//设置发件人的SMTP服务器地址
props.setProperty("mail.smtp.host", "smtp.qq.com");
//2、创建定义整个应用程序所需的环境信息的 Session 对象
Session session = Session.getInstance(props);
//设置调试信息在控制台打印出来
session.setDebug(true);
//3、创建邮件的实例对象
Message msg = getMimeMessage(session,senderAddress);
//4、根据session对象获取邮件传输对象Transport
Transport transport = session.getTransport();
//设置发件人的账户名和密码
transport.connect(senderAccount, senderPassword);
//发送邮件，并发送到所有收件人地址，message.getAllRecipients() 获取到的是在创建邮件对象时添加的所有收件人, 抄送人, 密送人
transport.sendMessage(msg, msg.getAllRecipients());
//如果只想发送给指定的人，可以如下写法
//transport.sendMessage(msg, new Address[]{new InternetAddress("xxx@qq.com")});
//5、关闭邮件连接
transport.close();
```


```
//创建一封邮件的实例对象
MimeMessage msg = new MimeMessage(session);
//设置发件人地址
msg.setFrom(new InternetAddress(senderAddress));
/**
* 设置收件人地址（可以增加多个收件人、抄送、密送），即下面这一行代码书写多行
* MimeMessage.RecipientType.TO:发送
* MimeMessage.RecipientType.CC：抄送
* MimeMessage.RecipientType.BCC：密送
*/
msg.setRecipient(MimeMessage.RecipientType.TO, new InternetAddress(recipientAddress));
//设置邮件主题
msg.setSubject("邮件主题", "UTF-8");
//设置邮件正文
msg.setContent("简单的纯文本邮件！"+senderAddress, "text/html;charset=UTF-8");
//设置邮件的发送时间,默认立即发送
msg.setSentDate(new Date());

return msg;

```

这样就可以发送邮件了，是不是很简单。

