[Java基础教程JDK8日期API解析_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1aZ4y1j78G/?spm_id_from=333.337.search-card.all.click&vd_source=796ed40051b301bfa3a84ba357f4828c)的自学笔记

[帮助文档](.\.\帮助文档\jdk api 1.8_google.CHM)

## 1. 日期类API导学

### 1.1 日期类的演变

- Java面世之初就有标准库就引入了`java.util.Date`和`java.util.Calendar`
- 从Java1.1开始,`Date`类中的所有方法就已经被弃用
- Java1.1推荐采用`Calendar`类处理日期和时间
- Calendar类同样存在不少问题
- `SimpleDateFormat`日期的转换类，可以将字符串转换为日期类，也可以将日期类转换为字符串，是线程不安全的,在多线程的情况下,全局共享一个`SimpleDateForma`t类中的`Calendar`对象有可能会出现异常
- 为了处理实际开发中遇到的问题,标准库随后引入了java.sql.Date作为java.util.Date的子类,但是还是没能彻底解决问题
- 最终JavaSE 8中引入了`java.time`包,这种全新的包从根本上解决了长久以来的存在的诸多弊端,`java.time`包基于Joda-Time库构件,是一种免费的开源解决方案

### 1.2  对于日期的计算困难问题

- Date 和 Calendar：毫秒值与日期直接转换比较繁琐,其次通过毫秒值来计算时间的差额步骤较多

````java
package com.itheima.time;
import java.util.Calendar;
import java.util.Date; 
/**
* 计算当前时间距离2000年6月1日相差了多少天. 
*
* 通过距离1970年1月1日的毫秒差值,可以计算出两个日期之间相隔的天数.
*/
public class JavaUtilTimeDemo01 { 
    public static void main(String[] args) {
        //1.初始化Date对象,无参构造(无参构造默认代表的就是当前时间)
        Date dateNow = new Date(); 
        //2.获取当前时间的距离1970年1月1日过了多少毫秒. 
        long dateTimeNow = dateNow.getTime();
        //3.初始化Calendar对象并设时间为2006年6月1日并且将Calendar对象转换 为Date对象.
        Calendar paramterTime = Calendar.getInstance(); 
        paramterTime.set(2000, Calendar.JUNE, 1);
        Date paramterDateTime = paramterTime.getTime(); 
        //4.计算paramterDateTime与dateTimeNow之间的毫秒差额.
        Long intervalTime = dateTimeNow - paramterDateTime.getTime();
        //5.对intervalTime进行计算获取差额,毫秒值/1000->/60->/60->/24 
        long intervalDay = intervalTime / 1000 / 60 / 60 / 24; System.out.println("当前时间距离2000年6月1日已经过了" + intervalDay+"天."); } }
````

### 1.3 线程安全问题

- `SimpleDateFormat`日期的转换类，可以将字符串转换为日期类，也可以将日期类转换为字符串，是线程不安全的,在多线程的情况下,全局共享一个`SimpleDateFormat`类中的`Calendar`对象有可能会出现异常
- Calendar对象是SimpleDateFormat类中的一个全局变量，当Calendar被操作其他线程中的数据就会改变，在SimpleDateFormat类中的parse()调用了clear()方法，将Calendar对象清空，其他线程就会出现问题，报错

```java
package com.itheima.time; 
import java.text.ParseException; 
import java.text.SimpleDateFormat;
public class JavaUtilTimeDemo02 { 
    //创建SimpleDateFormat的对象(单例) 
    static SimpleDateFormat SIMPLEDATEFORMAT = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss"); 
    public static void main(String[] args) {
        //创建10个线程并启动.
        for (int i = 0; i < 10; i++) { 
            new Thread(() -> {
                try {
                    System.out.println(SIMPLEDATEFORMAT.parse("2018-12-12 12:12:12")); 
                } catch (ParseException e) {
                    e.printStackTrace(); 
                } 
            }).start(); 
        }
    } 
}
```

### 1.4  在字段中使用整数常量导致整数常量都是可变的,而不是线程安全的

```java
//初始化Calendar对象 --》getInstance()
Calendar calendar = Calendar.getInstance();
//通过set方法设置年/月/日参数 2008/7/20 --》 开发规范：不允许使用没有定义的魔法数字
//   calendar.set(2008, 8, 20);//这里尽量不要用魔法数字8，这里的月份是从0开始的，8是指7月
calendar.set(2008, Calendar.AUGUST, 20);
```



##  2. Date-Time API中的基本类使用

### 2.1 常用类的概述与功能介绍

- Instant类(即时类)
  - Instant类对时间轴上的单一瞬时点建模
  - 可以用于记录应用程序中的**事件时间戳**
  - 可以在类型转换中,使用Instant类作为中间类完成转换
- Duration类(持续时间类)

  - Duration类表示秒或纳秒时间间隔,适合**处理较短的时间**,需要更高的精确性
  - 可以用于秒杀等
- Period类
  - Period类表示**一段时间**的年、月、日.
- LocalDate类

  - LocalDate是一个**不可变的日期时间对象**,表示日期,通常被视为**年月日**
- LocalTime类

  - LocalTime是一个**不可变的日期时间对象**,代表一个时间,通常被看作是**时-分-秒T纳秒**,时间表示为纳秒精度

- LocalDateTime类

  - LocalDateTime是一个**不可变的日期时间对象**,代表日期时间,通常被视为**年-月-日- 时-分-秒**.

- ZonedDateTime类

  - ZonedDateTime是**具有时区的日期时间的不可变**表示,此类存储所有日期和时间字段,精度为纳秒,时区为区域偏移量,用于处理模糊的本地日期时间。

### 2.2 now方法创建实例

> - Date-Time API中的所有类均生成**不可变实例**
> - 它们是线程**安全**的
> - 这些类**不提供公共构造函数**,也就是说没办法通过new的方式直接创建
> - 需要采用**工厂方法加以实例化**

now方法可以根据当前日期或时间创建实例

