---
title: "[设计模式系列]单例模式"
date: 2020-02-17T17:01:20+08:00
Description: ""
Tags: []
Categories: []

---

开一个新坑，记录一下设计模式的学习和使用经过。因为最近在写一个项目，首先接触到的和思考的就是单例模式，就从这里写起吧。

### 出发点

有时候不论是出于易用性考虑还是效率上的考虑，我们希望某些资源仅被初始化一次，但是其他所有的实例都可以使用它。大部分情况下这类资源是不会被改动的，当然也会有意外，一旦涉及到改动可能有需要考虑线程安全的问题。

听起来是不是和static全局对象的作用很相似？不过如果你能记起Effective C++中的内容，就会知道静态全局变量的初始化顺序是完全看编译器的心情的。虽然同一个编译单元内的静态全局变量初始化顺序是一定的，但若是不同编译单元内的静态全局变量产生了相互依赖，结果可能就会变得不可预料。

但我们可以通过一个静态的方法，获取只初始化一次的实例对象。这就是单例模式的实现方式。

### 需要定义的方法和成员变量

1. 既然是需要访问这个全局实例，一定是通过一个静态的类方法获取这个实例的指针或者引用；
2. 单例类不能有公有的构造函数，那么一个共有的析构函数也就变得没有意义；
3. 显然单例类不能有任何公有的拷贝和赋值的行为；
4. 单例类应该需要有一个指向全局唯一实例的指针，为了让静态方法能够获取到这个实例指针，该指针也应该是静态的。

暂时就想到这么多，下面再来思考单例类实例化的时机。是的，单例类要有一个全局唯一的实例，且又不能在外部显示实例化，那就只有一个方法，将构造函数声明为私有的或者保护的，并在某些时刻调用它。

根据调用单例类实例化的时机，可将其分为懒汉版单例模式（Lazy Singleton）和饿汉版单例模式（Eager Singleton）。

### Lazy Singleton

顾名思义，什么是懒汉，就是不到万不得已不进行实例的初始化操作，也就是第一次调用GetInstance获取实例对象时才进行初始化：

```c++
// 单例模式
class Singleton
{
public:
    static Singleton *GetInstance();

    // 禁止拷贝和赋值, delete需要在public中实现
    Singleton(const Singleton &) = delete;

    Singleton &operator=(const Singleton &) = delete;

private:
    Singleton();

    ~Singleton();

    static Singleton *instance;
};

Singleton *Singleton::instance = nullptr;

Singleton *Singleton::GetInstance()
{
    if (instance == nullptr) {
        instance = new Singleton();
    }
    return instance;
}

Singleton::Singleton()
{
    std::cout << "singleton ctor" << std::endl;
}

Singleton::~Singleton()
{
    std::cout << "singleton dtor" << std::endl;
}
```

然后我们在main函数中观察一下实例化的时机：

```c++
int main()
{
    std::cout << "entry main" << std::endl;
    auto instance = Singleton::GetInstance();
    return 0;
}
```

会发现输出结果是这样的顺序：

