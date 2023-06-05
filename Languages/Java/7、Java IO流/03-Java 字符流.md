## 字符流简介

使用字节流读取文本文件的时候，可能会有一个小问题：就是遇到中文字符的时候，可能显示不完整，那是因为一个中文字符可能占用多个字节存储。所以，Java 提供了一些字符流，以字符为单位读写数据，专门用于处理文本文件。

## Writer

`java.io.Writer` 抽象类是表示用于写出字符流的所有类的超类，**将指定的字符信息写出到目的地，也就是写入字符**，它定义了字符输出流的基本共性功能方法。

-  写入单个字符：

```java
public void write(int c) throws IOException {}
```

-  写入字符数组：

```java
public void write(char cbuf[]) throws IOException {}
```

-  写入从 offset 索引开始，len 长度的字符数组：

```java
abstract public void write(char cbuf[], int off, int len) throws IOException;
```

-  写入字符串：

```java
public void write(String str) throws IOException {}
```

-  写入从 offset 索引开始，len 长度的字符串：

```java
public void write(String str, int off, int len) throws IOException {}
```

-  刷新流：

```java
abstract public void flush() throws IOException;
```

-  关闭流：

```java
abstract public void close() throws IOException;
```

### FileWriter

`java.io.FileWriter` 类是写出字符到文本的类。构造的时候使用系统默认的字符编码和默认字节缓冲区。

-  创建一个新的 FileWriter，给定要读取的 File 对象：

```java
public FileWriter(String fileName) throws IOException {}
```

-  创建一个新的 FileWriter，给定要读取的文件的名称：

```java
public FileWriter(File file) throws IOException {}
```

-  追加内容：创建一个新的 FileWriter ，给定要读取的File对象：

```java
public FileWriter(String fileName, boolean append) throws IOException {}
```

-  追加内容：创建一个新的 FileWriter ，给定要读取的文件的名称：

```java
public FileWriter(File file, boolean append) throws IOException {}
```

### 示例

-  示例：写入单个字符

```java
import java.io.FileWriter;
import java.io.IOException;
import java.io.Writer;

public class Test {
    public static void main(String[] args) {
        try (Writer writer = new FileWriter("d:/demo.txt")) {
            writer.write(97);
            writer.write('a');
            writer.write('b');
            writer.write('c');
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

-  示例：写入字符数组

```java
import java.io.FileWriter;
import java.io.IOException;
import java.io.Writer;

public class Test {
    public static void main(String[] args) {
        try (Writer writer = new FileWriter("d:/demo.txt")) {
            writer.write("zx".toCharArray());
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

-  示例：写入字符串

```java
import java.io.FileWriter;
import java.io.IOException;
import java.io.Writer;

public class Test {
    public static void main(String[] args) {
        try (Writer writer = new FileWriter("d:/demo.txt")) {
            writer.write("zx");
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```
## Reader

`java.io.Reader` 抽象类是表示用于**读取字符流**的所有类的超类，可以**读取字符信息到内存中，也就是读取字符**。它定义了字符输入流的基本共性功能方法。

-  读取一个字符：

```java
public int read() throws IOException
```

-  读取字符数组：

```java
public int read(char cbuf[]) throws IOException {}
```

-  读取从 offset 索引开始，len 长度的字符数组：

```java
abstract public int read(char cbuf[], int off, int len) throws IOException;
```

-  关闭此流并释放与此流相关联的任何系统资源：

```java
abstract public void close() throws IOException;
```

### FileReader

`java.io.FileReader` 类是读取字符文件的类。构造的时候使用系统默认的字符编码和默认字节缓冲区。