| 类            | 封装的时间                                                   | 输出对象示例                                                 |
| ------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Instant       | 祖鲁时间并非当前时间，又叫格林尼治时间,也就是国际标准时间    | 2023-02-23T08:14:18.752502200Z                               |
| LocalDate     | 年月日，格式为yyyy-MM-dd                                     | 2023-02-23Year:2023                                          |
| LocalTime     | 时分秒纳秒，格式为hh:mm:ss.sss,最后的sss是纳秒               | 16:14:18.782501600                                           |
| LocalDateTime | 年月日T时分秒纳秒                                            | LocalDateTime:2023-02-23T16:14:18.782501600                  |
| ZonedDateTime | 年月日T时分秒纳秒 以及UTC(祖鲁时间)偏移量，并有一个地区名，+8：00代表中国是东八区 | ZonedDateTime:2023-02-23T<br>16:14:18.783501700+08:00[Asia/Shanghai] |
| Year          | 年(更精准)                                                   | 2023                                                         |
| YearMonth     | 年月(更精准)                                                 | 2023-02                                                      |
| MonthDay      | 月日(更精准)                                                 | --02-23                                                      |

```java
import java.time.*;
/**
 * Date-Time API中的所有类均生成不可变实例,它们是线程安全的,并且这些类不提供公共构造函数,也就是说没办法通过new的方式直接创建,需要采用工厂方法加以实例化.
 *
 * now方法可以根据当前日期或时间创建实例.
 */
public class Java8TimeClassMethodDemo1 {
    public static void main(String[] args) {
        //使用now方法创建Instant的实例对象.祖鲁时间：国际使用一个标准的时间
        Instant instantNow = Instant.now();
        //使用now方法创建LocalDate的实例对象.
        LocalDate localDateNow = LocalDate.now();
        //使用now方法创建LocalTime的实例对象.
        LocalTime localTimeNow = LocalTime.now();
        //使用now方法创建LocalDateTime的实例对象.
        LocalDateTime localDateTimeNow = LocalDateTime.now();
        //使用now方法创建ZonedDateTime的实例对象.
        ZonedDateTime zonedDateTimeNow = ZonedDateTime.now();
        
        /*
        不仅仅是刚才提供的几个类可以使用now方法,
        Java8的Time包中还提供了其他的几个类，
        可以更精准的获取某些信息.
        */
        //使用now方法初始化Year的实例化对象.
        Year year = Year.now();
        //使用now方法初始化YearMonth的实例化对象
        YearMonth yearMonth = YearMonth.now();
        //使用now方法初始化MonthDay的实例化对象.
        MonthDay monthDay = MonthDay.now();

        //将实例对象打印到控制台.
        System.out.println("Instant:"+instantNow);//Instant:2023-02-23T08:14:18.752502200Z
        System.out.println("LocalDate:"+localDateNow);//LocalDate:2023-02-23Year:2023
        System.out.println("LocalTime:"+localTimeNow);//LocalTime:16:14:18.782501600
        System.out.println("LocalDateTime:"+localDateTimeNow);//LocalDateTime:2023-02-23T16:14:18.782501600
        System.out.println("ZonedDateTime:"+zonedDateTimeNow);//ZonedDateTime:2023-02-23T16:14:18.783501700+08:00[Asia/Shanghai]
        
        System.out.println("Year:" + year);//Year:2023
        System.out.println("YearMonth:" + yearMonth);//YearMonth:2023-02
        System.out.println("MonthDay:" + monthDay);//monthDay:--02-23
    }
}
```

### 2.3 of方法根据参数创建实例

* of方法可以根据给定的参数生成对应的日期/时间对象
* 基本上每个基本类都有of方法用于生成的对应的对象
* 重载形式多变,可以根据不同的参数生成对应的数据

> 注意:
> LocalDateTime of(LocalDate date, LocalTime time)方法可以将一个LocalDate对象和一个LocalTime对象合并封装为一个LocalDateTime对象.`LocalDateTime localDateTime = LocalDateTime.of(date, time);`

```java
import java.time.LocalDate;
import java.time.LocalDateTime;
import java.time.LocalTime;

public class Java8TimeClassMethodDemo3 {
    public static void main(String[] args) {
        //初始化2018年8月8日的LocalDate对象.
        LocalDate date = LocalDate.of(2018, 8, 8);
        System.out.println("LocalDate:" + date);

        /*
         LocalTime.of方法的重载形式有以下几种,可以根据实际情况自行使用.
         LocalTime of(int hour, int minute) -> 根据小时/分钟生成对象.
         LocalTime of(int hour, int minute, int second) -> 根据小时/分钟/秒生成对象.
         LocalTime of(int hour, int minute, int second, int nanoOfSecond) -> 根据小时/分钟/毫秒/纳秒生成对象.
         注意:如果秒和纳秒为0的话,那么默认不会封装这些数据,只显示小时和分钟.
         */

        //初始化晚上7点0分0秒的LocalTime对象.
        LocalTime time = LocalTime.of(19, 0, 0, 0);
        System.out.println("LocalTime:" + time);

        /*
        初始化2018年8月8日下午7点0分的LocalDateTime对象.
        LocalDateTime.of方法的重载形式有以下几种,可以根据事情自行使用.
        LocalDateTime of(int year, int month, int dayOfMonth, int hour, int minute, int second, int nanoOfSecond) -> 根据年/月/日/时/分/秒生成对象.
        LocalDateTime of(int year, int month, int dayOfMonth, int hour, int minute) -> 根据年/月/日/时/分生成对象.
        */
        LocalDateTime.of(2018, 8, 8, 19, 0, 0, 0);
        System.out.println("LocalDateTime:" + localDateTime);
        /*
        注意:LocalDateTime of(LocalDate date, LocalTime time)方法可以将一个LocalDate对象和一个LocalTime对象合并封装为一个LocalDateTime对象.
         */
        LocalDateTime localDateTime2 = LocalDateTime.of(date, time);
        System.out.println("LocalDateTime2:" + localDateTime);
    }
}
```

