# SpringBoot

## SpringBoot启动配置原理

### 启动流程

1创建springApplication对象

保存主配置类->判断当前是否是一个web'应用->从类路径下找到META-INF/spring.factories配置的所有ApplicationContextInitializer然后保存起来->从类路径下找到META-INF/spring.factories配置的所有ApplicationListener ->从多个主配置类中找到有main方法的主配置类

使用以下代码创建SpringApplication

```java
public SpringApplication(ResourceLoader resourceLoader, Class<?>... primarySources) {
        this.sources = new LinkedHashSet();
        this.bannerMode = Mode.CONSOLE;
        this.logStartupInfo = true;
        this.addCommandLineProperties = true;
        this.addConversionService = true;
        this.headless = true;
        this.registerShutdownHook = true;
        this.additionalProfiles = new HashSet();
        this.isCustomEnvironment = false;
        this.lazyInitialization = false;
        this.resourceLoader = resourceLoader;
        Assert.notNull(primarySources, "PrimarySources must not be null");
        this.primarySources = new LinkedHashSet(Arrays.asList(primarySources));
        this.webApplicationType = WebApplicationType.deduceFromClasspath();
  //从图中方法的类路径下找到配置的所有ApplicationInitalizer然后保存起来
        this.setInitializers(this.getSpringFactoriesInstances(ApplicationContextInitializer.class));
  //从图中方法的类路径下找到配置的所有ApplicationListener然后保存起来
        this.setListeners(this.getSpringFactoriesInstances(ApplicationListener.class));
  //从new RuntimeException()).getStackTrace()获得的结果值中找有main方法的主配置类，方法匹配结果的getMethodName()是否为main
        this.mainApplicationClass = this.deduceMainApplicationClass();
    }
```

![](/Users/guoqisheng/Desktop/笔记/biji/springBoot/WeChat52126d65f1ff522648cde01f9816489d.png)

2运行run方法

```java
public ConfigurableApplicationContext run(String... args) {
        StopWatch stopWatch = new StopWatch();
        stopWatch.start();
        ConfigurableApplicationContext context = null;
        Collection<SpringBootExceptionReporter> exceptionReporters = new ArrayList();
        this.configureHeadlessProperty();
  //获取SpringApplicationRunListeners；从类路径下获取
        SpringApplicationRunListeners listeners = this.getRunListeners(args);
  //回调所有的获取获取SpringApplicationRunListener.starting()方法‘
        listeners.starting();

        Collection exceptionReporters;
        try {
            ApplicationArguments applicationArguments = new DefaultApplicationArguments(args);
          //准备环境
            ConfigurableEnvironment environment = this.prepare完成后，用 listeners.environmentPrepared((ConfigurableEnvironment)environment);nvironment(listeners, applicationArguments);
          //创建环境完成后，回调listeners.environmentPrepared((ConfigurableEnvironment)environment);表示环境准备完成
            this.configureIgnoreBeanInfo(environment);
            Banner printedBanner = this.printBanner(environment);
          //创建ApplicationContext；决定创建是什么ioc
            context = this.createApplicationContext();
            exceptionReporters = this.getSpringFactoriesInstances(SpringBootExceptionReporter.class, new Class[]{ConfigurableApplicationContext.class}, context);
          //准备上下文；将environment保存到ioc中；而且applyInitializers()回调之前保存的所有的ApplicationContextInitializer的initializer方法；回调所有的springApplicationRunListener的contextPrepared的方法
          //prepareContext完全运行完成后回调所有的SpringApplicationRunListeners的contextLoaded()方法
            this.prepareContext(context, environment, listeners, applicationArguments, printedBanner);
          //刷新容器；ioc容器初始化
          //扫描，创建，加载所有组件的地方
            this.refreshContext(context);
            this.afterRefresh(context, applicationArguments);
            stopWatch.stop();
            if (this.logStartupInfo) {
                (new StartupInfoLogger(this.mainApplicationClass)).logStarted(this.getApplicationLog(), stopWatch);
            }

            listeners.started(context);
          //从ioc容器中获取所有的ApplicationRunner和CommandLineRunner进行回调
            this.callRunners(context, applicationArguments);
        } catch (Throwable var10) {
            this.handleRunFailure(context, var10, exceptionReporters, listeners);
            throw new IllegalStateException(var10);
        }

        try {
            listeners.running(context);
          //整个springboot容器应用启动完成以后返回启动的ioc容器
            return context;
        } catch (Throwable var9) {
            this.handleRunFailure(context, var9, exceptionReporters, (SpringApplicationRunListeners)null);
            throw new IllegalStateException(var9);
        }
    }
```

3事件监听机制

## Mybatius配置

开启驼峰命名

```yml
mybatis:
  configuration:
    map-underscore-to-camel-case: true
```

开启控制台的sqllog

```yml
logging:
  level:
    com:
      luoqingshang:
        cache: debug
```

自动返回主键

```java
@Options(useGeneratedKeys = true,keyProperty = "id")自动返回主键
```

