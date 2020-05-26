

# lambda

## Jdk1.8新特性

### 速度更快

jdk1.8之前

数组+链表：为了解决哈希碰撞，数组使用超过75%时，进行哈希扩容，扩容后，重新使用哈希算法算出数组下标，进行排序，但是还是会出现哈希碰撞

Jdk1.8之后

数组+链表+红黑树（二叉树的一种）：当哈希碰撞数量大于8时，并且在总元素大于64时，将链表转化成红黑树，红黑树除了添加以外，其他效率都比链表快

jdk1.8之前内存结构分栈，堆（永久区，垃圾回收区），方法区（方法区是堆中的永久区中的一部分）

![](/Users/guoqisheng/Desktop/笔记/biji/lanbda/截屏2020-05-23 下午11.44.13.png)

Jdk1.8之后，永久区取消，方法区改为元空间，使用物理内存（不再自己分配），垃圾回收机制效率提升，原PremGenSize和MaxPremGenSize参数无效，新增参数MetaSpaceSize和MaxMetaSpaceSize

### 代码更少（增加新语法lambda表达式）

### 强大的Stream API

### 便于并行

### 最大化减少空指针异常 Optional

提供了Optional容器类

## lambda表达式

### 优化过程

第一代优化

使用策略模式，新建一个接口，返回值代替if条件

新建实现类，将实现类作为参数传递

```java
package com.luoqingshang.lambda.interfaces;

public interface MyPredicate<T> {
    public boolean test(T t);
}

```

```java
package com.luoqingshang.lambda.interfaces;

import com.luoqingshang.lambda.bean.Employee;

public class FilterEmployeeByAge implements  MyPredicate<Employee>{
    @Override
    public boolean test(Employee t) {
        return t.getAge()>=35 ;
    }
}

```

```java
List<Employee> list=fifterEmployee(employees,new FilterEmployeeByAge());
```

第二代优化

使用匿名内部类省去新建实现类

```java
List<Employee> list=fifterEmployee(employees, new MyPredicate<Employee>() {
            @Override
            public boolean test(Employee employee) {
                return employee.getSalary()<5000;
            }
        });
```

第三代优化

使用lambda表达式省去匿名内部类的冗余代码

```java
List<Employee> list=fifterEmployee(employees, (e)->e.getSalary()<5000);
list.forEach(System.out::println);
```

第四代优化

使用stream api

什么都不要,直接操作数据

```java
 employees.stream()
                .filter((e)->e.getSalary()<5000)
                .forEach(System.out::println);
```

### lambda基本语法

左侧：lambda表达式的参数列表

右侧：lambda表达式所需执行的功能，即lambda体

语法格式一：无参数，无返回值

```java
()->System.out.println("Hello")
```

语法格式二：有一个参数，并且无返回值（若只有一个参数，小括号可以省略）

```java
(e)->System.out.println(e)
```

语法格式三：有两个以上的参数，有返回值，并且lambda体中有多条语句

```java
(x,y)->{
  Boolean flag=false;
  if(x>y){
    flag=true;
  }  
  return flag;
};
```

语法格式五：若lambda中只有一条语句，return和{}都可以省略不写

语法格式六：lambda表达式的参数列表的数据类型可以省略不写，因为jvm编译器可以通过上下文推断出，数据类型，即“类型推断”

### 函数式接口

lambda表达式需要函数式接口的支持

函数式接口：接口中只有一个抽象方法的接口，称为函数式接口

可以使用注解@FUnctionalInterface修饰，可以检查是否是函数式接口

## 四大核心函数式接口

Consumer<T> :消费型接口

void accept(T t);

Supplier<T> ：供给型接口

T get();

Function<T,R>:函数型接口

R apply(T t);

Predicate<T>: 断言型接口

boolean test(T t);

## 方法引用

注意：

lambda体中调用方法的参数列表与返回值类型，要与函数式接口中抽象方法的函数列表和返回值类型保持一致

若lambda参数列表中的第一个参数是实例方法的调用者，而第二个参数是实力方法的参数时，可以使用类名：：实例方法名

主要有三种语法格式

对象：：实例方法名

```java
Emp emp=new Emp();
Supplier<String> sup=emp::getName
```

类：：静态方法名

```java
Compartor<Integer> com=Integer::compare
```

类：：实例方法名

```
BiPredicate<String,String> bp2=String::equals
```

## 构造器引用

注意

需要调用的构造器的参数列表要与函数式接口中的抽象方法的参数列表一致

格式

类名：：new

```java
Supplier<Employee> sup2=Employee::new
```

## 数组引用

Type[]：：new

```java
Function<Integer,String[]> fun=String[]::new
```

## Stream API

注意：

stream自己不会存储元素

stream不会改变源数据。相反，他们会返回一个持有结果的新stream

stream操作是延迟执行的。这意味着他们会等到需要结果的时候才执行

stream的三个操作步骤

### 1创建stream

(1)可以通过Collection系列集合提供的stream()或parallelStream()