### 2.4 为LocalDateTime添加时区信息(拓展)

在学习`ZonedDateTime`的时候,发现了这个对象里面封装的不仅有时间日期,并且还有偏移量+时区

#### 2.4.1 获取时区

通过提供的一个类ZoneId的`getAvailableZoneIds`方法可以**获取**到一个Set集合,集合中封装了600个时区. 

```java
import java.time.ZoneId;
import java.util.Set;

public class Java8TimeClassMethodDemo4 {
    public static void main(String[] args) {
        //获取所有的时区信息
        Set<String> availableZoneIds = ZoneId.getAvailableZoneIds();
        for (String zoneId : availableZoneIds) {
            System.out.println(zoneId);
        }

        //获取当前系统默认的时区信息
        ZoneId zoneId = ZoneId.systemDefault();
        System.out.println(zoneId);
    }
}
```

#### 2.4.2 为LocalDateTime添加时区

> localDateTime.atZone(ZoneId.of("时区"));

```java
import java.time.LocalDateTime;
import java.time.ZoneId;
import java.time.ZonedDateTime;

/**
 * 为LocalDateTime添加时区信息.
 */
public class Java8TimeClassMethodDemo5 {
    public static void main(String[] args) {
        //1.封装LocalDateTime对象,参数自定义 -> 2018年11月11日 8点54分38秒
        LocalDateTime time = LocalDateTime.of(2018, 11, 11, 8, 54, 38);
        //2.封装完成后的time对象只是封装的是一个时间,并没有时区相关的数据,所以添加时区到对象中,使用atZone方法.
        ZonedDateTime zonedDateTime = time.atZone(ZoneId.of("Asia/Shanghai"));
        System.out.println("Asia/Shanghai的时间是:" + zonedDateTime);
        //3.更改时区查看其它时区的当前时间,通过withZoneSameInstant方法即可更改.
        ZonedDateTime otherZonedTime = zonedDateTime.withZoneSameInstant(ZoneId.of("Asia/Tokyo"));
        System.out.println("在同一时刻,Asia/Tokyo的时间是:" + otherZonedTime);
    }
}
```

### 2.5 Month枚举类的使用

java.time*包中引入了Month的枚举,Month中包含标准日历中的12个月份的常量(从JANURAY到DECEMEBER)也提供了一些方便的方法供我们使用

推荐在初始化LocalDate和LocalDateTime对象的时候,月份的参数使用枚举的方式传入,这样更简单易懂而且不易出错,因为如果是老的思维,Calendar传入0的话,那么会出现异常

```java
import java.time.LocalDateTime;
import java.time.Month;

/**
 * Month枚举类的使用.
 */
public class Java8TimeClassMethodDemo6 {
    public static void main(String[] args) {
        //在初始化LocalDate和LocalDateTime的时候,月份的参数传入枚举类(2011年5月15日11时11分11秒)
        LocalDateTime.of(2011, Month.JUNE,15,11,11,11);

        //of方法可以根据传入的数字返回对应的月份.
        Month month = Month.of(12);
        System.out.println(month);//DECEMBER
    }
}
```

## 3. 根据现有实例创建日期与时间对象

想要修改某个日期/时间对象的现有实例时,我们可以使用`plus`和`minus`方法来完成操作

Java8中日期时间相关的API中的所有**实例都是不可改变的**,一旦创建LocalDate,LocalTime,LocalDateTime就无法修改他们(类似于String),这对于线程安全非常有利

### 3.1 plus方法和minus方法日期增减

plus加，minus减，日期的增减并不是直接将现有日期改变，而是返回一个加减好了的新的日期，现有日期是不可改变的

#### 3.1.1 LoaclDate中增减操作

**LocalDate**中定义了多种对日期进行**增减**操作的方法plus加，minus减

- LocalDate plusDays(long days)增加天数
- LocalDate plusweeks( long weeks)增加周数
- LocalDate plusMonths(long months)增加月数
- LocalDate plusYears( long years)增加年数

```java
import java.time.LocalDate;
import java.time.Month;

public class Java8TimeMethodPlusDemo1 {
    public static void main(String[] args) {
        //封装LocalDate对象参数为2016年2月13日.
        LocalDate date = LocalDate.of(2016, Month.FEBRUARY, 13);
        //计算当前时间的4天后的时间.
        LocalDate plusDaysTime = date.plusDays(4);
        //计算当前时间的周后的时间.
        LocalDate plusWeeksTime = date.plusWeeks(3);
        //计算当前时间的5个月后的时间.
        LocalDate plusMonthsTime = date.plusMonths(5);
        //计算当前时间的2年后的时间.
        LocalDate plusYearsTime = date.plusYears(2);
        System.out.println("当前的时间是:"+date);
        System.out.println("4天后的时间是:"+plusDaysTime);
        System.out.println("3周后的时间是:"+plusWeeksTime);
        System.out.println("5个月后的时间是:"+plusMonthsTime);
        System.out.println("2年后的时间是:"+plusYearsTime);
    }
}
```

#### 3.1.2 LocalTime中增减操作

**LocalTime**中定义了多种对时间进行**增减**操作的方法plus加，minus减

- LocalTime plusNanos( long nanos)增加纳秒
- LocalTime plusseconds(long seconds)增加秒
- LocalTime plusMinutes(long minutes)增加分钟
- LocalTime plusHours ( long hours)增加小时