## jpa

先在pom文件中引入jpa

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
```

在yml文件中配置数据源

```yml
spring:
  datasource:
    url: jdbc:mysql://10.102.71.136:3308/jpa?useUnicode=true&characterEncoding=utf8&useSSL=false&useLegacyDatetimeCode=false&serverTimezone=Asia/Shanghai&allowPublicKeyRetrieval=true
    username: root
    password: "gqs123456"
    driver-class-name: com.mysql.jdbc.Driver
  jpa:
    hibernate:
#      更新或者创建数据表结构
      ddl-auto: update
#      每次jpa sql在控制台打印
      show-sql: true
```

编写 一个实体类（bean）和数据表进行映射，并且配置好映射关系

```java
package com.example.web.entity;

import lombok.Data;

import javax.persistence.*;

//使用jpa注解配置映射关系
@Entity//告诉jpa这是一个实体类（和数据表映射的类）
@Table(name="tbl_user")//指定和哪个数据表对应；如果省略默认表名就是user（类名小写）
@Data
public class User {

    @Id//这是一个主键
    @GeneratedValue(strategy = GenerationType.IDENTITY)//自增主键
    private Integer id;
    @Column(name="last_name",length = 50)//这是和数据表对应的一个列，长度为50
    private String lastName;
    @Column//省略默认列名就是属性名
    private String email;

}

```

编写一个Dao接口来操作

## 自定义starter

starter：

1这个场景需要使用到的依赖是什么

2如何编写自动配置

```java
@Configuartion//指定这个类是一个配置类
@ConditionalOnxxx//在指定条件成立的情况下自动配置类生效
@AutoConfigureAfter//指定自动配置类的顺序
@Bean//给容器中添加组件
@ConfigurationPropertie//结合相关xxxProperties类来绑定相关的配置
@EnableConfigurationProperties//让xxxProperties生效加入到容器中
//自动配置类要能加载将需要启动就加载的自动配置类，配置在META-INF/Spring.factories

```

3模式

启动器只用来做依赖导入；

专门来写一个自动配置模块；

启动器依赖自动配置；别人只需要引入启动器（starter）

命名规范

官方命名空间

前缀：spring-boot-starter-

模式：spring-boot-starter-模块名

举例：spring-boot-starter-web

自定义命名空间

后缀：-spring-boot-starter

模式：模块名-spring-boot-starter

举例：mybatis-spring-boot-starter

### 创建starter步骤

1先创建一个空的工程

![](/Users/guoqisheng/Desktop/笔记/biji/springBoot/截屏2020-05-19 上午9.49.42.png)

2添加模块，先添加maven模块

![](/Users/guoqisheng/Desktop/笔记/biji/springBoot/截屏2020-05-19 上午9.50.42.png)

![](/Users/guoqisheng/Desktop/笔记/biji/springBoot/截屏2020-05-19 上午9.53.25.png)

3在添加一个Springboot初始器

![](/Users/guoqisheng/Desktop/笔记/biji/springBoot/截屏2020-05-19 上午10.00.21.png)

4在starter中引入自动配置模块

```java
<dependencies>
        <dependency>
            <groupId>com.luoqingshang.starter</groupId>
            <artifactId>luoqingshang-spring-boot-starter-autoconfigurer</artifactId>
            <version>0.0.1-SNAPSHOT</version>
        </dependency>
    </dependencies>
```

![](/Users/guoqisheng/Desktop/笔记/biji/springBoot/截屏2020-05-19 上午10.31.58.png)

5删除自动配置模块中的配置文件，启动器，pom中的test和插件（build标签）

6在自动配置模块中新建xxxProperties

```java
package com.luoqingshang.starter;

import org.springframework.boot.context.properties.ConfigurationProperties;

@ConfigurationProperties(prefix = "luoqingshang.hello")

public class HelloProperties {
    private String prefix;
    private String suffix;

    public String getPrefix() {
        return prefix;
    }

    public void setPrefix(String prefix) {
        this.prefix = prefix;
    }

    public String getSuffix() {
        return suffix;
    }

    public void setSuffix(String suffix) {
        this.suffix = suffix;
    }
}

```

7在自动配置模块中新建service

```java
package com.luoqingshang.starter;

public class HelloService {

    HelloProperties helloProperties;

    public HelloProperties getHelloProperties() {
        return helloProperties;
    }

    public void setHelloProperties(HelloProperties helloProperties) {
        this.helloProperties = helloProperties;
    }

