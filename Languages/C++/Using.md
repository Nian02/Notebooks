## 全局using和局部using
```C++
#include <iostream>
#define isNs1 1
//#define isGlobal 2
using namespace std;//全局using，将 C++ 标准库命名空间 `std` 中的所有成员引入到当前的作用域中
void func() 
{
    cout<<"::func"<<endl;
}

namespace ns1 {
    void func()
    {
        cout<<"ns1::func"<<endl; 
    }
}

namespace ns2 {
#ifdef isNs1 
    using ns1::func;// 使用局部域下的函数
#elif isGlobal
    using ::func; /// 使用全局域下的函数
#else
    void func()
    {
        cout<<"other::func"<<endl; 
    }
#endif
}

int main() 
{
    ns2::func(); // 会根据当前环境定义宏的不同来调用不同命名空间下的func()函数
    return 0;
}
```

## 取代typedef
```C++
typedef vector<int> V1;//将V1定义为vector<int>类型
using V2 = vector<int>;//将V2定义为vector<int>类型

typedef void (*FunctionType)(int);//FunctionType为函数指针，类型为void (*)(int)
using FunctionType = void (*)(int);//使用using关键字定义函数指针类型别名
```

## 改变访问性
```C++
class Base{
public:
 std::size_t size() const { return n;  }
protected:
 std::size_t n;
};
class Derived : private Base {//私有继承，成员变量n和成员函数size都是私有的
public:
 using Base::size;//改变size的访问性为public
protected:
 using Base::n;//改变n的访问性为protected
};
```

## 函数重载
在继承过程中，派生类可以覆盖重载函数的0个或多个实例，一旦定义了一个重载版本，那么其他的重载版本都会变为不可见。

如果对于基类的重载函数，我们需要在派生类中修改一个，又要让其他的保持可见，必须要重载所有版本，这样十分的繁琐。
```C++
#include <iostream>
using namespace std;

class Base{
    public:
        void f(){ cout<<"f()"<<endl;
        }
        void f(int n){
            cout<<"Base::f(int)"<<endl;
        }
};

class Derived : private Base {
    public:
        using Base::f;//该函数的所有重载实例添加到派生类的作用域中
        void f(int n){//只需要定义其特有的函数就行了，而无需为继承而来的其他函数重新定义。
            cout<<"Derived::f(int)"<<endl;
        }
};

int main()
{
    Base b;
    Derived d;
    d.f();//f()
    d.f(1);//Derived::f(int)
    return 0;
}
```