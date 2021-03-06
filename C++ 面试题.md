# C++ 面试题

收录 C++ 面试题。

PS：不保证回答正确。

## 一个程序在内存中的布局是怎样的？

1. 文本段（Text Segment）

    也被称为代码段（Code segment），包含了可执行指令。

2. 数据段（Data Segment）

    数据段分为两部分：

    **已初始化的数据段（Initialized Data Segment）**

    包含了已经初始化的全局和静态变量

    **未初始化的数据段（Uninitialized Data Segment）**

    这个通常也叫做 BSS 段（BSS Segment）。

    程序执行之前，在这个段的数据通常被内核初始化为 0。

    BSS 段 一般在已初始化的数据段之后

3. 栈（Stack）

    临时变量存储在这个区域。

4. 堆（Heap）

    动态内存分配

最后附上图片

![img](./img/memoryLayoutC.jpg)


## new operator、operator new 和 placement new

**区别：**

**(1) operator new**

* operator new 是一个函数

* 只分配空间，不调用构造函数

* 当无法满足所分配的空间时：

    * 如果有 `new_handler` 则调用 `new_handler`；
    * 否则如果能抛出异常，则抛出 `bad_alloc`；
    * 否则返回 `NULL`

* 可以重载，重载格式如下：

    * 返回类型是 `void*`
    * 第一个形参为表达要求分配空间的大小
    * 其他形参自定义

    > 通过重载，就可以利用自己写的内存管理库

* 在头文件 `<new>` 中定义了一个全局 operator new，用来分配空间，通过

    ```c++
    ::operator new
    ```

    方式调用

**(2) placement new**

* placement new 是一个函数

* 本质是 operator new 的重载

    ```c++
    void * operator new(size_t, void* __p) { return __p }
    ```

* 作用是在已分配的空间上构造对象

* 优点：允许在缓冲区上创建对象，不必动态分配内存空间

**(3) new operator**

* new operator 是一个关键字，操作符
* 分配空间，调用构造函数。

**使用 new 关键字创建一个对象会发生什么？**

1. 调用全局 operator new 分配空间（也可以使用自定义的 operator new）
2. 在该空间上调用构造函数构造一个对象

## 什么是完美转发？

完美转发是指模板函数内部调用其他函数时，其类型不会改变。

```c++
// function with lvalue and rvalue reference overloads:
void overloaded (const int& x) {std::cout << "[lvalue]";}
void overloaded (int&& x) {std::cout << "[rvalue]";}

// function template taking rvalue reference to deduced type:
template <class T> 
void fn (T&& x) {
  overloaded (x);                   // always an lvalue
  overloaded (std::forward<T>(x));  // rvalue if argument is rvalue
}

int main () {
  int a;

  std::cout << "calling fn with lvalue: ";
  fn (a);
  std::cout << '\n';

  std::cout << "calling fn with rvalue: ";
  fn (0);
  std::cout << '\n';

  return 0;
}
```

结果是：

```
calling fn with lvalue: [lvalue][lvalue]
calling fn with rvalue: [lvalue][rvalue]
```


## 构造函数可以是虚函数吗？

不可以。

* 虚函数是一种**只需要部分信息**就可以调用的函数。特别的，我们可以调用只知道接口类型而不知道对象的确切类型的虚函数。

* 创建一个对象则**需要完整信息**。你需要知道你想要创建对象的确切类型。


