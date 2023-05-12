## Java API
**API**（_Application Programming Interface_）：目前是JDK中提供的各种功能的Java类
API帮助文档：帮助开发人员更好的使用API和查询API的一个工具
1. 打开API帮助文档
2. 打开显示，并找到索引下面的输入
3. 在输入框中输入类名并点击显示
4. 查看类所在的包
5. 查看类的描述
6. 查看构造方法
7. 查看成员方法

## String概述
java.lang.String类代表字符串，Java程序中所有的字符串文字（例如"abc"）都为此类对象。

String 被声明为 final，因此它不可被继承。(Integer 等包装类也不能被继承）
在 Java 8 中，String 内部使用 char 数组存储数据。
```java
public final class String
    implements java.io.Serializable, Comparable<String>, CharSequence {
    /** The value is used for character storage. */
    private final char value[];
}
```

在 Java 9 之后，String 类的实现改用 byte 数组存储字符串，同时使用 `coder` 来标识使用了哪种编码。
```java
public final class String
    implements java.io.Serializable, Comparable<String>, CharSequence {
    /** The value is used for character storage. */
    private final byte[] value;

    /** The identifier of the encoding used to encode the bytes in {@code value}. */
    private final byte coder;
}
```

value 数组被声明为 final，这意味着 value 数组初始化之后就不能再引用其它数组。并且 String 内部没有改变 value 数组的方法，因此可以保证 **String 不可变**。
```java
String name = "张三";
name = "李四";// 此时有两个字符串对象
```

## String不可变
**1. 可以缓存 hash 值**
因为 String 的 hash 值经常被使用，例如 String 用做 HashMap 的 key。不可变的特性可以使得 hash 值也不可变，因此只需要进行一次计算。

**2. String Pool 的需要**
如果一个 String 对象已经被创建过了，那么就会从 内存池（_String Pool_）中取得引用。内存池一开始在方法区，JDK8之后位于堆区。只有 String 是不可变的，才可能使用 String Pool。
![](../../../Image/Java/Java%20StringPool.png)

注意**直接赋值**的方法创建对象才会从内存池中取出，用new方法创建对象会直接在new区创建。
```java
char[] chs = {'a', 'b', 'c'};
String s1 = new String(chs);
String s2 = new String(chs);// 两者地址值不一样
```
**3. 安全性**
String 经常作为参数，String 不可变性可以保证参数不可变。例如在作为网络连接参数的情况下如果 String 是可变的，那么在网络连接过程中，String 被改变，改变 String 的那一方以为现在连接的是其它主机，而实际情况却不一定是。

**4. 线程安全**
String 不可变性天生具备线程安全，可以在多个线程中安全地使用。

## new String("abc")
使用这种方式一共会创建两个字符串对象（前提是 String Pool 中还没有 "abc" 字符串对象）。

-   "abc" 属于字符串字面量，因此编译时期会在 String Pool 中创建一个字符串对象，指向这个 "abc" 字符串字面量；
-   而使用 new 的方式会在堆中创建一个字符串对象
>因此`String s1 = new String("abc");`和`String s2 = "abc"`的变量地址值不同，`s1 == s2`的结果是false

## 创建String类对象
- 使用直接赋值的方法获取一个字符串对象`String s1 = "abc"`
- 使用new方法获取一个字符串对象
	- 空参构造获得一个空的字符串对象`String s2 = new String()`
	- 传递**字符数组**（_char_）创建一个字符串对象`char[] chs = {'a', 'b', 'c'}; String s3 = new String(chs);// abc` 因此假如我要修改字符串的内容：`abc->{'a', 'b', 'c'}->{'Q', 'b', 'c'}->Qbc`
	- 传递**字符数组**（_byte_）创建一个字符串对象`byte[] bytes = {'97', '98', '99'}; String s4 = new String(bytes);// abc`根据ASCII码表查询字符

## String比较
- boolean equals方法(比较的字符串) 完全一样为true
- boolean equalsIgnoreCase方法(比较的字符串) 忽略大小写的比较