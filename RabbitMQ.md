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
        /**定义队列（新建一个队列）
         * 参数一：定义的队列名称
         * 参数二：队列中的数据是否持久化
         * 参数三：是否排外（当前队列是否是当前连接私有）
         * 参数四：自动删除（当此队列的连接数为0时，此队列会销毁，无论队列中是否还有数据）
         * 参数五：设置参数
         */
        //channel.queueDeclare("queue7",false,false,false,null);
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

### 订阅模式

交换机模式：fanout

多个消息队列，每个消息队列有一个消费者监听

消息生产者发送的消息可以被每一个消费者监听

![](/Users/guoqisheng/Desktop/笔记/biji/rabbitMQ/截屏2020-06-17 下午3.16.49.png)

### 路由模式

交换机模式：redirect

一个交换机绑定多个消息队列，每个消息队列都有自己唯一的key，每个消息队列有一个消费者监听

![](/Users/guoqisheng/Desktop/笔记/biji/rabbitMQ/截屏2020-06-17 下午3.22.54.png)

## 交换机和队列管理

### 创建队列

![](/Users/guoqisheng/Desktop/笔记/biji/rabbitMQ/WeChat45f3ea2beb577b26cb8e86cf9887e040.png)

### 创建交换机

![](/Users/guoqisheng/Desktop/笔记/biji/rabbitMQ/WeChatffa752858219bbc60ac40f319ad3bd09.png)

### 交换机绑定队列

点击交换机的名字

![](/Users/guoqisheng/Desktop/笔记/biji/rabbitMQ/WeChat8d943514922a39c36f3273fccadfca27.png)

注意⚠️路由交换机需要给定routing key

