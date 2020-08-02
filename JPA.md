# JPA

## 实现流程

![](/Users/guoqisheng/Desktop/笔记/biji/jpa/WeChatd679a89f682ffc24afe4abfe08b81d8c.png)

## JPA规范

### 引入基本依赖

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <project.hibernate.version>5.0.7.Final</project.hibernate.version>
</properties>

<dependencies>
    <!-- junit -->
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.12</version>
        <scope>test</scope>
    </dependency>

    <!-- hibernate对jpa的支持包 -->
    <dependency>
        <groupId>org.hibernate</groupId>
        <artifactId>hibernate-entitymanager</artifactId>
        <version>${project.hibernate.version}</version>
    </dependency>

    <!-- c3p0 -->
    <dependency>
        <groupId>org.hibernate</groupId>
        <artifactId>hibernate-c3p0</artifactId>
        <version>${project.hibernate.version}</version>
    </dependency>

    <!-- log日志 -->
    <dependency>
        <groupId>log4j</groupId>
        <artifactId>log4j</artifactId>
        <version>1.2.17</version>
    </dependency>

    <!-- Mysql and MariaDB -->
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>5.1.6</version>
    </dependency>
</dependencies>
```

### 配置jpa的核心配置文件

位置：配置到类路径下的一个叫做 META-INF 的文件夹下
命名：persistence.xml

找寻模版：在文件包上右键->new-> edit file Templates

![](/Users/guoqisheng/Desktop/笔记/biji/jpa/WeChatdb720c54615e89d9ffb78e0c424672d0.png)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<persistence xmlns="http://java.sun.com/xml/ns/persistence" version="2.0">
    <!--需要配置persistence-unit节点
        持久化单元：
            name：持久化单元名称
            transaction-type：事务管理的方式
                    JTA：分布式事务管理
                    RESOURCE_LOCAL：本地事务管理
    -->
    <persistence-unit name="myJpa" transaction-type="RESOURCE_LOCAL">
        <!--jpa的实现方式 -->
        <provider>org.hibernate.jpa.HibernatePersistenceProvider</provider>

        <!--可选配置：配置jpa实现方的配置信息-->
        <properties>
            <!-- 数据库信息
                用户名，javax.persistence.jdbc.user
                密码，  javax.persistence.jdbc.password
                驱动，  javax.persistence.jdbc.driver
                数据库地址   javax.persistence.jdbc.url
            -->
            <property name="javax.persistence.jdbc.user" value="root"/>
            <property name="javax.persistence.jdbc.password" value="gqs123456"/>
            <property name="javax.persistence.jdbc.driver" value="com.mysql.jdbc.Driver"/>
            <property name="javax.persistence.jdbc.url" value="jdbc:mysql://127.0.0.1:3307/test?useUnicode=true&amp;characterEncoding=utf8&amp;useSSL=false&amp;useLegacyDatetimeCode=false&amp;serverTimezone=Asia/Shanghai&amp;allowPublicKeyRetrieval=true"/>

            <!--配置jpa实现方(hibernate)的配置信息
                显示sql           ：   false|true
                自动创建数据库表    ：  hibernate.hbm2ddl.auto
                        create      : 程序运行时创建数据库表（如果有表，先删除表再创建）
                        update      ：程序运行时创建表（如果有表，不会创建表）
                        none        ：不会创建表

            -->
            <property name="hibernate.show_sql" value="true" />
            <property name="hibernate.hbm2ddl.auto" value="update" />
        </properties>
    </persistence-unit>
</persistence>
```

### 编写实体类

```java
package com.luoqingshang.domain;

import lombok.Data;

import javax.persistence.*;

/**
 * 客户的实体类
 *      配置映射关系
 *
 *
 *   1.实体类和表的映射关系
 *      @Entity:声明实体类
 *      @Table : 配置实体类和表的映射关系
 *          name : 配置数据库表的名称
 *   2.实体类中属性和表中字段的映射关系
 *
 *
 */
@Entity
@Table(name = "cst_customer")
@Data
public class Customer {

    /**
     * @Id：声明主键的配置
     * @GeneratedValue:配置主键的生成策略
     *      strategy
     *          GenerationType.IDENTITY ：自增，mysql
     *                 * 底层数据库必须支持自动增长（底层数据库支持的自动增长方式，对id自增）
     *          GenerationType.SEQUENCE : 序列，oracle
     *                  * 底层数据库必须支持序列
     *          GenerationType.TABLE : jpa提供的一种机制，通过一张数据库表的形式帮助我们完成主键自增
     *          GenerationType.AUTO ： 由程序自动的帮助我们选择主键生成策略
     * @Column:配置属性和字段的映射关系
     *      name：数据库表中字段的名称
     */
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "cust_id")
    private Long custId; //客户的主键

    @Column(name = "cust_name")
    private String custName;//客户名称

    @Column(name="cust_source")
    private String custSource;//客户来源

    @Column(name="cust_level")
    private String custLevel;//客户级别

    @Column(name="cust_industry")
    private String custIndustry;//客户所属行业

    @Column(name="cust_phone")
    private String custPhone;//客户的联系方式

    @Column(name="cust_address")
    private String custAddress;//客户地址

}
```

测试

