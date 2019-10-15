---
title: springboot的configuration跟auto-configuration的区别。

tags:  
	[springboot,configuration,autoconfiguration]
---
spring-boot提倡通过annotation来进行bean的配置，现在spring-boot里面常用的两种创建bean的方式有auto-configuration和configuration两种方式。


### 1、configuration配置的使用。
在配置文件(添加@configuration的类)跟启动类在同个包路径下可用直接被扫描，因为springbootApplication注解自带ComponentScan,不需要在main方法添加扫描器。
<!--more-->

注解SpringBootApplication源码：
``` @springbootApplication源码
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(
    excludeFilters = {@Filter(
    type = FilterType.CUSTOM,
    classes = {TypeExcludeFilter.class}
), @Filter(
    type = FilterType.CUSTOM,
    classes = {AutoConfigurationExcludeFilter.class}
)}
)
public @interface SpringBootApplication {
    @AliasFor(
        annotation = EnableAutoConfiguration.class,
        attribute = "exclude"
    )
	···
}
```

但如果多模块、jar形式或者不在通过包路径下就需要添加自定义类扫描器，在启动的时候使用@ComponentScan注解会扫描包(路径可以指定，默认的情况下就是这个目录所在的包开始扫描)，这个注解扫到@configuration注解以后，就会初始化这个类下面所有加了@bean的方法，并初始化这个bean

在application中添加扫描
``` 在application中添加扫描
@SpringBootApplication
public class demo {
public static void main(String []args) {
		···
	}
}	
```

定义configuration的配置文件
``` 定义configuration的配置文件
@Configuration
@Slf4j
public class BeanConfig {  
  @Bean  
  public BeanConfiguration beanConfig() { 
    log.info("log bean config");    
    return new BeanConfiguration();  
  }
}
```

### 2、Auto-configuration配置的使用
spring-boot有一个@EnableAutoConfiguration注解，他通过读取spring.factories文件里面的EnableAutoConfiguration下面指定的类，来初始化指定类下面的所有加了@bean的方法，并初始化这个bean
查看下源码

@EnableAutoConfiguration
```
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@AutoConfigurationPackage
@Import(AutoConfigurationImportSelector.class)
public @interface EnableAutoConfiguration {
    String ENABLED_OVERRIDE_PROPERTY = "spring.boot.enableautoconfiguration";
    Class<?>[] exclude() default {};
    String[] excludeName() default {};
}
```
可以看到有个Import引入类
``` AutoConfigurationImportSelector
@Override
    public String[] selectImports(AnnotationMetadata annotationMetadata) {
        if (!isEnabled(annotationMetadata)) {
            return NO_IMPORTS;
        }
        try {
            AutoConfigurationMetadata autoConfigurationMetadata = AutoConfigurationMetadataLoader
                    .loadMetadata(this.beanClassLoader);
            AnnotationAttributes attributes = getAttributes(annotationMetadata);
            List<String> configurations = getCandidateConfigurations(annotationMetadata,
                    attributes);
            configurations = removeDuplicates(configurations);
            configurations = sort(configurations, autoConfigurationMetadata);
            Set<String> exclusions = getExclusions(annotationMetadata, attributes);
            checkExcludedClasses(configurations, exclusions);
            configurations.removeAll(exclusions);
            configurations = filter(configurations, autoConfigurationMetadata);
            fireAutoConfigurationImportEvents(configurations, exclusions);
            return configurations.toArray(new String[configurations.size()]);
        }
        catch (IOException ex) {
            throw new IllegalStateException(ex);
        }
    }
```
看如下代码，获取类路径下spring.factories下key为EnableAutoConfiguration全限定名对应值
List<String> configurations = getCandidateConfigurations(annotationMetadata,
attributes);
```
protected List<String> getCandidateConfigurations(AnnotationMetadata metadata,
            AnnotationAttributes attributes) {
        List<String> configurations = SpringFactoriesLoader.loadFactoryNames(
                getSpringFactoriesLoaderFactoryClass(), getBeanClassLoader());
        Assert.notEmpty(configurations,
                "No auto configuration classes found in META-INF/spring.factories. If you "
                        + "are using a custom packaging, make sure that file is correct.");
        return configurations;
    }

    protected Class<?> getSpringFactoriesLoaderFactoryClass() {
        return EnableAutoConfiguration.class;
    }
```
getCandidateConfigurations会到classpath下的读取META-INF/spring.factories文件的配置，并返回一个字符串数组。
查看下spring.factories
```
# Auto Configure
org.springframework.boot.autoconfigure.EnableAutoConfiguration=com.ylzinfo.ms.abc,com.ylzinfo.ms.bbc
```

### 3、两者差异
初始化的时机configuration初始化的方式总是在auto-configuration初始化方式之前
configuration初始化的顺序和扫描的过程相关，并不能进行有效的进行指定，不方便确定文件加载的顺序
auto-configuration可以通过@AutoConfigureAfter  @AutoConfigureBefore 和 @AutoConfigureOrder来指定类的加载顺序
configuration初始化会先初始化所有被扫到加了@Configuration文件的@PostConstruct注解然后再初始化这些文件里面的@Bean注解，但是auto-configuration是根据文件来进行初始化的，所以会初始化完一个文件的@PostConstruct注解然后再初始化这个文件的@Bean注解，然后再接着处理另外的文件。
所以需要提供bean给其他jar包进行使用的时候，最好使用 auto-configuration 方式（spring-boot-starters里面的都是通过这种方式来进行提供的，他的所有初始化的过程全部在spring-boot-autoconfigure项目中），因为能更好的控制类文件的加载顺序。有助于维护更佳复杂的项目。
另外需要注意一点的就是，如果auto-configuration提供的类名称在扫描的路径之中，spring-boot会把这些类作为configuration先进行初始化了，然后@AutoConfigureAfter @AutoConfigureBefore 和 @AutoConfigureOrder 这类指定顺序的注解都会失效。

### 4、总结
@EnableAutoConfiguration 作用从classpath中搜索所有META-INF/spring.factories配置文件然后，将其中org.springframework.boot.autoconfigure.EnableAutoConfiguration key对应的配置项加载到spring容器
只有spring.boot.enableautoconfiguration为true（默认为true）的时候，才启用自动配置
@EnableAutoConfiguration还可以进行排除，排除方式有2中，一是根据class来排除（exclude），二是根据class name（excludeName）来排除
其内部实现的关键点有
1）ImportSelector 该接口的方法的返回值都会被纳入到spring容器管理中
2）SpringFactoriesLoader 该类可以从classpath中搜索所有META-INF/spring.factories配置文件，并读取配置
