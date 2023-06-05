## 多态简介

多态：同种类型的对象，表现出的不同形态。个人认为，多态就是把不同的子类对象都当作父类对象来看待。

多态的前提条件：

-  有继承关系
-  有方法重写
-  **父类引用**指向子类对象（子类对象赋值给父类对象 `Fu f = new Zi ();` ）

```java
父类类型 变量名 = new 子类类名();
```

>多态不仅可以应用在**普通类**上，也可以应用在**抽象类和接口**上

## 绑定

当通过对象变量调用函数的时候，调用哪个函数这个事情叫做绑定

-  静态绑定：根据变量的声明类型来决定
-  动态绑定：根据变量的动态类型来决定

在成员函数中调用其他成员函数也是通过this这个对象变量来调用的
## 向上造型（_upcast_）

造型：把一个类型的对象赋给另一个类型的变量。只是把一个类型当作另一个类型看待，没有改造成另外一个类型。

向上：把子类对象当作父类对象看待（子类对象赋值给父类变量，子类拥有父类的所有成员变量和成员函数），也就是在继承关系当作“向上”了。

父类的对象不能赋值给子类的变量：

```java
Vechicle v;// 父类
Car c = new Car();// 子类
v = c;// 可以
c = v;// 编译错误
```

可以用造型（只有当v这个变量实际管理的是Car才行）：

```java
c = (Car)v;
```

在编译期间会发生**向上造型，自动类型转换**。向上造型：左边的变量的类型（父类）= 右边对象/变量的类型（子类）

-  编译时是父类对象的类型，只能调用父类中有的变量和方法，不能调用子类独有的变量和方法
-  运行时是子类对象的类型
-  自动完成的，安全的，不会出现异常

```java
Person p = new Student ();
System.out.println(p.getClass());// 运行时是子类的对象类型-Student
```

## 多态的应用

### 传参

假设有学生和老师类，在学生管理系统的注册界面我们不可能写两个方法来接收 student 和 teacher 类。

```java
public class Student extends Person{...}
public class Teacher extends Person{...}
```

```java
public void register(Person p){
	// 注册逻辑
}
```

因此我们使用 Person 类来接收这两个子类）。本质上相当于：
-  `Person p = new Student ();`
-  `Person p = new Teacher ();`

### 数组

数组元素类型声明为父类类型，实际存储的元素是子类对象。

```java
public class Student extends Person{
	@Override
	public void learn() {
        System.out.println("学生学习");
    }
}
public class Teacher extends Person{
	@Override
	public void learn() {
        System.out.println("老师学习");
    }
}
```

```java
Person[] p = new Person[2];
p[0] = new Student();
p[1] = new Teacher();

for(Person person:p){
	p.learn;// 分别打印学生学习和老师学习
}
```


