## 基本数据类型
变量当中存储的是**数据值**。比如四类八种。方法传递基本数据类型时，传递的是真实的数据，形参的改变不影响实际参数的值。
```java
int a = 10;
int b = a;

public static void change(int number){...}
```

-   byte/8：-128~127
-   char/16
-   short/16
-   int/32
-   float/32：数据值的末尾添加F(Java 不能隐式执行向下转型，因为这会使得精度降低。1.1是doouble类型)
-   long/64：数据值的末尾添加L
-   double/64
-   boolean/~
boolean 只有两个值：true、false，可以使用 1 bit 来存储，但是具体大小没有明确规定。JVM （_Java Virtual Machine_）会在编译时期将 boolean 类型的数据转换为 int，使用 1 来表示 true，0 表示 false。JVM 支持 boolean 数组，但是是通过读写 byte 数组来实现的。

## 引用数据类型
变量当中存储的是**地址值**。比如对象、数组。方法传递引用数据类型时，传递的是数据的地址值，形参的改变影响实际参数的值。
>引用：使用了其他空间中的数据
```java
int[] arr1 = {1,2,3,4,5};
int[] arr2 = arr1;

public static void change(int[] arr){...}
```
## 包装类型
基本类型都有对应的包装类型，基本类型与其对应的包装类型之间的赋值使用自动装箱与拆箱完成。
```java
Integer x = 2;     // 装箱 调用了 Integer.valueOf(2)
int y = x;         // 拆箱 调用了 X.intValue()
```

## 缓存池
在 Java 8 中，Integer 缓存池的大小默认为 -128~127。
基本类型对应的缓冲池如下：

-   boolean values true and false
-   all byte values
-   short values between -128 and 127
-   int values between -128 and 127
-   char in the range \u0000 to \u007F
在使用这些基本类型对应的包装类型时，如果该数值范围在缓冲池范围内，就可以直接使用缓冲池中的对象。

new Integer(123) 与 Integer.valueOf(123) 的区别在于：

-   new Integer(123) 每次都会新建一个对象；
-   Integer.valueOf(123) 会使用缓存池中的对象，多次调用会取得同一个对象的引用。
```Java
Integer x = new Integer(123);
Integer y = new Integer(123);
System.out.println(x == y);    // false

// 等价于 Interger z = 123; Interger k = 123;
Integer z = Integer.valueOf(123);
Integer k = Integer.valueOf(123);
System.out.println(z == k);   // true
```

编译器会在自动装箱过程调用 valueOf() 方法，因此多个值相同且值在缓存池范围内的 Integer 实例使用自动装箱来创建，那么就会引用相同的对象。
```java
Integer m = 123;
Integer n = 123;
System.out.println(m == n); // true
```

valueOf() 方法的实现比较简单，就是先判断值是否在缓存池中，如果在的话就直接返回缓存池的内容。
```java
public static Integer valueOf(int i) {
    if (i >= IntegerCache.low && i <= IntegerCache.high)
        return IntegerCache.cache[i + (-IntegerCache.low)];
    return new Integer(i);
}
```

