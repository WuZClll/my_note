# System

System类：系统类，主要用于获取系统的属性数据，没有构造方法。

- System类的常用方法

  | 方法名                                                       | 说明                                                         |
  | ------------------------------------------------------------ | ------------------------------------------------------------ |
  | public static void exit(int status)                          | 终止当前运行的 Java 虚拟机 　参数传入一个数字即可。通常传入0记为正常状态，其它为异常状态。 |
  | public static long currentTimeMillis()                       | \| 返回当前时间(以毫秒为单位)                                |
  | public static void gc()                                      | 该方法用来建议jvm赶快启动垃圾回收器回收垃圾。只是建议启动，但是Jvm是否启动又是另外一回事 |
  | public static void arraycopy(Object src, int srcPos, Object dest, int destPos, int length ) | 从指定源数组中复制一个数组，复制从指定的位置开始，到目标数组的指定位置结束 |

## 1.结束正在运行的Java程序（public staitc void exit(int status)）

示例代码1 => exit()

- 需求：结束下面的循环

```java
package com.edu1.test;
import java.util.Scanner;
public class Demo {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int i=0;
        while (true){
            System.out.println("请输入一个字符");
            String next = sc.next();
            if(next.equalsIgnoreCase("y")){
                System.exit(0);
            }else {
                System.out.println("循环第"+ ++i +"次,内容是"+next+",输入y停止");
            }
        }
    }
}
```

## 2.获取系统当前毫秒值（public static long currentTimeMillis()）

- 需求：在控制台输出1-10000，计算这段代码执行了多少毫秒

```java
public class SystemDemo {
    public static void main(String[] args) {
        // 获取开始的时间节点
        long start = System.currentTimeMillis();
        for (int i = 1; i <= 10000; i++) {
            System.out.println(i);
        }
        // 获取代码运行结束后的时间节点
        long end = System.currentTimeMillis();
        System.out.println("共耗时：" + (end - start) + "毫秒");
    }
}
```

## 3. 垃圾回收器（public static void gc()）

- 示例代码

```java
package com.edu1.test;
public class Demo {
    public static void main(String[] args) {
        new Person(1);
        new Person(2);
        new Person(3);
        new Person(4);
        new Person(5);
        System.gc();//建议马上启动垃圾回收器
    }
}
class Person {
    private int i;
    public Person(int i) {
        this.i = i;
    }
    @Override
    public void finalize() throws Throwable {
        //清除垃圾时，会默认调用被清空对象的finalize方法。
        System.out.println("第"+i+"次垃圾回收启动了" );
    }
}
//以上代码执行结果如下：(输出结果行数不一定一致)
//第5次垃圾回收启动了
//第4次垃圾回收启动了
//第3次垃圾回收启动了
//第2次垃圾回收启动了
//进程已结束，退出代码为 0
```

## 4. System类方法复制数组 ->数值扩容

实例:

```java
package com.edu1.test;
public class Demo {
    public static void main(String[] args) {
        int[] array = {1, 2, 3, 4, 5};
        int[] targetArr = new int[array.length*2];
        //　  Object src：要复制的原数组；
        //　　Int srcPos：数组源的起始索引
        //　　Object dest：复制后的目标数组；
        //　　int destPos：目标数组起始索引；
        //　　int length，指定复制的长度；
        System.arraycopy(array,0,targetArr,0,array.length);
        for (int i : targetArr) {
            System.out.println(i);
        }
    }
}
```

# BigInteger

## 1. 格式

构造方法这里列举出来三个比较常用的方法，并在下面给出实例以及输出结果便于理解，建议各位小伙伴还是自己敲一遍代码加深印象

> 注：
>
> 1. 如果BigInteger表示的数字没有超出long的范围，可以用静态方法获取。
> 2. 如果BigInteger表示的超出long的范围，可以用构造方法获取。
> 3. 对象一旦创建，BigInteger内部记录的值不能发生改变。
> 4. 只要进行计算都会产生一个新的BigInteger对象

```java
public BigInteger(int num, Random rnd) 		//获取随机大整数，范围：[0 ~ 2的num次方-1]
public BigInteger(String val) 				//获取指定的大整数，默认十进制
public BigInteger(String val, int radix) 	//获取指定进制的大整数,val：值radix：进制 值与进制必须对应

//---------------------------------------
//静态方法获取BigInteger的对象，内部有优化
//细节:
//1.能表示范围比较小，只能在1ong的取值范围之内，如果超出long的范围就不行了。
//2.在内部对常用的数字:-16 ~ 16进行了优化。
//提前把-16 ~ 16 先创建好BigInteger的对象，如果多次获取不会重新创建新的。
BigInteger bd5 = BigInteger.valueof(16);
BigInteger bd6 = BigInteger.vaLueof(16);
System.out.println(bd5==bd6);//true
BigInteger bd7 = BigInteger.valueof(17);
BigInteger bd8 = BigInteger.vaLueof(17);
System.out.println(bd7==bd8);//false
```

## 2. 实例

```java
import java.math.BigInteger;
        import java.util.Random;

public class TestBigInteger {
    public static void main(String[] args) {

        //获取随机大整数
        Random r=new Random();
        for (int i = 0; i < 5; i++) {
            BigInteger bd1 = new BigInteger(4,r);
            System.out.println(bd1);
        }
        System.out.println("----------此处为分割线----------");
       
        //获取指定的大整数
        BigInteger bd2 = new BigInteger("345678998345623567256780435093457001561080748");
        System.out.println(bd2);
        System.out.println("----------此处为分割线----------");

        //获取指定进制的大整数
        BigInteger bd3 = new BigInteger("1101010110",2);
        System.out.println(bd3);
    }
    
}
```

## 3.常用方法

```java
    BigInteger add(BigInteger val) ：返回其值为 (this + val) 的 BigInteger
    BigInteger subtract(BigInteger val) ：返回其值为 (this - val) 的 BigInteger;
    BigInteger multiply(BigInteger val) ：返回其值为 (this * val) 的 BigInteger
    BigInteger divide(BigInteger val) ：返回其值为 (this / val) 的 BigInteger。整数 相除只保留整数部分。
    BigInteger remainder(BigInteger val) ：返回其值为 (this % val) 的 BigInteger。
    BigInteger[] divideAndRemainder(BigInteger val)：返回包含 (this / val) 后跟(this % val) 的两个 BigInteger 的数组。
    BigInteger pow(int exponent) ：返回其值为 (thisexponent) 的 BigInteger。
    System.out.println(b1.add(b2));   //加法操作
    System.out.println(b1.subtract(b2));   //减法操作
    System.out.println(b1.multiply(b2));   //乘法操作
    System.out.println(b1.divide(b2));   //除法操作
    System.out.println(b1.max(b2));    //较大数
    System.out.println(b1.min(b2));    //较小数
```