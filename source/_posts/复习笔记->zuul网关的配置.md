title: zuul网关的简单配置

tags: 
	[zuul,hystrix,rabbion]
	
description: 很久之前很认真的学习了一波微服务网关的配置，随着时间流逝基本忘记差不多了，这次来重温复习下。

---

## 1、前言
什么是网关：可以粗略的理解成一个nginx，实现动态路由，监控，弹性和安全性等。这边的网关都是在eureka前提下，如果要使用无eureka的参考以下

<!--more-->

```
#listOfServers 通过listOfServers 来实现链路表实现
zuul：
 routes：
  apiGateway：
   path：/api/***
   serviceId：api-service
   stripPrefix：false
 
apiGateway：
 ribbon：#负载
  NIWSServerListClassName：com.netflix.loadbalancer.ConfigurationBasedServerList
  listOfServers：http：//example1.com,http：//example2.com
  ConnectTimeout：1000 
  ReadTimeout：3000 
  MaxTotalHttpConnections：500 

```

## 2、hystrix、feign、rabbion的配置使用
使用springcloud不使用它的全家桶那不如去使用dubbo。

hystrix：通过隔离服务之间的访问点，阻止跨服务的级联故障，并提供了退路选项，所有这些都可以提高系统的整体弹性。

feign：集成了rabbion用于进行远程调用，可以通过配置选择使用google的httpclient或者okhttp。

rabbion： 集成了负责均衡的算法，默认是轮循。

### zuul默认的通用配置
 设置信号量，可在Zuul节点下对所有路由统一设置信号量（semaphore）大小，在实际项目中推荐为每个服务设置不同的信号量（semaphore）。
 
```
zuul:
# 丢弃这些服务的访问
#  ignored-services: "*"
  sensitive-headers:
  semaphore:
    max-semaphores: 1000   #设置全部路由最大信号量
  prefix: /api #为zuul设置一个公共的前缀
  routes:
    back:
      path: /admin/**
      serviceId: cloud-sysmanager
      semaphore:
        max-semaphores: 200  #针对单个服务路由的最大信号量
#    demo:
#      path: /demo/**
#      serviceId: cloud-feign-demo
```


### rabbion的配置
在高并发或者后端服务由于网络等原因，导致请求某一瞬间发生故障，也许后端服务只是暂时不可达或者响应比较慢。通过调整响应时间以及重试次数提高请求成功率。

``` ribbon全局配置
ribbon:
  eureka:
    enabled: true			  # 开启eureka的使用
  ReadTimeout: 120000		  #读超时时间
  ConnectTimeout: 120000    #连接超时时间
  MaxAutoRetries: 0		      #最大重试次数
  MaxAutoRetriesNextServer: 1		 #切换实例的重试次数
  OkToRetryOnAllOperations:true     #全部请求开启重试机制
  MaxConnectionsPerHost: 100        #每个host最大连接数
  MaxTotalHttpConnections: 1000     #最大http连接数
```

SpringCloud中ribbon提供负载均衡能力，实际项目中后端不同服务都是多实例，因此从Zuul路由到某个服务也需要支持负载均衡。

``` ribbon在zuul的个性配置
zuul:
 ribbon:
  OkToRetryOnAllOperations:true     #全部请求开启重试机制
  ReadTimeout: 6000                 #请求处理超时时间
  ConnectTimeout: 6000              #请求连接超时时间
  MaxTotalHttpConnections: 1000     #最大http连接数
  MaxConnectionsPerHost: 100        #每个host最大连接数
  MaxAutoRetries: 10                #最大重试次数
  MaxAutoRetriesNextServer: 10      #切换实例的重试次数
 eureka:
  enabled: true
```

### hystrix
hystrix(熔断)，当通过服务网关（基于Zuul实现）调用后端服务时候，难免会出现网络、响应超时等情况。通过hystrix可断掉与后端服务的连接，防止拖垮网关服务器。也可以通过hystrix实现服务降级，当发生异常时候，通过fallback处理熔断（比如：返回一些用户能看懂的错误提示等），两种方式实现fallback

* 方法一、 在FeignClient 上配置默认fallback = DefaultFallback.class，DefaultFallback类实现这个接口。
* 方法二、 通过fallbackFactory工厂类 在FeignClient 上配置fallbackFactory = HystrixClientFallbackFactory.class 
`public class HystrixClientFallbackFactory implements FallbackFactory<方法二> `

hystrix配置
---
``` hystrix配置
hystrix:
  threadpool:
    default:
      coreSize: 1000 ##并发执行的最大线程数，默认10
      maxQueueSize: 1000 ##BlockingQueue的最大队列数
      queueSizeRejectionThreshold: 500 ##即使maxQueueSize没有达到，达到queueSizeRejectionThreshold该值后，请求也会被拒绝
  command:
    default:
      execution:
        isolation:
          thread:
            timeoutInMilliseconds: 60  #发生熔断的超时时间
            strategy: SEMAPHORE   #隔离策略	
            semaphore:
        		  max-semaphores: 2000 #信号量大小
```
## 总结
hystrix 跟ribbion个配置有个换算关系大致是：发生熔断的超时时间>=ribbion读超时+连接超时*最大尝试次数。

## 测试用例
[hystrix的基础配置](https://gitee.com/rogchen/hystrix-demo)
