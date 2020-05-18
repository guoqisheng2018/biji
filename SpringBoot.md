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

编写一个Dao借口来操作