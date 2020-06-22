# RabbitMQ

## 安装

```

docker run -d --hostname my-rabbit -p 5672:5672 -p 15672:15672 rabbitmq:3.7.7-management
```

## 管理台管理

### 用户级别

1.administrator 可以登陆控制台，查看所有信息，可以对rabbitMQ进行管理

```
policymaker和monitoring可以做的任何事外加:
创建和删除virtual hosts
查看、创建和删除users
查看创建和删除permissions
关闭其他用户的connections
```

2.monitoring 监控者 可以登陆控制台，查看所有信息

```
management可以做的任何事外加：
列出所有virtual hosts，包括他们不能登录的virtual hosts
查看其他用户的connections和channels
查看节点级别的数据如clustering和memory使用情况
查看真正的关于所有virtual hosts的全局的统计信息
```

3.policymaker 策略制定者 登陆控制台，指定策略

```
management可以做的任何事外加：
查看、创建和删除自己的virtual hosts所属的policies和parameters
```

4.managment  普通管理员 登陆控制台

```
用户可以通过AMQP做的任何事外加：
列出自己可以通过AMQP登入的virtual hosts  
查看自己的virtual hosts中的queues, exchanges 和 bindings
查看和关闭自己的channels 和 connections
查看有关自己的virtual hosts的“全局”的统计信息，包含其他用户在这些virtual hosts中的活动。
```

5.none

```
不能访问 management plugin
```

### 创建用户

![](/Users/guoqisheng/Desktop/笔记/biji/rabbitMQ/截屏2020-06-17 上午10.48.13.png)

### 删除用户

点击要删除的用户名，拉到最下面删除

![](/Users/guoqisheng/Desktop/笔记/biji/rabbitMQ/截屏2020-06-17 上午11.09.53.png)

### 创建虚拟主机

![](/Users/guoqisheng/Desktop/笔记/biji/rabbitMQ/截屏2020-06-17 上午10.54.38.png)

### 删除虚拟主机

点击要删除的虚拟主机，拉到最下面删除

![](/Users/guoqisheng/Desktop/笔记/biji/rabbitMQ/截屏2020-06-17 上午10.56.32.png)

### 用户绑定虚拟主机

点击用户，选择虚拟主机名

![](/Users/guoqisheng/Desktop/笔记/biji/rabbitMQ/截屏2020-06-17 上午11.01.43.png)

## 交换机和队列管理

### 创建队列

![](/Users/guoqisheng/Desktop/笔记/biji/rabbitMQ/WeChat45f3ea2beb577b26cb8e86cf9887e040.png)

### 创建交换机

![](/Users/guoqisheng/Desktop/笔记/biji/rabbitMQ/WeChatffa752858219bbc60ac40f319ad3bd09.png)

### 交换机绑定队列

点击交换机的名字

![](/Users/guoqisheng/Desktop/笔记/biji/rabbitMQ/WeChat8d943514922a39c36f3273fccadfca27.png)

注意⚠️路由交换机需要给定routing key

## rabbitMQ的工作模式

### 配置

pom配置

```xml
	<dependencies>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
            <version>1.7.30</version>
        </dependency>
        <dependency>
            <groupId>org.apache.commons</groupId>
            <artifactId>commons-lang3</artifactId>
            <version>3.8.1</version>
        </dependency>
        <dependency>
            <groupId>com.rabbitmq</groupId>
            <artifactId>amqp-client</artifactId>
            <version>5.9.0</version>
        </dependency>
    </dependencies>
```

创建mq连接帮助类

```java
package com.luoqingshang.mq.utils;
import com.rabbitmq.client.Connection;
import com.rabbitmq.client.ConnectionFactory;
import java.io.IOException;
import java.util.concurrent.TimeoutException;
public class ConnectionUtil {
    public static Connection getConnection() throws IOException, TimeoutException {
        //1.创建连接工厂
        ConnectionFactory factory=new ConnectionFactory();
        //2.在工厂对象中设置MQ的连接信息（ip，端口，虚拟主机，用户名，密码）
        factory.setHost("127.0.0.1");
        factory.setPort(5672);
        factory.setVirtualHost("host1");
        factory.setUsername("admin");
        factory.setPassword("admin");
        //通过工厂对象获取与mq的连接
        return factory.newConnection();
    }
}
```



