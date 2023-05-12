## 封装
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

## private关键字
- 权限修饰符
- 可以修饰成员（成员变量和成员方法）
- 被private修饰的成员只能在**本类中**才能访问
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