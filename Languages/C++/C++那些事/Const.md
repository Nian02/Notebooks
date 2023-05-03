## const含义
**常类型**是指使用类型修饰符**const**说明的类型，常类型的变量或对象的值是不能被更新的。

## const作用
-  **可以定义常量**
```C++
const int a=100;
```

- **类型检查**
	- const常量与`#define`宏定义常量的区别：
	> **const常量具有类型，编译器可以进行安全检查；#define宏定义没有数据类型，只是简单的字符串替换，不能进行安全检查。**

	- const常量支持所有类型。
	- 其他情况下它只是一个 `const` 限定的变量，不要将与常量混淆。

- **防止修改，起保护作用，增加程序健壮性**
```C++
void f(const int i){
    i++; //error!
}
```
- **可以节省空间，避免不必要的内存分配**
`const` 定义的常量由于是整数或枚举，所以直接变成机器码上的立即数往往性能更好。

## const对象默认为文件局部变量
注意：非const变量默认为extern。要使const变量能够在其他文件中访问，必须在文件中显式地指定它为extern。

- **未被const修饰**的变量在不同文件的访问
```C++
// file1.cpp
int ext;
// file2.cpp
#include<iostream>

extern int ext;
int main(){
    std::cout<<(ext+10)<<std::endl;
}
```

- **const常量**在不同文件的访问
```C++
//extern_file1.cpp
extern const int ext=12;//显式声明extern，并且需要做初始化，因为常量在定义后就不能被修改，所以定义时必须初始化。
//extern_file2.cpp
#include<iostream>
extern const int ext;
int main(){
    std::cout<<ext<<std::endl;
}
```

## 定义常量
常量不可更改，且必须初始化
```C++
const int b = 10;
b = 0; // error: assignment of read-only variable ‘b’
const string s = "helloworld";
const int i,j=0 // error: uninitialized const ‘i’
```

## 指针与const
```C++
const char * a; //常量指针（指向常量的指针）const修饰*a，值不可变，指向可以变
char const * a; //同上
char * const a; //指针常量、常指针（指针是常量）const修饰a，值可以变，指向不可以变
const char * const a; //指向常量的常指针（const char & a）
```

### 指向常量的指针
1. 不能使用`void *`指针保存const对象的地址，必须使用`const void *`类型的指针保存const对象的地址。
```C++
const int p = 10;
const void * vp = &p;
void *vp = &p; //error
```

2. **允许把非const对象的地址赋给指向const对象的指针**
```C++
const int *ptr;
int val = 3;
ptr = &val; //ok
```

3. 可以修改const指针所指向的常量的值（**通过其他指针**）
```C++
int *ptr1 = &val;
*ptr1=4;
cout<<*ptr<<endl;
```

### 常指针
1. 必须初始化
```C++
#include<iostream>
using namespace std;
int main(){
    int num=0;
    int * const ptr=&num; //const指针必须初始化！且const指针的值不能修改
    int * t = &num;
    *t = 1;
    cout<<*ptr<<endl;
}
```
2. **类型匹配**
```C++
#include<iostream>
using namespace std;
int main(){
    const int num=0;
    int * const ptr=&num; //error! const int* -> int*
    const int * ptr=&num; //correct! const int* ->const int*
    const int * const ptr=&num; //correct! const int* ->const int*
    cout<<*ptr<<endl;
}
```

## 函数中使用const
### const修饰函数返回值
1. const int
```C++
const int func1();
```
这个本身无意义，因为参数返回本身就是赋值给其他的变量！
2. const int * 
```C++
const int* func2();
```
指针指向的内容不变。
3. int * const
```C++
int *const func2();
```
指针本身不可变。

### const修饰函数参数
1. 传递过来的参数不可变（无意义）
输入参数采用“值传递”，由于函数将自动产生临时变量用于复制该参数，该输入参数本来就无需保护，所以不要加const 修饰。