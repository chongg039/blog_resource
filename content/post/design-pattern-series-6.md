---
title: "[设计模式系列]享元模式、外观模式与代理模式"
date: 2020-02-23T09:30:35+08:00
Description: ""
Tags: [DesignPattern]
Categories: [C++]

---

![5ZeooCGNw3s](https://cdn.jsdelivr.net/gh/chongg039/blog-pic-repo@master/uPic/5ZeooCGNw3s.jpg#center)

不多啰嗦直奔主题，来看下GOF中结构型模式的最后三部分。

### 享元模式(Flyweight Pattern)

过多的对象创建会造成很大的存储开销，而这其中有相当一部分可能是重复且不必要的。因此对于一个特定的对象我们可以将其分为”内部状态“和”外部状态“两部分。内部状态指可以被共享的、一般不会变化的状态，存储在类的内部；对于可能产生变化的外部状态，我们选择将其作为参数传递给对象。

享元模式类似工厂模式，有一个工厂对象，这个工厂用于接收用户的每一次申请对象的请求。工厂内部有一个维护了所有对象的”仓库“。每一次会先检查这个仓库中是否存在了这个对象。若存在便返回，若不存在，就创建一个新的对象返回，并将其添加进这个仓库中。

```c++
class Flyweight;

// 工厂对象
class FlyweightFactory
{
public:
    FlyweightFactory();

    ~FlyweightFactory();

    Flyweight *GetFlyweight(const std::string &key);

private:
    std::map<std::string, Flyweight *> store_;
};

class Flyweight
{
public:
    virtual ~Flyweight() = default;

    virtual void Operation(int extrinsic_state) = 0;
};

class ConcreteFlyweight : public Flyweight
{
public:
    void Operation(int extrinsic_state) override;
};

class UnShareConcreteFlyweight : public Flyweight
{
public:
    void Operation(int extrinsic_state) override;
};

FlyweightFactory::FlyweightFactory()
= default;

FlyweightFactory::~FlyweightFactory()
= default;

Flyweight *FlyweightFactory::GetFlyweight(const std::string &key)
{
    if (store_.find(key) == store_.end()) {
        store_[key] = new ConcreteFlyweight();
    }
    return store_[key];
}

void ConcreteFlyweight::Operation(int extrinsic_state)
{
    std::cout << "ConcreteFlyweight: " << extrinsic_state << std::endl;
}

void UnShareConcreteFlyweight::Operation(int extrinsic_state)
{
    std::cout << "UnShareConcreteFlyweight: " << extrinsic_state << std::endl;
}
```

测试状态输出：

```c++
int main()
{
    // 外部状态
    int extrinsic_state = 10;

    FlyweightFactory *ff = new FlyweightFactory();

    Flyweight *f1 = ff->GetFlyweight("test1");
    f1->Operation(--extrinsic_state);

    Flyweight *f2 = ff->GetFlyweight("test2");
    f2->Operation(--extrinsic_state);

    // 理论上也可共享
    UnShareConcreteFlyweight *f3 = new UnShareConcreteFlyweight();
    f3->Operation(--extrinsic_state);

    delete f3;
    delete f2;
    delete f1;
    delete ff;

    return 0;
}
```

![Gp7joC](https://cdn.jsdelivr.net/gh/chongg039/blog-pic-repo@master/uPic/Gp7joC.png#center)

### 外观模式(Facade Pattern)

外观模式就是提供一个Facade接口类，负责与客户端直接交互。外观类管理各个功能组件的对外接口，屏蔽内部各个组件的功能细节和相互联系。Facade模式实现了子系统和客户端之间的松耦合关系，而子系统的内部各个部件通常都是紧耦合的。

```c++
class ClsA
{
public:
    void Method();
};

class ClsB
{
public:
    void Method();
};

class ClsC
{
public:
    void Method();
};

class Facade
{
public:
    Facade();

    ~Facade();

    void FuncA();

    void FuncB();

private:
    ClsA *cls_a_;
    ClsB *cls_b_;
    ClsC *cls_c_;
};

void ClsA::Method()
{
    std::cout << "this is class A's method, do something..." << std::endl;
}

void ClsB::Method()
{
    std::cout << "this is class B's method, do something..." << std::endl;
}

void ClsC::Method()
{
    std::cout << "this is class C's method, do something..." << std::endl;
}

Facade::Facade()
        : cls_a_(new ClsA()), cls_b_(new ClsB()), cls_c_(new ClsC())
{}

Facade::~Facade()
{
    if (cls_a_) {
        delete cls_a_;
        cls_a_ = nullptr;
    }
    if (cls_b_) {
        delete cls_b_;
        cls_b_ = nullptr;
    }
    if (cls_c_) {
        delete cls_c_;
        cls_c_ = nullptr;
    }
}

void Facade::FuncA()
{
    cls_a_->Method();
    cls_b_->Method();
    cls_c_->Method();
}

void Facade::FuncB()
{
    cls_b_->Method();
    cls_c_->Method();
    cls_a_->Method();
}
```

对外接口FuncA和FuncB一般是不变的，内部逻辑则不需要客户端关心：

```c++
int main()
{
    Facade facade;
    facade.FuncA();
    facade.FuncB();
    return 0;
}
```

### 代理模式(Proxy Pattern)

先来看下Wikipedia中代理模式的UML图：

![oPcmCP](https://cdn.jsdelivr.net/gh/chongg039/blog-pic-repo@master/uPic/oPcmCP.jpg#center)

实际上代理模式的想法很简单，Proxy中存放了RealSubject的引用或指针来完成对其实例的访问。同时Proxy继承Subject，提供了一个相同的接口，这样就可以完整RealSubject接口提供的任务。

```c++
class Subject
{
public:
    virtual ~Subject() = default;

    virtual void DoAction() = 0;
};

class RealSubject : public Subject
{
public:
    void DoAction() override;
};

class Proxy : public Subject
{
public:
    Proxy();

    ~Proxy() override;

    explicit Proxy(RealSubject *real);

    void DoAction() override;

private:
    RealSubject *real_;
};

void RealSubject::DoAction()
{
    std::cout << "real subject do some work..." << std::endl;
}

Proxy::Proxy()
        : real_(new RealSubject())
{}

Proxy::~Proxy()
{
    if (real_) {
        delete real_;
        real_ = nullptr;
    }
}

Proxy::Proxy(RealSubject *real)
        : real_(real)
{}

void Proxy::DoAction()
{
    std::cout << "proxy now works as real..." << std::endl;
    real_->DoAction();
}
```

这里用单参构造函数看一下：

```c++
int main()
{
    RealSubject *real_subject = new RealSubject();
    real_subject->DoAction();

    Proxy proxy(real_subject);
    proxy.DoAction();

    return 0;
}
```

![57sTdk](https://cdn.jsdelivr.net/gh/chongg039/blog-pic-repo@master/uPic/57sTdk.png#center)

一般来说以下情景可以用到Proxy模式：

1. 创建开销较大的对象的过程，交给Proxy去做，称为虚代理；
2. 为网络上的对象创建一个局部的本地代理，操作过程交给一个代理去做，称为远程代理；
3. 对对象进行（不同访问权限的）控制访问时，交给一个代理去完成，称为保护代理；
4. 智能指针当调用真实对象时，使用代理处理一些其他事务。

### 总结

至此GOF中的7种结构性模式也就学习完毕了，总的来说我觉得比较实用的就是装饰器模式、适配器模式和代理模式，其他的多多少少都比较直观，编写代码的时候也基本能想得到。

