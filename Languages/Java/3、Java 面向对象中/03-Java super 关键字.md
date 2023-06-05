在 Java 类中使用super来调用父类中的指定操作

-   super 可用于访问父类中定义的**属性**
-   super 可用于调用父类中定义的**成员方法**
-   super 可用于在子类构造器中调用父类的**构造器**

>当子类和父类出现同名成员的时候，可以用 super 表明调用的是父类的成员
>super 和 this 的用法很像，this 代表的是本类对象的引用，super 代表的是父类的内存空间的标识

## super 调用父类方法

```java
public class Person {
    protected String name = "张三";
    protected int age;
    public String getInfo() {
        return "姓名：" + this.name + ",年龄" + this.age;
    }
}
```

```java
public class Student extends Person {
    private final String school = "中国社会大学";
    protected String name = "李四";

    @Override
    public String getInfo() {
        this.age = 18;
        // super表示调用父类的成员方法
        return super.getInfo() + "，学校：" + this.school;
    }
}
```

## super 调用父类构造器

```java
public class Person {
    private final String name;
    private final int age;
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
    @Override
    public String toString() {
        return "name='" + this.name + '\'' + ", age=" + this.age;
    }
}
```

```java
public class Student extends Person {
    private final String school;
    public Student(String name, int age, String school) {
        super(name, age); // 调用父类的构造器Person(String name, int age)
        this.school = school;
    }
    @Override
    public String toString() {
        return super.toString() + ", school='" + this.school;
    }
}
```

## this 和 super 的区别

| 区别点   | this                            | super               |
|-------|---------------------------------|---------------------|
| 访问属性  | 访问本类中的属性，如果本类中没有找到此属性，则从父类中继续查找 | 直接访问父类中的属性          |
| 访问方法  | 访问本类中的方法，如果本类中没有找到此方法，则从父类中继续查找 | 直接访问父类中的方法          |
| 调用构造器 | 调用本类构造器，必须放在构造器的首行              | 调用父类构造器，必须放在子类构造器首行 |