### 简单模式

一个队列只有一个消费者

![](/Users/guoqisheng/Desktop/笔记/biji/rabbitMQ/截屏2020-06-17 下午3.14.25.png)

消息生产者代码

```java
package com.luoqingshang.mq.service;
import com.luoqingshang.mq.utils.ConnectionUtil;
import com.rabbitmq.client.Channel;
import com.rabbitmq.client.Connection;
public class sendMsg {
    public static void main(String[] args) throws Exception {
        String msg="Hello rabbitmq";
        //相当于JDBC操作的数据库连接
        Connection connection= ConnectionUtil.getConnection();
        //相当于JDBC操作的statement
        Channel channel = connection.createChannel();
        /**发送消息
         * 参数一：交换机名称，如果直接发送消息到队列，则交换机名称为""
         * 参数二：目标队列名称
         * 参数三：设置当前这条消息的属性
         * 参数四：消息的内容
         */
        channel.basicPublish("","queue1",null,msg.getBytes());
        //关闭
        channel.close();
        connection.close();
    }
}
```

消息消费者代码

```
package mq.service;
import com.rabbitmq.client.*;
import mq.utils.ConnectionUtil;
import java.io.IOException;
public class ReceiveMsg {
    public static void main(String[] args) throws Exception {
        Connection connection= ConnectionUtil.getConnection();
        Channel channel = connection.createChannel();
        Consumer consumer=new DefaultConsumer(channel){
            @Override
            public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
                //body就是从队列中获取的数据
                System.out.println(new String(body));
            }
        };
        channel.basicConsume("queue1",consumer);
    }
}

```



### 工作模式

多个消费者监听同一个队列，消费完就移除

![](/Users/guoqisheng/Desktop/笔记/biji/rabbitMQ/截屏2020-06-17 下午3.14.51.png)

生产者代码与简单模式一致

消费者代码与简单模式也一致，不过同时启用多个consumer，多个consumer会随机消费队列里的数据

### 订阅模式

交换机模式：fanout

多个消息队列，每个消息队列有一个消费者监听

消息生产者发送的消息可以被每一个消费者监听

![](/Users/guoqisheng/Desktop/笔记/biji/rabbitMQ/截屏2020-06-17 下午3.16.49.png)

生产者代码发送到交换机

```
package com.luoqingshang.mq.service;
import com.luoqingshang.mq.utils.ConnectionUtil;
import com.rabbitmq.client.Channel;
import com.rabbitmq.client.Connection;
public class sendMsg {
    public static void main(String[] args) throws Exception {
        String msg="hello";
        Connection connection = ConnectionUtil.getConnection();
        Channel channel = connection.createChannel();
        channel.basicPublish("ex1", "", null, msg.getBytes());
        channel.close();
        connection.close();
    }
}
```

消费者代码与简单模式一致，多个consumer分别监听交换机下的多个队列

### 路由模式

交换机模式：redirect

一个交换机绑定多个消息队列，每个消息队列都有自己唯一的key，每个消息队列有一个消费者监听

![](/Users/guoqisheng/Desktop/笔记/biji/rabbitMQ/截屏2020-06-17 下午3.22.54.png)

生产者代码发送到交换机

```
package com.luoqingshang.mq.service;
import com.luoqingshang.mq.utils.ConnectionUtil;
import com.rabbitmq.client.Channel;
import com.rabbitmq.client.Connection;
public class sendMsg {
    public static void main(String[] args) throws Exception {
        String msg="hello";
        Connection connection = ConnectionUtil.getConnection();
        Channel channel = connection.createChannel();
        channel.basicPublish("ex2", "a", null, msg.getBytes());//如果启用交换机，原队列的第二个参数变为交换机和队列关联的Routing key
        channel.close();
        connection.close();
    }
}
```

## 在SpringBoot中使用rabbitMQ