```java
package com.luoqingshang.test;

import com.luoqingshang.domain.Customer;
import org.junit.Test;

import javax.persistence.EntityManager;
import javax.persistence.EntityManagerFactory;
import javax.persistence.EntityTransaction;
import javax.persistence.Persistence;

public class JpaTest {
    /**
     * 测试jpa的保存
     *      案例：保存一个客户到数据库中
     *  Jpa的操作步骤
     *     1.加载配置文件创建工厂（实体管理器工厂）对象
     *     2.通过实体管理器工厂获取实体管理器
     *     3.获取事务对象，开启事务
     *     4.完成增删改查操作
     *     5.提交事务（回滚事务）
     *     6.释放资源
     */

    @Test
    public void testSave() {
//        //1.加载配置文件创建工厂（实体管理器工厂）对象
        EntityManagerFactory factory = Persistence.createEntityManagerFactory("myJpa");
//        //2.通过实体管理器工厂获取实体管理器
        EntityManager em = factory.createEntityManager();
        //3.获取事务对象，开启事务
        EntityTransaction tx = em.getTransaction(); //获取事务对象
        tx.begin();//开启事务
        //4.完成增删改查操作：保存一个客户到数据库中
        Customer customer = new Customer();
        customer.setCustName("客户名称");
        customer.setCustIndustry("行业");
        //保存，
        em.persist(customer); //保存操作
        //5.提交事务
        tx.commit();
        //6.释放资源
        em.close();
        factory.close();
    }

}
```

### 编写工具类

```java
package com.luoqingshang.utils;

import javax.persistence.EntityManager;
import javax.persistence.EntityManagerFactory;
import javax.persistence.Persistence;

/**
 * 解决实体管理器工厂的浪费资源和耗时问题
 *      通过静态代码块的形式，当程序第一次访问此工具类时，创建一个公共的实体管理器工厂对象
 *
 * 第一次访问getEntityManager方法：经过静态代码块创建一个factory对象，再调用方法创建一个EntityManager对象
 * 第二次方法getEntityManager方法：直接通过一个已经创建好的factory对象，创建EntityManager对象
 */
public class JpaUtils {

    private static EntityManagerFactory factory;

    static {
        //1.加载配置文件，创建entityManagerFactory
        factory = Persistence.createEntityManagerFactory("myJpa");
    }

    /**
     * 获取EntityManager对象
     */
    public static EntityManager getEntityManager() {
        return factory.createEntityManager();
    }
}
```

测试

```java
package com.luoqingshang.test;

import com.luoqingshang.domain.Customer;
import com.luoqingshang.utils.JpaUtils;
import org.junit.Test;

import javax.persistence.EntityManager;
import javax.persistence.EntityManagerFactory;
import javax.persistence.EntityTransaction;
import javax.persistence.Persistence;

public class JpaTest {
    /**
     * 测试jpa的保存
     *      案例：保存一个客户到数据库中
     *  Jpa的操作步骤
     *     1.加载配置文件创建工厂（实体管理器工厂）对象
     *     2.通过实体管理器工厂获取实体管理器
     *     3.获取事务对象，开启事务
     *     4.完成增删改查操作
     *     5.提交事务（回滚事务）
     *     6.释放资源
     */

    @Test
    public void testSave() {
//        //1.加载配置文件创建工厂（实体管理器工厂）对象
//        EntityManagerFactory factory = Persistence.createEntityManagerFactory("myJpa");
//        //2.通过实体管理器工厂获取实体管理器
//        EntityManager em = factory.createEntityManager();
        EntityManager em= JpaUtils.getEntityManager();
        //3.获取事务对象，开启事务
        EntityTransaction tx = em.getTransaction(); //获取事务对象
        tx.begin();//开启事务
        //4.完成增删改查操作：保存一个客户到数据库中
        Customer customer = new Customer();
        customer.setCustName("客户名称");
        customer.setCustIndustry("行业");
        //保存，
        em.persist(customer); //保存操作
        //5.提交事务
        tx.commit();
        //6.释放资源
        em.close();
//        factory.close();为了其他线程可以使用工厂，工厂不可关闭
    }

}
```

### 普通JPA

#### find方法和Reference方法的区别

```java
/**
 * 根据id查询客户
 *  使用find方法查询：
 *      1.查询的对象就是当前客户对象本身
 *      2.在调用find方法的时候，就会发送sql语句查询数据库
 *
 *  立即加载
 *
 *
 */
@Test
public  void testFind() {
    //1.通过工具类获取entityManager
    EntityManager entityManager = JpaUtils.getEntityManager();
    //2.开启事务
    EntityTransaction tx = entityManager.getTransaction();
    tx.begin();
    //3.增删改查 -- 根据id查询客户
    /**
     * find : 根据id查询数据
     *      class：查询数据的结果需要包装的实体类类型的字节码
     *      id：查询的主键的取值
     */
    Customer customer = entityManager.find(Customer.class, 1l);
    // System.out.print(customer);
    //4.提交事务
    tx.commit();
    //5.释放资源
    entityManager.close();
}

/**
 * 根据id查询客户
 *      getReference方法
 *          1.获取的对象是一个动态代理对象
 *          2.调用getReference方法不会立即发送sql语句查询数据库
 *              * 当调用查询结果对象的时候，才会发送查询的sql语句：什么时候用，什么时候发送sql语句查询数据库
 *
 * 延迟加载（懒加载）
 *      * 得到的是一个动态代理对象
 *      * 什么时候用，什么使用才会查询
 */
@Test
public  void testReference() {
    //1.通过工具类获取entityManager
    EntityManager entityManager = JpaUtils.getEntityManager();
    //2.开启事务
    EntityTransaction tx = entityManager.getTransaction();
    tx.begin();
    //3.增删改查 -- 根据id查询客户
    /**
     * getReference : 根据id查询数据
     *      class：查询数据的结果需要包装的实体类类型的字节码
     *      id：查询的主键的取值
     */
    Customer customer = entityManager.getReference(Customer.class, 1l);
    System.out.print(customer);
    //4.提交事务
    tx.commit();
    //5.释放资源
    entityManager.close();
}
```

#### 删除方法

