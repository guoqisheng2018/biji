# Spring5

[下载spring各种jar的地址](https://repo.spring.io/release/org/springframework/spring/)

## Spring结构

![](/Users/guoqisheng/Desktop/笔记/biji/spring5/截屏2020-06-23 上午10.35.47.png)

## 导入最基础的jar包

![](/Users/guoqisheng/Desktop/笔记/biji/spring5/截屏2020-06-23 上午10.56.02.png)

将jar包拷贝进项目的lib目录下

使用project Structure导入jar包

![](/Users/guoqisheng/Desktop/笔记/biji/spring5/截屏2020-06-23 上午10.55.39.png)

配置bean的xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="user" class="com.luoqingshang.spring5.User"></bean>

</beans>
```

测试代码

```java
package com.luoqingshang.spring5.testdemo;

import com.luoqingshang.spring5.User;
import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class TestSpring5 {

    @Test
    public void testAddUser(){
        //加载配置文件
        ApplicationContext context=new ClassPathXmlApplicationContext("bean1.xml");//或者BeanFactory

        //获取配置文件的创建对象
        User usre=context.getBean("user",User.class);

        usre.add();

    }
}
```

## IOC

### 什么是ioc

1控制反转，把对象创建和对象之间调用过程，交给spring进行管理

2使用ioc目的：为了耦合度降低

### ioc的底层原理

xml解析，工厂模式，反射

## 演变过程

普通new的原始方式演变成工厂模式，达到降低耦合度（service中解除耦合，但工厂类中依然有耦合）

工厂类方式演变成ioc，进一步降低耦合度（利用反射，工厂类中也没有耦合）

## IOC接口

1.IOC思想基于IOC容器完成，IOC容器底层就是工厂对象

2.Spring提供IOC容器实现两种方式：（两个接口）

（1）BeanFactory：IOC容器基本实现，是Spring内部的使用接口，不提倡给开发人员使用

```
加载配置文件时不会创建对象，在获取对象或使用时才会创建对象（懒汉式）
```

（2）ApplicationContext：BeanFactory接口的子接口，提供更多更强大的功能，一般由开发人员使用

```
加载配置文件的时候就会把对象创建（饿汉式）//一般把耗时耗资源的过程交给服务器，所以一般性使用饿汉式
```

3.ApplicationContext的实现类

```
FileSystemXmlApplicationContext  //文件系统路径，从盘符开始算路径
ClassPathXmlApplicationContext  //类路径，从src开始算起
```

## IOC操作Bean管理

什么是bean管理

bean管理指的是两个操作

（1）Spring创建对象

（2）Spring注入属性

bean管理操作由两种方式

### 基于xml配置文件方式实现

#### 基于xml创建对象

1.在spring配置文件中，使用bean标签，标签里面添加对应属性，就可以实现对象创建

2.在bean标签中有很多属性，常用的有：

id：唯一标识

class：类全路径

3.创建对象时，默认执行无参构造方法（若没无参构造会报错）

```xml
<bean id="user" class="com.luoqingshang.spring5.User"></bean>
```

#### 基于xml方式注入属性

1.DI：依赖注入，就是注入属性

```xml
//无参构造，用set方法进行设置参数
<bean id="book" class="com.luoqingshang.spring5.Book">
  <property name="name" value="bookname"/>
  <property name="auther" value="bookauther"/>
</bean>
//简化版，p名称空间注入,有参构造
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"//增加p空间
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
  <bean id="book" class="com.luoqingshang.spring5.Book" p:bname-="xxx" p:bauthor="aaa">
  </bean>//使用p空间给属性赋值
</beans>
//设置null值
<bean id="book" class="com.luoqingshang.spring5.Book">
  <property name="bname" value="bookname"/>
  <property name="bauthor">
    <null></null>
  </property>
</bean>
//属性值包含特殊符号
<bean id="book" class="com.luoqingshang.spring5.Book">
  <property name="bname" value="bookname"/>
  <property name="bauthor">
    <value><![CDATA[带<<特殊符号>>的内容]]></value>
  </property>
</bean>
```

```xml
<bean id="book" class="com.luoqingshang.spring5.Book">
    <constructor-arg name="bname" value="abc"></constructor-arg>
    <constructor-arg name="bauthor" value="bcd"></constructor-arg>
  <!--<constructor-arg index="0" value="abc"></constructor-arg>-->//根据有参构造方法的属性的下标进行设置参数
</bean>//有参构造
```

##### 注入外部bean

```xml
<bean id="userServicr" class="com.luoqingshang.spring5.service.UserService">
  <!-注入外部对象
  name属性：类里面属性的名字(实际上是set方法的名字去掉set)
  ref属性：创建外部对象bean标签的id值
  -->
  <property name="userDao" ref="userImpl"></property>
</bean>

<bean id="userImpl" class="com.luoqingshang.spring5.dao.UserDaoImpl"></bean>
```

##### 注入内部bean

```xml
<bean id="emp" class="com.luoqingshang.spring5.bean.Emp">
  <property name="name" value="lili"></property>
  <property name="gender" value="男"></property>
  <property name="dept">
    <bean id="dept" class="com.luoqingshang.spring5.bean.Dept">
      <property name="deptName" value="财务"></property>
    </bean>
  </property>
</bean>

<!--用注入外部bean的方式也可以-->
<bean id="emp" class="com.luoqingshang.spring5.bean.Emp">
  <property name="name" value="lili"></property>
  <property name="gender" value="男"></property>
  <property name="dept" ref="dept"></property>
</bean>
<bean id="dept" class="com.luoqingshang.spring5.bean.Dept">
  <property name="deptName" value="财务"></property>
</bean>
```

##### 级联赋值

```xml
<bean id="emp" class="com.luoqingshang.spring5.bean.Emp">
  <property name="name" value="lili"></property>
  <property name="gender" value="男"></property>
  <property name="dept" ref="dept"></property>
</bean>
<bean id="dept" class="com.luoqingshang.spring5.bean.Dept">
  <property name="deptName" value="财务"></property>
</bean>
<!--或者-->
<bean id="emp" class="com.luoqingshang.spring5.bean.Emp">
  <property name="name" value="lili"></property>
  <property name="gender" value="男"></property>
  <property name="dept" ref="dept"></property>
  <property name="dept.deptName" value="财务"></property>
</bean>
<bean id="dept" class="com.luoqingshang.spring5.bean.Dept">
</bean>
```

##### 注入集合属性

```xml
<bean id="stu" class="com.luoqingshang.spring5.bean.Stu">
  <property name="arrays">
    <array>
      <value>arrays1</value>
      <value>arrays2</value>
      <value>arrays3</value>
    </array>
  </property>
  <property name="list" >
    <list>
      <value>list1</value>
      <value>list2</value>
      <value>list3</value>
    </list>
  </property>
  <property name="maps">
    <map>
      <entry key="key1" value="value1"></entry>
      <entry key="key2" value="value2"></entry>
      <entry key="key3" value="value3"></entry>
    </map>
  </property>
  <property name="set">
    <set>
      <value>set1</value>
      <value>set2</value>
      <value>set1</value>
    </set>
  </property>
</bean>
//list和array可以互换
```

##### 注入集合对象

```xml
<bean id="shop" class="com.luoqingshang.spring5.bean.Shop">
  <property name="goodsList">
    <list>
      <bean id="goods1" class="com.luoqingshang.spring5.bean.Goods">
        <property name="name" value="goods1"></property>
      </bean>//第一种注入方式
      <ref bean="goods2"></ref>//第二种注入方式
    </list>
  </property>
</bean>

<bean id="goods2" class="com.luoqingshang.spring5.bean.Goods">
  <property name="name" value="goods2"></property>
</bean>
```

##### 集合注入部分提取

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:util="http://www.springframework.org/schema/util"//增加名称空间util
       xsi:schemaLocation="http://www.springframework.org/schema/beans 		http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util.xsd">//增加名称空间的依赖

  <util:list id="drinks">
    <value>name1</value>
    <value>name2</value>
    <value>name3</value>
  </util:list>

  <bean id="drink" class="com.luoqingshang.spring5.bean.Drink">
    <property name="name" ref="drinks"></property>
  </bean>
```

#### String中Bean的类型

普通Bean：在配置文件中定义bean的类型就是返回类型

工厂Bean：在配置文件中定义的bean类型可以和返回类型不一样

创建工厂，实现接口里面的方法，在实现的方法中定义返回的bean类型

```java
package com.luoqingshang.spring5.factoryBean;

import com.luoqingshang.spring5.bean.Goods;
import org.springframework.beans.factory.FactoryBean;


public class GoodsFactory implements FactoryBean<Goods> {
    @Override
    public Goods getObject() throws Exception {
        Goods goods=new Goods();
        goods.setName("goods");
        return goods;
    }

    @Override
    public Class<?> getObjectType() {
        return null;
    }

    @Override
    public boolean isSingleton() {
        return false;
    }
}
```

xml

```xml
<bean id="goodsFactory" class="com.luoqingshang.spring5.factoryBean.GoodsFactory"></bean>
```

测试

```java
@Test
public void test8(){
    ApplicationContext context=new ClassPathXmlApplicationContext("bean7.xml");
    Goods goodsFactory = context.getBean("goodsFactory", Goods.class);
    System.out.println(goodsFactory.getName());
}
```

#### bean的作用域

1.在spring里，默认情况下，bean是单实例对象

2.如何设置单实例还是多实例

（1）在spring配置文件bean标签里面有属性（scope）用于设置单实例还是多实例

（2）scope属性值

​	singleton，表示单实例对象，默认值

​	prototype，表示多实例对象

3.singleton与prototype区别

（1）singleton是单实例，prototype是多实例

（2）设置scope值是singleton的时候，加载spring配置文件的时候就会创建单实例对象

​		  设置scope值是prototype的时候，在调用getBean方法的时候才会创建多个实例对象

#### bean的生命周期

1.通过构造器创建bean实例（无参构造）

2.为bean的属性设置值和对其他bean引用（调用set方法）

3.调用bean的初始化的方法（需要进行配置初始化的方法）

4.bean可以使用（bean对象获取到了）

5.当容器关闭时候，调用bean的销毁方法（需要进行配置销毁的方法）

```java
package com.luoqingshang.spring5.bean;

public class Orders {

    private String oname;

    public Orders() {
        System.out.println("1.通过无参构造器创建bean实例");
    }

    public String getOname() {
        return oname;
    }

    public void setOname(String oname) {
        this.oname = oname;
        System.out.println("2.为bean的属性设置值和对其他bean引用");
    }

    private void init(){
        System.out.println("3.调用bean的初始化的方法");
    }

    private void destroy(){
        System.out.println("5.当容器关闭时候，调用bean的销毁方法");
    }


}

```

```xml
<bean id="orders" class="com.luoqingshang.spring5.bean.Orders" init-method="init" destroy-method="destroy">
    <property name="oname" value="lll"></property>
</bean>
```

```java
@Test
public void test9(){
    ApplicationContext context=new ClassPathXmlApplicationContext("bean8.xml");
    Orders orders = context.getBean("orders", Orders.class);
    System.out.println("4.bean可以使用");
    ((ClassPathXmlApplicationContext) context).close();
}
```

加上bean的后置处理器，有七步

1.通过构造器创建bean实例（无参构造）

2.为bean的属性设置值和对其他bean引用（调用set方法）

3.把bean实例传递给bean的后置处理器的方法

4.调用bean的初始化的方法（需要进行配置初始化的方法）

5.把bean实例传递给bean的后置处理器的方法

6.bean可以使用（bean对象获取到了）

7.当容器关闭时候，调用bean的销毁方法（需要进行配置销毁的方法）

后置处理器

```java
package com.luoqingshang.spring5.bean;

import org.springframework.beans.BeansException;
import org.springframework.beans.factory.config.BeanPostProcessor;

public class MyBeanPost implements BeanPostProcessor {

    @Override
    public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
        System.out.println("初始化之前执行的postProcessBeforeInitialization");
        return bean;
    }

    @Override
    public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
        System.out.println("初始化之后执行的postProcessAfterInitialization");
        return bean;
    }
}
```

配置后置处理器

```xml
<bean id="myBeanPost" class="com.luoqingshang.spring5.bean.MyBeanPost"></bean>
//会为所有当前xml的bean添加后置处理器
```

#### bean的自动装配

使用property为手动装配

根据指定装配规则（属性名称或者属性类型），Spring自动将匹配的属性值进行注入

根据装配规则——属性名称

注入bean的id值必须与类属性名称一致

```xml
<bean id="emp" class="com.luoqingshang.spring5.autowire.Emp" autowire="byName"></bean>

<bean id="dept" class="com.luoqingshang.spring5.autowire.Dept"></bean>
```

根据装配规则——属性类型

不可以同时有多个一样的类型

```xml
<bean id="emp" class="com.luoqingshang.spring5.autowire.Emp" autowire="byType"></bean>

<bean id="dept" class="com.luoqingshang.spring5.autowire.Dept"></bean>

<!--<bean id="dept1" class="com.luoqingshang.spring5.autowire.Dept"></bean>-->
```

#### 引入外部文件

1先引入context空间

2用context标签将需要引入的文件加载进来

3用${}引用文件中的值

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

    <context:property-placeholder location="classpath:jdbc.properties"/>

    <bean id="druid" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="driverClassName" value="${prop.driverClassName}"></property>
        <property name="url" value="${prop.url}"></property>
        <property name="username" value="${prop.username}"></property>
        <property name="password" value="${prop.password}"></property>
    </bean>

</beans>
```

### 基于注解方式实现

第一步，多引入一个aop依赖

第二步，开启组件扫描

如果有多个包，就用,隔开或者扫描上层目录

```xml
<context:component-scan base-package="com.luoqingshang.spring5"/>
<!--<context:component-scan base-package="com.luoqingshang.spring5.dao,com.luoqingshang.spring5.service"/>-->
```

第三步，使用注解

注意⚠️：注解里的value值可以省略不写，默认值是类名称的首字母小写

#### 组件扫描详解

创建bean的注解有

```java
@Component
@Controller
@Service
@Repository
```

```xml
<context:component-scan base-package="com.luoqingshang.spring5" use-default-filters="false">//use-default-filters关闭全部扫描，改为只扫描自己配置的扫描规则
    <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
  //include-filter 表示包含的规则 type="annotation"表示扫描注解。expression="org.springframework.stereotype.Controller"表示只有注解为Controller才会被扫描
  //扫描内容  扫描类型  扫描地址
</context:component-scan>
```

注入属性的注解有

```java
@Autowired//根据类型进行注入
@Qualifier//根据名称进行注入，需要与@Autowired配合使用，如果该类型被多个实现类所实现，根据类型注入无法知道该注入哪个则使用@Qualifier辅助注入，value值填入实现的类的名称
/**例如
*@Autowired
*@Qualifier(value = "userDaoImpl")
*private UserDao userDao;
*/
@Resource//不加括号相当于@Autowired，加了括号并给name传值则相当于@Autowired+@Qualifier
@Value//只注入基本属性的值
```

#### 完全注解配置类

用配置类替换xml文件

```java
package com.luoqingshang.spring5.config;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

@Configuration
@ComponentScan(basePackages = {"com.luoqingshang.spring5"})
public class SpringConfig {
}
```

```java
ApplicationContext context=new AnnotationConfigApplicationContext(SpringConfig.class);//读取配置文件代替ClassPathXmlApplicationContext读取xml
```

## AOP

### 什么是aop

面向切面编程（方面），利用AOP可以对业务逻辑的各个部分进行隔离，从而使得业务逻辑各部分之间的耦合度降低，提高程序的可重用性，同时提高了开发的效率

通俗描述：不通过修改源代码方式，在主干功能里面添加新功能

### aop的术语

连接点：类中哪些方法可以被增强，这些方法就称为连接点，类中有四个方法可以被增强就有四个连接点

切入点：实际被真正增强的方法，称为切入点

通知（增强）：实际增强中的逻辑部分称为通知（增强），实际就是新增加的部分代码

| 通知类型 |
| -------- |
| 前置通知 |
| 后置通知 |
| 环绕通知 |
| 异常通知 |
| 最终通知 |

切面：把通知应用到切入点的过程

### aop的底层原理

有两种动态代理

有接口，使用JDK动态代理

```
创建接口实现类代理对象，增强类的方法
```

jdk实现动态代理

```java
package com.luoqingshang.spring5;

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;
import java.util.Arrays;

public class JDKProxy {

    public static void main(String[] args) {
      //被代理的类对象
        Class[] classes={UserDao.class};
        UserDaoImpl userDao=new UserDaoImpl();
        UserDao dao = (UserDao)Proxy.newProxyInstance(JDKProxy.class.getClassLoader(), classes, new UserDaoProxy(userDao));
        int result = dao.add(1, 2);//使用动态代理后调用加强方法
        System.out.println(result);

    }
}

//创建代理对象
class UserDaoProxy implements InvocationHandler{

    private UserDaoImpl userDao;

    public UserDaoProxy(UserDaoImpl userDao){
        this.userDao=userDao;
    }

    @Override
  //增强的逻辑
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        System.out.println("执行之前"+Arrays.toString(args));
      //被增强的代码被执行
        int res = (int)method.invoke(userDao, args);
        System.out.println("执行之后"+"----"+method.getName());
        return res;
    }
}
```

没有接口，使用CGLIB动态代理

```
创建子类的代理对象，增强类的方法
```

## AOP操作

### 准备

Spring框架一般都是基于AspectJ实现AOP操作

AspectJ不是Spring组成部分，是一个独立的AOP框架，一般把AspectJ和Spring框架一起使用，进行AOP操作

#### 切入点表达式

切入点表达式的作用：知道对哪个类里面的哪个方法进行增强

语法结构

execution([][][权限修饰符] [返回类型] [类全路径] [方法名称] ([参数列表]))

```java
execution(* com.luoqingshang.spring5.UserDao.add(..))//*表示任意权限修饰符，返回类型可以不写
execution(* com.luoqingshang.spring5.UserDao.*(..))//表示对类里所有方法增强
execution(* com.luoqingshang.spring5.*.*(..)) //对com.luoqingshang.spring5包里的所有类的所有方法进行增强
```



### 基于AspectJ实现AOP操作（基于xml配置文件实现）

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd">

    <!--创建bean对象-->
    <bean id="book" class="com.luoqingshang.spring5.aopxml.Book"></bean>
    <bean id="bookProxy" class="com.luoqingshang.spring5.aopxml.BookProxy"></bean>


    <!--配置aop增强-->
    <aop:config>
        <!--配置切入点-->
        <aop:pointcut id="p" expression="execution(* com.luoqingshang.spring5.aopxml.Book.buy(..))"></aop:pointcut>
        <!--配置切面-->
        <aop:aspect ref="bookProxy">
            <!--配置通知-->
            <aop:before method="before" pointcut-ref="p"></aop:before>
        </aop:aspect>
    </aop:config>

</beans>
```

