---
title: "[CXX学习笔记]基础知识"
date: 2019-10-20T15:51:31+08:00
Description: ""
Tags: []
Categories: []

---

## 指针和引用

### 联系

C++底层中引用是通过指针实现的，在实现层面上引用就是指针，指向同一对象的内存地址。

所有对引用的操作就是对原始对象的操作。

### 区别

1. 引用只是别名，不是实体类型，因此编译器不为引用分配单独的内存空间，而指针有自己的内存空间，因此某种程度上指针不能被引用取代；
2. 引用必须被初始化且不能为空，但可以有未初始化的空指针；
3. 引用一旦初始化就不能更换目标，即不能被初始化多次，指针可以更改指向的对象；
4. 可以有指针数组，不能有引用数组，因为引用没有被分配内存；
5. 当引用作为函数的形参时，因为函数形参和实参是同一个对象，避免了复制对象的开销；
6. 若对引用型参数做const修饰，则不会调用拷贝构造函数，提高效率；
7. 若引用作为函数的返回结果，需要保证函数返回后被引用的目标一直有效，即不能返回函数内部的局部对象的引用（因为离开作用域后会被析构掉）。

## 数组和指针

C++不记录数组本身的大小，因此一定注意不要越界访问。

数组作为形参传递时会退化为同类型的指针：

```c++
int data[] = {1, 2, 3, 4, 5};
int size1 = sizeof(data); // 20
int *data2 = data;
int size2 = sizeof(data2); // 4

int GetSize(int data[])
{
  return sizeof(data);
}
int size3 = GetSize(data); // 4
```

## sizeof使用与陷阱

https://www.cnblogs.com/chio/archive/2007/06/11/778934.html

## 左值和右值

### c++中的变化

https://www.cnblogs.com/catch/p/3500678.html

### 右值引用和std::move

https://blog.csdn.net/luotuo44/article/details/46779063

http://shaoyuan1943.github.io/2016/03/26/explain-move-forward/

### 右值引用减少对象拷贝

https://www.jianshu.com/p/da704a9c3969

https://www.cnblogs.com/qingergege/p/7607089.html