## 封装概述

程序设计追求 高内聚、低耦合，因此出现了封装。

-  高内聚：类的内部数据操作细节自己完成，不允许外部干涉。
-  低耦合：仅对外暴露少量的方法用于使用。

封装：隐藏对象内部的复杂性，只对外部公开简单的接口，便于外界调用，从而提高系统的可扩展性、可维护性。换言之，将该隐藏的隐藏起来，该暴露的暴露出来。

原则：对象代表什么，就得封装对应的**数据（属性）**，并提供数据对应的**行为**。

```java
public class Circle{
	double radius;// 封装对应的数据：半径
	// 与半径对应的行为->画圆，因此画圆属于"圆"类而不属于"人"类，人调用圆方法进行画圆
	public void draw(){
	system.out.println("根据半径" + radus + "画一个圆");
	}
}
```

## 四种访问权限修饰符

-  Java 权限修饰符 public、protected 、缺省 、private 置于**类的成员**定义前，用来限定对象对该类成员的访问权限。
-  对于 class 的权限修饰符只可以用 public 和 缺省

对于不同的修饰符修饰的类的成员或类，其作用域为：
| 修饰符       | 类内部 | 同一个包中 | 不同包的子类 | 同一个工程 |
|-----------|-----|-------|--------|-------|
| private   | √   |       |        |       |
| 缺省        | √   | √     |        |       |
| protected | √   | √     | √      |       |
| public    | √   | √     | √      | √     |


## private关键字

-  权限修饰符
-  可以修饰成员（成员变量和成员方法）
-  被private修饰的成员只能在**本类中**才能访问

```java
public class GirlFriend{
	// 私有属性
	private String name;
	private int age;
	private String gender;
	
	// 封装属性对应的行为
	public void setName(String n){
		name = n;
	}
	public String getName(){
		return name;
	}
	// 对属性的判断也要放在类的行为中
	public void setAge(int a){
		if(a >= 18 && a <= 50){
			age = a;
		}else{
			System.out.println("非法参数");
		}
	}
	public int getAge(){
		return age;
	}
}
```