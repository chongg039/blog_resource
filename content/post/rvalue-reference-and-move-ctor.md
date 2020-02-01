---
title: "右值引用、移动构造和移动语义std::move"
date: 2020-02-01T10:02:22+08:00
Description: ""
Tags: [cxx]
Categories: [cxx]

---

前段时间在读Effective C++，当时对书中提到的移动构造函数一知半解。最近在做项目的时候遇到并重新思考了一下这个问题，在这里写一下自己的浅见。这里假设你已经理解并能熟练运用copy constructor以及copy assignment了。

### 左值与右值

介绍这个的文章很多，这也是理解什么时候调用拷贝构造、什么时候调用移动构造的一个比较关键的地方。

#### 左值（lvalue）

左值（左值引用）是一个可被赋值的对象，通常出现在赋值表达式等号的左侧。有文章将左值定义为非临时的对象，即可以在后面被使用、被引用，如：

```c++
int i = 0;
std::string s{"hello"};
```

这里的`i`和`s`都是左值，后面都可以使用这两个非临时对象。

#### 右值（rvalue）

与之对应，右值不可以被赋值，通常是一个临时对象。如上面的`0`、`"hello"`就是右值，只在当前语句中生效，脱离了当前语句的话一般不能在下面的句子中获取到，一般也不具备实际意义。

这里需要注意几点：

1. C++11前，最多可以用一个常量引用绑定一个右值在下文使用，但这样绑定的右值不能被修改：

	```c++
	const int &i = 1; // can not be changed
	```

2. 右值本身不是不能修改的，如上文所说，临时对象都是右值，那么调用一个临时对象的`setter`方法即可修改这个临时对象：

	```
	T().set_value();
	```

这里比较需要注意的是，临时对象都是右值，准确的说是一个“将亡值”，这对于返回值为non-trivial对象的函数有关键性的意义。

#### 左值和右值的符号表示

函数传参时，`&`表示接受一个左值，为了与之区分，`&&`表示接受一个右值，看下面这个重载函数：

```c++
void RecvValue(int &i)
{
    std::cout << "get a lvalue";
}
void RecvValue(int &&i)
{
    std::cout << "get a rvalue";
}
void Wapper(int &&i)
{
    // 注意这种情况，调用的是第一个
   	// 因为临时对象在参数传递时会变成命名对象（左值）
    RecvValue(i);
}
```

一般也称一个`T &&t`为一个右值引用。

### 移动构造函数（move ctor）

说了这些，那么右值引用有什么用呢。这就得再谈一下拷贝构造与移动构造的区别了。我们知道C++中一个对象一般会有默认构造函数、自定义的构造函数、拷贝构造函数和与之对应的copy assignment操作符重载，比如下面这个类：

```c++
class T
{
public:
    T() : ptr_(nullptr), data_(0)
    {};

    T(char *ptr, int data) : ptr_(ptr), data_(data)
    {};

    ~T()
    {
        if (ptr_) {
            delete ptr_;
            ptr_ = nullptr;
        }
    };

    // copy ctor
    T(const T &rhs)
    {
        ptr_ = rhs.ptr_;
        data_ = rhs.data_;
    };
    
    // copy assignment
    T &operator=(const T &rhs)
    {
        if (this != &rhs) {
            ptr_ = rhs.ptr_;
            data_ = rhs.data_;
        }
        return *this;
    };
private:
    char *ptr_;
    int data_;
};
```

这里忽略我对堆内存的管理没用智能指针，上面这个类支持了拷贝构造和拷贝赋值语义，可以看出两个函数都是接受的一个左值。也就是说，copy ctor和copy assignment产生的是一个新的对象，是将旧对象的内容“拷贝”一份到新对象，至少会产生一次构造和析构，这在某种情况下会产生性能的损耗，看下面这个例子：

```c++
// 假设有一个函数，返回一个T临时对象
T Process()
{
    T result;
    // some process...
    return result;
}
// 现在需要在外部接受这个返回值
T t = Process();
```

在这个过程，首先是在函数内部调用默认的构造函数，经过处理后传出这个临时对象。外部首先调用默认构造函数初始化一个`t`，然后调用copy assignment复制这个临时对象（将亡值）中的内容，然后再将这个将亡值析构掉。**然而，临时对象的析构会导致对象内的指针指向的资源被释放掉，这样`t`中的指针指向的资源也就无效了**。虽然智能指针可以帮助解决这个问题，但我们先从“移动”的角度考虑一下，看看能不能一定程度上避免这种现象的发生。

现在有个问题是，我们希望做的是“转移”操作，就像文件操作中的“移动”一样，而不是“复制”，尽管这个例子看不出来性能损耗。**而事实上，C++11对“将亡值”的处理就是“移动”而非“拷贝”，但有个前提是对自己定义的类需要自己定义移动构造函数和移动操作语义，像STL中的容器对象等则已定义好**。如果不自己重载move ctor和move assignment，编译器会自动调用copy ctor和copy assignment（是的，如果你偷懒什么都不写，编译器只会帮你自动生成copy ctor和copy assignment，而不会帮你自动写好move ctor和move assignment……）。

