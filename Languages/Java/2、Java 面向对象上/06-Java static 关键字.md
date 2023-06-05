当我们在编写一个类的时候，其实就是在描述其对象的属性和行为，而并没有产生实质上的对象，只有通过 new 关键字才会产生出对象，这时系统才会分配内存空间给对象，其方法才可以供外部调用。我们有时候希望无论是否产生了对象或无论产生了多少个对象的情况下，**某些特定的数据在内存空间里只有一份**。

## 类属性、类方法

在 Java 中，static 可以修饰属性、方法。

### 静态属性

类属性作为该类各个对象之间的共享的变量。也就是说，该属性不随着对象的不同而改变。

-  属性不随着对象的不同而改变
-  不用创建对象就可以访问静态成员（类变量初始化是优先于对象的）

```java
public class Person {
    public static int total = 0;// 不随对象的改变而改变
    private int id;

    public Person() {// 构造函数，在创建对象时total的值会+1
        total++;
        id = total;
    }
}
```

```java
public class PersonTest {
    public static void main(String[] args) {
        System.out.println(Person.total); // 0(没有创建实例)
        // 不用创建对象就可以访问静态成员
        // 访问的方式：类名.类属性 类名.类方法
        Person.total = 100;
        System.out.println(Person.total); // 100

        Person p = new Person();
        System.out.println(p.total); // 101 
    }
}
```

### 静态方法

-  在静态方法内部只能访问类的 static 修饰的属性或方法，不能访问类的非 static 的结构（加载时机不同，类变量初始化是优先于对象的）
-  不用创建对象就可以访问静态成员（因此 static 方法内部是不能有 this 或 super 的）
-  静态方法**不能被重写**（静态方法唯一）

```java
public class Person {
    public static int total = 0;
    private final int id;

    public Person() {
        total++;
        this.id = total;
    }

    public static int getTotal() {
        return total;// 静态类只可以访问静态变量
    }
}
```

```java
public class PersonTest {
    public static void main(String[] args) {
        System.out.println(Person.total); // 0(没有创建实例)
        // 不用创建对象就可以访问静态成员
        // 访问的方式：类名.类属性 类名.类方法
        Person.total = 100;
        System.out.println(Person.total); // 100

        int total = Person.getTotal(); // 没有创建实例就可以访问静态类
        System.out.println(total); // 100

        Person p = new Person();
        System.out.println(Person.total); // 101
    }
}
```

## 单例模式

设计模式是在大量的实践中总结和理论化之后优选的**代码结构、编程风格以及解决问题的思考方式**。而单例模式是指：某个类有且仅有存在一个对象实例，并且该类只提供一个取得其对象实例的方法。

-  将类的构造器的访问权限设置为private：让类在一个虚拟机中只能产生一个对象（不能new了）
-  此时类外部不能产生对象，内部依然可以产生对象。因此类内部可以使用**静态方法创建对象**（内存唯一，因此仅有一个对象实例），外部通过调用静态方法返回类内部创建的对象

```java
public class Singleton {
	// 内部提供一个当前类的实例 此实例也必须是静态化的
    private static final Singleton singleton = new Singleton();

    // 私有化构造器
    private Singleton() {}
    
	// 提供公共的静态方法，返回当前类的对象
    public static Singleton getInstance() {
        return singleton;
    }
}
```

### 单例模式的应用场景

-  **应用程序的日志应用**，由于共享日志的文件一直处于打开状态，所以只能有一个实例去操作，否则内容不好追加。
-  **数据库连接池**，因为数据库连接是一种数据库资源。
-  **读取资源文件的类**，没有必要每次使用配置文件数据的时候，都生成一个对象去读取。

### 理解 main 方法的语法

-  由于 Java 虚拟机需要调用类的 `main()` 方法，所以该方法的访问权限必须是 `public` 。
-  因为 Java 虚拟机执行 `main()` 方法的时候不需要创建对象，所以该方法必须是 `static` 的。
-  `main()` 接收一个 String 类型的数组参数，该数组汇总保存**执行 Java 命令时传递给所运行的类的参数**。
-  因为 `main()` 方法是静态的，我们不能直接访问 `main()` 方法中的非静态成员，必须创建该类的一个实例对象后，才能通过这个对象去访问类中的非静态成员。