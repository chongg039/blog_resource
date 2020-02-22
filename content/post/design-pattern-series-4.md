---
title: "[设计模式系列]桥接模式与适配器模式"
date: 2020-02-21T21:04:39+08:00
Description: ""
Tags: [DesignPattern]
Categories: [C++]

---

![1_CMoFsPfso](https://cdn.jsdelivr.net/gh/chongg039/blog-pic-repo@master/uPic/1_CMoFsPfso.jpg#center)

这篇文章进入GOF中的结构型模式篇幅。

### Bridge模式

桥接模式号称设计模式中最难理解的模式，同时用好了能够很大程度上降低模块之间的耦合程度。因为本质上桥接模式实现了抽象化(Abstraction)与实现化(Implementation)的解耦，将继承关系转变为了组合关系，减少了代码编写量。

说得通俗一点，就是一个实现中若涉及多个不同层次的继承关系，如果不将其合理解耦，则会产生很多设计上冗余的代码。[cabinriver](https://blog.csdn.net/cabinriver/article/details/8924789)写的这篇文章中举了一个非常棒的例子，我也暂时没有想到更合适的，就在这里重复一下，方便学习记录。感谢他的工作。

一台电脑可能有不同的制造商，每台电脑又可以安装多种不同的操作系统。电脑上在生产一台电脑时，需要生产出安装了不同操作系统的电脑。那么我们就应该将操作系统这个继承体系分离出去：

```c++
// 操作系统
class OS
{
public:
    virtual ~OS() = default;

    virtual void Run() = 0;
};

class Linux : public OS
{
public:
    void Run() override;
};

class Windows : public OS
{
public:
    void Run() override;
};

// 不同品牌的电脑
class Computer
{
public:
    virtual ~Computer() = default;

    virtual void Install(OS *os) = 0;
};

class Dell : public Computer
{
public:
    void Install(OS *os) override;
};

class Apple : public Computer
{
public:
    void Install(OS *os) override;
};

void Linux::Run()
{
    std::cout << "Linux running..." << std::endl;
}

void Windows::Run()
{
    std::cout << "Windows running..." << std::endl;
}

void Dell::Install(OS *os)
{
    std::cout << "Dell install os..." << std::endl;
    os->Run();
}

void Apple::Install(OS *os)
{
    std::cout << "Apple install os..." << std::endl;
    os->Run();
}
```

这不是简单的继承层次，也不像Builder模式一样侧重构建的过程，而是采用了合理的方式将多种继承层次组合，降低了耦合度。用好Bridge模式是一件很不容易的事情。

### Adapter模式

比较官方的说法是将一个类的接口转换为另外一个类中用户需要的接口，原本这两个是不兼容的。有句老话”如果一个东西走起路来像鸭子，叫起来也像鸭子，那么他就是一只鸭子“。对鸭子调用相应的接口是可以的，现在来了一只鹦鹉，它模仿鸭子走路的姿势，它模仿鸭子的叫声，但是我们不能对它调用鸭子的接口，为什么？因为它不是一只鸭子。

那么我们如何让一只鹦鹉调用鸭子的接口呢，很简单，把调用鸭子的接口适配鹦鹉对象就行了。不过一般来说，鸭子接口是用户使用的，不希望去对他进行更改。那么反过来我们编写一个鹦鹉适配器的类，继承鸭子类这个接口，适配器类中保存一个鹦鹉类的对象指针就可以了，这种适配器方法称为对象适配器。

```c++
// 鸭子接口类
class Duck
{
public:
    virtual ~Duck() = default;

    virtual void DuckWalk();

    virtual void DuckQuack();
};

// 鹦鹉类
class Parrot
{
public:
    void ParrotFly();

    void ParrotSay();
};

// 鹦鹉适配器
class ParrotAdapter : public Duck
{
public:
    explicit ParrotAdapter(Parrot *parrot);

    ~ParrotAdapter() override;

    void DuckWalk() override;

    void DuckQuack() override;

private:
    Parrot *parrot_;
};

void Duck::DuckWalk()
{
    std::cout << "duck walking" << std::endl;
}

void Duck::DuckQuack()
{
    std::cout << "duck saying: gaga" << std::endl;
}

void Parrot::ParrotFly()
{
    std::cout << "parrot flying" << std::endl;
}

void Parrot::ParrotSay()
{
    std::cout << "actually im a parrot" << std::endl;
}

ParrotAdapter::ParrotAdapter(Parrot *parrot)
        : parrot_(parrot)
{}

ParrotAdapter::~ParrotAdapter()
{
    if (parrot_) {
        delete parrot_;
        parrot_ = nullptr;
    }
}

void ParrotAdapter::DuckWalk()
{
    parrot_->ParrotFly();
}

void ParrotAdapter::DuckQuack()
{
    parrot_->ParrotSay();
}
```

然后生成一个适配器实例，可以调用鸭子接口的方法了：

```c++
int main()
{
    ParrotAdapter parrot_adapter(new Parrot);
    parrot_adapter.DuckWalk();
    parrot_adapter.DuckQuack();
    return 0;
}
```

适配器中的方法会转调鹦鹉成员的对应的方法：

![wysDQv](https://cdn.jsdelivr.net/gh/chongg039/blog-pic-repo@master/uPic/wysDQv.png#center)

除了对象适配器，还有类适配器。类适配器同时继承了接口类(Duck)和实现类(Parrot)，在适配器中重写相应的方法。类适配器耦合度较高，且使用了多继承，不是很推荐使用。

### 总结

桥接模式和适配器模式都涉及数据结构的高度解耦，非常考验开发者的设计能力和工程素养。