```java
/**
 * 删除客户的案例
 *
 */
@Test
public  void testRemove() {
    //1.通过工具类获取entityManager
    EntityManager entityManager = JpaUtils.getEntityManager();
    //2.开启事务
    EntityTransaction tx = entityManager.getTransaction();
    tx.begin();
    //3.增删改查 -- 删除客户

    //i 根据id查询客户
    Customer customer = entityManager.find(Customer.class,1l);
    //ii 调用remove方法完成删除操作
    entityManager.remove(customer);

    //4.提交事务
    tx.commit();
    //5.释放资源
    entityManager.close();
}
```

#### 修改方法

```java
/**
 * 更新客户的操作
 *      merge(Object)
 */
@Test
public  void testUpdate() {
    //1.通过工具类获取entityManager
    EntityManager entityManager = JpaUtils.getEntityManager();
    //2.开启事务
    EntityTransaction tx = entityManager.getTransaction();
    tx.begin();
    //3.增删改查 -- 更新操作

    //i 查询客户
    Customer customer = entityManager.find(Customer.class,2l);
    //ii 更新客户
    customer.setCustIndustry("it教育");
    entityManager.merge(customer);

    //4.提交事务
    tx.commit();
    //5.释放资源
    entityManager.close();
}
```

### JPQL

#### 查询全部

```java
/**
 * 查询全部
 *      jqpl：from cn.itcast.domain.Customer
 *      sql：SELECT * FROM cst_customer
 */
@Test
public void testFindAll() {
    //1.获取entityManager对象
    EntityManager em = JpaUtils.getEntityManager();
    //2.开启事务
    EntityTransaction tx = em.getTransaction();
    tx.begin();
    //3.查询全部
    String jpql = "from Customer ";
    Query query = em.createQuery(jpql);//创建Query查询对象，query对象才是执行jqpl的对象

    //发送查询，并封装结果集
    List list = query.getResultList();

    for (Object obj : list) {
        System.out.println(obj);
    }

    //4.提交事务
    tx.commit();
    //5.释放资源
    em.close();
}
```

#### 排序查询

```java
/**
 * 排序查询： 倒序查询全部客户（根据id倒序）
 *      sql：SELECT * FROM cst_customer ORDER BY cust_id DESC
 *      jpql：from Customer order by custId desc
 *
 * 进行jpql查询
 *      1.创建query查询对象
 *      2.对参数进行赋值
 *      3.查询，并得到返回结果
 */
@Test
public void testOrders() {
    //1.获取entityManager对象
    EntityManager em = JpaUtils.getEntityManager();
    //2.开启事务
    EntityTransaction tx = em.getTransaction();
    tx.begin();
    //3.查询全部
    String jpql = "from Customer order by custId desc";
    Query query = em.createQuery(jpql);//创建Query查询对象，query对象才是执行jqpl的对象

    //发送查询，并封装结果集
    List list = query.getResultList();

    for (Object obj : list) {
        System.out.println(obj);
    }

    //4.提交事务
    tx.commit();
    //5.释放资源
    em.close();
}
```

#### 统计总数

```java
/**
 * 使用jpql查询，统计客户的总数
 *      sql：SELECT COUNT(cust_id) FROM cst_customer
 *      jpql：select count(custId) from Customer
 */
@Test
public void testCount() {
    //1.获取entityManager对象
    EntityManager em = JpaUtils.getEntityManager();
    //2.开启事务
    EntityTransaction tx = em.getTransaction();
    tx.begin();
    //3.查询全部
    //i.根据jpql语句创建Query查询对象
    String jpql = "select count(custId) from Customer";
    Query query = em.createQuery(jpql);
    //ii.对参数赋值
    //iii.发送查询，并封装结果

    /**
     * getResultList ： 直接将查询结果封装为list集合
     * getSingleResult : 得到唯一的结果集
     */
    Object result = query.getSingleResult();

    System.out.println(result);

    //4.提交事务
    tx.commit();
    //5.释放资源
    em.close();
}
```

#### 分页查询

```java
/**
 * 分页查询
 *      sql：select * from cst_customer limit 0,2
 *      jqpl : from Customer
 */
@Test
public void testPaged() {
    //1.获取entityManager对象
    EntityManager em = JpaUtils.getEntityManager();
    //2.开启事务
    EntityTransaction tx = em.getTransaction();
    tx.begin();
    //3.查询全部
    //i.根据jpql语句创建Query查询对象
    String jpql = "from Customer";
    Query query = em.createQuery(jpql);
    //ii.对参数赋值 -- 分页参数
    //起始索引
    query.setFirstResult(0);
    //每页查询的条数
    query.setMaxResults(2);

    //iii.发送查询，并封装结果

    /**
     * getResultList ： 直接将查询结果封装为list集合
     * getSingleResult : 得到唯一的结果集
     */
    List list = query.getResultList();

    for(Object obj : list) {
        System.out.println(obj);
    }

    //4.提交事务
    tx.commit();
    //5.释放资源
    em.close();
}
```

#### 条件查询

```java
/**
 * 条件查询
 *     案例：查询客户名称以‘客户名称’开头的客户
 *          sql：SELECT * FROM cst_customer WHERE cust_name LIKE  ?
 *          jpql : from Customer where custName like ?
 */
@Test
public void testCondition() {
    //1.获取entityManager对象
    EntityManager em = JpaUtils.getEntityManager();
    //2.开启事务
    EntityTransaction tx = em.getTransaction();
    tx.begin();
    //3.查询全部
    //i.根据jpql语句创建Query查询对象
    String jpql = "from Customer where custName like ? ";
    Query query = em.createQuery(jpql);
    //ii.对参数赋值 -- 占位符参数
    //第一个参数：占位符的索引位置（从1开始），第二个参数：取值
    query.setParameter(1,"客户名称%");

    //iii.发送查询，并封装结果

    /**
     * getResultList ： 直接将查询结果封装为list集合
     * getSingleResult : 得到唯一的结果集
     */
    List list = query.getResultList();

    for(Object obj : list) {
        System.out.println(obj);
    }

    //4.提交事务
    tx.commit();
    //5.释放资源
    em.close();
}
```

