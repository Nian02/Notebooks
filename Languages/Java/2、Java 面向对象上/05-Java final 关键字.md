在 Java 中声明 类 、变量和方法时，可以使用关键字final来修饰。
-  final修饰的类不能被继承，目的是为了提高安全性，提高程序的可读性。比如：`String` 、`System` 、`StringBuilder` 
-  final修饰的方法不能被子类重写
-  final修饰的变量（成员变量或局部变量）即为**常量**。名称大写，且只能被赋值一次。final修饰的成员变量必须在声明时或每个构造器中或代码块中**显式赋值**，然后才能使用

```java
class A {
    private final String INFO = "你好，世界";
    
    public void method(){
        // 错误，常量不能被修改
        INFO = "你好，Java";
    }
}
```

>static final 修饰的变量被称为全局常量