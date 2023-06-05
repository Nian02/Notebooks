-  作用：区分局部变量和成员变量
-  使用 this 访问属性和方法时，如果在本类中没有找到，会从父类中查找

```java
public class Student{
	private int age;
	public void method(){
		int age = 10;
		System.out.println(age); // 局部变量：10
		System.out.println(this.age); // 成员变量：0
	}
}
```

```java
public class StudentTest{
	public static void main(String[] args){
		Student s = new Student();
		s.method();
	}
}
```

本质：代表**方法调用者的地址值**。
假如s是地址值001，那么this也是地址值001，也就是说`this.age`相当于`s.age`