## SpringDataJpa

### 引入基本依赖

```java
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>cn.itcast</groupId>
    <artifactId>jpa-day2</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <spring.version>5.0.2.RELEASE</spring.version>
        <hibernate.version>5.0.7.Final</hibernate.version>
        <slf4j.version>1.6.6</slf4j.version>
        <log4j.version>1.2.12</log4j.version>
        <c3p0.version>0.9.1.2</c3p0.version>
        <mysql.version>5.1.6</mysql.version>
    </properties>

    <dependencies>
        <!-- junit单元测试 -->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
        </dependency>

        <!-- spring beg -->
        <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjweaver</artifactId>
            <version>1.6.9</version>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-aop</artifactId>
            <version>${spring.version}</version>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>${spring.version}</version>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context-support</artifactId>
            <version>${spring.version}</version>
        </dependency>

        <!-- spring对orm框架的支持包-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-orm</artifactId>
            <version>${spring.version}</version>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-beans</artifactId>
            <version>${spring.version}</version>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-core</artifactId>
            <version>${spring.version}</version>
        </dependency>

        <!-- spring end -->

        <!-- hibernate beg -->
        <dependency>
            <groupId>org.hibernate</groupId>
            <artifactId>hibernate-core</artifactId>
            <version>${hibernate.version}</version>
        </dependency>
        <dependency>
            <groupId>org.hibernate</groupId>
            <artifactId>hibernate-entitymanager</artifactId>
            <version>${hibernate.version}</version>
        </dependency>
        <dependency>
            <groupId>org.hibernate</groupId>
            <artifactId>hibernate-validator</artifactId>
            <version>5.2.1.Final</version>
        </dependency>
        <!-- hibernate end -->

        <!-- c3p0 beg -->
        <dependency>
            <groupId>c3p0</groupId>
            <artifactId>c3p0</artifactId>
            <version>${c3p0.version}</version>
        </dependency>
        <!-- c3p0 end -->

        <!-- log end -->
        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>${log4j.version}</version>
        </dependency>

        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-api</artifactId>
            <version>${slf4j.version}</version>
        </dependency>

        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
            <version>${slf4j.version}</version>
        </dependency>
        <!-- log end -->


        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>${mysql.version}</version>
        </dependency>

        <!-- spring data jpa 的坐标-->
        <dependency>
            <groupId>org.springframework.data</groupId>
            <artifactId>spring-data-jpa</artifactId>
            <version>1.9.0.RELEASE</version>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
            <version>${spring.version}</version>
        </dependency>

        <!-- el beg 使用spring data jpa 必须引入 -->
        <dependency>
            <groupId>javax.el</groupId>
            <artifactId>javax.el-api</artifactId>
            <version>2.2.4</version>
        </dependency>

        <dependency>
            <groupId>org.glassfish.web</groupId>
            <artifactId>javax.el</artifactId>
            <version>2.2.4</version>
        </dependency>
        <!-- el end -->
        <!--        lombok用于数据注入-->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.16.10</version>
        </dependency>
    </dependencies>

</project>
```

### xml配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:jdbc="http://www.springframework.org/schema/jdbc" xmlns:tx="http://www.springframework.org/schema/tx"
       xmlns:jpa="http://www.springframework.org/schema/data/jpa" xmlns:task="http://www.springframework.org/schema/task"
       xsi:schemaLocation="
      http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
      http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd
      http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
      http://www.springframework.org/schema/jdbc http://www.springframework.org/schema/jdbc/spring-jdbc.xsd
      http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd
      http://www.springframework.org/schema/data/jpa
      http://www.springframework.org/schema/data/jpa/spring-jpa.xsd">

    <!--spring 和 spring data jpa的配置-->

    <!-- 1.创建entityManagerFactory对象交给spring容器管理-->
    <bean id="entityManagerFactoty" class="org.springframework.orm.jpa.LocalContainerEntityManagerFactoryBean">
        <property name="dataSource" ref="dataSource" />
        <!--配置的扫描的包（实体类所在的包） -->
        <property name="packagesToScan" value="com.luoqingshang.domain" />
        <!-- jpa的实现厂家 -->
        <property name="persistenceProvider">
            <bean class="org.hibernate.jpa.HibernatePersistenceProvider"/>
        </property>

        <!--jpa的供应商适配器 -->
        <property name="jpaVendorAdapter">
            <bean class="org.springframework.orm.jpa.vendor.HibernateJpaVendorAdapter">
                <!--配置是否自动创建数据库表 -->
                <property name="generateDdl" value="false" />
                <!--指定数据库类型 -->
                <property name="database" value="MYSQL" />
                <!--数据库方言：支持的特有语法 -->
                <property name="databasePlatform" value="org.hibernate.dialect.MySQLDialect" />
                <!--是否显示sql -->
                <property name="showSql" value="true" />
            </bean>
        </property>

        <!--jpa的方言 ：高级的特性 -->
        <property name="jpaDialect" >
            <bean class="org.springframework.orm.jpa.vendor.HibernateJpaDialect" />
        </property>

    </bean>

    <!--2.创建数据库连接池 -->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="user" value="root"></property>
        <property name="password" value="gqs123456"></property>
        <property name="jdbcUrl" value="jdbc:mysql://127.0.0.1:3307/jpa?useUnicode=true&amp;characterEncoding=utf8&amp;useSSL=false&amp;useLegacyDatetimeCode=false&amp;serverTimezone=Asia/Shanghai&amp;allowPublicKeyRetrieval=true" ></property>
        <property name="driverClass" value="com.mysql.jdbc.Driver"></property>
    </bean>

    <!--3.整合spring dataJpa-->
    <jpa:repositories base-package="com.luoqingshang.dao" transaction-manager-ref="transactionManager"
                   entity-manager-factory-ref="entityManagerFactoty" ></jpa:repositories>

    <!--4.配置事务管理器 -->
    <bean id="transactionManager" class="org.springframework.orm.jpa.JpaTransactionManager">
        <property name="entityManagerFactory" ref="entityManagerFactoty"></property>
    </bean>

    <!-- 4.txAdvice-->
    <tx:advice id="txAdvice" transaction-manager="transactionManager">
        <tx:attributes>
            <tx:method name="save*" propagation="REQUIRED"/>
            <tx:method name="insert*" propagation="REQUIRED"/>
            <tx:method name="update*" propagation="REQUIRED"/>
            <tx:method name="delete*" propagation="REQUIRED"/>
            <tx:method name="get*" read-only="true"/>
            <tx:method name="find*" read-only="true"/>
            <tx:method name="*" propagation="REQUIRED"/>
        </tx:attributes>
    </tx:advice>

    <!-- 5.aop-->
    <aop:config>
        <aop:pointcut id="pointcut" expression="execution(* com.luoqingshang.service.*.*(..))" />
        <aop:advisor advice-ref="txAdvice" pointcut-ref="pointcut" />
    </aop:config>


    <!--5.声明式事务 -->

    <!-- 6. 配置包扫描-->
    <context:component-scan base-package="com.luoqingshang" ></context:component-scan>
