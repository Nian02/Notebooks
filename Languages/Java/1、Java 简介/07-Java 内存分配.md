## 内存图

- 栈：**方法运行**时使用的内存（方法被调用后就会进栈执行），比如main开始执行时会进栈，代码执行完毕会出栈。**方法里定义的变量**也是在这里
- 堆：存储**对象**或者**数组**，**new**创建的都存储在堆区，堆开辟的空间都有地址值
- 方法区：存储可以运行的class文件。**字节码文件**如`HelloWorld.class`加载时进入的内存
- 本地方法栈：JVM在使用操作系统功能的时候使用，和开发无关
- 寄存器：给CPU使用，与开发无关

JDK8时取消方法区新增元空间。将原来方法区的功能拆分到堆和元空间中

## 创建对象的内存图

```java
public class Student{
	String name;//显示初始化：String name = "张三";
	int age;
	
	public void study(){
		System.out.println("好好学习");
	}
}
```

```java
public class TestStudent{
	public static void main(String[] args){
		student s = new Student();
		System.out.println(s);
		
		s.study();// 调用study方法，从堆中找到成员方法的地址，导入栈区
	}
}
```

1. 加载class文件，`Student.class、TestStudent.class`传入**方法区**
2. 申明局部变量s（等号左边）放在**栈区**
3. 堆空间中开辟一个空间（等号右边）放在**堆区**
4. 默认**初始化**，name赋值为null，age赋值为0
5. 显示**初始化**，属性等号赋值
6. 构造方法**初始化**，若使用有参构造函数初始化，则会在构造方法初始化时给属性赋值
7. 将堆内存中的地址值赋值给左边的局部变量

![](../../../Image/Java/Java%20单对象内存.png)

