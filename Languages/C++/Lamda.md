通常用于简化代码，特别是在需要传递函数对象作为参数的情况下（直接定义函数对象），例如在标准库中的算法、容器和其他函数接口中。
Lambda 表达式的基本语法如下：
```C++
[ capture_list ] ( parameters ) -> return_type {
    // 函数体
}
```
-   `capture_list` 是捕获列表，用于指定在 lambda 表达式中**引用的外部变量**。可以通过值捕获、引用捕获或混合方式来捕获外部变量。捕获列表是可选的，如果不需要访问外部变量，**可以省略**。
-   `parameters` 是参数列表，用于指定 lambda 表达式的参数。和普通函数一样，可以有零个或多个参数。
-   `return_type` 是返回类型，用于指定 lambda 表达式的**返回值类型**。**可以省略**，编译器可以根据 lambda 表达式的函数体推断返回类型。

>Lambda 表达式的主要优点是可以在代码中方便地创建和使用小型的、局部的、一次性的函数对象，避免了显式定义命名函数的繁琐过程。

比如：
```C++
auto _f = new std::function<void(int id)>([pck](int id) {
                (*pck)(id);
            });
```
创建了一个 `std::function` 对象 `_f`，该 `std::function` 对象可以调用一个接受 `int` 类型参数的函数，并在内部通过调用 `(*pck)(id)` 来调用 `pck` 所指向的 `std::packaged_task` 对象，传递 `id` 作为参数。

`pck` 是一个指向 `std::packaged_task` 对象的智能指针。