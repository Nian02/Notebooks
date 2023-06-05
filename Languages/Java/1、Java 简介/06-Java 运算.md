## 参数传递
Java 的参数是以**值传递**（_PassbyValue_）的形式传入方法中，而不是引用传递。在将一个参数传入一个方法时，本质上是将**对象的地址**以值的方式传递到形参中。

在方法中改变对象的字段值会改变原对象该字段值，因为引用的是同一个对象。
```java
class PassByValueExample {
    public static void main(String[] args) {
        Dog dog = new Dog("A");//dog 是一个指针，存储的是对象的地址。
        func(dog);
        System.out.println(dog.getName());//getName: return this.name; B
    }

    private static void func(Dog dog) {
        dog.setName("B");//setName: this.name = name;
    }
}
```

在方法中将指针引用了其它对象，那么此时改变对象的字段值不会改变原对象该字段值(两个指针指向不同对象)
```java
public class PassByValueExample {
    public static void main(String[] args) {
        Dog dog = new Dog("A");
        // getObjectAddress: return super.toString();
        System.out.println(dog.getObjectAddress()); // Dog@4554617c
        func(dog);
        System.out.println(dog.getObjectAddress()); // Dog@4554617c
        System.out.println(dog.getName());          // A（未改变）
    }

    private static void func(Dog dog) {
        System.out.println(dog.getObjectAddress()); // Dog@4554617c
        dog = new Dog("B");// dog指针指向另一个对象
        System.out.println(dog.getObjectAddress()); // Dog@74a14482
        System.out.println(dog.getName());          // B
    }
}
```

## 隐式类型转换
- 自动类型提升：取值范围小的->取值范围大的
- byte、short、char三种类型的数据在运算时都会提升为int，再运算
```java
int a = 10;
double b = a;// double类型 10.0

byte a = 10;
byte b = 20;
c = a + b;// int类型
```

## 强制类型转换
 += 或者 ++ 运算符会执行隐式类型转换。
```java
short s1 = 1;
s1 += 1;
s1++;
// 等价于进行了向下转型，强制类型转换
s1 = (short) (s1 + 1);
```

## 运算符
运算符：对字面量或变量进行操作的符号
表达式：用运算符把字面量、变量连接起来，复合Java语法的句子就可以称为表达式
```java
int a = 10;
int b = 20;
int c = a + b;
```
“+”是运算符（_算术运算符_） “a+b”是表达式（_算术表达式_）

### 逻辑运算符
| 符号 | 作用   | 说明        |
|----|------|-----------|
| &  | 逻辑与  | 同真为真      |
| \| | 逻辑或  | 有真为真      |
| ^  | 逻辑异或 | 相同为假，不同为真 |
| ！  | 逻辑非  | 取反        |

### 短路运算符
| 符号 | 作业  | 说明             |
|----|-----|----------------|
| && | 短路与 | 结果与&相同，但是有短路效果 |
| \|\| | 短路或 | 结果与\|相同，但是有短路效果 |
>短路效果：比如短路与，若判断第一个为假，就不用判断第二个了

## Java switch
```java
String s = "a";
switch (s) {
    case "a":
        System.out.println("aaa");
        break;
    case "b":
        System.out.println("bbb");
        break;
    default:
	    break;
}
```

- 表达式：将要匹配的值。取值为byte、short、int、char。JDK5之后可以是枚举，JDK7之后可以是String。
- case：被匹配的值。case后面的值只能是字面量，不能是常量，且不能重复。且case后面可以跟多个值：
```java
case 1,2,3:
```

JDK12 switch新特性：
```java
case "a" -> System.out.println("aaa");
```