### 基于AspectJ实现AOP操作（基于注解方式实现）

先开启注解扫描和aop扫描(引入context和aop空间)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
                           http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd">

  	<!--开启注解扫描-->
    <context:component-scan base-package="com.luoqingshang.spring5.aopanno"/>
		<!--开启aspectj生成代理对象-->
    <aop:aspectj-autoproxy/>
</beans>
```

被增强的类

```java
package com.luoqingshang.spring5.aopanno;

import org.springframework.stereotype.Component;

@Component
public class User {

    public void add(){
        System.out.println("add ....");
    }
}
```

增强的类

```java
package com.luoqingshang.spring5.aopanno;

import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.*;
import org.springframework.stereotype.Component;

@Component
@Aspect//生成代理对象
public class UserProxy {
    //前置通知（使用切入点表达式配置）
    @Before("execution(* com.luoqingshang.spring5.aopanno.User.add(..))")
    public void before(){
        System.out.println("before...");
    }
    //后置通知（使用切入点表达式配置）返回结果后执行
    @AfterReturning("execution(* com.luoqingshang.spring5.aopanno.User.add(..))")
    public void afterReturning(){
        System.out.println("afterReturning...");
    }
    //异常通知（使用切入点表达式配置）遇到异常执行
    @AfterThrowing("execution(* com.luoqingshang.spring5.aopanno.User.add(..))")
    public void afterThrowing(){
        System.out.println("afterThrowing...");
    }
    //最终通知（使用切入点表达式配置）
    @After("execution(* com.luoqingshang.spring5.aopanno.User.add(..))")
    public void after(){
        System.out.println("after...");
    }
    //环绕通知（使用切入点表达式配置）如果遇到异常环绕通知的后半段不执行，相当于Before+AfterReturning
    @Around("execution(* com.luoqingshang.spring5.aopanno.User.add(..))")
    public void around(ProceedingJoinPoint proceedingJoinPoint) throws Throwable{
        System.out.println("around...之前");
        proceedingJoinPoint.proceed();
        System.out.println("around...之后");
    }
}
```

测试

```java
@Test
public void test1(){
    ApplicationContext context=new ClassPathXmlApplicationContext("bean1.xml");
    User user = context.getBean("user", User.class);
    user.add();
}
```

#### 抽取相同的切入点

```java
package com.luoqingshang.spring5.aopanno;