配置

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-amqp</artifactId>
</dependency>
```

```yml
spring:
  rabbitmq:
    host: 127.0.0.1
    port: 5672
    virtual-host: host1
    username: admin
    password: admin
```

消息生产者代码

```java
package com.luoqingshang.springmq.service;
import org.springframework.amqp.core.AmqpTemplate;
import org.springframework.stereotype.Service;
import javax.annotation.Resource;
@Service
public class TestService {
    @Resource
    private AmqpTemplate amqpTemplate;
    public void sendMsg(String msg){
        //发送消息到队列第一个参数为队列名，第二个参数为发送的消息
        amqpTemplate.convertAndSend("queue1",msg);
        //发送消息到交换机
        //订阅交换机第一个参数为交换机的名字，第二个参数为key，第三个参数为发送的消息
        amqpTemplate.convertAndSend("ex1","",msg);
        //路由交换机
        amqpTemplate.convertAndSend("ex1","a",msg);
    }
}
```

消费者代码

```java
package com.luoqingshang.consumer.service;
import org.springframework.amqp.rabbit.annotation.RabbitHandler;
import org.springframework.amqp.rabbit.annotation.RabbitListener;
import org.springframework.stereotype.Service;
@Service
@RabbitListener(queues = {"queue1"})//监听这个队列
public class ReceiveMsg {
    @RabbitHandler//接受的数据传给此方法
    public void receiveMsg(String msg){//数据类型要与生产者发送的数据类型匹配,如果生产者给的是Byte[]，此处String改为Byte[]
        System.out.println(msg);
    }
  	@RabbitHandler
    public void receiveMsg(byte[] msg){
        System.out.println(new String(msg));
    }
}
```

## 使用RabbitMQ传递对象

消息队列可以发送字符串，字节数组，序列化对象

1.使用序列化对象发送

注意⚠️给序列化对象添加上序列号，否则有可能会反序列化失败

```java
//生产者
public void sendMsg(Employee emp){
	amqpTemplate.convertAndSend("queue1",emp);
}
//消费者
@RabbitHandler
public void receiveMsg(Employee msg){
	System.out.println(msg);
}
```

2.使用序列化字节数组传递

注意和第一种方法一样也需要序列化

```java
public void sendMsg(Employee emp){
	byte[] bytes = SerializationUtils.serialize(emp);
	amqpTemplate.convertAndSend("queue1",bytes);
}
@RabbitHandler
public void receiveMsg(byte[] msg){
	Employee deserialize = (Employee)SerializationUtils.deserialize(msg);
	System.out.println(deserialize);
}
```

使用json字符串传递

不需要序列化对象

```java
public void sendMsg(Employee emp) throws JsonProcessingException {
  ObjectMapper objectMapper=new ObjectMapper();
  String msg = objectMapper.writeValueAsString(emp);
  amqpTemplate.convertAndSend("queue1",msg);
}

```

## 基于java的交换机与队列的创建

### maven创建队列和交换机

```java
package com.luoqingshang.mq.service;
import com.luoqingshang.mq.utils.ConnectionUtil;
import com.rabbitmq.client.BuiltinExchangeType;
import com.rabbitmq.client.Channel;
import com.rabbitmq.client.Connection;
public class sendMsg {
    public static void main(String[] args) throws Exception {
        String msg="hello";
        Connection connection = ConnectionUtil.getConnection();
        Channel channel = connection.createChannel();
        /**定义队列（新建一个队列）
         * 参数一：定义的队列名称
         * 参数二：队列中的数据是否持久化
         * 参数三：是否排外（当前队列是否是当前连接私有）
         * 参数四：自动删除（当此队列的连接数为0时，此队列会销毁，无论队列中是否还有数据）
         * 参数五：设置参数
         */
        channel.queueDeclare("queue7",false,false,false,null);
        channel.queueDeclare("queue8",false,false,false,null);
        /**定义一个交换机
         * 第一个参数交换机的名字
         * 第二个参数交换机的类型
         */
        channel.exchangeDeclare("ex3", BuiltinExchangeType.FANOUT);//订阅交换机
        channel.exchangeDeclare("ex4", BuiltinExchangeType.DIRECT);//路由交换机
        /**绑定交换机
         * 第一个参数：队列名
         * 第二个参数：交换机名
         * 第三个参数：key的名字，如果不需要填写key则传""
         */
        channel.queueBind("queue7","ex3","");
        channel.queueBind("queue8","ex4","k3");
        
        channel.basicPublish("ex4", "k3", null, msg.getBytes());
        channel.close();
        connection.close();
    }
}
```

### SpringBoot创建队列和交换机

```java
package com.luoqingshang.produce.config;