![kcuFsZ](https://cdn.jsdelivr.net/gh/chongg039/blog-pic-repo@master/uPic/kcuFsZ.png#center)

显然是首先进入了main函数，然后在第一次调用该实例的时候调用了私有的构造函数进行实例化，这就是懒汉模式的基本实现思路。那么它存在什么问题呢？

首先肯定有内存泄漏的问题，因为析构函数的私有化导致不能使用delete来显式调用析构函数释放堆上的内存。我们看下valgrind给出的内存泄漏信息：

![yVBMYP](https://cdn.jsdelivr.net/gh/chongg039/blog-pic-repo@master/uPic/yVBMYP.png#center)

很显然堆上的instance实例没有被释放。有朋友可能会说，你为什么不将析构函数声明为公有的，然后调用delete释放这个指针呢？好吧，我们看一下这为什么会导致一个未定义的行为：

现在我们的析构函数就定义成了这样：

```c++
class Singleton
{
public:
    ~Singleton();
}

Singleton::~Singleton()
{
    std::cout << "singleton dtor" << std::endl;
    if (instance) {
        delete instance;
        instance = nullptr;
    }
}
```

看到这里你可能就明白了，当你在外部调用delete删除一个Singleton实例指针时，将会调用该对象的析构函数，而在这个析构函数中将会无限次地递归调用自己的析构函数去删除这部分资源，根本原因就是这个单例类中的保存了堆上的资源就是自己的一个实例。那我们要是想用的话需要怎么办呢，首先虽然析构函数要被声明为public（因为要被delete调用），但析构函数中不能有任何释放堆上资源的行为，然后我们需要在外部使用delete手动删除由GetInstance获取的堆上的实例。：

```c++
int main()
{
    std::cout << "entry main" << std::endl;
    auto instance = Singleton::GetInstance();
    delete Singleton::GetInstance();
    return 0;
}
```

此时valgrind就不会报内存泄漏了。但有朋友说了，我不想每次都手动释放单例对象，毕竟是个“懒汉”方式，能不能一懒懒到底。当然可以，请出智能指针帮我们管理这份堆上的资源就可以了。但需要注意的是，析构函数同样需要声明为public：

```c++
class SingletonSmartPtr
{
public:
    static std::unique_ptr<SingletonSmartPtr> &GetInstance();

    // 禁止拷贝和赋值
    SingletonSmartPtr(const SingletonSmartPtr &) = delete;

    SingletonSmartPtr &operator=(const SingletonSmartPtr &) = delete;

    ~SingletonSmartPtr();

private:
    SingletonSmartPtr();

    static std::unique_ptr<SingletonSmartPtr> instance;
};

std::unique_ptr<SingletonSmartPtr> SingletonSmartPtr::instance{nullptr};

std::unique_ptr<SingletonSmartPtr> &SingletonSmartPtr::GetInstance()
{
    if (instance == nullptr) {
        instance = std::unique_ptr<SingletonSmartPtr>(new SingletonSmartPtr());
    }
    return instance;
}

SingletonSmartPtr::SingletonSmartPtr()
{
    std::cout << "singleton with smart ptr ctor" << std::endl;
}

SingletonSmartPtr::~SingletonSmartPtr()
{
    std::cout << "singleton with smart ptr dtor" << std::endl;
}
```

这里我的GetInstance方法返回的是这个智能指针的引用，可以达到帮我们自动管理堆上资源的效果。其实还有一个不使用智能指针方法，就是在内部声明一个静态的类，利用程序结束时自动调用静态成员析构函数的特点来释放堆上的这部分资源：

```c++
class LazySingletonInnerClass
{
public:
    static LazySingletonInnerClass *GetInstance();

    // 禁止拷贝和赋值, delete需要在public中实现
    LazySingletonInnerClass(const LazySingleton &) = delete;

    LazySingleton &operator=(const LazySingleton &) = delete;

private:
    LazySingletonInnerClass();

    ~LazySingletonInnerClass();

    static LazySingletonInnerClass *instance;

    // 内部类, 用于删除堆上的实例
    class Deleter
    {
    public:
        ~Deleter()
        {
            if (LazySingletonInnerClass::instance != nullptr) {
                delete LazySingletonInnerClass::instance;
                LazySingletonInnerClass::instance = nullptr;
            }
        }
    };

    static Deleter deleter;
};

LazySingletonInnerClass *LazySingletonInnerClass::instance{nullptr};
// 注意一定要实现这个静态的内部类, 不然没有效果
LazySingletonInnerClass::Deleter LazySingletonInnerClass::deleter;

LazySingletonInnerClass *LazySingletonInnerClass::GetInstance()
{
    if (instance == nullptr) {
        instance = new LazySingletonInnerClass();
    }
    return instance;
}

LazySingletonInnerClass::LazySingletonInnerClass()
{
    std::cout << "singleton ctor" << std::endl;
}

LazySingletonInnerClass::~LazySingletonInnerClass()
{
    std::cout << "singleton dtor" << std::endl;
}

```

使用内部类的方式可以将析构函数声明为private的，比较符合逻辑，但带来的则是不优雅的编码方式。

### Eager Singleton

与之相对，饿汉版的单例模式则是能有多早实例化便多早，也就是我们利用了静态全局变量在main函数前就初始化的特点。因此可以这样编码：

```c++
// 饿汉版单例
class EagerSingleton
{
public:
    static EagerSingleton &GetInstance();

    // 不能拷贝和赋值
    EagerSingleton(const EagerSingleton &) = delete;

    EagerSingleton &operator=(const EagerSingleton &) = delete;

private:
    static EagerSingleton instance;

    EagerSingleton();

    ~EagerSingleton();
};

EagerSingleton EagerSingleton::instance;

EagerSingleton &EagerSingleton::GetInstance()
{
    return instance;
}

EagerSingleton::EagerSingleton()
{
    std::cout << "eager singleton ctor" << std::endl;
}

EagerSingleton::~EagerSingleton()
{
    std::cout << "eager singleton dtor" << std::endl;
}
```

观察构造函数和析构函数被调用位置：

![PGgTgN](https://cdn.jsdelivr.net/gh/chongg039/blog-pic-repo@master/uPic/PGgTgN.png#center)

显然，由于非局部静态变量在main函数前被初始化，在之后析构，输出是符合我们预期的。当然，静态变量最终会自己乖乖析构掉，也就不存在懒汉版单例的内存溢出的隐患问题。但是可不要以为饿汉版单例就是那么完美的，事实上这里面存在更大的隐患。

如果你仔细回忆一下Effective C++中的内容，就知道非局部静态变量的初始化顺序是不一定的（尽管同一个编译单元内部的顺序由于编译器的某些行为一般会固定下来）。想象一下在两个不同的编译单元内，一个调用了你所写的这个饿汉版单例，但是恰好这个单例所在的编译单元还没有被初始化，就会进入未定义行为的世界。

### 更好的解决方案

Effective C++的Item04中提到了一种更优雅的实现方案，相信看完上面的分析你也能想到。既然静态变量这么方便，非局部的静态变量初始化顺序又不固定，那我使用局部静态变量保存这个实例，不是一种更优雅的解决方案了吗：

```c++
// 使用静态局部变量的优雅饿汉版单例
class LazySingletonLocalStatic
{
public:
    static LazySingletonLocalStatic &GetInstance();

    // 不能拷贝和赋值
    LazySingletonLocalStatic(const LazySingletonLocalStatic &) = delete;

    LazySingletonLocalStatic &operator=(const LazySingletonLocalStatic &) = delete;

private:
    LazySingletonLocalStatic();

    ~LazySingletonLocalStatic();
};

LazySingletonLocalStatic &LazySingletonLocalStatic::GetInstance()
{
    static LazySingletonLocalStatic instance;
    return instance;
}

LazySingletonLocalStatic::LazySingletonLocalStatic()
{
    std::cout << "lazy singleton with local static ctor" << std::endl;
}

LazySingletonLocalStatic::~LazySingletonLocalStatic()
{
    std::cout << "lazy singleton with local static dtor" << std::endl;
}
```

静态局部变量并不存储在栈上，而是存储在静态存储区。当离开它的作用域后并不会被销毁，仍然可以被访问得到，直到程序结束自动析构静态变量。因此这是一种更为优雅的单例实现方式，只不过是懒汉版的。

我有疑惑的是这个静态局部变量的初始化方式，去查了查，编译器在编译时会给静态局部变量设置一个标识判断是否已经被初始化，并在每次运行调用的时候判断，如果需要则执行初始化操作，否则什么都不做。

### 线程安全问题

先看饿汉版的单例，在main函数执行前初始化，所以一定是线程安全的。

再来看懒汉版本，先看那个使用静态局部变量的优雅实现
