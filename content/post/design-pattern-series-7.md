---
title: "[设计模式系列]职责链模式与策略模式"
date: 2020-02-24T17:12:56+08:00
Description: ""
Tags: [DesignPattern]
Categories: [C++]

---

![Q1p7bh3SHj8](https://cdn.jsdelivr.net/gh/chongg039/blog-pic-repo@master/uPic/Q1p7bh3SHj8.jpg#center)

进入GOF中的最后一部分，行为模式。

### 职责链模式(Chain-of-Responsibility Pattern)

职责链模式也不难理解，就是用户提交一个请求，允许这个请求沿着一条多个具体行为对象组成的链流动，直到有某个对象处理后（或是一个最终的处理对象处理完成）返回对应的结果，用户并不知道是哪个具体的行为人处理的，用户也不需要关心这个。考虑到这个层面，所有的行为对象应该继承一个公有的接口类：

```c++
class Handler
{
public:
    virtual ~Handler() = default;

    virtual void HandleRequest(int request) = 0;

    // 设置继任者
    void SetSuccessor(std::shared_ptr<Handler> successor);

protected:
    std::shared_ptr<Handler> successor_;
};

class ConcreteA : public Handler
{
public:
    void HandleRequest(int request) override;
};

class ConcreteB : public Handler
{
public:
    void HandleRequest(int request) override;
};

class DefaultConcrete : public Handler
{
public:
    void HandleRequest(int request) override;
};

void Handler::SetSuccessor(std::shared_ptr<Handler> successor)
{
    successor_ = std::move(successor);
}

void ConcreteA::HandleRequest(int request)
{
    if (1 == request) {
        std::cout << "concrete A handle something" << std::endl;
    } else if (successor_) {
        successor_->HandleRequest(request);
    }
}

void ConcreteB::HandleRequest(int request)
{
    if (2 == request) {
        std::cout << "concrete B handle something" << std::endl;
    } else if (successor_) {
        successor_->HandleRequest(request);
    }
}

void DefaultConcrete::HandleRequest(int request)
{
    std::cout << "default concrete final handle something" << std::endl;
}
```

设置了两个处理实例和一个默认处理实例，将它们连接起来：

```c++
int main()
{
    int r1 = 1, r2 = 2, r3 = 3;
    auto c1 = std::make_shared<ConcreteA>();
    auto c2 = std::make_shared<ConcreteB>();
    auto c3 = std::make_shared<DefaultConcrete>();

    // 设置链
    c1->SetSuccessor(c2);
    c2->SetSuccessor(c3);

    c1->HandleRequest(r1);
    c1->HandleRequest(r2);
    c1->HandleRequest(r3);

    return 0;
}
```

最终将在这条链上不同的位置得到处理：

![XKcIPc](https://cdn.jsdelivr.net/gh/chongg039/blog-pic-repo@master/uPic/XKcIPc.png#center)

### 策略模式(Strategy Pattern)

策略模式定义了一个算法族，使用者在调用接口时，算法的变化不会影响对外接口的变化。

```c++
// 算法族
class Strategy
{
public:
    virtual ~Strategy() = default;

    virtual void AlgorithmInterface() = 0;
};

class ConcreteA : public Strategy
{
public:
    void AlgorithmInterface() override;
};

class ConcreteB : public Strategy
{
public:
    void AlgorithmInterface() override;
};

// 暴露给用户的上下文
class Context
{
public:
    explicit Context(std::shared_ptr<Strategy> strategy);

    void ContextInterface();

private:
    std::shared_ptr<Strategy> strategy_;
};

void ConcreteA::AlgorithmInterface()
{
    std::cout << "use A algorithm" << std::endl;
}

void ConcreteB::AlgorithmInterface()
{
    std::cout << "use B algorithm" << std::endl;
}

Context::Context(std::shared_ptr<Strategy> strategy)
    : strategy_(std::move(strategy))
{}

void Context::ContextInterface()
{
    strategy_->AlgorithmInterface();
}
```

上下文接口一次只能使用一种算法，一个容易想到的方式就是和简单工厂模式相结合，声明一个ContextFactory，根据不同的情况选择不同的算法。

### 总结

职责链模式和策略模式都是简单但非常实用的设计模式。