import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.*;
import org.springframework.stereotype.Component;

@Component
@Aspect//生成代理对象
public class UserProxy {

    @Pointcut(value = "execution(* com.luoqingshang.spring5.aopanno.User.add(..))")
    public void pointCut(){

    }

    //前置通知（使用切入点表达式配置）
    @Before("pointCut()")
    public void before(){
        System.out.println("before...");
    }
    //后置通知（使用切入点表达式配置）返回结果后执行
    @AfterReturning("pointCut()")
    public void afterReturning(){
        System.out.println("afterReturning...");
    }
    //异常通知（使用切入点表达式配置）
    @AfterThrowing("pointCut()")
    public void afterThrowing(){
        System.out.println("afterThrowing...");
    }
    //最终通知（使用切入点表达式配置）
    @After("pointCut()")
    public void after(){
        System.out.println("after...");
    }
    //环绕通知（使用切入点表达式配置）
    @Around("pointCut()")
    public void around(ProceedingJoinPoint proceedingJoinPoint) throws Throwable{
        System.out.println("around...之前");
        proceedingJoinPoint.proceed();
        System.out.println("around...之后");
    }
}
```

有多个增强类对同一个方法进行增强，可以设置增强类的优先级

```
在类上加@Order注解，数字越小，优先级越高，只在类上生效
```

#### 完全注解的配置类

```java
package com.luoqingshang.spring5.config;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.EnableAspectJAutoProxy;