</beans>
```

### 编写实体类

```java
package com.luoqingshang.domain;

import lombok.Data;

import javax.persistence.*;

/**
 * 1.实体类和表的映射关系
 *      @Eitity
 *      @Table
 * 2.类中属性和表中字段的映射关系
 *      @Id
 *      @GeneratedValue
 *      @Column
 */
@Entity
@Table(name="cst_customer")
@Data
public class Customer {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name="cust_id")
    private Long custId;
    @Column(name="cust_address")
    private String custAddress;
    @Column(name="cust_industry")
    private String custIndustry;
    @Column(name="cust_level")
    private String custLevel;
    @Column(name="cust_name")
    private String custName;
    @Column(name="cust_phone")
    private String custPhone;
    @Column(name="cust_source")
    private String custSource;
}
```

### 编写接口

```java
package com.luoqingshang.dao;

import com.luoqingshang.domain.Customer;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.JpaSpecificationExecutor;

public interface CustomerDao extends JpaRepository<Customer,Long>, JpaSpecificationExecutor<Customer> {

}
```

#### 根据id查询

```java
package com.luoqingshang;

import com.luoqingshang.dao.CustomerDao;
import com.luoqingshang.domain.Customer;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;
import org.springframework.transaction.annotation.Transactional;

import java.util.List;

@RunWith(SpringJUnit4ClassRunner.class) //声明spring提供的单元测试环境
@ContextConfiguration(locations = "classpath:applicationContext.xml")//指定spring容器的配置信息
public class CustomerDaoTest {

    @Autowired
    private CustomerDao customerDao;

    /**
     * 根据id查询
     */
    @Test
    public void testFindOne() {
        Customer customer = customerDao.findOne(4l);
        System.out.println(customer);
    }
}
```

#### 保存或者更新

```java
/**
 * save : 保存或者更新
 *      根据传递的对象是否存在主键id，
 *      如果没有id主键属性：保存
 *      存在id主键属性，根据id查询数据，更新数据
 */
@Test
public void testSave() {
    Customer customer  = new Customer();
    customer.setCustName("客户名称4");
    customer.setCustLevel("vip");
    customer.setCustIndustry("it教育");
    customerDao.save(customer);
}

@Test
    public void testUpdate() {
        Customer customer  = new Customer();
        customer.setCustId(4l);
        customer.setCustName("客户名称5");
        customerDao.save(customer);
    }
```

#### 删除

```java
@Test
public void testDelete () {
    customerDao.delete(3l);
}
```

#### 查询所有

```java
/**
 * 查询所有
 */
@Test
public void testFindAll() {
    List<Customer> list = customerDao.findAll();
    for(Customer customer : list) {
        System.out.println(customer);
    }
}
```

#### 统计数量

```java
/**
 * 测试统计查询：查询客户的总数量
 *      count:统计总条数
 */
@Test
public void testCount() {
    long count = customerDao.count();//查询全部的客户数量
    System.out.println(count);
}
```

#### 判断是否存在

```java
/**
 * 测试：判断id为4的客户是否存在
 *      1. 可以查询以下id为4的客户
 *          如果值为空，代表不存在，如果不为空，代表存在
 *      2. 判断数据库中id为4的客户的数量
 *          如果数量为0，代表不存在，如果大于0，代表存在
 */
@Test
public void  testExists() {
    boolean exists = customerDao.exists(4l);
    System.out.println("id为4的客户 是否存在："+exists);
}
```

#### findOne和getOne的区别

```java
/**
 * 根据id从数据库查询
 *      @Transactional : 保证getOne正常运行
 *
 *  findOne：
 *      em.find()           :立即加载
 *  getOne：
 *      em.getReference     :延迟加载
 *      * 返回的是一个客户的动态代理对象
 *      * 什么时候用，什么时候查询
 */
