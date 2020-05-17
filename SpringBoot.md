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