```java
import java.time.LocalTime;

public class Java8TimeMethodPlusDemo2 {
    public static void main(String[] args) {
        //封装LocalTime对象参数为8时14分39秒218纳秒.
        LocalTime time = LocalTime.of(8, 14, 39, 218);

        //计算当前时间500纳秒后的时间.
        LocalTime plusNanosTime = time.plusNanos(500);
        //计算当前时间45秒后的时间.
        LocalTime plusSecondsTime = time.plusSeconds(45);
        //计算当前时间19分钟后的时间.
        LocalTime plusMinutesTime = time.plusMinutes(19);
        //计算当前时间3小时后的时间.
        LocalTime plusHoursTime = time.plusHours(3);

        System.out.println("当前的时间是:" + time);
        System.out.println("45秒后的时间是:" + plusSecondsTime);
        System.out.println("19分钟后的时间是:" + plusMinutesTime);
        System.out.println("500纳秒后的时间是:" + plusNanosTime);
        System.out.println("3小时后的时间是:" + plusHoursTime);
    }
}
```

#### 3.1.3 日期增减一段时间

> plus加，minus减
>
> plus(TemporaAmount amountToAdd)
>
> plus(long l,TemporaUnit unit)

##### 3.1.3.1 plus(TemporaAmount amountToAdd)

TemporaAmount是一个接口,当接口作为方法的参数的时候,实际上传入的是接口的实现

类对象,根据查看这个接口的体系,可以看到这个接口有一个实现类,名字叫做Period,这个类表示一段时间.

| 语法                            | 示例                                                    | 含义                                                         |
| ------------------------------- | ------------------------------------------------------- | ------------------------------------------------------------ |
| plus(TemporaAmount amountToAdd) | Period period = Period.of(2, 3, 8);<br />a.plus(period) | 将一段时间封装为一个Period，在现有时间a的基础上加上一段period时间 |



**TemporalAmount接口实现关系:**

![TemporalAmount接口实现关系](.\MDImg\日期API-JDK8\1.1TemporalAmount接口实现关系.png)

**案例**

```java
/*
今天程序员小郝在查看自己的车辆保险记录的时候查看到还有2年3个月零8天保险就到期了,计算2年3个月零8天后的时间是多少.
 */
public class Java8TimeMethodPlusDemo4 {
    public static void main(String[] args) {
        LocalDate date = LocalDate.now(); //date表示当前时间.
        //固然可以使用对于年月日依次+2,+3,+8的方式来操作,但是有些繁琐
        //首先我们先将2年3月8天封装为一段时间,也就是封装为一个Period对象.
        Period time = Period.of(2, 3, 8);
        //使用plus方法对于date对象直接进行增加的操作.
        LocalDate endDate = date.plus(time);
        System.out.println("今天是" + date + ",保险到期的时间是" + endDate + ".");
    }
}
```

##### 3.1.3.2 plus(long l,TemporaUnit unit)

在实际开发过程中,可能还会更精准的去操作日期或者说增加一些特殊的时间,比如说1个世纪,1个半天,1千年,10年等,Java8提供了这些日期的表示方式而不需要去单独进行计算了.

TemporaUnit是一个接口,通过查看体系接口发现,可以使用子类ChronoUnit来表示,ChronoUnit封装了很多时间段供我们使用

| 语法                          | 示例                                   | 含义                          |
| ----------------------------- | -------------------------------------- | ----------------------------- |
| plus(long l,TemporaUnit unit) | marryTime.plus(1, ChronoUnit.DECADES); | 在marryTime的基础上加一个十年 |



 LocalDateTime time = marryTime.plus(1, ChronoUnit.DECADES);

**TemporaUnit接口实现关系:**

![TemporalAmount接口实现关系](.\MDImg\日期API-JDK8\1.2TemporalUnit接口实现关系.png)

接口实现类ChronoUnit封装的参数：

![ChronoUnit封装的参数](.\MDImg\日期API-JDK8\1.3 ChronoUnit封装的参数.png)

```java
import java.time.LocalDateTime;
import java.time.Month;
import java.time.temporal.ChronoUnit;

/*
结婚10年称为锡婚,2020年2月2日11点11分11秒称为对称日,很多情侣准备在那天结婚,如果在那天结婚了,那么锡婚会发生在什么时候.
 */
public class Java8TimeMethodPlusDemo5 {
    public static void main(String[] args) {
        LocalDateTime marryTime = LocalDateTime.of(2020, Month.FEBRUARY, 2, 11, 11, 11);
        //使用plus方法进行计算,添加1个,ChronoUnit.DECADES(十年).
        LocalDateTime time = marryTime.plus(1, ChronoUnit.DECADES);
        System.out.println("如果在" + marryTime + "结婚,那么锡婚是" + time);

        //如果锡婚后的半天准备要请所有亲戚朋友吃饭,那么吃饭的时间是.
        LocalDateTime eatTime = time.plus(1, ChronoUnit.HALF_DAYS);
        System.out.println("半天后吃饭,吃饭的时候是:" + eatTime);
    }
}
```

### 3.2 with 方法直接修改日期

如果不需要对日期进行加减而是要直接修改日期的话,那么可以使用with方法,with方法提供了很多种修改时间的方式.

#### 3.2.1 with 方法在LocalDateTime类的应用

方法(返回值都是LocalDateTime)：

| 方法                   | 功能     |
| ---------------------- | -------- |
| withNano(int i)        | 修改纳秒 |
| withSecond(int i)      | 修改秒   |
| withMinute(int i)      | 修改分钟 |
| withHour(int i)        | 修改小时 |
| withDayOfMoonth(int i) | 修改日   |
| withMonth(int i)       | 修改月   |
| withYear(int i)        | 修改年   |

```java
import java.time.LocalDateTime;

public class Java8TimeMethodWithDemo1 {
    public static void main(String[] args) {
        //外界传值
//        LocalDateTime time = getTime();
        //模拟外界传值
        LocalDateTime time = LocalDateTime.now();
        //经过使用发现time中的时间有错误,应该是1日,在不知道原有时间的基础上,无法进行增减操作,所以可以直接使用with方法进行修改.
        LocalDateTime endTime = time.withDayOfMonth(1);
        System.out.println("修改前错误的时间是:" + time);
        System.out.println("修改完成之后正确的时间是:" + endTime);
    }
   
    /*
    public static LocalDateTime getTime() {
        //外界传值
        return LocalDateTime.of(1999, 12, 12, 12, 0);
    }
    */
}
```