@Test
@Transactional
public void  testGetOne() {
    Customer customer = customerDao.getOne(4l);
    System.out.println(customer);
}
```

### jpql

```java
/**
 * 案例：根据客户名称和客户id查询客户
 *      jpql： from Customer where custName = ? and custId = ?
 *
 *  对于多个占位符参数
 *      赋值的时候，默认的情况下，占位符的位置需要和方法参数中的位置保持一致
 *
 *  可以指定占位符参数的位置
 *      ? 索引的方式，指定此占位的取值来源
 */
@Query(value = "from Customer where custName = ?2 and custId = ?1")
public Customer findCustNameAndId(Long id, String name);
```

### sql

```java
/**
 * 使用sql的形式查询：
 *     查询全部的客户
 *  sql ： select * from cst_customer;
 *  Query : 配置sql查询
 *      value ： sql语句
 *      nativeQuery ： 查询方式
 *          true ： sql查询
 *          false：jpql查询
 *
 */
@Query(value="select * from cst_customer where cust_name like ?1",nativeQuery = true)
public List<Object [] > findSql(String name);
```

### 方法名称规则查询

```java
public List<Customer> findByCustNameLike(String custName);
```

### Specifications动态查询

```java
java@Test
public void testSpec() {
    //匿名内部类
    /**
     * 自定义查询条件
     *      1.实现Specification接口（提供泛型：查询的对象类型）
     *      2.实现toPredicate方法（构造查询条件）
     *      3.需要借助方法参数中的两个参数（
     *          root：获取需要查询的对象属性
     *          CriteriaBuilder：构造查询条件的，内部封装了很多的查询条件（模糊匹配，精准匹配）
     *       ）
     *  案例：根据客户名称查询，查询客户名为传智播客的客户
     *          查询条件
     *              1.查询方式
     *                  cb对象
     *              2.比较的属性名称
     *                  root对象
     *
     */
    Specification<Customer> spec = new Specification<Customer>() {
        @Override
        public Predicate toPredicate(Root<Customer> root, CriteriaQuery<?> query, CriteriaBuilder cb) {
            //1.获取比较的属性
            Path<Object> custName = root.get("custName");
            //2.构造查询条件  ：    select * from cst_customer where cust_name = '客户名称1'
            /**
             * 第一个参数：需要比较的属性（path对象）
             * 第二个参数：当前需要比较的取值
             */
            Predicate predicate = cb.equal(custName, "客户名称1");//进行精准的匹配  （比较的属性，比较的属性的取值）
            predicate=cb.and(cb.equal(custName, "客户名称1"));//再次追加
            return predicate;
        }
    };
    Customer customer = customerDao.findOne(spec);
    System.out.println(customer);
}
```

### 一对多关系

普通基本jpa实现，在实体里配置映射关系

```java
package com.luoqingshang.domain;

import lombok.Data;
import lombok.Getter;
import lombok.Setter;

import javax.persistence.*;
import java.util.HashSet;
import java.util.Set;

/**
 * 1.实体类和表的映射关系
 *      @Eitity
 *      @Table
 * 2.类中属性和表中字段的映射关系
 *      @Id
 *      @GeneratedValue
 *      @Column
 */
@Entity
@Table(name="cst_customer")
//@Data
@Getter
@Setter
public class Customer {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name="cust_id")
    private Long custId;
    @Column(name="cust_address")
    private String custAddress;
    @Column(name="cust_industry")
    private String custIndustry;
    @Column(name="cust_level")
    private String custLevel;
    @Column(name="cust_name")
    private String custName;
    @Column(name="cust_phone")
    private String custPhone;
    @Column(name="cust_source")
    private String custSource;

    //配置客户和联系人之间的关系（一对多关系）
    /**
     * 使用注解的形式配置多表关系
     *      1.声明关系
     *          @OneToMany : 配置一对多关系
     *              targetEntity ：对方对象的字节码对象
     *      2.配置外键（中间表）
     *              @JoinColumn : 配置外键
     *                  name：外键字段名称
     *                  referencedColumnName：参照的主表的主键字段名称
     *
     *  * 在客户实体类上（一的一方）添加了外键了配置，所以对于客户而言，也具备了维护外键的作用
     *
     */

//    @OneToMany(targetEntity = LinkMan.class)
//    @JoinColumn(name = "lkm_cust_id",referencedColumnName = "cust_id")
    /**
     * 放弃外键维护权
     *      mappedBy：对方配置关系的属性名称\
     * cascade : 配置级联（可以配置到设置多表的映射关系的注解上）
     *      CascadeType.all         : 所有
     *                  MERGE       ：更新
     *                  PERSIST     ：保存
     *                  REMOVE      ：删除
     *
     * fetch : 配置关联对象的加载方式
     *          EAGER   ：立即加载
     *          LAZY    ：延迟加载

     */
    @OneToMany(mappedBy = "customer",cascade = CascadeType.ALL)
    private Set<LinkMan> linkMans = new HashSet<>();
}
```

```java
package com.luoqingshang.domain;

import lombok.Data;

import javax.persistence.*;

@Entity
@Table(name = "cst_linkman")
@Data
public class LinkMan {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "lkm_id")
    private Long lkmId; //联系人编号(主键)
    @Column(name = "lkm_name")
    private String lkmName;//联系人姓名
    @Column(name = "lkm_gender")
    private String lkmGender;//联系人性别
    @Column(name = "lkm_phone")
    private String lkmPhone;//联系人办公电话
    @Column(name = "lkm_mobile")
    private String lkmMobile;//联系人手机
    @Column(name = "lkm_email")
    private String lkmEmail;//联系人邮箱
    @Column(name = "lkm_position")
    private String lkmPosition;//联系人职位
    @Column(name = "lkm_memo")
    private String lkmMemo;//联系人备注

    /**
     * 配置联系人到客户的多对一关系
     *     使用注解的形式配置多对一关系
     *      1.配置表关系
     *          @ManyToOne : 配置多对一关系
     *              targetEntity：对方的实体类字节码
     *      2.配置外键（中间表）
     *
     * * 配置外键的过程，配置到了多的一方，就会在多的一方维护外键
     *
     */
    @ManyToOne(targetEntity = Customer.class)
    @JoinColumn(name = "lkm_cust_id",referencedColumnName = "cust_id")
    private Customer customer;


}
```

测试

```java
package com.luoqingshang.test;

