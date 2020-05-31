

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

## 并行流和串行流

Stream()可以通过声明.parallel()和.sequential()切换并行流和串行流

```java
 Instant start=Instant.now();
        Long sum=LongStream.rangeClosed(0,1000000000L)
                .reduce(0,Long::sum);
        Instant end=Instant.now();
        System.out.println(sum+"===="+ Duration.between(start,end).toMillis());

        Instant start1=Instant.now();
        Long sum1=LongStream.rangeClosed(0,1000000000L)
                .parallel()
                .reduce(0,Long::sum);
        Instant end1=Instant.now();
        System.out.println(sum1+"===="+ Duration.between(start1,end1).toMillis());
```

结果

```
500000000500000000====336
500000000500000000====68
```

## Optional类

Optional<T>类(java.util.Optional）是一个容器类，代表一个值存在或不存在，
原来用 null表示一个值不存在，现在Optional可以更好的表达这个概念。并且
可以避免空指针异常。

### 常用方法：

Optional.of(T t)： 创建一个 Optional 实例   通过.get()方法可以拿回T对象
Optional.empty()：创建一个空的 Optional 实例
Optional.ofNullable(T t):若 t 不为 null,创建 Optional 实例,否则创建空实例
isPresent()；判断是否包含值
orElse(T t）;如果调用对象包含值，返回该值，否则返回t
orElseGet(Supplier s）：如果调用对象包含值，返回该值，否则返回 s获取的值
map(Function f）：如果有值对其处理，并返回处理后的Optional，否则返回 Optional.erptyO
flatMap(Function mapper):与 map 类似，要求返回值必须是Optional

```java
package com.luoqingshang.lambda.bean;


import java.util.Optional;

public class EmployeeFactory {
    private String name;
    private Integer age;
    private Double salary;
    private Optional<Employee> employee=Optional.empty();//可能为空的对象一定要给个初始值

    public EmployeeFactory(String name, Integer age, Double salary, Optional<Employee> employee) {
        this.name = name;
        this.age = age;
        this.salary = salary;
        this.employee = employee;
    }


    public Optional<Employee> getEmployee() {
        return employee;
    }

    public void setEmployee(Optional<Employee> employee) {
        this.employee = employee;
    }

    @Override
    public String toString() {
        return "EmployeeFactory{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", salary=" + salary +
                ", employee=" + employee +
                '}';
    }

    public EmployeeFactory() {
    }



    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

    public Double getSalary() {
        return salary;
    }

    public void setSalary(Double salary) {
        this.salary = salary;
    }


}

```



注意：可能为空的对象一定要用Opertional.empty()初始化一下，否则会爆空指针异常

Opertional.empty()生成出来的是个Opertional<Object>如果要点几层，先声明成为带泛型的参数

## 接口中的默认方法和静态方法

接口默认方法的

若一个接口中定义了一个默认方法，而另外一个父类或接口中又定义了一个同名的方法时
1.选择父类中的方法。如果一个父类提供了具体的实现，那么接口中具有相同名称和参数的默认方法会被忽略。

2接口冲突。如果一个父接口提供一个默认方法，而另一个接口也提供了一个具有相同名称和参数列表的方（不管方法是否是默认方法），那么必须覆盖该方法来解决冲突

```java
package com.luoqingshang.lambda.interfaces;

public class Function implements Fun1,Fun2{

    @Override
    public String getName() {//必须重写方法实现解决冲突
        return Fun1.super.getName();
    }
}

```

静态方法

可以直接使用接口类点静态方法了

```java
package com.luoqingshang.lambda.interfaces;

public interface Fun1 {
    default String getName(){
        return "哈哈哈";
    }

    static void show(){
        System.out.println("这是静态方法");
    }
}
```

## 新时间日期API

相对于老版的时间是线程安全的

新建时间格式可以使用常量，也可以自己指定

```
DateTimeFormatter dtf=DateTimeFormatter.ofPattern("yyyy-MM-dd");
DateTimeFormatter dtf=DateTimeFormatter.ISO_DATE;

LocalDate s=LocalDate.parse("2020-10-18",dtf);
System.out.println(s);
```

LocalDate、LocalTime、LocalDateTime 类的实例是不可变的对象，分别表示使用 ISO-8601日历系统的日期、时间、日期和时间。它们提供了简单的日期或时间，并不包含当前的时间信息。也不包含与时区相关的信息。
注：ISO-8601日历系统是国际标准化组织制定的现代公民的日期和时间的表示法

方法除了还有plus系列还有minus系列，还有get系列

```java
LocalDateTime localDateTime=LocalDateTime.now();
System.out.println(localDateTime);

LocalDateTime ldt = LocalDateTime.of(2020, 10, 25, 13, 22, 33);
System.out.println(ldt);

LocalDateTime ldt2 = ldt.plusYears(2);
System.out.println(ldt2);
```

运行结果

```
2020-05-30T21:53:52.156

2020-10-25T13:22:33

2022-10-25T13:22:33
```

 时间戳获得（unix元年（1970-01-01 00:00:00）到现在的毫秒值）

```
Instant now = Instant.now();//默认获取UTC时区
OffsetDateTime offsetDateTime = now.atOffset(ZoneOffset.ofHours(8));//设置偏移量为正8区
OffsetDateTime offsetDateTime = now.atOffset(ZoneOffset.ofHours(-8));//设置偏移量为负8区
System.out.println(offsetDateTime.toEpochSecond());//获取秒级的时间戳
Instant instant = Instant.ofEpochMilli(1000);//相较于unix元年做时间戳相加
System.out.println(instant);
```

计算两个时间相差的时间用Duration

计算两个日期相差的时间用Period

### 时间矫正器

emporalAdjuster：时间校正器。有时我们可能需要获取例如：将日期调整到“下个周日”等操作。
TemporalAdjusters：该类通过静态方法提供了大量的常用TemporalAdjuster的实现。

```java
LocalDate nextSunday = LocalDate.now().with(
  TemporalAdjusters.next(DayOfWeek.SUNDAY)
);
```

可以实现TemporalAdjuster接口自定义矫正器，或直接写lambda表达式

### 时区操作

```java
Set<String> set=ZoneId.getAvailableZoneIds();//获取时区名
set.forEach(System.out::println);
```

```java
LocalDateTime ldt11=LocalDateTime.now(ZoneId.of("Europe/Monaco"));
System.out.println(ldt11);
ZonedDateTime ldt12 = ldt11.atZone(ZoneId.of("Europe/Monaco"));
System.out.println(ldt12);
```

## 重复注解与类型注解

注解

```java
package com.example.web.annotation;

import java.lang.annotation.Repeatable;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

import static java.lang.annotation.ElementType.*;
import static java.lang.annotation.ElementType.LOCAL_VARIABLE;


@Repeatable(MyAnnotations.class)//要想被重复注解，必须加注解Repeatable
@Target({TYPE, FIELD, METHOD, PARAMETER, CONSTRUCTOR, LOCAL_VARIABLE})
@Retention(RetentionPolicy.RUNTIME)
public @interface MyAnnotation {

    String value() default "luoqingshang";

}
```

重复注解的容器

```java
package com.example.web.annotation;

import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

import static java.lang.annotation.ElementType.*;
import static java.lang.annotation.ElementType.LOCAL_VARIABLE;

@Target({TYPE, FIELD, METHOD, PARAMETER, CONSTRUCTOR, LOCAL_VARIABLE})
@Retention(RetentionPolicy.RUNTIME)
public @interface MyAnnotations {

    MyAnnotation[] value();
}
```

```java
package com.example.web;

import com.example.web.annotation.MyAnnotation;
import com.example.web.annotation.MyAnnotations;
import org.junit.jupiter.api.Test;

import java.lang.reflect.Method;

public class TestAnnotation {


    @Test
    public void test1() throws Exception{
        Class<TestAnnotation> clazz=TestAnnotation.class;//利用java反射
        Method m1=clazz.getMethod("show");
        MyAnnotation[] mas = m1.getAnnotationsByType(MyAnnotation.class);
        for(MyAnnotation myAnnotation:mas){
            System.out.println(myAnnotation.value());
        }

    }

    @MyAnnotation("hello")
    @MyAnnotation("world")
    public void show(){

    }
}
```

类型注解

```
package com.example.web.annotation;

import java.lang.annotation.Repeatable;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

import static java.lang.annotation.ElementType.*;
import static java.lang.annotation.ElementType.LOCAL_VARIABLE;


@Repeatable(MyAnnotations.class)
@Target({TYPE, FIELD, METHOD, PARAMETER, CONSTRUCTOR, LOCAL_VARIABLE,TYPE_PARAMETER})//加一个参数为TYPE_PARAMETER实现类型注解
@Retention(RetentionPolicy.RUNTIME)
public @interface MyAnnotation {

    String value() default "luoqingshang";

}
```

```java
package com.example.web;

import com.example.web.annotation.MyAnnotation;
import com.example.web.annotation.MyAnnotations;
import org.junit.jupiter.api.Test;

import java.lang.reflect.Method;

public class TestAnnotation {


    @Test
    public void test1() throws Exception{
        Class<TestAnnotation> clazz=TestAnnotation.class;//利用java反射
        Method m1=clazz.getMethod("show");
        MyAnnotation[] mas = m1.getAnnotationsByType(MyAnnotation.class);
        for(MyAnnotation myAnnotation:mas){
            System.out.println(myAnnotation.value());
        }

    }

    @MyAnnotation("hello")
    @MyAnnotation("world")
    public void show(@MyAnnotation("abc") String str){//此处用注解注释类型

    }
}

```