#### 3.2.2 with

| 语法                                     | 含义                             |
| ---------------------------------------- | -------------------------------- |
| with(TemporalFIeld field, long newValue) | 将日期的某一组成部分修改为某一值 |

TemporalField是一个接口,通过查看体系结构,可以使用它的子类ChronoField

ChronoField中封装了一些日期时间中的组成部分,
可以直接选择之后传入第二个参数进行修改

> eg:
>
> `with(ChronoField.DAY_OF_MONTH, 1); `将日期中的月份中的天数改为1.
>
> `with(ChronoField.YEAR, 2021); `将日期中的年份改为2021.

![TenporalField接口实现关系](.\MDImg\日期API-JDK8\1.4TenporalField接口实现关系.png)

```java
import java.time.LocalDateTime;
import java.time.temporal.ChronoField;

public class Java8TimeMethodWithDemo2 {
    public static void main(String[] args) {
        LocalDateTime time = LocalDateTime.now();
        //经过使用发现time中的时间有错误,应该是1日,在不知道原有时间的基础上,无法进行增减操作,所以可以直接使用with方法进行修改.
        LocalDateTime endTime = time.with(ChronoField.DAY_OF_MONTH,1);
        System.out.println("修改前错误的时间是:" + time);
        System.out.println("修改完成之后的时间是:" + endTime);
    }
}
```

## 4. 调节器 TemporalAdjuster 与查询 TemporalQuery

### 4.1TemporalAdjuster调节器的使用

#### 4.1.1 基础用法

有一些时候可能会做一些复杂的操作,比如说将时间调整到下个周的周日,下一个工作日,或者本月中的某一天,这个时候可以使用调节器TemporalAdjuster来更方便的处理日.

| 语法                            |
| ------------------------------- |
| with(TemporalAdjuster adjuster) |

通过查看发现TemporalAdjuster是一个接口,方法的参数是一个接口,那么实际上传入的是这个接口的实现类对象. 

一个叫做TemporalAdjusters的类可以给我们提供一些常用的方法,方法如下

> eg:
>
> `static TemporalAdjuster firsyDayOfMonth()`:下个月的第一天↓↓↓↓
>
> ```java
>     public static TemporalAdjuster firstDayOfMonth() {
>         return (temporal) -> temporal.with(DAY_OF_MONTH, 1);
>     }
> ```

![1.5TemporalAdjusters](.\MDImg\日期API-JDK8\1.5TemporalAdjusters.png)

```java
import java.time.LocalDate;
import java.time.Month;
import java.time.temporal.TemporalAdjuster;
import java.time.temporal.TemporalAdjusters;

public class Java8TimeTemporalAdjusterDemo1 {
    public static void main(String[] args) {
        //封装日期时间对象为当前时间,LocalDate.
        LocalDate time = LocalDate.now();
        /*
        with方法可以修改time对象中封装的数据,需要传入一个TemporalAdjuster对象,
        通过查看发现TemporalAdjuster是一个接口,方法的参数是一个接口,那么实际上传入的是这个接口的实现类对象.
        TemporalAdjusters的类可以给我们提供一些常用的方法.
         */

        //with方法传入了TemporalAdjuster类的实现对象,是由TemporalAdjusters类的方法实现了adjustInto方法,当前的逻辑是:将时间修改为当月的第一天.
        LocalDate firstDayOfMonth = time.with(TemporalAdjusters.firstDayOfMonth());
        //将时间修改为下个月的第一天.
        LocalDate firstDayOfNextMonth = time.with(TemporalAdjusters.firstDayOfNextMonth());
        //将时间修改为下一年的第一天.
        LocalDate firstDayOfNextYear = time.with(TemporalAdjusters.firstDayOfNextYear());
        //将时间修改为本年的第一天.
        LocalDate firstDayOfYear = time.with(TemporalAdjusters.firstDayOfYear());
        //将时间修改为本月的最后一天.
        LocalDate lastDayOfMonth = time.with(TemporalAdjusters.lastDayOfMonth());
        //将时间修改为本年的最后一天.
        LocalDate lastDayOfYear = time.with(TemporalAdjusters.lastDayOfYear());

        System.out.println("当月的第一天是:" + firstDayOfMonth);
        System.out.println("下个月的第一天是:" + firstDayOfNextMonth);
        System.out.println("下一年的第一天是:" + firstDayOfNextYear);
        System.out.println("本年的第一天是:" + firstDayOfYear);
        System.out.println("本月的最后一天是:" + lastDayOfMonth);
        System.out.println("本年的最后一天是:" + lastDayOfYear);
    }
}
```

#### 4.1.2 DayOfWeek枚举类的使用

![1.5TemporalAdjusters](.\MDImg\日期API-JDK8\1.6DayOfWeek枚举类.png)

```java
import java.time.DayOfWeek;
import java.time.LocalDate;
import java.time.temporal.TemporalAdjusters;

public class Java8TimeTemporalAdjusterDemo2 {
    public static void main(String[] args) {
        //封装日期时间对象为当前时间,LocalDate.
        LocalDate time = LocalDate.now();
        /*
        DayOfWeek是一周中星期几的枚举类,其中封装了从周一到周日.
         */
        //将当前时间修改为下一个周日
        LocalDate nextSunday = time.with(TemporalAdjusters.next(DayOfWeek.SUNDAY));
        //将当前时间修改为上一个周三
        LocalDate previousWednesday = time.with(TemporalAdjusters.previous(DayOfWeek.WEDNESDAY));
        System.out.println("下一个周日是:"+nextSunday);
        System.out.println("上一个周三是:"+previousWednesday);
    }
}
```

#### 4.1.3 自定义TemporalAdjuster调节器

源码：

````java
@FunctionalInterface
public interface TemporalAdjuster {
        Temporal adjustInto(Temporal temporal);
}
````

Temporal接口实现类

![1.5TemporalAdjusters](.\MDImg\日期API-JDK8\1.7Temporal实现类.png)

