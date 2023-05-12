decltype的作用是“查询表达式的类型”，可用于返回表达式`expression`的类型
```C++
decltype (expression)
```
### 与using/typedef合用，用于定义类型
```C++
using size_t = decltype(sizeof(0));//sizeof(a)的返回值为size_t类型
using ptrdiff_t = decltype((int*)0 - (int*)0);//表示两个指针之间的差值的整数类型
using nullptr_t = decltype(nullptr);
vector<int >vec;
typedef decltype(vec.begin()) vectype;
for (vectype i = vec.begin; i != vec.end(); i++)
{
//...
}
```