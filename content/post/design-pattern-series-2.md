---
title: "[设计模式系列]工厂模式"
date: 2020-02-19T15:41:09+08:00
Description: ""
Tags: [DesignPattern]
Categories: [C++]

---

![EsvpmQ4zp5Y](https://cdn.jsdelivr.net/gh/chongg039/blog-pic-repo@master/uPic/EsvpmQ4zp5Y.jpg#center)

这一节再来讨论一下工厂模式，主要分为三种：简单工厂模式、工厂方法模式和抽象工厂模式。其实说实话我觉得工厂模式比单例模式还要直观一点，因为这就是多态基类实例化子类的方式，自然而然就能想到。

考虑以下这个实例，一份完整的数学选择题应该会包含如下几个部分：

1. 题干部分(stem)
2. 小问部分(sub_stem)
3. 选项部分(option)
4. 解答过程(solution)

在进行设计时，需要对这四个部分抽象出相同的特性。那么这四个部分不论怎么变化，首先都是“语句(sentence)”（这里并不是指字符串的意思）。那么我们就可以在Sentence基类的基础上讨论工厂方法的实现方式。

```c++
// 基类, 不能被实例化
class Sentence
{
public:
    virtual ~Sentence() = default;

    virtual void ShowMsg() = 0;
};

// 题干
class Stem : public Sentence
{
public:
    void ShowMsg() override
    {
        std::cout << "this is a stem" << std::endl;
    };
};

// 小问
class SubStem : public Sentence
{
public:
    void ShowMsg() override
    {
        std::cout << "this is a sub_stem" << std::endl;
    };
};

// 选项
class Option : public Sentence
{
public:
    void ShowMsg() override
    {
        std::cout << "this is an option" << std::endl;
    };
};

// 解答过程
class Solution : public Sentence
{
public:
    void ShowMsg() override
    {
        std::cout << "this is a solution" << std::endl;
    };
};
```

### 简单工厂模式(Simple Factory)

简单工厂模式就是多态，C++中基类的指针或引用都可以调用子类的虚函数达到多态的效果，看例子：

```c++
enum SentenceType
{
    kStem,
    kSubStem,
    kOption,
    kSolution,
};

// 简单工厂模式
class SimpleFactory
{
public:
    Sentence *GenerateSentence(SentenceType type);
};

Sentence *SimpleFactory::GenerateSentence(SentenceType type)
{
    switch (type) {
        case kStem:
            return new Stem;
        case kSubStem:
            return new SubStem;
        case kOption:
            return new Option;
        case kSolution:
            return new Solution;
        default:
            return nullptr;
    }
}
```

注意调用简单工厂模式产生的对象是在堆上分配的，需要手动delete掉。其实智能指针也可以实现多态，而且可以帮我们管理堆上的资源：

```c++
// 使用智能指针的简单工厂模式
class SimpleFactorySmartPtr
{
public:
    std::unique_ptr<Sentence> GenerateSentence(SentenceType type);
};

std::unique_ptr<Sentence> SimpleFactorySmartPtr::GenerateSentence(SentenceType type)
{
    switch (type) {
        case kStem:
            return std::unique_ptr<Sentence>(new Stem);
        case kSubStem:
            return std::unique_ptr<Sentence>(new SubStem);
        case kOption:
            return std::unique_ptr<Sentence>(new Option);
        case kSolution:
            return std::unique_ptr<Sentence>(new Solution);
        default:
            return nullptr;
    }
}
```

这样不需要我们手动释放资源了，看起来也会更简洁。

### 工厂方法模式(Factory Method)

对一个Factory进行进一步的抽象，每个Factory子类仅负责对应Sentence子类的产出。对应的工厂子类相当于自己的生产线只生产一类产品。

```c++
// 工厂方法模式
class SentenceFactory
{
public:
    virtual ~SentenceFactory()
    {};

    virtual Sentence *GenerateSentence() = 0;
};

class StemFactory : public SentenceFactory
{
public:
    Sentence *GenerateSentence() override
    {
        return new Stem;
    }
};

class SubStemFactory : public SentenceFactory
{
    Sentence *GenerateSentence() override
    {
        return new SubStem;
    }
};

class OptionFactory : public SentenceFactory
{
    Sentence *GenerateSentence() override
    {
        return new Option;
    }
};

class SolutionFactory : SentenceFactory
{
    Sentence *GenerateSentence() override
    {
        return new Solution;
    }
};
```

想要避免delete资源，可以用智能指针的方式，这里就不写了。

### 抽象工厂模式(Abstract Factory)

抽象工厂模式就是假设“工厂“不止可以生产Sentence，也可以生产Word、Paragraph等多种产品，只需要在抽象工厂中声明相应的虚函数即可，因此我也就不写了，和工厂方法模式如出一辙。

抽象工厂模式是工厂模式中最具有一般性、灵活性的模式，用户不需要关心具体产品的接口，只需要看工厂”生产“什么便”取用“什么即可，但为之带来的却是每当添加一个新的产品，就需要去工厂类中修改代码、添加新的方法，一定程度上增加了代码量。

### 总结

常用工厂模式也就是大概这些东西，需要注意的是抽象工厂模式一般结合上一篇的单例模式一起使用，理由也很简单，工厂只有一个是符合人们的一般认知的。不过使用的时候还是需要根据实际情况选择合适的模式。