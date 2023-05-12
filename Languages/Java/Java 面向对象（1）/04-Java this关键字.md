作用：区分局部变量和成员变量
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

本质：代表方法调用者的地址值。
假如s是地址值001，那么this也是地址值001，也就是说`this.age`相当于`s.age`
