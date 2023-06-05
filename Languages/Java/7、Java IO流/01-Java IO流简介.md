## IO流简介

-  I/O 是 Input 和 Output 的缩写，IO 技术是非常实用的技术，用于处理设备之间的数据传输，如：读写文件、网络通讯等。
	-  输入（ _Input_ ）：读取外部数据（磁盘、U 盘等存储设备的数据）到程序（内存）中。
	-  输出（ _Output_ ）：将程序（内存）数据输出到磁盘、U 盘等存储设备中。
-  Java 程序中，对于**数据的输入/输出操作以流stream的方式进行**。
-  java.io 包下提供了各种流类和接口，用于获取不同种类的数据，并通过标准的方法输入或输出数据。

## IO 的分类

根据流的流向分类：

-  输入流：将数据从其他设备上读取到内存中的流。以 InputStream 、Reader 结尾。
-  输出流：将数据从内存中写出到其他设备上的流。以 OutputStream 和 Writer 结尾。

根据数据的类型分类：

-  字节流：以字节为单位，读写数据的流。以 InputStream 和 OutputStream 结尾。
-  字符流：以字符为单位，读写数据的流。以 Reader 和 Writer 结尾。

根据 IO 流的角色不同分类：

-  节点流：可以从或向一个特定的地方（节点）读取数据。如 FileReader 。
	-  文件：FileInputStream 、FileOutputStream 、FileReader 、FileWriter 文件进行处理的节点流。
	-  字符串：StringReader 、StringWriter 对字符串进行处理的节点流。
	-  数组： ByteArrayInputStream 、ByteArrayOutputStream、CharArrayReader 、CharArrayWriter 对数组进行处理的节点流（对应的不再是文件，而是内存中的一个数组）。
	-  管道：PipedInputStream 、PipedOutputStream 、PipedReader 、PipedWriter 对管道进行处理的节点流。

-  处理流：对一个已经存在的流进行连接和封装，通过锁封装的流的功能实现数据读写。如：BufferReader ，处理流的构造方法总是要带一个其它的流对象做参数。一个流对象经过多次其它流的多次包装，称为流的链接。
	-  缓冲流：BufferedInputStream 、BufferedOutputStream 、BufferedReader 、BufferedWriter ---增加缓冲功能，避免频繁读写硬盘。
	-  转换流：InputStreamReader 、OutputStreamReader --- 实现字节流和字符流之间的转换。
	-  数据流：DataInputStream 、DataOutputStream - 提供读写 Java 基础数据类型功能。
	-  对象流：ObjectInputStream 、ObjectOutputStream -- 提供直接读写 Java 对象功能。

## 四大顶级抽象流父类

|     | 输入流               | 输出流                |
|-----|-------------------|--------------------|
| 字节流 | 字节输入流 InputStream | 字节输出流 OutputStream |
| 字符流 | 字符输入流 Reader      | 字符输出流 Writer       |