import org.springframework.amqp.core.*;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class RabbitMQConfig {

    @Bean("queue9")
    public Queue newQueue9(){
        return new Queue("queue9");
    }

    @Bean
    public Queue newQueue10(){
        Queue queue10 = new Queue("queue10");
//        若想添加属性给队列设置值就行了
//        queue10.setActualName("");
        return queue10;
    }

    @Bean
    public FanoutExchange newFanoutExchange(){
        return new FanoutExchange("ex5");
    }

    @Bean
    public DirectExchange newDirectExchange(){
        return new DirectExchange("ex6");
    }

    @Bean
    //bean后面指定名字传值用bean的名字，若没指定，传值使用方法名
    public Binding bindingQueue9ToEx6(Queue queue9,DirectExchange newDirectExchange){
        //with表示路由key的名字，订阅模式可以不需要添加with
        return BindingBuilder.bind(queue9).to(newDirectExchange).with("k9");
    }

    @Bean
    public Binding bindingQueue10ToEx6(Queue newQueue10,DirectExchange newDirectExchange){
        return BindingBuilder.bind(newQueue10).to(newDirectExchange).with("k10");
    }
}
```

## 消息可靠性

### rabbitMQ事务

当在消息发送过程中添加了事务，处理效率降低几十倍甚至上百倍（所以一般不用事务）

```java
channel.txSelect();//开启事务
try {
	channel.basicPublish("", "queue1", null, msg.getBytes());
	channel.txCommit();//提交事务
}catch (Exception e){
	channel.txRollback();//回滚事务
}
```

### 消息确认和return机制

消息确认：确认消息提供者是否成功发送带交换机

 return机制：确认消息是否成功的从交换机分发到队列

消息确认失败，则不会进行return机制，若消息确认成功，return机制有可能成功也有可能失败

#### 普通maven项目

##### Confirm机制

###### 同步

```java
channel.confirmSelect();//开启消息确认
channel.basicPublish("", "queue1", null, msg.getBytes());
//for(int i=0;i<10;i++){
//  channel.basicPublish("", "queue1", null, msg.getBytes());
//}
boolean b = channel.waitForConfirms();//接受消息确认
```

普通：发送失败，则失败

批量：发送的所有消息中，如果有一条是失败的，则所有消息发送失败，抛出io异常

waitForConfirms会进入消息阻塞，等待状态

###### 异步

```java
 channel.confirmSelect();//开启消息确认
 channel.basicPublish("", "queue1", null, msg.getBytes());
 //        boolean b = channel.waitForConfirms();//接受消息确认
 //使用监听器异步确认
 channel.addConfirmListener(new ConfirmListener() {
 //参数一： long l 返回消息的表示
 //参数二： boolean b 是否为批量确认 false为一条，true为批量
 @Override
 public void handleAck(long l, boolean b) throws IOException {
 		System.out.println("消息发送到交换机成功");
 }

@Override
public void handleNack(long l, boolean b) throws IOException {
		System.out.println("消息发送到交换机失败");
}
});
```

异步需要把关闭连接关掉

##### return机制

```java
channel.addReturnListener(new ReturnListener() {
            @Override
            public void handleReturn(int i, String s, String s1, String s2, AMQP.BasicProperties basicProperties, byte[] bytes) throws IOException {
                //如果交换机分发消息队列失败，则会执行此方法
                //i标识  s1交换机名  s2交换机对应队列的key  basicProperties队列的属性(一般不设置属性)  bytes发送的消息
                System.out.println("交换机发送到消息队列失败");
            }
        });
