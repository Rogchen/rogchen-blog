title: 使用eureka注册中心

tags: 
	[eureka,springcloud,微服务]

---
## 1、单机下

  推荐使用idea快速构造springboot工程，自动引入相关配置文件。

引入相关pom文件
``` pom.xml
	<dependency>
		<groupId>org.springframework.cloud</groupId>
		<artifactId>spring-cloud-starter-eureka-server</artifactId>
	</dependency>
```
<!--more-->

在启动文件***Application.java上添加注解
  
  `@EnableEurekaServer`
  
配置注册中心配置文件

```
#eureka配置信息
eureka:
  server:
    enable-self-preservation: false #注册中心自我保护机制-true打开默认true,false关闭
    eviction-interval-timer-in-ms: 60000 #修改检查失效服务的时间。
  instance:
    prefer-ip-address: true
    hostname: rogchen.net
  client:
    registerWithEureka: true #false:不作为一个客户端注册到注册中心
    fetchRegistry: false #为true时，可以启动，但报异常：Cannot execute request on any known server
#    service-url:
#      defaultZone: http://${eureka.instance.hostname}:10801/eureka/

```

***

## 2、集群化部署

集群化部署跟单机区别在于本身也相当于一个服务注册到另外的eureka上去。
所以需要在启动类上新增另个注解。

```
@EnableEurekaClient
@EnableDiscoveryClient

```

二选一都可.spring cloud中discovery service有许多种实现（eureka、consul、zookeeper等等），@EnableDiscoveryClient基于spring-cloud-commons, @EnableEurekaClient基于spring-cloud-netflix。
其实用更简单的话来说，就是如果选用的注册中心是eureka，那么就推荐@EnableEurekaClient，如果是其他的注册中心，那么推荐使用@EnableDiscoveryClient。


另个差别就是在配置文件上面了。

```
eureka:
  server:
	#注册中心自我保护机制-true打开,默认true,false关闭
    enable-self-preservation: true
	#修改检查失效服务的时间。
    eviction-interval-timer-in-ms: 60000
  instance:
    prefer-ip-address: true
    hostname: rogchen.com
  client:
    #false:不作为一个客户端注册到注册中心,集群作为一个客户端应该true，默认true
    registerWithEureka: true 
	 #为true时，可以启动，但报异常：Cannot execute request on any known server,
	 #集群情况下必须设置为true。
    fetchRegistry: true
    service-url:
	#另个或者多个eurek的注册地址，使用','分割
      defaultZone: http://${eureka.instance.hostname}:10801/eureka/
	  
```