import com.luoqingshang.dao.CustomerDao;
import com.luoqingshang.dao.LinkManDao;
import com.luoqingshang.domain.Customer;
import com.luoqingshang.domain.LinkMan;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.annotation.Rollback;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;
import org.springframework.transaction.annotation.Transactional;

@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = "classpath:applicationContext.xml")
public class OneToManyTest {

    @Autowired
    private CustomerDao customerDao;

    @Autowired
    private LinkManDao linkManDao;

    /**
     * 保存一个客户，保存一个联系人
     *  效果：客户和联系人作为独立的数据保存到数据库中
     *      联系人的外键为空
     *  原因？
     *      实体类中没有配置关系
     */
    @Test
    @Transactional //配置事务
    @Rollback(false) //不自动回滚
    public void testAdd() {
        //创建一个客户，创建一个联系人
        Customer customer = new Customer();
        customer.setCustName("百度");

        LinkMan linkMan = new LinkMan();
        linkMan.setLkmName("小李");

        /**
         * 配置了客户到联系人的关系
         *      从客户的角度上：发送两条insert语句，发送一条更新语句更新数据库（更新外键）
         * 由于我们配置了客户到联系人的关系：客户可以对外键进行维护
         */
        customer.getLinkMans().add(linkMan);


        customerDao.save(customer);
        linkManDao.save(linkMan);
    }

    @Test
    @Transactional //配置事务
    @Rollback(false) //不自动回滚
    public void testAdd1() {
        //创建一个客户，创建一个联系人
        Customer customer = new Customer();
        customer.setCustName("百度");

        LinkMan linkMan = new LinkMan();
        linkMan.setLkmName("小李");

        /**
         * 配置联系人到客户的关系（多对一）
         *    只发送了两条insert语句
         * 由于配置了联系人到客户的映射关系（多对一）
         *
         *
         */
        linkMan.setCustomer(customer);

        customerDao.save(customer);
        linkManDao.save(linkMan);
    }

    /**
     * 会有一条多余的update语句
     *      * 由于一的一方可以维护外键：会发送update语句
     *      * 解决此问题：只需要在一的一方放弃维护权即可
     *
     */
    @Test
    @Transactional //配置事务
    @Rollback(false) //不自动回滚
    public void testAdd2() {
        //创建一个客户，创建一个联系人
        Customer customer = new Customer();
        customer.setCustName("百度");

        LinkMan linkMan = new LinkMan();
        linkMan.setLkmName("小李");


        linkMan.setCustomer(customer);//由于配置了多的一方到一的一方的关联关系（当保存的时候，就已经对外键赋值）
        customer.getLinkMans().add(linkMan);//由于配置了一的一方到多的一方的关联关系（发送一条update语句）

        customerDao.save(customer);
        linkManDao.save(linkMan);
    }

    /**
     * 级联添加：保存一个客户的同时，保存客户的所有联系人
     *      需要在操作主体的实体类上，配置casacde属性
     */
    @Test
    @Transactional //配置事务
    @Rollback(false) //不自动回滚
    public void testCascadeAdd() {
        Customer customer = new Customer();
        customer.setCustName("百度1");

        LinkMan linkMan = new LinkMan();
        linkMan.setLkmName("小李1");

        linkMan.setCustomer(customer);
        customer.getLinkMans().add(linkMan);

        customerDao.save(customer);
    }


    /**
     * 级联删除：
     *      删除1号客户的同时，删除1号客户的所有联系人
     */
    @Test
    @Transactional //配置事务
    @Rollback(false) //不自动回滚
    public void testCascadeRemove() {
        //1.查询1号客户
        Customer customer = customerDao.findOne(6l);
        //2.删除1号客户
        customerDao.delete(customer);
    }
}
```

### 多对多关系

```java
package com.luoqingshang.domain;

import javax.persistence.*;
import java.util.HashSet;
import java.util.Set;

@Entity
@Table(name = "sys_role")
public class Role {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "role_id")
    private Long roleId;
    @Column(name = "role_name")
    private String roleName;

    //配置多对多
//    @ManyToMany(targetEntity = User.class,cascade = CascadeType.ALL)
//    @JoinTable(name = "sys_user_role",
//            //joinColumns,当前对象在中间表中的外键
//            joinColumns = {@JoinColumn(name = "sys_role_id",referencedColumnName = "role_id")},
//            //inverseJoinColumns，对方对象在中间表的外键
//            inverseJoinColumns = {@JoinColumn(name = "sys_user_id",referencedColumnName = "user_id")}
//    )
    @ManyToMany(mappedBy = "roles")  //配置多表关系
    private Set<User> users = new HashSet<>();

    public Long getRoleId() {
        return roleId;
    }

    public void setRoleId(Long roleId) {
        this.roleId = roleId;
    }

    public String getRoleName() {
        return roleName;
    }

    public void setRoleName(String roleName) {
        this.roleName = roleName;
    }

    public Set<User> getUsers() {
        return users;
    }

    public void setUsers(Set<User> users) {
        this.users = users;
    }
}

```

```java
package com.luoqingshang.domain;

import javax.persistence.*;
import java.util.HashSet;
import java.util.Set;