@Configuration
@ComponentScan(basePackages = {"com.luoqingshang.spring5"})//开启组件扫描
@EnableAspectJAutoProxy(proxyTargetClass = true)//开启aspectj生成代理对象
public class AopConfig {
}
```

## JdbcTemplate

Mysql5.+的driverClassName是com.mysql.jdbc.Driver

Mysql8.+的driverClassName是com.mysql.cj.jdbc.Driver

配置文件

```properties
prop.driverClassName=com.mysql.jdbc.Driver
prop.url=jdbc:mysql://127.0.0.1:3307/test?useUnicode=true&characterEncoding=utf8&useSSL=false&useLegacyDatetimeCode=false&serverTimezone=Asia/Shanghai&allowPublicKeyRetrieval=true
prop.username=root
prop.password=gqs123456
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

    <context:component-scan base-package="com.luoqingshang.spring5"/>

    <context:property-placeholder location="classpath:jdbc.properties"/>

    <!--配置数据源信息-->
    <bean id="druid" class="com.alibaba.druid.pool.DruidDataSource" destroy-method="close">
        <property name="driverClassName" value="${prop.driverClassName}"></property>
        <property name="url" value="${prop.url}"></property>
        <property name="username" value="${prop.username}"></property>
        <property name="password" value="${prop.password}"></property>
    </bean>

    <!--配置JdbcTemple对象，注入数据源-->
    <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
        <property name="dataSource" ref="druid"></property>
    </bean>

