## 内部类简介

类的五大成员：属性、方法、构造方法、代码块、内部类

当一个事物的内部，还有一个部分需要一个完整的结构进行描述，而这个内部的完整的结构又只为外部事物提供服务，那么整个内部的完整结构最好使用内部类。

在 Java 中，允许一个类 A 的定义位于另一个类 B 的内部，类 A 就被称为内部类，而类 B 就被称为外部类。因为内部类在外部类的里面，因此**可以直接访问外部类的私有成员**。

```java
public class Outer{// 外部类
	public class Innor{// 内部类
	
	}
}
```

## 内部类分类

根据内部类声明的位置不同，我们可以将内部类分为如下两类：

- 成员内部类（方法外）
	- 静态成员内部类
	- 非静态成员内部类

- 局部内部类（方法内）
	- 有名字的局部内部类
	- 匿名内部类

### 静态内部类

-  外部类使用内部类的资源
	-  静态资源，可以直接通过 `内部类.资源名`，比如：`Inner.a`
	-  非静态资源，那么需要通过 `内部类的对象.资源名`，比如：`new Inner().num`
-  静态内部类可以访问外部类**静态**的资源（属性和方法）比如访问 `outerMethod2()` 
-  如果在内部类中有变量和外部类的成员变量相同，可以使用 `外部类.变量名` 进行区别，比如访问 `Outer.age` 

```java
public class Outer { // 外部类
    private static final int age = 66;
    int num = 10;

    public static void outerMethod2() {
        System.out.println("outerMethod2");
    }

    public void outerMethod1() {
        System.out.println(Inner.a);// 100
        System.out.println(new Inner().num);// 100
    }

    static class Inner { // 静态内部类
        static int a = 100;
        static int age = 100;
        int num = 100;

        public void method() {
            System.out.println(age);// 100
            System.out.println(Outer.age);// 66

            outerMethod2();// 访问静态方法
        }
    }
}
```

```java
public class Test {
    public static void main(String[] args) {
        Outer outer = new Outer();
        System.out.println("outer.num = " + outer.num);// outer.num = 10
		
        Outer.Inner inner = new Outer.Inner();
        System.out.println("inner.num = " + inner.num);// inner.num = 100
        inner.method();
    }
}
```

### 非静态内部类

-  外部类使用内部的资源：与静态内部类的使用方法一样
-  非静态内部类可以直接使用外部类的所有资源，比如访问外部类静态方法 `outerMethod2()`
-  如果在内部类中有变量和外部类的成员变量相同，可以使用 `外部类.this.变量名` 进行区别，比如`Outer.this.num`

```java
public class Outer {
    static int age = 20;
    int num = 30;

    public static void outerMethod2() {
        System.out.println("outerMethod2");
    }

    public void outerMethod1() {
        System.out.println("outerMethod1");
        System.out.println(Inner.a);
		
        Inner inner = new Inner();
        System.out.println(inner.name);
        inner.innerMethod();
    }

    class Inner {
        static final int a = 10;
        int num = 100;
        String name = "张三";

        public void innerMethod() {
            System.out.println(age);// 20
            System.out.println(Outer.this.num);// 30
            System.out.println(this.num);// 100
            outerMethod2();
        }

    }
}
```

```java
public class Test {
    public static void main(String[] args) {
        Outer outer = new Outer();
        System.out.println("outer.num = " + outer.num);// 30

        Outer.Inner inner = new Outer().new Inner();
        inner.innerMethod();
        System.out.println(inner.num);// 100
    }
}
```

### 匿名内部类

当我们在开发的过程中，需要用到一个**抽象类的子类**或**一个接口的实现类**对象，而且只创建一个对象，并且逻辑也不复杂，我们原来是怎么做的？

-  编写类，继承这个父类或实现这个接口
-  重写父类或父接口的方法
-  创建这个这个类的对象

因为考虑到子类或实现类是一次性的，那么我们费尽心思给它取名字，就显得多余，我们完全可以使用匿名内部类的方式来实现，避免给类命名的问题。

```java
class Person{...}
new Person(){...}// 没有名字的子类，其本质是继承该类或实现接口的子类（实现类）匿名对象。
```

```java
public class Test {
    public static void main(String[] args) {
        // 证明了只能在方法中使用，不就是局部内部类吗？
        Person person = new Person() {
            @Override
            public void show() {
                System.out.println("重写的show");
            }
        };
        // true 证明了person是Person的对象
        System.out.println(person instanceof Person); 
        // class com.inner.demo4.Test$1 证明了 person是内部类
        System.out.println(person.getClass()); 
        // 重写的show 证明了重写了Person类中的show方法
        person.show(); 

        Person person2 = new Person(20) {
            @Override
            public void show() {
                super.show();
            }
        };
        person2.show(); // show20
    }
}

class Person {
    int num;

    public Person() {}

    public Person(int num) {
        this.num = num;
    }

    public void show() {
        System.out.println("show" + this.num);
    }
}
```

因为匿名内部类是没有名字的，就只能是一次性的、唯一的对象，必须在声明类的同时将对象创建好，否则后期将无法创建它的对象；即声明类的同时就将对象创建好，换言之，匿名内部类既声明了一个类，也创建了一个匿名对象。

因为匿名内部类没有名字，所以创建对象的时候，就只能用父类或父接口来表示，并且表明了该匿名内部类的父类和父接口是谁。

```java
new Person(){...}
// 等价于
class 匿名子类名 extends Person{} 
```

匿名内部类的成员，**除了静态成员之外**，其他成员都可以有；但是构造器无法手动编写，只能由编译器自动产生，因为类是没有名字的，就无法编写构造器（我们知道构造器需要和类名需要一致）

### Lamda

**能够使用Lambda的依据是必须有相应的函数接口**（函数接口，是指内部只有一个抽象方法的接口）比如：

```java
// 自定义函数接口
@FunctionalInterface
public interface ConsumerInterface<T>{
	void accept(T t);
}
```

有了上述接口定义，就可以写出类似如下的代码：

```java
ConsumerInterface<String> consumer = str -> System.out.println(str);
```

实际上Lambda的类型就是对应函数接口的类型。**Lambda表达式另一个依据是类型推断机制**，在上下文信息足够的情况下，编译器可以推断出参数表的类型，而不需要显式指名。

### 匿名内部类注意事项

匿名内部类是一种特殊的局部内部类，只不过没有名字而已，所有局部内部类的限制都适用于匿名内部类，比如：

-  在匿名内部类中是否可以使用外部类的非静态成员变量，看所在方法是否静态。
-  在匿名内部类中如果需要访问当前方法的局部变量，该局部变量需要加 final（ JDK 1.8 之后，如果某个局部变量在局部内部类中被使用了，自动加 final ）