    public String sayHello(String name){
        return helloProperties.getPrefix()+"-"+name+"-"+helloProperties.getSuffix();
    }
}
```

8在自动配置模块中新建xxxAutoConfiguration

```java
package com.luoqingshang.starter;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.autoconfigure.condition.ConditionalOnWebApplication;
import org.springframework.boot.context.properties.EnableConfigurationProperties;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
@ConditionalOnWebApplication//web应用生效
@EnableConfigurationProperties(HelloProperties.class)
public class HelloServiceAutoConfiguration {
    @Autowired
    HelloProperties helloProperties;
    @Bean
    public HelloService helloService(){
        HelloService service=new HelloService();
        service.setHelloProperties(helloProperties);
        return service;
    }
}
```

9在自动配置模块中的resources中新建META-INF及其下的spring.factories

```
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
com.luoqingshang.starter.HelloServiceAutoConfiguration
```

10使用maven装到仓库中方便使用，先装autoConfigurer，再装starter

![](/Users/guoqisheng/Desktop/笔记/biji/springBoot/截屏2020-05-19 下午12.47.43.png)

### 使用starter

先在pom中引入starter

```xml
<dependency>
            <groupId>com.luoqingshang.starter</groupId>
            <artifactId>luoqingshang-spring-boot-starter</artifactId>
            <version>1.0-SNAPSHOT</version>
</dependency>
```

编写使用方法

```java
package com.example.demo01.controller;

import com.luoqingshang.starter.HelloService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloController {
    @Autowired
    HelloService helloService;

    @GetMapping("/hello")
    public String hello(){
       return  helloService.sayHello("章三");
    }
}

```

编写配置文件(名字和自动配置模块中的自动配置类一致)

```properties
luoqingshang.hello.prefix=PREFIX
luoqingshang.hello.suffix=SUFFIX
```

## 缓存

### 原理：

1自动配置类；CacheAutoConfiguration

2SimpleCacheConfiguration配置类默认生效

3给容器中注册了一个CacheManager：ConcurrentMapCacheManager

4可以获取和创建ConcurrentMapCache类型的缓存组件，他的作用将数据保存在ConcurrentMap中

运行流程

1方法运行前，先去查询Cache(缓存组件)，按照cacheNames指定的名字获取；（CacheManager先获取相应的缓存，用的是getCache（）方法）第一次获取缓存如果没有Cache组件会自动创建出来

2去Cache中查找缓存的内容，使用一个key，默认为方法的参数；key是按照某种策略生成的；默认是使用keyGenerator生成的，默认使用SimpleKeyGenerator生成key；

如果没有参数：key=new SimpleKey();

如果有一个参数：key=参数的值；

如果有多个参数：key=new SimpleKey(params);

3没有查到缓存就调用目标方法

4将目标方法返回的结果，放进缓存中

@Cacheable标注的方法执行之前先检查缓存中有没有这个数据，默认按照参数的值作为key去查询缓存，如果没有就运行方法并将结果放入缓存；以后再来调用就可以直接使用缓存中的数据

核心

1使用CacheManager（ConcurrentMapCacheManager）按照名字得到Cache（ConcurrentMapCache）组件

2key使用keyGenerator生成的，默认是SimpleKeyGenerator

### 使用

1开启基于注解的缓存

在启动类上标注

```java
@EnableCaching
```

2标注缓存注解即可

```java
//CacheManager管理多个Cache组件，对缓存的真正CRUD操作在Cache组件中，每一个缓存组件有自己唯一一个名字
@Cacheable//将方法的运行结果进行缓存，以后再要相同的数据，直接从缓存中读取，不用调用方法
//cacheNames/value：指定缓存的名字
//key：缓存数据使用的key；可以用它来指定，默认是使用方法参数的值
//keyGenerator:key的生成器；可以自己指定key的生成器的组件id
//key/keyGenerator二选一
//cacheManager：指定缓存管理器；或者cacheResolver指定缓存解析器
//condition：指定符合条件的情况下，才缓存
//unless:否定缓存，当unless指定的条件为true，方法的返回值就不会被缓存，可以获取到结果进行判断
//sync:是否使用异步模式
```

```java
@Cacheable(cacheNames = {"emp","temp"},key="#root.methodName+'['+#id+']'",condition = "#id>0",unless = "#result==null")
```

或者声明一个config

```java
package com.luoqingshang.cache.config;

import org.springframework.cache.interceptor.KeyGenerator;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import java.lang.reflect.Method;
import java.util.Arrays;


@Configuration
public class MyCacheConfig {

    @Bean("myKeyGenerator")
    public KeyGenerator keyGenerator(){
      return new KeyGenerator(){
            @Override
            public Object generate(Object o, Method method, Object... objects) {
                return method.getName()+"["+ Arrays.asList(objects).toString()+"]";
            }
        };
    }
}

```

再在需要缓存的方法上标注解

```java
@Cacheable(cacheNames = {"emp","temp"},keyGenerator = "myKeyGenerator",condition = "#id>0",unless = "#result==null")
```



```java
 @CachePut
//既调用方法，又更新缓存数据
//修改数据库的某个数据，同时更新缓存
//运行时机，先调用目标方法，再将目标方法的结果缓存起来
```

```java
@CacheEvict
//清除缓存
//属性 allEntries=true 清楚指定缓存中的全部数据
//属性 beforeInvocation 缓存的清除是否在方法之前执行；默认在方法执行之后执行
```

### 注意

注解标注的目标方法必须有返回值，否则无法将结果存到缓存中（拿不到结果）