</beans>
```

调用jdbcTemplate

```java
package com.luoqingshang.spring5.dao;

import com.luoqingshang.spring5.bean.Book;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.stereotype.Repository;

import javax.annotation.Resource;

@Repository
public class BookDaoImpl implements BookDao{

    @Resource
    private JdbcTemplate jdbcTemplate;

    @Override
    public void add(Book book) {
        String sql="insert into t_book (name,bstate) values(?,?)";
        Object[] orgs={ book.getName(), book.getState()};
        int update = jdbcTemplate.update(sql,orgs);
//        int update = jdbcTemplate.update(sql,book.getName(), book.getState();
        System.out.println(update);

    }
}
```

### 查询返回某个值

```java
public int findCount() {
    String sql="select count(1) from t_book";
    Integer integer = jdbcTemplate.queryForObject(sql, Integer.class);
    return integer;
}
```

### 查询返回对象

```java
public Book findOne(int id) {
    String sql="select * from t_book where id=?";
    Book book = jdbcTemplate.queryForObject(sql, new BeanPropertyRowMapper<Book>(Book.class), id);
    return book;
}
```

### 查询返回集合

```java
public List<Book> findAll() {
    String sql="select * from t_book";
    List<Book> query = jdbcTemplate.query(sql, new BeanPropertyRowMapper<>(Book.class));
    return query;
}
```

### 批量操作（增加、修改、删除）

```java
public void batchAdd(List<Book> books) {
    String sql="insert into t_book (name,bstate) values(?,?)";
    List<Object[]> args=new ArrayList<>();
    for(Book b:books){
        Object[] arg={b.getName(),b.getBstate()};
        args.add(arg);
    }
    jdbcTemplate.batchUpdate(sql,args);
}
```

## 事务

事务添加到javaee三层结构里的service层（业务逻辑层）

### 在Spring进行事务管理操作有两种方式

#### 1编程式事务管理



#### 2声明式事务管理（使用）

底层使用AOP原理

##### 基于注解方式

先创建事务管理器，然后引入tx管理空间，然后开启事务注解引入事务管理器

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
                           http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd">

    <!--开启注解-->
    <context:component-scan base-package="com.luoqingshang.spring5"/>


    <!--引入数据源信息的值-->
    <context:property-placeholder location="classpath:jdbc.properties"/>

    <!--配置数据源信息-->
    <bean id="druid" class="com.alibaba.druid.pool.DruidDataSource" destroy-method="close">
        <property name="driverClassName" value="${prop.driverClassName}"></property>
        <property name="url" value="${prop.url}"></property>
        <property name="username" value="${prop.username}"></property>
        <property name="password" value="${prop.password}"></property>
    </bean>

    <!--配置JdbcTemple对象，注入数据源-->
    <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
        <property name="dataSource" ref="druid"></property>
    </bean>

    <!--创建事务管理器-->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="druid"></property>
    </bean>

    <!--开启事务的注解-->
    <tx:annotation-driven transaction-manager="transactionManager"></tx:annotation-driven>

</beans>
```

