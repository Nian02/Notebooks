## 内存管理
-  代码区  
存放函数体的**二进制代码**，由操作系统进行管理的。**共享、只读**。
-  全局区  
存放**全局变量和静态变量（static）以及常量**（const修饰的全局变量、字符串常量）
-  堆区  
**new出来的**；由程序员分配和释放，若程序员不释放，程序结束后由**操作系统回收**。
-  栈区  
由**编译器自动分配释放**，存放**函数的参数值，局部变量**等。

## Static的使用
内存空间**将在程序的生命周期内分配**（在main结束后调用析构函数），也就是说变量放在全局区，即使多次调用该函数，静态变量的空间也**只分配一次**。

### 静态变量
类中的静态变量**由对象共享**，对于不同的对象，不能有相同静态变量的多个副本。

>因此，静态变量不能使用构造函数初始化。
>类中的静态变量应由用户使用类外的类名和范围解析运算符**显式初始化**。比如：
```C++
class Apple 
{ 
public: 
	static int i; 
	Apple() 
	{ 
		// Do nothing，静态变量不能使用构造函数初始化 
	}; 
};
int Apple::i = 1;
```
### 静态类的成员

- 类中的静态对象 
静态对象`static Apple obj;`类似静态变量，在main结束后调用析构函数。
- 类中的静态函数
建议使用类名和范围解析运算符**调用静态成员函数**。
允许**静态成员函数仅访问静态数据成员或其他静态成员函数**，它们无法访问类的非静态数据成员或成员函数。

**静态成员函数**：`static void printMsg(){...}`**调用静态成员函数**：`Apple::printMsg();`

>类中的静态变量**由对象共享**，对于不同的对象，不能有相同静态变量的多个副本。
>因此，静态变量不能使用构造函数初始化。

## 限定访问范围
可以限制在不同
```C++
// source1.cpp
extern void sayHello();
const char* msg = "Hello World!\n";
int main()
{
    sayHello();
    return 0;
}

// source2.cpp
#include <cstdio>
extern char* msg;
void sayHello()
{
    printf("%s", msg);
}
```

g++对于上面两个代码文件是可以正常编译并且打印Hello World!，但如果给source1.cpp中的msg加上static，则会导致`undefined reference to 'msg'`的编译错误：
```C++
// source1.cpp
extern void sayHello();
static const char* msg = "Hello World!\n";
int main()
{
    sayHello();
    return 0;
}
```