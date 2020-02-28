---
title: "[设计模式系列]迭代器模式与解释器模式"
date: 2020-02-28T09:33:01+08:00
Description: ""
Tags: [DesignPattern]
Categories: [C++]

---

![prULhL339cE](https://cdn.jsdelivr.net/gh/chongg039/blog-pic-repo@master/uPic/prULhL339cE.jpg#center)

来看下行为模式的最后两个部分。

### 迭代器模式(Iterator Pattern)

STL里面的迭代器就是这个。迭代器模式主要就是为了解决对一个聚合对象的遍历问题，将遍历行为封装到一个类型中，避免了过度暴露聚合类内部表示。

来看下Wikipedia上的UNL图：

![3cML0j](https://cdn.jsdelivr.net/gh/chongg039/blog-pic-repo@master/uPic/3cML0j.jpg#center)

```c++
class Iterator;

// 聚合对象
class Aggregate
{
public:
    virtual ~Aggregate() = default;

    virtual Iterator *CreateIterator() = 0;

    virtual int GetSize() const = 0;

    virtual int GetItem(int idx) const = 0;
};

class ConcreteAggregate : public Aggregate
{
public:
    explicit ConcreteAggregate(std::vector<int> nums);

    Iterator *CreateIterator() override;

    int GetSize() const override;

    int GetItem(int idx) const override;

private:
    std::vector<int> nums_;
};

// 迭代器抽象类
class Iterator
{
public:
    virtual ~Iterator() = default;

    virtual bool HasNext() = 0;

    virtual void Next() = 0;

    virtual int operator*() = 0;
};

class ConcreteIterator : public Iterator
{
public:
    explicit ConcreteIterator(Aggregate *aggregate, int idx = 0);

    bool HasNext() override;

    void Next() override;

    int operator*() override;

private:
    Aggregate *aggregate_;
    int idx_;
};

ConcreteAggregate::ConcreteAggregate(std::vector<int> nums)
        : nums_(std::move(nums))
{}

Iterator *ConcreteAggregate::CreateIterator()
{
    return new ConcreteIterator(this);
}

int ConcreteAggregate::GetSize() const
{
    return nums_.size();
}

int ConcreteAggregate::GetItem(int idx) const
{
    return nums_[idx];
}

ConcreteIterator::ConcreteIterator(Aggregate *aggregate, int idx)
        : aggregate_(aggregate), idx_(idx)
{}

bool ConcreteIterator::HasNext()
{
    return idx_ < aggregate_->GetSize() - 1;
}

void ConcreteIterator::Next()
{
    if (HasNext()) {
        ++idx_;
    }
}

int ConcreteIterator::operator*()
{
    return aggregate_->GetItem(idx_);
}
```

### 解释器模式(Interpreter Patter)

先看这样一张Wikipedia上的UML图：

![RKkIYQ](https://cdn.jsdelivr.net/gh/chongg039/blog-pic-repo@master/uPic/RKkIYQ.jpg#center)

解释器模式有点象bash等解释性语言的解释器，对于一种特定的文法表示，定义一个专门的解释器去解释它。如上面这张图，首先我们需要有一个Context类保存一些全文的信息，然后声明一个抽象解释类AbstractExpression，再声明一些继承这个基类的、针对不同文法表示的子类操作。比如图片中就声明了一个终结符表达式派生类TerminalExpression和非终结符表达式派生类NonterminalExpression，分别处理与终结符相关的操作和与非终结符相关的操作。

```c++
// 全局信息
class Context
{
public:
    const std::string &get_input() const;

    void set_input(const std::string &input);

    const std::string &get_output() const;

    void set_output(const std::string &output);

private:
    std::string input_;
    std::string output_;
};

// 抽象表达式解释类
class AbstractExpression
{
public:
    virtual ~AbstractExpression() = default;

    virtual void Interpret(const Context &context) = 0;
};

// 终结符表达式解释类
class TerminalExpression : public AbstractExpression
{
public:
    void Interpret(const Context &context) override;
};

// 非终结符表达式解释类
class NonTerminalExpression : public AbstractExpression
{
public:
    void Interpret(const Context &context) override;
};

const std::string &Context::get_input() const
{
    return input_;
}

void Context::set_input(const std::string &input)
{
    input_ = input;
}

const std::string &Context::get_output() const
{
    return output_;
}

void Context::set_output(const std::string &output)
{
    output_ = output;
}

void TerminalExpression::Interpret(const Context &context)
{
    std::cout << "TerminalExpression: input: " << context.get_input()
              << ", output: " << context.get_output()
              << std::endl;
}

void NonTerminalExpression::Interpret(const Context &context)
{
    std::cout << "NonTerminalExpression: input: " << context.get_input()
              << ", output: " << context.get_output()
              << std::endl;
}
```

### 总结

至此GOF中的23种设计模式就介绍完了，整个学习过程虽然有些匆忙，但还是非常有收获的。尤其是单例模式、工厂模式、观察者模式和责任链模式，恰好要用到最近做的项目当中，给我的启发很大。

下一步应该会去做一个Reactor模式的详细分析和学习，因为项目中也需要封装一个多路复用的TCP服务器，准备好好啃啃陈硕大神的书还有muduo的源码。