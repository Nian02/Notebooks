## 包装类概述

Java 提供了两种数据类型：基本数据类型和引用数据类型。

-  使用基本数据类型在于效率，然而要使用**只针对对象设计的 API 或新特性**（如：泛型），那么基本数据类型的数据就需要用包装类来包装了。
-  Java 针对基本数据类型定义了相应的引用类型，**这些对应的引用类型称为包装类**。
-  有了类的特点，就可以调用类中的方法，Java 才是真正的面向对象。

| 序号 | 基本数据类型  | 包装类（java.lang包） |
|----|---------|-----------------|
| 1  | byte    | Byte            |
| 2  | short   | Short           |
| 3  | int     | Integer         |
| 4  | long    | Long            |
| 5  | float   | Float           |
| 6  | double  | Double          |
| 7  | char    | Character       |
| 8  | boolean | Boolean         |
| 9  | void    | Void            |

> Byte 、Short 、Integer 、Long 、Float 、Double 的父类是 Number

```java
public class Test {
    public static void main(String[] args) {
        System.out.println("int数据类型的最小值 = " + Integer.MIN_VALUE); // -2147483648
        System.out.println("int数据类型的足最大值 = " + Integer.MAX_VALUE); // 2147483647
        // 将一个十进制的数转换为十六进制
        String s = Integer.toHexString(11);
        System.out.println("s = " + s); // b
        // 将一个十进制的数转换为八进制
        String s1 = Integer.toOctalString(11);
        System.out.println("s1 = " + s1); // 13
        // 将一个十进制的数转换为二进制
        String s2 = Integer.toBinaryString(11);
        System.out.println("s2 = " + s2); // 1011
    }
}
```

## 装箱和拆箱

在 JDK 5 之后，可以自动进行装箱和拆箱，但是需要注意的是：只能和自己对应的类型之间才能实现自动装箱和拆箱。

### 装箱

-  定义：将基本数据类型转换为包装类对象
-  目的：为了使用专门为对象设计的 API 和特性

```java
public class Test {
    public static void main(String[] args) {
        int num = 10;
        // 通过构造函数将基本数据类型转换为包装类对象
        Integer integer = new Integer(num);
        System.out.println("integer = " + integer);
        // 通过Integer.valueOf()方法将基本数据类型转换为包装类对象
        Integer integer1 = Integer.valueOf(num);
        System.out.println("integer1 = " + integer1);
    }
}
```

自动装箱：

```java
public class Test3 {
    public static void main(String[] args) {
        // 自动装箱
        Integer num = 10;
        System.out.println("num = " + num);
    }
}
```

### 拆箱

-  定义：将包装类对象转换为基本数据类型
-  目的：一般是因为需要运算，Java 中的大多数的运算符都是为基本数据类型而设计的，比如：比较、算术等

```java
public class Test2 {
    public static void main(String[] args) {
        Integer integer = new Integer(10);
        // 通过Integer对象的intValue方法，将包装类对象转换为基本数据类型
        int i = integer.intValue();
        System.out.println("i = " + i);
    }
}
```

自动拆箱：

```java
public class Test4 {
    public static void main(String[] args) {
        Integer num = 10;
        int i = num;
        System.out.println("i = " + i);
    }
}
```

## 类型转换

基本数据类型转换为字符串：

```java
String str = 5 + "";
```

```java
String str = String.valueOf(5);
```

字符串转换为基本数据类型：

```java
int i = new Integer("12");
```

```java
int i = Integer.parsetInt("12");
```

```java
int i = Integer.valueOf("12");
```

## 包装类对象的缓存问题

| 包装类       | 缓存对象         |
|-----------|--------------|
| Byte      | -128~127     |
| Short     | -128~127     |
| Integer   | -128~127     |
| Long      | -128~127     |
| Float     | 没有           |
| Double    | 没有           |
| Character | 0~127        |
| Boolean   | true 和 false |

【问】为什么要有缓存对象？如果每次自动装箱，都产生新对象，会发生什么？
【答】-128 ~ 127 范围内的数字在程序中出现的频率是非常高的，如果每次自动自动装箱，都产生新的对象，就会导致包装类对象泛滥，进而导致内存紧张。考虑到这一点，就会想着需要重复使用一些对象，所以就需要将这部分数据范围内的对象缓存起来。

>缓存对象是针对自动装箱而言的，手动装箱中的 `包装类.valueof()` 方法也使用缓存对象。
>包装类对象不可变：包装类对象一旦修改，就是指向新对象。

-  如果一个基本数据类型和一个包装类型（不论它们之间是否对应）一定是将包装类型拆箱为基本数据类型之后再按照基本数据类型的规则进行比较。
-  如果两个包装类进行比较，要求必须是同一种类型进行比较，否则将编译失败。

```java
public class Test {
    public static void main(String[] args) {

        Integer i1 = 100; // Integer.valueOf(100)
        Integer i2 = 100;
        System.out.println(i1 == i2); // true，比较的是地址，但是因为有缓存对象，所以是 true

        i1 = new Integer(100);
        i2 = new Integer(100);
        System.out.println(i1 == i2); // false，比较的是地址，缓存对象是针对自动装箱的。
        // new 是没有缓存对象的

        i1 = 400;
        i2 = 400;
        System.out.println(i1 == i2); // false，超过了缓存对象的范围。

        Double d1 = 1.0;
        Double d2 = 1.0;
        System.out.println(d1 == d2);// false 比较地址，没有缓存对象，每一个都是新new的
    }
}
```