stream()是串型流

parallelStream()是并行流

```java
List<String> list=new ArrayList<>();
Stream<String> stream1=list.stream();
```

(2)通过Arrays中静态方法stream()获取数组流

```java
Employee[] emps=new Employee[10];
Stream<Employee> stream2=Arrays.stream(emps); 
```

(3) 通过Stream类中的静态方法of()

```java
List<String> list=new ArrayList<>();
Stream<String> stream3=Stream.of(list);
```

(4)创建无限流

迭代

```java
Stream<Integer> stream4=Stream.iterate(0,(x)->x+2);
stream4.forEach(System.out::println);
```

生成

```java
Stream.generate(()->Math.random());
```

### 2中间操作

#### 筛选与切片

filter——接收lambda，从流中排除某些元素

limit（n）——截断流，使其元素不超过给定量

skip（n）——跳过元素，返回一个扔掉前n个元素的流，若流中元素不足n个，则返回一个空流，与limit（n）互补

distinct（）——筛选，通过流所生成元素的hashCode（）和equals（）去除重复元素

概念

多个中间操作可以连接起来形成一个流水线，除非流水线上触发终止操作，否则中间操作不会执行任何的处理，而终止操作时一次性全部处理称为“惰性求值”。

例如

```java
employees.stream().filter((e)->{
            System.out.println("Stream API的中间操作");
            return e.getAge()>35;
        }).forEach(System.out::println);
```

输出结果

```
Stream API的中间操作
Stream API的中间操作
Employee{name='李四', age=38, salary=5555.99}
Stream API的中间操作
Employee{name='王五', age=50, salary=6666.66}
Stream API的中间操作
Stream API的中间操作
```

注意：内部迭代由StreamAPI完成

要使用distinct（）方法要到实体中重写hashCode（）和equals（）

#### 映射

map——接收lambda，将元素转换成其他形式或提取信息。接收一个函数作为参数，该函数会被应用到每个元素上，并将其映射成一个新的元素。

例如

```java
employees.stream().map(Employee::getName).forEach(System.out::println);
```

输出结果

```
张三
李四
王五
赵六
田七
```

flatMap——接收一个函数作为参数，将流中的每个值都换成另一个流，然后把所有流连接成一个流

使用map的到的结果的流的类型可能是Stream<Stream<Character>>

转换为flatMap得到的结果的流是Stream<Character>

也就是说flatMap将map中的多个流整合成了一个流

#### 排序

sorted（）——自然排序（Comparable）

sorted（Comparator com）——定制排序（Comparator ）

### 3终止操作（终端操作）

#### 查找与匹配

allMatch——检查是否匹配所有元素

anyMatch——检查是否至少匹配一个元素

noneMatch——检查是否没有匹配所有元素

findFirst——返回第一个元素

findAny——返回当前流中的任意一个元素

count——返回流中元素总个数

max——返回流中最大值

min——返回流中最小值

#### 归约

reduce（T identity(初始值)，BinaryOperator）/reduce（BinaryOperator）——可以将流中元素反复结合起来，得到一个值

有了初始值可以使用原类型接收，只要可能为空就用Optional<T>接收

例如

```java
List<Integer> list=Arrays.asList(1,2,3,4,5);
Integer sum=list.stream().reduce(5, Integer::sum);
System.out.println(sum);
Optional<Integer> su=list.stream().reduce( Integer::sum);
System.out.println(su);
```

```
20
Optional[15]
```

#### 搜集

collect——将流转换为其他形式。接收一个Collector接口实现，用于给Stream中元素做汇总的方法

```java
List<String> collect = employees.stream()
                .map(Employee::getName)
                .collect(Collectors.toList());
```

求最大值和最小值不用collect怎么做？

分组

```java
Map<Double, List<Employee>> collect2 = employees.stream()
                .collect(Collectors.groupingBy(Employee::getSalary))
```

多级分组

```java
Map<String, Map<Double, List<Employee>>> collect3 = employees.stream()
                .collect(Collectors.groupingBy(Employee::getName, Collectors.groupingBy(Employee::getSalary)));
```

分区

```java
Map<Boolean, List<Employee>> collect4 = employees.stream()
                .collect(Collectors.partitioningBy((e) -> e.getSalary() > 8000));
```

主函数

```java
DoubleSummaryStatistics collect5 = employees.stream()
                .collect(Collectors.summarizingDouble(Employee::getSalary));
        System.out.println(collect5.getMax()+ collect5.getAverage()+ collect5.getMin());
```

连接字符串

```java
String collect6 = employees.stream()
                .map(Employee::getName)
                .collect(Collectors.joining(","));
        System.out.println(collect6);

String collect6 = employees.stream()
                .map(Employee::getName)
                .collect(Collectors.joining(",","---","+++"));
        System.out.println(collect6);
```

输出结果

```
张三,李四,王五,赵六,田七

---张三,李四,王五,赵六,田七+++
```

