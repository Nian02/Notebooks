# 异常处理的方式

-  try catch
-  throw 和 throws

## 抛出异常 throw

Java 程序的执行过程中如果出现了异常，会产生一个**异常类对象**，该异常对象将被提交给 JVM ，这个过程称为**抛出异常** 。

异常对象的生成：

-  由 JVM自动生成：程序运行结果中，JVM 检测到程序发生了问题，如果在当前代码中没有找到相应的处理程序，就会在后台自动创建一个对应的异常类的实例对象并抛出（自动抛出）。
-  由开发人员手动抛出：由开发人员通过 `throw new Exception对象` 或 `throw new Exception的子类对象` 手动抛出异常。

手动抛出异常的场景：**在定义方法的时候**，方法需要接收参数，当调用方法使用接收到的参数的时候，需要先对参数数据进行合法性的校验，数据如果不合法，就应该告诉调用者，这时我们可以通过抛出异常的方式来告诉调用者。

>throw 关键字必须用在方法内，它用来抛出一个异常对象，将这个异常传递给方法的调用者，并结束当前方法的执行。

手动抛出异常的步骤：

-  创建一个异常对象。
-  通过 `throw new 异常对象` ，将异常告诉给调用者。

```java
thorw new 异常类名(实参);
```

```java
public class TestException {
    public static void main(String[] args) {
        int[] arr = {1, 2, 3};

        int element = getElement(arr, 2);
        System.out.println("element = " + element);// element = 3

        /*
        Exception in thread "main" java.lang.ArrayIndexOutOfBoundsException: 数组索引越界
	at com.szu.zx.TestException.getElement(TestException.java:26)
	at com.szu.zx.TestException.main(TestException.java:15)
         */
        int e = getElement(arr, 3); 
        System.out.println("e = " + e);

        System.out.println("main方法结束");
    }

    public static int getElement(int[] arr, int index) {
        if (null == arr) {
            throw new NullPointerException("要访问的arr数组不存在");
        }
        if (index < 0 || index > arr.length - 1) {
            throw new ArrayIndexOutOfBoundsException("数组索引越界");
        }
        return arr[index];
    }
}
```

## 声明异常 throws

声明异常：如果方法中可能抛出异常，就要在头部加以声明，提醒该方法的调用者来处理异常。所有调用该方法的方法也要做好准备，比如 `main()` 方法。

```java
import java.io.File;
import java.io.FileNotFoundException;

public class TestException {
    public static void main(String[] args) throws FileNotFoundException {
        readFile("java秘籍");
    }

    public static void readFile(String filePath) throws FileNotFoundException {
        File file = new File(filePath);
        if (!file.exists()) {
            throw new FileNotFoundException(filePath + "文件不存在");
        }
    }
}
```

## try catch

按顺序对catch内的异常进行检索，若匹配则执行catch的程序体。抛出子类异常会被捕捉父类异常的catch给捉到。

```java
class OpenException extends Exception{}
class CloseException extends OpenException{}

public class ArrayIndex{
	public static int open(){
		return -1;
	}
		// 可以声明更多的异常
		public static void readfile() throws OpenException, CloseException{
			if(open() == -1){
				throw new CloseException;// 抛出子类异常
			}
		}
		public static void main(String[] args){
			try{
				readfile();
			}catch(OpenException){
				e.printStackTrace();
				System.out.println("Open");// 捕捉父类异常的catch给捉到，打印Open
			}
		}
}
```

## 异常与继承

-  子类的方法不能抛出比父类更多的异常（可能把子类对象赋值给父类对象，也就是向上转型，假如通过父类的变量调用子类的方法时，子类方法抛出的异常多了，那么本来能够处理父类方法异常的代码在处理子类方法异常时就会报错）因此子类的方法可以不抛出异常。
-  子类的构造器不能抛出比父类更少的异常（构造过程会先调用父类构造，因此父类的构造的所有异常都需要声明）