在类上面或者方法上面加@Transactional注解

```java
package com.luoqingshang.spring5.service;

import com.luoqingshang.spring5.dao.UserDao;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

@Service
@Transactional
public class UserService {

    @Autowired
    private UserDao userDao;

    public void sendMoney(){


            userDao.reduceMoney(100.0,"lucy");
            int i=10/0;//模拟异常
            userDao.addMoney(100.0,"marry");


    }
}
```

###### @Transactional的参数配置

1.propagation

事务传播行为

前提条件：有两个方法A和方法B

方法A中调用了方法B

| 传播属性     | 描述                                                         | 通俗说法                                                     |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| REQUIRED     | 如果有事务在运行，当前的方法就在这个事务内运行，否则，就启动一个新的事务，并在自己的事务内运行 | 如果A有事务，则B调用A中的事务，如果A中没有事务则B创建一个新的事务 |
| REQUIRED_NEW | 当前的方法必须启动新事务，并在它自己的事务内运行，如果有事务正在运行，应该将它挂起 | 无论方法A中是否有事务，都将创建新的事务                      |
| SUPPORTS     | 如果有事务在运行，当前的方法就在这个事务内运行，否则它可以不运行在事务中 |                                                              |
| NOT_SUPPORTE | 当前的方法不应该运行在事务中，如果有运行的事务，将它挂起     |                                                              |
| MANDATORY    | 当前的方法必须运行在事务内部，如果没有正在运行的事务，就抛异常 |                                                              |
| NEVER        | 当前的方法不应该运行在事务中，如果有运行的事务，就抛异常     |                                                              |
| NESTED       | 如果有事务在运行，当前的方法就应该在这个事务的嵌套事务内运行，否则，就启动一个新的事务，并在它自己的事务内运行 |                                                              |