自定义调节器：

```java
import java.time.DayOfWeek;
import java.time.LocalDate;
import java.time.temporal.*;

/**
 * 假如员工一个月中领取工资,发薪日是每个月的15号,如果发薪日是周末,则调整为周五.
 */
public class PayDayAdjuster implements TemporalAdjuster {
    @Override
    public Temporal adjustInto(Temporal temporal) {
        //1.将temporal转换为子类对象LocalDate,from方法可以将任何时态对象转换为LocalDate.
        LocalDate payDay = LocalDate.from(temporal);
        //2.判断当前封装的时间中的日期是不是当月15日,如果不是,则更改为15日.
        int day;
        if (payDay.getDayOfMonth() != 15) {
            day = 15;
        } else {
            day = payDay.getDayOfMonth();
        }
        LocalDate realPayDay = payDay.withDayOfMonth(day);
        //3.判断realPayDay对象中封装的星期数是不是周六或者是周日,如果是周末或者是周日则更改为周五.
        if (realPayDay.getDayOfWeek() == DayOfWeek.SUNDAY || realPayDay.getDayOfWeek() == DayOfWeek.SATURDAY) {
            //说明发薪日是周末,则更改为上一个周五.
            realPayDay =  realPayDay.with(TemporalAdjusters.previous(DayOfWeek.FRIDAY));//previous：以前的，上一个
        }
        return realPayDay;
    }
}
```

测试类：

```java
import Ad_Java8TimeTemporalAdjuster.src.com.itheima.time.impl.PayDayAdjuster;
import java.time.DayOfWeek;
import java.time.LocalDate;
import java.time.temporal.TemporalAdjusters;

public class Java8TimeTemporalAdjusterTest1 {
    public static void main(String[] args) {
        //封装LocalDate对象为2018年12月1日.
        LocalDate payDay = LocalDate.of(2019, 12, 1);
        //2018年12月15日为周末,所以要提前到周五发放工资,通过自定义调节器完成对时间的修改.
        LocalDate realPayDay = LocalDate.from(new PayDayAdjuster().adjustInto(payDay));
        System.out.println("预计的发薪日是2018年12月15日,实际的发薪日为:" + realPayDay);
    }
}
```

### 4.2 TemporalQuery的应用

 TemporalQuery<R>是一个泛型函数式接口

源码：

```java
@FunctionalInterface
public interface TemporalQuery<R> {
        R queryFrom(TemporalAccessor temporal);

}
```



```java
import java.time.LocalDate;
import java.time.Month;
import java.time.temporal.ChronoField;
import java.time.temporal.ChronoUnit;
import java.time.temporal.TemporalAccessor;
import java.time.temporal.TemporalQuery;

/**
 * 获取某一天距离下一个劳动节的相隔天数的实现类.
 */
public class UntilDayQueryImpl implements TemporalQuery<Long> {
    @Override
    public Long queryFrom(TemporalAccessor temporal) {
        //将temporal转换为LocalDate类型的参数
        LocalDate from = LocalDate.from(temporal);

        //将获取到的数据封装为一个LocalDate对象.
        LocalDate time = LocalDate.of(year, month, day);
        //封装劳动节的时间,年参数传递year,month和day是5和1.
        LocalDate laborDay = LocalDate.of(temporal.get(ChronoField.YEAR), Month.MAY,1);
        //判断当前时间是否已经超过了当年的劳动节,如果超过了,则laborDay+1年.
        if (time.isAfter(laborDay)){
            laborDay = laborDay.plusYears(1);
        }
        //通过ChronoUnit的between方法计算两个时间点的差额.
        long l = ChronoUnit.DAYS.between(time, laborDay);
        return l;
    }
}
```

测试类：

```java
import Ad_Java8TimeTemporalAdjuster.src.com.itheima.time.impl.UntilDayQueryImpl;


import java.time.LocalDate;

public class Java8TimeTemporalQueryDemo1 {
    public static void main(String[] args) {
        //封装LocalDate对象为当前时间.
        LocalDate time = LocalDate.now();
        //调用time对象的query方法查询距离下一个五一劳动节还有多少天.
        Long l = time.query(new UntilDayQueryImpl());
        System.out.println("距离下一个五一劳动节还有:" + l + "天.");
    }
}
```

## 5. 日期格式转换

Java8中的`java.time`包中并没有提供太多的内置方式来转换`java.util`包中用预处理标准日期和时间的类

我们可以使用`Instant`类作为中介,也可以使用`java.sql.Date`和`java.sql.Timestamp`类提供的方法进行转换.

### 5.1 转换 java.util.Date

#### 5.1.1 使用 Instant 类作为中介转换 java.util.Date 为 LocalDate

java.time包中并没有提供很多的方式来进行直接转换

- 给之前的Date类,calendar类在Java1.8都提供了一个新的方法,叫做toInstant()
  ```java
  Date d = new Date();
  ```

- **toInstant()**,可以将当前对象转换为Instant对象

  ```java
  Instant i = d.toInstant();
  ```

- 通过给Instant**添加时区信息**之后就可以转换为LocalDate对象.
  ```java
  ZonedDateTime zonedDateTime = i.atZone(ZoneId.systemDefault());
  LocalDate localDate = zonedDateTime.toLocalDate();
  ```

  代码演示：

```java
import java.time.Instant;
import java.time.LocalDate;
import java.time.ZoneId;
import java.time.ZonedDateTime;
import java.util.Date;

public class Java8TimeDateToLocalDateDemo1 {
    public static void main(String[] args) {
        //初始化Date对象.
        Date d = new Date();
        //将Date类对象转换为Instant类对象.
        Instant i = d.toInstant();
        //Date类包含日期和时间信息,但是并不提供时区信息,和Instant类一样,可以通过Instant类的atZone方法添加时区信息之后进行转换.
        ZonedDateTime zonedDateTime = i.atZone(ZoneId.systemDefault());
        //将ZonedDateTime通过toLocalDate方法转换为LocalDate对象.
        LocalDate localDate = zonedDateTime.toLocalDate();
        System.out.println("转换之前的Date对象是:" + d);
        System.out.println("转换之后的LocalDate对象是:" + localDate);
    }
}
```

