缓冲流，也叫高效流，按照数据类型分类：

-  字节缓冲流：BufferedOutputStream 、BufferedInputStream 。
-  字符缓冲流：BufferedReader 、BufferedWriter 。

缓冲流会在内部创建一个缓冲区数组，缺省使用 8192 个字节（8kb）的缓冲区。

```java
public class BufferedInputStream extends FilterInputStream {
   private static int DEFAULT_BUFFER_SIZE = 8192;
   ... 
}
```

## 缓冲流的基本原理

-  当读取数据的时候，数据按块读入缓冲区，其后的读操作则直接访问缓冲区。
-  当使用 BufferedInputStream 读取字节文件时，BufferedInputStream 会一次性的从文件中**读取 8192 个，存储在缓冲区中**，直到缓冲区中装满了，才重新从文件中读取下一个 8192 个字节数组。
-  向流中写入字节的时候，不会直接写到文件中，先**写到缓冲区中直到缓冲区中写满**，BufferedOutputStream 才会将缓冲区中的**数据一次性的写到文件里**。使用 flush() 方法可以强制将缓冲区的内容全部写入输出流。

![](../../../Image/Java/Java%20缓冲流的基本原理.png)

-  关闭流的顺序和打开流的顺序相反，只需要关闭最外层流即可，因为关闭最外层流的同时也会关闭对应的内层流。
-  flush() 方法的使用：手动将 buffer 中内容写入文件。
-  如果是带缓冲区的流对象的 close() 方法，不但会关闭流，还会在关闭流之前刷新缓冲区，关闭后不能再写出。

## 字节缓冲流

-  BufferedOutputStream 的构造方法：

```java
public BufferedOutputStream(OutputStream out) {}
```

-  BufferedInputStream 的构造方法：

```java
public BufferedInputStream(InputStream in) {}
```

```java
import java.io.*;

public class Test {
    public static void main(String[] args) {
        try (BufferedInputStream bis = new BufferedInputStream(new FileInputStream("d:/demo.txt"));
            BufferedOutputStream bos = new BufferedOutputStream(new FileOutputStream("d:/test.txt"))) {
            byte[] bytes = new byte[1024];
            int len;
            while ((len = bis.read(bytes)) != -1) {
                bos.write(bytes, 0, len);
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

## 字符缓冲流

-  BufferedReader 的构造方法：

```java
public BufferedReader(Reader in) {}
```

-  BufferedReader 的特殊方法读取一行：

```java
public String readLine() throws IOException {}
```

-  BufferedWriter 的构造方法：

```java
public BufferedWriter(Writer out) {}
```

-  BufferedWriter 的特殊方法写入换行符：

```java
public void newLine() throws IOException {}
```

```java
import java.io.*;

public class Test {
    public static void main(String[] args) {
        try (BufferedReader reader = new BufferedReader(new FileReader("d:/demo.txt"));
            BufferedWriter writer = new BufferedWriter(new FileWriter("d:/test.txt"))) {
            char[] buffer = new char[1024];
            int len;
            while ((len = reader.read(buffer)) != -1) {
                writer.write(new String(buffer, 0, len));
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```