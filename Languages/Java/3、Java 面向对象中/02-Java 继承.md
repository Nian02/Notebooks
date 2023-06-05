## 继承概述

![](../../../Image/Java/Java%20继承猫狗子类.png)

多个类中存在相同属性和行为的时候，将这些内容抽取到单独的一个类中，那么多个类中无需再定义这些属性和行为，只需要继承那个类即可。

![](../../../Image/Java/Java%20继承猫狗父类.png)

```java
public class Animal {
    String name;
    int age;
    
    public void eat() {
        System.out.println(this.name + "在吃饭，年龄是：" + this.age);
    }
}
```

```java
public class Cat extends Animal {
    public void catchMouse() {
        System.out.println("抓老鼠");
    }

public class Dog extends Animal {
    public void lookHome() {
        System.out.println("看家护院");
    }
```

## 继承的优点

-  减少了代码的冗余（重复），提供了代码的复用性
-  有利于功能的扩展
-  让类和类之间产生了关系，提供了多态的前提

## 成员变量继承

-  父类的成员，无论是公有（ public ）还是私有（ private ），均为被子类所继承。
-  子类虽然会继承父类私有的（ private ）的成员，但是子类不能对继承的私有成员直接进行访问，却可以通过继承的 setter 和 getter 方法进行范访问。
-  父类和子类成员变量重名时
	-  父类的成员变量 private：在子类中是无法直接访问的，所以是否重名没有任何影响
	-  父类的成员变量非 private：要加上 `super.父类成员变量名` 来进行区分

## 成员方法继承

### 方法重写（_override_）

在子类中可以根据需要对从父类中继承而来的方法进行改造 。在程序执行的时候，子类的方法将覆盖父类的方法。

-  子类重写的方法必须和父类被重写的方法具有相同的**方法名称、参数列表**
-  子类不能重写父类中声明为 private 权限的方法或 final 修饰的方法
-  子类和父类中同名同参数的方法必须同时声明为非 static的，或者同时声明为 static（不是重写，因为 static 方法是属于类的，子类无法覆盖父类的方法。

```java
	public class Phone {
    public void sendMessage() {
        System.out.println("发送短信");
    }

    public void call() {
        System.out.println("打电话");
    }

    public void showNum() {
        System.out.println("显示来电号码");
    }
}

public class SmartPhone extends Phone {
    public void showNum() {
        // super.父类成员方法，表示调用父类的成员方法。
        super.showNum();
        // 增加自己的显示姓名和图片功能
        System.out.println("显示来电姓名");
        System.out.println("显示头像");
    }
}
```

```java
public class SmartPhoneTest {
    public static void main(String[] args) {
        SmartPhone smartPhone = new SmartPhone();
        // 调用父类继承而来的方法
        smartPhone.call();
        // 调用子类重写的方法
        smartPhone.showNum();
    }
}
```

### 方法重载（_overload_）

```java
public class Father {
    public int max(int a, int b) {
        return a > b ? a : b;
    }
}
```

```java
public class Son extends Father {
    public double max(double a, double b) {
        return a > b ? a : b;
    }
}
```

## 单继承

-  Java 只支持单继承，不支持多继承。

```java
// 一个类只能有一个父类
class C extends A{}
```
-  一个父类可以同时有多个子类

```java
// 一个父类可以有多个子类
class B extends A{}
class C extends A{}
```
-  Java 支持多层继承。

```java
// A是B的父类，B是C的父类
class B extends A{}
class C extends B{}
```

## 构造方法

-  构造方法的名称和类名是一致的
-  构造器不声明返回值类型（和声明 void 不同）
-  构造器不能被 static 、final 、synchronized 、abstract 、native 修饰，不能有 return 语句返回值

所以，子类是无法继承父类的构造方法的。

子类在初始化的过程中，必须先执行父类的初始化动作（子类的构造方法中默认有一个 `super()` ，表示调用父类的实例初始化方法，父类成员变量初始化后，才可以给子类使用），才能执行自己的初始化动作。