下面定义`T`的移动构造和移动赋值语义：

```c++
class T
{
public:
	......
    // move ctor
    T(T &&rhs) noexcept
    {
        ptr_ = rhs.ptr_;
        data_ = rhs.data_;
        rhs.ptr_ = nullptr;
        rhs.data_ = 0;
    }

    // move assignment
    T &operator=(T &&rhs) noexcept
    {
        if (this != &rhs) {
            ptr_ = rhs.ptr_;
            data_ = rhs.data_;
            rhs.ptr_ = nullptr;
            rhs.data_ = 0;
        }
        return *this;
    }
	......
private:
    char *ptr_;
    int data_;
};
```

如你所见，我们重载了一个构造函数和一个赋值操作符，有以下几点需要注意：

1. 这两个重载函数接受的都是右值；
2. 两个函数形参都不能为const，因为原始对象需要被修改；
3. 两个函数一般需要被声明为noexcept，不能抛出异常；
4. 右值指向的资源链接必须修改，否则右值的析构函数会释放资源，转移到新对象的资源也就无效了。

使用这个修改过后的`T`，再执行上面那条语句时由于等号右边是一个将亡值（右值），会调用移动赋值操作而非拷贝赋值操作。临时对象在析构时由于指向原本资源的指针已经被置为空，所以不会去释放原本的资源。换句话说，也就实现了资源管理权的“移动”。

### 移动语义std::move

来看一个和容器有关的例子：

```c++
std::vector<T> vec;
vec.push_back(Process());
```

STL容器类在执行添加或插入操作时，会调用被插入元素的拷贝构造函数生成一个一模一样的对象，并将其插入到容器中。比如`std::vector`的`push_back`函数：

```c++
void push_back(const T &x)
{
    if (finish != end_of_storage) {
        // 全局构造函数
        construct(finish, x);
        // finish 为 T*
        ++finish;
    } else {
        // 当备用空间不够时的辅助函数
        insert_aux(end(), x);
    }
}
```

其中在全局构造函数`construct`中可以看出：

```c++
template <class T1, class T2>
inline void construct(T1 *p, const T2 &value)
{
    // 调用T1::T1(value)
    new (p) T1(value);
}
```

因此，由于`Process()`产生了一个右值引用，所以自然会调用我们的移动构造函数，相当于将产生的临时对象移动到了`vec`中，而不是复制一份新的再添加进去。

现在从这个例子来看，假设我们有这样一个命名对象（左值），也想像上面这样做移动操作：

```c++
T t; // t is a lvalue
vec.push_back(t); // use copy ctor
```

这样操作是不行的，因为移动构造函数只能接受右值引用作为参数，传入左支一定会调用拷贝构造函数。那么能不能把一个左值变换成右值引用，调用移动构造函数呢？答案是用移动语义`std::move()`：

```c++
T t; // t is a lvalue
vec.push_back(std::move(t)); // use move ctor
```

这样就避免了没有必要的拷贝操作，提高了效率。

再来看一个`swap`函数的例子，这个例子是在参考文献中看到的，首先是一个普通的`swap`：

```c++
template <class T>
void swap(T &a, T &b)
{
	T tmp(a);   // copy a to tmp 
	a = b;      // copy b to a 
	b = tmp;    // copy tmp to b 
}
```

然后是使用了`std::move`后的`swap`：

```c++
template <class T>
void swap(T &a, T &b)
{
	T tmp(std::move(a));   // move a to tmp 
	a = std::move(b);      // move b to a 
	b = std::move(tmp);    // move tmp to b 
}
```

一个`swap`函数就避免了三次不必要的拷贝操作。

### 完美转发（Perfect forwarding）

大致意思就是在C++11中，若函数接受一个右值引用的参数，就能够将所有的参数类型原封不动的传递给目标函数，相当于减少了函数重载。对右值引用形参推导规则为：

```
若实参为右值，则为右值引用；若实参为左值，则为左值引用。
```

的这个例子因为暂时没有在实际应用中碰到，就不展开详细说了。一句话就是一个接受右值引用形参的函数可以原封不动的将实参的左值/右值类型、const/non-const类型完美传递进来。这部分例子可以看参考文献中的2。

### 总结

以上是本人在实际项目中遇到并思考后的一些拙见，不足之处还请见谅。感谢参考文献中几位的文章让我对这个问题有了一个比较整体的认识。

### 参考文献

[1] Effective C++

[2] [右值引用与转移语义](https://www.ibm.com/developerworks/cn/aix/library/1307_lisl_c11/index.html)

[3] [右值和移动究竟解决了什么问题？](https://time.geekbang.org/column/article/169268)

[4] STL源码剖析