```java
@Transactional(propagation = Propagation.REQUIRED)
```

2.isolation

为了不发生脏读，不可重复读和幻读的情况

```java
@Transactional(isolation = Isolation.REPEATABLE_READ)
```

3.timeout

事务需要在一定时间内进行提交，如果不提交进行回滚

默认值是-1，设置时间以秒单位进行计算

```java
@Transactional(timeout = -1)
```

4.readOnly

读：查询操作 写：添加修改及删除操作

```java
@Transactional(readOnly = false)
```

readOnly默认值为false 表示可以增删改查

设置readOnly值是true，表示只可以查询

5.rollbackFor

设置出现哪些异常进行回滚

```java
@Transactional(rollbackFor = {RuntimeException.class})
```

6.noRollbackFor

设置出现哪些异常不进行回滚

```java
@Transactional(noRollbackFor = {RuntimeException.class})
```

##### 基于xml配置文件方式

在spring配置文件中进行配置

第一步配置事务管理器

第二步配置通知

第三步配置切入点和切面

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:tx="http://www.springframework.org/schema/tx" xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
                           http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd http://www.springframework.org/schema/aop https://www.springframework.org/schema/aop/spring-aop.xsd">

    <!--开启注解-->
    <context:component-scan base-package="com.luoqingshang.spring5"/>


    <!--引入数据源信息的值-->
    <context:property-placeholder location="classpath:jdbc.properties"/>

    <!--配置数据源信息-->
    <bean id="druid" class="com.alibaba.druid.pool.DruidDataSource" destroy-method="close">
        <property name="driverClassName" value="${prop.driverClassName}"></property>
        <property name="url" value="${prop.url}"></property>
        <property name="username" value="${prop.username}"></property>
        <property name="password" value="${prop.password}"></property>
    </bean>

    <!--配置JdbcTemple对象，注入数据源-->
    <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
        <property name="dataSource" ref="druid"></property>
    </bean>

    <!--创建事务管理器-->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="druid"></property>
    </bean>

    <!--配置通知-->
   <tx:advice id="txadvice">
       <tx:attributes>
           <tx:method name="sendMoney" propagation="REQUIRED"/><!--直接写要加事务的方法名-->
           <!--<tx:method name="send*"/>--><!--配置符合规则的方法名-->
       </tx:attributes>
   </tx:advice>

    <!--配置切入点和切面-->
    <aop:config>
        <!--配置切入点-->
        <aop:pointcut id="pt" expression="execution(* com.luoqingshang.spring5.service.UserService.*(..))"></aop:pointcut>
        <!--配置切面-->
        <aop:advisor advice-ref="txadvice" pointcut-ref="pt"></aop:advisor>
    </aop:config>
