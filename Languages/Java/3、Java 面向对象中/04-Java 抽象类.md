## 抽象类简介

类的设计应该保证父类和子类能够共享特征。这些公共特征应该抽取到一个公共的父类中，而这些方法在父类中又无法给出具体的实现，而是应该交给子类各自去实现。

在父类中声明这些方法的时候，就只有方法签名，没有方法体，我们将这些**没有方法体的方法称为抽象方法**。包含抽象方法的类称为**抽象类**。

>抽象类和继承很像，只是抽象类定义了方法名而没有其实现

```java
public abstract class Animal {// 抽象类
    public abstract void eat();// 抽象方法(只有方法签名，没有方法体)
}
```

```java
public class Cat extends Animal {
    @Override
    public void eat() {
        System.out.println("猫吃老鼠");
    }
}

public class Dog extends Animal {
    @Override
    public void eat() {
        System.out.println("狗吃肉");
    }
}
```

## 注意事项

-  抽象类不能创建对象，如果创建，编译将无法通过。只能创建其非抽象子类的对象

```java
public class Test {
    public static void main(String[] args) {
        Dog dog = new Dog();
        dog.eat(); // 狗吃肉

        Cat cat = new Cat();
        cat.eat(); // 猫吃老鼠
    }
}
```

-  抽象类中，也有构造方法，是供子类创建对象时，初始化父类成员变量使用的
子类的构造方法中有默认的 super() 或手动的 super(实参列表) ，需要访问父类的构造方法。

-  抽象类中，不一定包含抽象方法，但是有抽象方法的类一定属于抽象类
没有包含抽象方法的抽象类，目的就是不想让调用者创建该类对象，通常用于某些特殊的类结构设计。

-  抽象类的子类，必须重写抽象父类的所有抽象方法；否则，编译无法通过。除非，该子类也是抽象类。
不重写所有的抽象方法，则类中可能包含抽象方法，调用抽象的方法，没有意义。
