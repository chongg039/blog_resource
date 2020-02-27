---
title: "[设计模式系列]命令模式与访问者模式"
date: 2020-02-27T09:08:40+08:00
Description: ""
Tags: [DesignPattern]
Categories: [C++]

---

![GjXaHFnIOn0](https://cdn.jsdelivr.net/gh/chongg039/blog-pic-repo@master/uPic/GjXaHFnIOn0.jpg#center)

命令模式和访问者模式也是两种常见的行为模式。

### 命令模式(Command Pattern)

Command模式结构图中，将请求的接收者（处理者）放到Command的具体子类ConcreteCommand中，当请求到来时（Invoker发出Notify消息激活 Command对象），ConcreteCommand将处理请求交给Receiver对象进行处理。

```c++
// 具体的接收人
class Receiver
{
public:
    void Action();
};

// 抽象类
class Command
{
public:
    virtual ~Command() = default;

    virtual void set_receiver(Receiver *receiver) = 0;

    virtual void Execute() = 0;
};

class ConcreteCommand : public Command
{
public:
    void set_receiver(Receiver *receiver) override;

    void Execute() override;

private:
    Receiver *receiver_;
};

class Invoker
{
public:
    void set_command(Command *command);

    void Notify();

private:
    std::list<Command *> commands_;
};

void Receiver::Action()
{
    std::cout << "receiver do action..." << std::endl;
}

void ConcreteCommand::set_receiver(Receiver *receiver)
{
    receiver_ = receiver;
}

void ConcreteCommand::Execute()
{
    receiver_->Action();
}

void Invoker::set_command(Command *command)
{
    commands_.push_back(command);
}

void Invoker::Notify()
{
    for (auto &command : commands_) {
        command->Execute();
    }
}
```

```c++
int main()
{
    Command *c = new ConcreteCommand();
    Receiver *r = new Receiver();
    c->set_receiver(r);

    Invoker invoker;
    invoker.set_command(c);
    invoker.Notify();

    delete r;
    delete c;

    return 0;
}
```

### 访问者模式(Visitor Pattern)

访问者模式就是保证不改变一个类的前提下，对这个类的元素定义一种新的操作，非常适用于不断变更系统需求的情况...

看下Wikipedia上的UML图：

![dNZMTl](https://cdn.jsdelivr.net/gh/chongg039/blog-pic-repo@master/uPic/dNZMTl.jpg#center)

该模式的关键就是accept操作是一个"Double-Dispatch"的，即具体调用哪个accept有两个因素决定，一是动态绑定的具体的Element类型，而是accept的参数Visitor的类型，也是一个动态绑定的型别参数。

```c++
class Element;

class ConcreteElementA;

class ConcreteElementB;

class Visitor
{
public:
    virtual ~Visitor() = default;

    virtual void Visit(ConcreteElementA *element) = 0;

    virtual void Visit(ConcreteElementB *element) = 0;
};

class ConcreteVisitorA : public Visitor
{
public:
    void Visit(ConcreteElementA *element) override;

    void Visit(ConcreteElementB *element) override;
};

class ConcreteVisitorB : public Visitor
{
public:
    void Visit(ConcreteElementA *element) override;

    void Visit(ConcreteElementB *element) override;
};

// 元素
class Element
{
public:
    virtual ~Element() = default;

    virtual void Accept(Visitor *visitor) = 0;
};

class ConcreteElementA : public Element
{
public:
    void Accept(Visitor *visitor) override;
};

class ConcreteElementB : public Element
{
public:
    void Accept(Visitor *visitor) override;
};

void ConcreteVisitorA::Visit(ConcreteElementA *element)
{
    std::cout << "visitor A visit element A" << std::endl;
}

void ConcreteVisitorA::Visit(ConcreteElementB *element)
{
    std::cout << "visitor A visit element B" << std::endl;
}

void ConcreteVisitorB::Visit(ConcreteElementA *element)
{
    std::cout << "visitor B visit element A" << std::endl;
}

void ConcreteVisitorB::Visit(ConcreteElementB *element)
{
    std::cout << "visitor B visit element B" << std::endl;
}

void ConcreteElementA::Accept(Visitor *visitor)
{
    std::cout << "can visit element A" << std::endl;
    visitor->Visit(this);
}

void ConcreteElementB::Accept(Visitor *visitor)
{
    std::cout << "can visit element B" << std::endl;
    visitor->Visit(this);
}
```

测试一下效果：

```c++
int main()
{
    Element *ea = new ConcreteElementA();
    Element *eb = new ConcreteElementB();

    Visitor *va = new ConcreteVisitorA();
    Visitor *vb = new ConcreteVisitorB();

    ea->Accept(va);
    ea->Accept(vb);
    eb->Accept(va);
    eb->Accept(vb);

    delete va;
    delete vb;

    delete ea;
    delete eb;

    return 0;
}
```

![bRaSYf](https://cdn.jsdelivr.net/gh/chongg039/blog-pic-repo@master/uPic/bRaSYf.png#center)

理解访问者模式的关键就是使用"Double-Dispatch"，单继承多态可以认为是"One-Dispatch"的，做到这样需要将另一个动态绑定体现在参数中。