</beans>
```

##### 完全注解的配置类

```java
package com.luoqingshang.spring5.config;

import com.alibaba.druid.pool.DruidDataSource;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.jdbc.datasource.DataSourceTransactionManager;
import org.springframework.transaction.annotation.EnableTransactionManagement;

import javax.sql.DataSource;

@ComponentScan(basePackages = "com.luoqingshang.spring5")//开启组件扫描
@EnableTransactionManagement//开启事务
public class TxConfig {

    //创建数据库连接池
    @Bean
    public DruidDataSource getDruidDataSource(){
        DruidDataSource dataSource=new DruidDataSource();
        dataSource.setUrl("jdbc:mysql://127.0.0.1:3307/test?useUnicode=true&characterEncoding=utf8&useSSL=false&useLegacyDatetimeCode=false&serverTimezone=Asia/Shanghai&allowPublicKeyRetrieval=true");
        dataSource.setDriverClassName("com.mysql.jdbc.Driver");
        dataSource.setUsername(@Value(""));
        dataSource.setPassword("gqs123456");
        return dataSource;
    }

    //创建JdbcTemplate对象
    @Bean
    public JdbcTemplate getJdbcTemplate(DataSource dataSource){//在ioc容器中已经存在dataSource对象，根据类型到ioc容器中找到对象作注入
        JdbcTemplate jdbcTemplate=new JdbcTemplate();
        jdbcTemplate.setDataSource(dataSource);
        return jdbcTemplate;
    }

    //创建事务管理器
    @Bean
    public DataSourceTransactionManager getDataSourceTransactionManager(DataSource dataSource){
        DataSourceTransactionManager dataSourceTransactionManager=new DataSourceTransactionManager();
        dataSourceTransactionManager.setDataSource(dataSource);
        return dataSourceTransactionManager;
    }


}
```

