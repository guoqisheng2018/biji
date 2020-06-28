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

### 1.基于xml配置文件方式实现

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
<bean id="book" class="com.luoqingshang.spring5.Book">
  <property name="name" value="bookname"/>
  <property name="auther" value="bookauther"/>
</bean>//无参构造，用set方法进行设置参数
//简化版，p名称空间注入
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"//增加p空间
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
  <bean id="book" class="com.luoqingshang.spring5.Book" p:bname-="xxx" p:bauthor="aaa">
  </bean>//使用p空间给属性赋值
</beans>//有参构造
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

### 2.基于注解方式实现

第一步，多引入一个aop依赖

第二步，开启组件扫描

如果有多个包，就用,隔开或者扫描上层目录

```xml
<context:component-scan base-package="com.luoqingshang.spring5"/>
<!--<context:component-scan base-package="com.luoqingshang.spring5.dao,com.luoqingshang.spring5.service"/>-->
```

第三步，使用注解

注意⚠️：注解里的value值可以省略不写，默认值是类名称的首字母小写