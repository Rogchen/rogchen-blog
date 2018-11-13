---
title: 通过注解和拦截器或aop实现日志

tags:
	[注解,拦截器,aop]
---
# 一、使用注解和拦截器
***
### 为什么使用拦截器而不是用过滤器呢？
* 拦截器是基于Java的反射机制的，而过滤器是基于函数回调。
* 拦截器不依赖与servlet容器，过滤器依赖与servlet容器。
* 拦截器可以获取IOC容器中的各个bean，而过滤器就不行，这点很重要，在拦截器里注入一个service，可以调用业务逻辑。
* 拦截器可以访问action上下文、值栈里的对象。

<!--more-->
## 1、添加注解
```

@Target(ElementType.METHOD)			//方法级别-更多可自行查阅
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
public @interface LogRecordAno {
}
```
---
## 2、添加拦截器
#### 根据自己的开发环境选择拦截器的注册方式
###  2.1 springmvc 版。
```
	<!-- 拦截器 -->
	<mvc:interceptors>
		<!-- 指定拦截器 -->
		<mvc:interceptor>
			<!-- 指定地址 -->
			<mvc:mapping path="/**"/>
			<bean class="com.im.core.util.Interceptor.LogRecordInterceptor"/>
		</mvc:interceptor>
	</mvc:interceptors>
	
```
---
## 2.2 springboot 版。
##### 如果没法被扫描那就加扫描呗。当然也可以在META-INF下添加spring.factories来配置类路径
```
@Configuration
public class WebMvcConfig extends WebMvcConfigurerAdapter {
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new LogRecordInterceptor()).addPathPatterns("/**");
        super.addInterceptors(registry);
    }
}
```
---
## 3、拦截器的实现是需要实现HandlerInterceptor的三个方法(preHandle、afterCompletion、postHandle)。
### 3.1 preHandle 请求前
```
   /**
     *  
     * preHandle方法是进行处理器拦截用的，顾名思义，该方法将在Controller处理之前进行调用，SpringMVC中的Interceptor拦截器是链式的，可以同时存在 
     * 多个Interceptor，然后SpringMVC会根据声明的前后顺序一个接一个的执行，而且所有的Interceptor中的preHandle方法都会在 
     * Controller方法调用之前调用。SpringMVC的这种Interceptor链式结构也是可以进行中断的，这种中断方式是令preHandle的返 
     * 回值为false，当preHandle的返回值为false的时候整个请求就结束了。 
     *      
     */
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object o) throws Exception {
        if (o instanceof HandlerMethod) {
                Method method = ((HandlerMethod) o).getMethod();
                LogRecordAno logRecord = method.getAnnotation(LogRecordAno.class);
                if (logRecord != null) {
				// 有request就可以拿到 很多参数，这里方便写就用Map做参数容器
				Map<String, Object> properties = new HashMap<String, Object>();
				    for (Object param : request.getParameterMap().keySet()) {
						properties.put(param.toString(), request.getParameter(param.toString()));
					}
					//其它的业务 逻辑
                }
            }
		return true;
    }
```
### 3.2 postHandle 请求时拦截
```
    /** 
      * 这个方法只会在当前这个Interceptor的preHandle方法返回值为true的时候才会执行。postHandle是进行处理器拦截用的，它的执行时间是在处理器进行处理之 
      * 后，也就是在Controller的方法调用之后执行，但是它会在DispatcherServlet进行视图的渲染之前执行，也就是说在这个方法中你可以对ModelAndView进行操 
      * 作。这个方法的链式结构跟正常访问的方向是相反的，也就是说先声明的Interceptor拦截器该方法反而会后调用，这跟Struts2里面的拦截器的执行过程有点像， 
      * 只是Struts2里面的intercept方法中要手动的调用ActionInvocation的invoke方法，Struts2中调用ActionInvocation的invoke方法就是调用下一个Interceptor 
      * 或者是调用action，然后要在Interceptor之前调用的内容都写在调用invoke之前，要在Interceptor之后调用的内容都写在调用invoke方法之后。 
     */  
    @Override
    public void postHandle(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object
            o, ModelAndView modelAndView) throws Exception {

    }
```

### 3.3 afterCompletion 返回拦截
```
   /**
     * 该方法也是需要当前对应的Interceptor的preHandle方法的返回值为true时才会执行。该方法将在整个请求完成之后，也就是DispatcherServlet渲染了视图执行， 
     * 这个方法的主要作用是用于清理资源的，当然这个方法也只能在当前这个Interceptor的preHandle方法的返回值为true时才会执行。 
     * url记录高于注解记录问题
     **/
    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object o, Exception e) throws
            Exception {
		}
```
---
## 4.1 触发实现
```
public class test{

	@LogRecordAno
	public void (String userid){
	}
}
```
---
# 二、使用aop和注解
## 1.添加注解（参考上面）
## 2.添加切面
>### 2.1添加jar
```
	<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-aop</artifactId>
	</dependency>
```
---
### 2.2采用切面来做aop处理
```
@Aspect
@Component
public class LogAopAction {
    private final Logger logger = LoggerFactory.getLogger(LogAopAction.class);

   //切入点设置到自定义的log注解上---也可以切路径、通配符等
    @Pointcut("@annotation(com.rogchen.annotation.LogRecordAno)")
    private void pointCutMethod(){}

    /**
     * 记录操作日志
     */
    @After("pointCutMethod()")  // 使用上面定义的切入点
    public void recordLog(JoinPoint joinPoint){
        Long newtime = System.currentTimeMillis();
		//业务逻辑代码
    }
}
```
---
## 3.最终实现
```
public class test{
	@LogRecordAno
	public void (String userid){
	}
}
```