#### 5.1.2 java.sql.Date 作为中介转换 java.util.Date 为 LocalDate

```java
import java.time.LocalDate;

public class Java8TimeDateToLocalDateDemo4 {
    public static void main(String[] args) {
        //初始化Date对象.
        java.util.Date d = new java.util.Date();
        /*
        java.sql.Date类提供了转换为LocalDate的方法,那么可以将java.util.Date先转换为java.sql.Date.
        通过java.sql.Date的构造方法直接传入一个毫秒值可以构造一个java.sql.Date对象,毫秒值可以通过java.util.Date对象的getTime方法获取到.
         */
        //d.getTime()或取毫秒值
        java.sql.Date date = new java.sql.Date(d.getTime());
        //将java.sql.Date转化为LocalDate.
        LocalDate localDate = date.toLocalDate();
        System.out.println("转换前的java.util.Date类对象是:" + d);
        System.out.println("转换后的LocalDate类对象是:" + localDate);
    }
}
```

### 5.2  java.sql.Date 类 和 java.sql.Timestamp类的转换

#### 5.2.1 java.sql.Date 类转换为 LocalDate

- toLocalDate()
  ```java
  LocalDate localDate = date.toLocalDate();
  ```

```java
import java.time.*;
import java.sql.Date;

public class Java8TimeDateToLocalDateDemo2 {
    public static void main(String[] args) {
        //初始化java.sql.Date对象.
        Date d = new Date(System.currentTimeMillis());
        //将java.sql.Date对象通过toLocalDate方法转换为LocalDate对象.
        LocalDate localDate = d.toLocalDate();
        System.out.println("转换前的java.sql.Date对象是:" + d);
        System.out.println("转换后的LocalDate对象是:" + localDate);
    }
}
```

#### 5.2.2 java.sql.Timestamp 类转换为 LocalDateTime

- toLocalDateTime()
  ```java
  Timestamp t = new Timestamp(System.currentTimeMillis());
  LocalDateTime localDateTime = t.toLocalDateTime();
  ```

代码演示：

```java
public class Java8TimeTimestampToLocalDateDemo1 {
    public static void main(String[] args) {
        //初始化java.sql.Timestamp对象.
        Timestamp t = new Timestamp(System.currentTimeMillis());
        //将java.sql.Timestamp对象通过toLocalDateTime方法转换为LocalDateTime对象.
        LocalDateTime localDateTime = t.toLocalDateTime();
        System.out.println("转换之前的Timestamp对象是:" + t);
        System.out.println("转换之后的LocalDateTime对象是:" + localDateTime);
    }
}
```

### 5.3 java.util.Calender 类转换为 java.time.ZonedDateTime 类

1. 获取时区对象
   ```java
   TimeZone timeZone = calender.getTimeZone();
   ```

2. 获取ZoneId
   ```java
   ZoneId zoneId = timeZone.toZoneId();
   ```

3. 转换为Instant对象
   ```java
   Instant ins = cal.toInstant(),
   ```

4. 转换为ZonedDateTime对象
   ```java
   ZonedDateTime zonedDateTime = ZonedDateTime.ofInstant(ins, zoneId);
   ```

转换有点儿繁琐，可以通过编写转换工具类达到传入Date对象直接进行转换的转换或者将新的日期时间类转换为Date对象.

```java
public class Java8TimeCalendarToZonedDateTimeDemo1 {
    public static void main(String[] args) {
        //初始化Canlendar对象.
        Calendar cal = Calendar.getInstance();
        //Calendar对象自Java1.1开始提供了一个方法获取时区对象的方法,getTimeZone,要将Calendar对象转换为ZonedDateTime需要先获取到时区对象.
        //获取时区对象
        TimeZone timeZone = cal.getTimeZone();
        //从Java1.8开始TimeZone类提供了一个方法可以获取到ZonedId.
        //获取到ZonedId
        ZoneId zoneId = timeZone.toZoneId();
        //获取到zoneId之后就可以初始化ZonedDateTime对象了,ZonedDateTime类有一个ofInstant方法,可以将一个Instant对象和ZonedId对象作为参数传入构造一个ZonedDateTime对象.
        ZonedDateTime zonedDateTime = ZonedDateTime.ofInstant(cal.toInstant(), zoneId);
        System.out.println("转换前的Calendar对象是:" + cal);
        System.out.println("转换后的ZonedDateTime对象是:" + zonedDateTime);
    }
}
```

### 5.4 将 java.util.Calendar 类转换为 java.time.LocalDateTime 类

Calendar对象可以获取到年月日时分秒的信息,这些信息可以作为LocalDateTime构造方法的参数

1. 初始化Calender对象
   ```java
   Calendar cal = Calendar.getInstance();
   ```

2. 获取到年月日时分秒的信息
   ```java
   int year = cal.get(Calendar.YEAR);
   int month = cal.get(Calendar.MONTH);
   int day = cal.get(Calendar.DAY_OF_MONTH);
   int hour = cal.get(Calendar.HOUR);
   int minute = cal.get(Calendar.MINUTE);
   int second = cal.get(Calendar.SECOND);
   ```

3. 构造LocalDateTime 对象
   ```java
   LocalDateTime localDateTime = LocalDateTime.of(year, month + 1, day, hour, minute, second);
   ```

代码演示：