@Entity
@Table(name = "sys_user")
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name="user_id")
    private Long userId;
    @Column(name="user_name")
    private String userName;
    @Column(name="age")
    private Integer age;

    /**
     * 配置用户到角色的多对多关系
     *      配置多对多的映射关系
     *          1.声明表关系的配置
     *              @ManyToMany(targetEntity = Role.class)  //多对多
     *                  targetEntity：代表对方的实体类字节码
     *          2.配置中间表（包含两个外键）
     *                @JoinTable
     *                  name : 中间表的名称
     *                  joinColumns：配置当前对象在中间表的外键
     *                      @JoinColumn的数组
     *                          name：外键名
     *                          referencedColumnName：参照的主表的主键名
     *                  inverseJoinColumns：配置对方对象在中间表的外键
     */
    @ManyToMany(targetEntity = Role.class,cascade = CascadeType.ALL)
    @JoinTable(name = "sys_user_role",
            //joinColumns,当前对象在中间表中的外键
            joinColumns = {@JoinColumn(name = "sys_user_id",referencedColumnName = "user_id")},
            //inverseJoinColumns，对方对象在中间表的外键
            inverseJoinColumns = {@JoinColumn(name = "sys_role_id",referencedColumnName = "role_id")}
    )
    private Set<Role> roles = new HashSet<>();

    public Long getUserId() {
        return userId;
    }

    public void setUserId(Long userId) {
        this.userId = userId;
    }

    public String getUserName() {
        return userName;
    }

    public void setUserName(String userName) {
        this.userName = userName;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

    public Set<Role> getRoles() {
        return roles;
    }

    public void setRoles(Set<Role> roles) {
        this.roles = roles;
    }
}
```

测试

```java
package com.luoqingshang.test;

import com.luoqingshang.dao.RoleDao;
import com.luoqingshang.dao.UserDao;
import com.luoqingshang.domain.Role;
import com.luoqingshang.domain.User;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.annotation.Rollback;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;
import org.springframework.transaction.annotation.Transactional;

@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = "classpath:applicationContext.xml")
public class ManyToManyTest {

    @Autowired
    private UserDao userDao;
    @Autowired
    private RoleDao roleDao;

    /**
     * 保存一个用户，保存一个角色
     *
     *  多对多放弃维护权：被动的一方放弃
     */
    @Test
    @Transactional
    @Rollback(false)
    public void  testAdd() {
        User user = new User();
        user.setUserName("小李");

        Role role = new Role();
        role.setRoleName("java程序员");

        //配置用户到角色关系，可以对中间表中的数据进行维护     1-1
        user.getRoles().add(role);

        //配置角色到用户的关系，可以对中间表的数据进行维护     1-1
//        role.getUsers().add(user);

        userDao.save(user);
//        roleDao.save(role);
    }


    //测试级联添加（保存一个用户的同时保存用户的关联角色）
    @Test
    @Transactional
    @Rollback(false)
    public void  testCasCadeAdd() {
        User user = new User();
        user.setUserName("小李");

        Role role = new Role();
        role.setRoleName("java程序员");

        //配置用户到角色关系，可以对中间表中的数据进行维护     1-1
        user.getRoles().add(role);

        //配置角色到用户的关系，可以对中间表的数据进行维护     1-1
        role.getUsers().add(user);

        userDao.save(user);
    }

    /**
     * 案例：删除id为1的用户，同时删除他的关联对象
     */
    @Test
    @Transactional
    @Rollback(false)
    public void  testCasCadeRemove() {
        //查询1号用户
        User user = userDao.findOne(1l);
        //删除1号用户
        userDao.delete(user);

    }
}
```

### 导航查询

```java
package com.luoqingshang.test;

import com.luoqingshang.dao.CustomerDao;
import com.luoqingshang.dao.LinkManDao;
import com.luoqingshang.domain.Customer;
import com.luoqingshang.domain.LinkMan;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;
import org.springframework.transaction.annotation.Transactional;

import java.util.Set;

@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = "classpath:applicationContext.xml")
public class ObjectQueryTest {
    @Autowired
    private CustomerDao customerDao;

    @Autowired
    private LinkManDao linkManDao;

    //could not initialize proxy - no Session
    //测试对象导航查询（查询一个对象的时候，通过此对象查询所有的关联对象）
    @Test
    @Transactional // 解决在java代码中的no session问题
    public void  testQuery1() {
        //查询id为1的客户
        Customer customer = customerDao.getOne(7l);
        //对象导航查询，此客户下的所有联系人
        Set<LinkMan> linkMans = customer.getLinkMans();

        for (LinkMan linkMan : linkMans) {
            System.out.println(linkMan);
        }
    }

    /**
     * 对象导航查询：
     *      默认使用的是延迟加载的形式查询的
     *          调用get方法并不会立即发送查询，而是在使用关联对象的时候才会差和讯
     *      延迟加载！
     * 修改配置，将延迟加载改为立即加载
     *      fetch，需要配置到多表映射关系的注解上
     *
     */

    @Test
    @Transactional // 解决在java代码中的no session问题
    public void  testQuery2() {
        //查询id为1的客户
        Customer customer = customerDao.findOne(1l);
        //对象导航查询，此客户下的所有联系人
        Set<LinkMan> linkMans = customer.getLinkMans();

        System.out.println(linkMans.size());
    }

    /**
     * 从联系人对象导航查询他的所属客户
     *      * 默认 ： 立即加载
     *  延迟加载：
     *
     */
    @Test
    @Transactional // 解决在java代码中的no session问题
    public void  testQuery3() {
        LinkMan linkMan = linkManDao.findOne(2l);
        //对象导航查询所属的客户
        Customer customer = linkMan.getCustomer();
        System.out.println(customer);
    }

}
```