```

注意⚠️：开启return机制在发送时需要加一个参数

```
channel.basicPublish("ex2", "b",true, null, msg.getBytes());//需要增加第三个boolean参数
```

#### springBoot项目

添加yml参数开启消息确认和return监听

```yaml
spring:
  rabbitmq:
    publisher-confirm-type: simple//简单的消息确认
    publisher-returns: true//开启return监听，默认是false
```

配置监听类

```java
package com.luoqingshang.produce.service;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.amqp.core.Message;
import org.springframework.amqp.rabbit.connection.CorrelationData;

import org.springframework.amqp.rabbit.core.RabbitTemplate;
import org.springframework.stereotype.Component;

import javax.annotation.PostConstruct;
import javax.annotation.Resource;

@Component
public class MsgConfigAndReturn implements RabbitTemplate.ConfirmCallback,RabbitTemplate.ReturnCallback {

    Logger logger= LoggerFactory.getLogger(MsgConfigAndReturn.class);

    @Resource
    private RabbitTemplate rabbitTemplate;

    @PostConstruct
    private void init(){
        rabbitTemplate.setConfirmCallback(this);
        rabbitTemplate.setReturnCallback(this);
    }

    @Override
    public void confirm(CorrelationData correlationData, boolean b, String s) {
        //此方法用于监听消息确认结果（消息是否发送到交换机）
        if(b){
            logger.info("消息成功发送到交换机");
        }else{
            logger.warn("消息发送到交换机失败");
        }
    }

    @Override
    public void returnedMessage(Message message, int i, String s, String s1, String s2) {
        logger.warn("交换机分发消息到对列失败");
    }
}

```

## 延迟机制

### 延迟队列

延迟队列--消息进入队列后，延迟指定的时间才能被消费者消费

AMQP协议和RabbitMQ队列本身是不支持延迟队列功能的，但可以通过TTL（Time To Live）特性模拟延迟队列的功能

TTL就是消息的存活时间。RabbitMQ可以分别对队列和消息设置存活时间

在创建队列的时候可以设置队列的存活时间，当消息进入到队列并且在存活时间内没有消费者消费，则此消息就会从当前队列被移除

创建消费队列没有设置TTL，但消息设置了TTL，那么当消息的存活时间结束，也会被移除

注意⚠️若既设置了队列的TTL，又设置了消息的TTL，那么短的生效

当TTL结束之后，我们可以指定将当前队列的消息转存到其他指定的队列

#### 创建死信队列

![创建死信队列](/Users/guoqisheng/Desktop/笔记/biji/rabbitMQ/截屏2020-06-21 下午9.18.13.png)

## 应用场景

1.解耦

应用场景：用户下单之后，订单系统通知库存系统

```
应用系统a写入消息队列，应用系统b订阅消息队列，即使应用系统b读取失败，不会消费数据，可以重新进行订阅处理数据，解除两个应用系统之间的耦合性
```

2.异步

应用场景：用户注册后之后需要发送邮件和短信

```
应用系统a注册成功后，将消息发送到消息队列，应用程序b和应用程序c订阅消息队列，相较于串型调用可以减少调用时间
```

3.消息通信

应用场景：应用系统之间的通行，例如聊天室

```
应用系统A写入消息队列应用系统B再从消息队列读取
应用系统B写入消息队列应用系统A再从消息队列读取
```

4.流量削峰

应用场景：双十一秒杀

```
应用系统A写入大量的数据进入消息队列，应用系统B慢慢地从消息队列读取消息并处理
分散压力，将需要的数据存储到消息中间件，让另一个服务主动读取，不至于一次性被压爆
```

5.限流

应用场景：限制个数

```
应用系统A写入大量的数据到消息队列，但消息队列限制了个数，很多消息无法进入队列，应用B只需要读取少量的数据并进行处理
```

6.日志处理

应用场景：系统的大量日志

```
类似于流量削峰，不过一般使用kafka
```

