## 字节流简介

一切文件数据（文本、图片、视频等）在存储的时候，都是以二进制的形式保存的，都是一个个的字节；同样，在传输的时候依然如此。所以，字节流可以传输任意文件数据。

在操作流的时候，我们要时刻明确，无论使用什么样的流对象，底层传输始终是二进制数据。

## OutputStream

`java.io.OutputStream` 抽象类是表示**字节输出流**的所有类的超类，**将指定的字节信息输出到目的地，也就是写入字节信息**。它定义了字节输出流的基本共性的功能方法。

-  写入单个字节：

```java
public abstract void write(int b) throws IOException;
```

-  写入字节数组：

```java
public void write(byte b[]) throws IOException {}
```

-  写入字节数组的一部分，从 offset 开始，一共 len 个：

```java
public void write(byte b[], int off, int len) throws IOException {}
```

-  刷新流：

```java
public void flush() throws IOException {}
```

-  关闭流：

```java
public void close() throws IOException {}
```

>当完成流的操作的时候，必须调用 close() 方法，释放系统资源。

### FileOutputStream

FileOutputStream 是 OutputStream 的子类，FileOutputStream 是文件输出流，用于**将数据写入到文件中**。

-  创建文件输出流以**写入由指定的 File 对象表示的文件**：

```java
public FileOutputStream(File file) throws FileNotFoundException {}
```

-  创建文件输出流**以指定的名称写入文件**：

```java
public FileOutputStream(String name) throws FileNotFoundException {}
```

>当创建 FileOutputStream 流对象的时候，必须传入一个文件路径。该路径下，如果没有这个文件，会创建该文件；如果有这个文件，会覆盖这个文件的数据。

-  **数据追加**：创建文件输出流以写入由指定的 File 对象表示的文件：

```java
public FileOutputStream(String name, boolean append)
      throws FileNotFoundException
  {}
```

-  数据追加：创建文件输出流以指定的名称写入文件：

```java
public FileOutputStream(File file, boolean append)
      throws FileNotFoundException
  {}
```

### 示例

-  示例：写入单个字节

```java
import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.OutputStream;

public class Test {
    public static void main(String[] args) {
        OutputStream os = null;
        try {
            // 使用文件名称创建流对象
            os = new FileOutputStream("d:/demo.txt");
            // 以ASCII码的形式写入
            os.write(97); // 写入a
            os.write(98); // 写入b
            os.write(99); // 写入c
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (null != os) {
                try {
                    // 关闭资源
                    os.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
```

-  示例：写入字节数组

```java
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.OutputStream;
import java.nio.charset.StandardCharsets;

public class Test {
    public static void main(String[] args) throws IOException {
        // 使用文件名称创建流对象
        OutputStream os = new FileOutputStream("d:/demo.txt");
        os.write("zx".getBytes(StandardCharsets.UTF_8));// 写入zx
        // 关闭资源
        os.close();
    }
}
```

-  示例：写入指定长度的字节数组

```java
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.OutputStream;
import java.nio.charset.StandardCharsets;

public class Test {
    public static void main(String[] args) throws IOException {
        // 使用文件名称创建流对象
        OutputStream os = new FileOutputStream("d:/demo.txt");
        os.write("abcd".getBytes(StandardCharsets.UTF_8), 2, 1);// 写入c（偏移2，长度1）
        // 关闭资源
        os.close();
    }
}
```

-  示例：数据追加

```java
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.OutputStream;
import java.nio.charset.StandardCharsets;

public class Test {
    public static void main(String[] args) throws IOException {
        // 使用文件名称创建流对象，append为true，表示追加写出
        OutputStream os = new FileOutputStream("d:/demo.txt", true);
        os.write("abcd".getBytes(StandardCharsets.UTF_8), 1, 3);// 追加写入bcd
        // 关闭资源
        os.close();
    }
}
```

-  示例：换行写入

```java
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.OutputStream;

public class Test {
    public static void main(String[] args) throws IOException {
        // 使用文件名称创建流对象，append为true，表示追加写入
        OutputStream os = new FileOutputStream("d:/demo.txt", true);
        char[] chs = {'a', 'b', 'c', 'd'};
        for (char ch : chs) {
            os.write(ch);// 依次写入a换行符、b换行符...
            os.write("\r\n".getBytes());
        }
        // 关闭资源
        os.close();
    }
}
```

>windows 系统中的换行符号是 `\r\n`

## try-with-resources

JDK 7 的新特性 try-with-resources ：

```java
try(需要关闭的资源对象的声明){
    业务逻辑代码
}catch(异常类型 e){
    处理异常代码
}catch(异常类型 e){
    处理异常代码
}
```

没有 finally ，也不需要程序员去关闭资源对象，无论是否发生异常，都会关闭资源对象（实现了自动资源关闭）

```java
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.OutputStream;

public class Test {
    public static void main(String[] args) {
        // 使用文件名称创建流对象，append为true，表示追加写出
        try (OutputStream os = new FileOutputStream("d:/demo.txt", true)) {
            char[] chs = {'a', 'b', 'c', 'd'};
            for (char ch : chs) {
                os.write(ch);
                os.write("\r\n".getBytes());
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

## InputStream

InputStream 抽象类是表示字节输入流的所有类的超类，可以**读取字节信息到内存中**。它定义了字节输入流的基本共性功能方法。

-  读取单个字节：

```java
  public abstract int read() throws IOException;
```

-  读取字节数组：

```java
  public int read(byte b[]) throws IOException {}
```

-  读取字节数组，从 offset 开始，len 长度：

```java
public int read(byte b[], int off, int len) throws IOException {}
```

-  关闭流：

```java
public void close() throws IOException {}
```

### FileInputStream

`java.io.FileInputStream` 类是文件输入流，**从文件中读取字节**。

-  通过打开与实际文件的连接来创建一个 FileInputStream ，该文件由文件系统中的 File 对象 file 命名：

```java
public FileInputStream(File file) throws FileNotFoundException {}
```

-  通过打开与实际文件的连接来创建一个 FileInputStream ，该文件由文件系统中的路径名 name 命名：

```java
public FileInputStream(String name) throws FileNotFoundException {}
```

### 示例

-  示例：读取单个字节，读取到文件末尾，返回 `-1`

```java
import java.io.FileInputStream;
import java.io.IOException;
import java.io.InputStream;

public class Test {
    public static void main(String[] args) {
        try (InputStream is = new FileInputStream("d:/demo.txt")) {
            int len;
            while ((len = is.read()) != -1) {
                System.out.println((char)len);
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

-  示例：读取字节数组，读取到文件末尾，返回 `-1`

```java
import java.io.FileInputStream;
import java.io.IOException;
import java.io.InputStream;

public class Test {
    public static void main(String[] args) {
        try (InputStream is = new FileInputStream("d:/demo.txt")) {
            byte[] bytes = new byte[1024];
            int len;
            while ((len = is.read(bytes)) != -1) {
                System.out.println(new String(bytes, 0, len));
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

## 文件复制

文件复制的本质是字节的搬家。

![](../../../Image/Java/Java%20文件复制.png)

```java
import java.io.*;

public class Test {
    public static void main(String[] args) {
        try (InputStream is = new FileInputStream("C:\\Users\\xudaxian\\Pictures\\枪械少女.jpg");
            OutputStream os = new FileOutputStream("C:\\Users\\xudaxian\\Pictures\\枪械少女1.jpg")) {
            byte[] bytes = new byte[1024];
            int len;
            while ((len = is.read(bytes)) != -1) {
                os.write(bytes, 0, len);
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```