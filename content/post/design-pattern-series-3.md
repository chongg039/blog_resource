---
title: "[设计模式系列]建造者模式与原型模式"
date: 2020-02-20T10:32:08+08:00
Description: ""
Tags: [DesignPattern]
Categories: [C++]

---

![x9WzKBJGYZs](https://cdn.jsdelivr.net/gh/chongg039/blog-pic-repo@master/uPic/x9WzKBJGYZs.jpg#center)

本篇再来学习一下GOF中提到的创建型模式的最后两种：建造者模式(Builder Pattern)和原型模式(Prototype Pattern)。

### 建造者模式(Builder Pattern)

Builder模式的作用是将一个复杂对象的构建与它的表示分离，使得同样的构建过程可以创建不同的表示。和抽象工厂模式有类似之处，建造者模式也是用来创建大而复杂的对象，只不过更强调按照某种顺序一步步构建的这个过程，并且通过相同的创建过程可以获得不同的最终对象。

一般而言建造者模式由一个Builder基类和一个具体的Director类构成。与抽象工厂模式不同的另一点在于Builder一般不直接返回一个对象，只是规定了所有属性的接口和初始化顺序，具体的对象生成工作交由Director中的一个方法来完成。

考虑上篇文章那个例子，我们要为Stem、SubStem、Option和Solution生成要保存的信息，假设每个里面都需要按顺序初始化下面两个部分：

```c++
// InfoBuilder
class InfoBuilder
{
public:
    virtual ~InfoBuilder() = default;

    // 构建Entity部分
    virtual void BuildEntityPart() = 0;

    // 构建Relation部分
    virtual void BuildRelationPart() = 0;
};

// 题干信息Builder
class StemInfoBuilder : public InfoBuilder
{
public:
    void BuildEntityPart() override;

    void BuildRelationPart() override;
};

// 小问信息Builder
class SubStemInfoBuilder : public InfoBuilder
{
public:
    void BuildEntityPart() override;

    void BuildRelationPart() override;
};

// InfoDirector
class InfoDirector
{
public:
    explicit InfoDirector(InfoBuilder *info_builder);

    ~InfoDirector();

    void Construct();

private:
    InfoBuilder *info_builder_;
};

void StemInfoBuilder::BuildEntityPart()
{
    std::cout << "now build entity part for stem" << std::endl;
}

void StemInfoBuilder::BuildRelationPart()
{
    std::cout << "now build relation part for stem" << std::endl;
}

void SubStemInfoBuilder::BuildEntityPart()
{
    std::cout << "now build entity part for sub_stem" << std::endl;
}

void SubStemInfoBuilder::BuildRelationPart()
{
    std::cout << "now build relation part for sub_stem" << std::endl;
}

InfoDirector::InfoDirector(InfoBuilder *info_builder)
        : info_builder_(info_builder)
{}

InfoDirector::~InfoDirector()
{
    if (info_builder_) {
        delete info_builder_;
        info_builder_ = nullptr;
    }
}

void InfoDirector::Construct()
{
    info_builder_->BuildEntityPart();
    info_builder_->BuildRelationPart();
}
```

注意我这个析构函数使用了delete，也就是代表指针指向的一定是堆上new出来的资源，不能是构造出来的对象再取地址。即：

```c++
int main()
{
    // 正确
    InfoDirector info_director1(new StemInfoBuilder);
    info_director1.Construct();

    // 错误, 将会得到"pointer being freed was not allocated"
    SubStemInfoBuilder builder;
    InfoDirector info_director2(&builder);
    info_director2.Construct();

    return 0;
}
```

每次delete一定要伴随着一次new，建议还是使用智能指针吧。

### 原型模式(Prototype Pattern)

Prototype模式的重心在于从一个已有的对象产生出其他的新的对象，对外的接口我们叫他Clone，而在这个接口内部很显然调用的就是拷贝构造函数。来看下怎么实现的：

```c++
// 原型, 通过Clone接口实例化不同的派生类
class Prototype
{
public:
    virtual ~Prototype() = default;

    virtual Prototype *Clone() = 0;
};

// 派生类A
class DerivedA : public Prototype
{
public:
    DerivedA();

    ~DerivedA() override;

    DerivedA(const DerivedA &);

    Prototype *Clone() override;
};

// 派生类B
class DerivedB : public Prototype
{
public:
    DerivedB();

    ~DerivedB() override;

    DerivedB(const DerivedB &);

    Prototype *Clone() override;
};

DerivedA::DerivedA()
{
    std::cout << "DerivedA ctor" << std::endl;
}

DerivedA::~DerivedA()
{
    std::cout << "DerivedA dtor" << std::endl;
}

DerivedA::DerivedA(const DerivedA &)
{
    std::cout << "DerivedA copy ctor" << std::endl;
}

Prototype *DerivedA::Clone()
{
    return new DerivedA(*this);
}

DerivedB::DerivedB()
{
    std::cout << "DerivedB ctor" << std::endl;
}

DerivedB::~DerivedB()
{
    std::cout << "DerivedB dtor" << std::endl;
}

DerivedB::DerivedB(const DerivedB &)
{
    std::cout << "DerivedB copy ctor" << std::endl;
}

Prototype *DerivedB::Clone()
{
    return new DerivedB(*this);
}
```

注意Clone使用的自定义拷贝构造中要使用深拷贝，new的是一个堆上的新的资源（原有资源的拷贝），所以根据原型拷贝出的对象指针不要忘记delete：

```c++
int main()
{
    // 原型a
    Prototype *prototype_a = new DerivedA();
    // clone原型a
    Prototype *copy_a = prototype_a->Clone();
    // 原型b
    Prototype *prototype_b = new DerivedB();
    // clone原型a
    Prototype *copy_b = prototype_b->Clone();

    delete prototype_a;
    delete copy_a;

    delete prototype_b;
    delete copy_b;

    return 0;
}
```

### 小结

抽象工厂方法、建造者模式和原型模式有相似之处。建造者模式较为侧重将复杂的构建过程屏蔽来达到”一步创建“的效果，并不直接返回对象；原型模式则侧重从自身对象拷贝出一个新的对象。