```java
import java.time.LocalDateTime;
import java.time.ZoneId;
import java.time.ZonedDateTime;
import java.util.Calendar;
import java.util.TimeZone;

public class Java8TimeCalendarToLocalDateTimeDemo1 {
    public static void main(String[] args) {
        //初始化Canlendar对象.
        Calendar cal = Calendar.getInstance();
        //通过Getter方法获取到Calendar对象中封装的数据.
        int year = cal.get(Calendar.YEAR);
        int month = cal.get(Calendar.MONTH);
        int day = cal.get(Calendar.DAY_OF_MONTH);
        int hour = cal.get(Calendar.HOUR);
        int minute = cal.get(Calendar.MINUTE);
        int second = cal.get(Calendar.SECOND);
        //将以上获取到的数据作为LocalDateTime的of方法的参数进行对象的封装.
        //注意Calendar获取到的月份依旧是从0开始的,需要在原有的基础上+1,如果不加1,轻则月份少算了1个月,重则出现异常．
        LocalDateTime localDateTime = LocalDateTime.of(year, month + 1, day, hour, minute, second);
        System.out.println("转换前的Calendar对象是:" + cal);
        System.out.println("转换后的LocalDateTime对象是:" + localDateTime);
    }
}
```

## 6. 日期的解析与格式化DateTimeFormatter

### 6.1 普通方式

SimpleDateFormat类在刚开始的讲过了是线程不安全的,所以Java8提供了新的格式化类DateTimeFormatter.

DateTimeFormatter类提供了大量预定义格式化器,包括常量(如ISo_LOCAL_DATE),模式字母(如yyyy-MM-dd)以及本地化样式.

与simpleDateFormat不同的是,新版本的日期/时间API的格式化与解析不需要在创建转换器对象再进行转换了,通过时间日期对象的parse/format方法可以直接进行转换.

LocalDate类定义的format方法：
```java
    @Override  // override for Javadoc and performance
    public String format(DateTimeFormatter formatter) {
        Objects.requireNonNull(formatter, "formatter");
        return formatter.format(this);
    }
```

format方法需要传入一个DateTimeFormatter对象,实际上查看DateTimeFormatter类之后,

指定DateTimeFormatter中的常量即可指定格式化方法,常用的格式化方式有ISO_DATE_TIME/ISO_DATE.

常用的格式化方式有:

![1.5TemporalAdjusters](.\MDImg\日期API-JDK8\1.8DateTimeFormatter.png)

>eg:
>
>```java
>LocalDateTime time = LocalDateTime.now();
>//格式化为字符串
>String result = time.format(DateTimeFormatter.ISO_DATE_TIME);
>//解析为LocalDateTime
>LocalDateTime localDateTime = LocalDateTime.parse(result);
>```

代码演示：

```java

import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;

public class Java8TimeFormatAndParseDemo1 {
    public static void main(String[] args) {
        //对LocalDateTime进行格式化与解析,初始化LocalDateTime对象.
        LocalDateTime time = LocalDateTime.now();

        //DateTimeFormatter类中定义了很多方式,通过常量名可以指定格式化方式.
        String result = time.format(DateTimeFormatter.ISO_DATE_TIME);
        System.out.println("ISO_DATE_TIME格式化之后的String是:" + result);

        String result1 = time.format(DateTimeFormatter.ISO_DATE);
        System.out.println("ISO_DATE格式化之后的String是:" + result1);

        //解析字符串的方式通过LocalDateTime类的静态方法parse方法传入需要解析的字符串即可.
        LocalDateTime localDateTime = LocalDateTime.parse(result);
        System.out.println("解析了字符串之后的LocalDateTime是:" + localDateTime);
    }
}
```

### 6.2 对日期进行格式化为指定格式字符串

通过DateTimeFormatter的ofLocalizedDate的方法也可以调整格式化的方式.

```java
    public static DateTimeFormatter ofLocalizedDate(FormatStyle dateStyle) {
        Objects.requireNonNull(dateStyle, "dateStyle");
        return new DateTimeFormatterBuilder().appendLocalized(dateStyle, null)
                .toFormatter(ResolverStyle.SMART, IsoChronology.INSTANCE);
    }
```

该方法需要传入一个枚举类FormatStyle对象

| 枚举项 | 含义                      |
| ------ | ------------------------- |
| Full   | 全显示 (年月日+星期)      |
| Long   | 全显示 (年月日)           |
| Medium | 缩略显示 (没有年月日汉字) |
| SHORT  | 精简显示 (精简年+月日)    |

代码演示：

```java
import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;
import java.time.format.FormatStyle;

public class Java8TimeFormatAndParseDemo2 {
    public static void main(String[] args) {
        //对LocalDateTime进行格式化与解析,初始化LocalDateTime对象.
        LocalDateTime time = LocalDateTime.now();
        //通过DateTimeFormatter的ofLocalizedDate指定解析格式也可以格式化日期
        String r1 = time.format(DateTimeFormatter.ofLocalizedDate(FormatStyle.FULL));
        String r2 = time.format(DateTimeFormatter.ofLocalizedDate(FormatStyle.LONG));
        String r3 = time.format(DateTimeFormatter.ofLocalizedDate(FormatStyle.MEDIUM));
        String r4 = time.format(DateTimeFormatter.ofLocalizedDate(FormatStyle.SHORT));
        System.out.println("FULL:"+r1);
        System.out.println("LONG:"+r2);
        System.out.println("MEDIUM:"+r3);
        System.out.println("SHORT:"+r4);
    }
}
```

### 6.3 自定义格式化器

```java
//自定义格式
String result = time.format(DateTimeFormatter.ofPattern("yyyy/MM/dd HH:mm:ss:SSS"));
```

代码演示：

```java
import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;
import java.time.format.FormatStyle;

public class Java8TimeFormatAndParseDemo3 {
    public static void main(String[] args) {
        //对LocalDateTime进行格式化与解析,初始化LocalDateTime对象.
        LocalDateTime time = LocalDateTime.now();
        //通过通过DateTimeFormatter的ofPattern方法可以自定义格式化模式.
        String result = time.format(DateTimeFormatter.ofPattern("yyyy/MM/dd HH:mm:ss:SSS"));
        System.out.println("LocalDateTime格式化前是:" + time);
        System.out.println("LocalDateTime格式化后是:" + result);
    }
}
```

