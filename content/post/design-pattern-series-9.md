---
title: "[设计模式系列]备忘录模式与中介者模式"
date: 2020-02-26T19:44:52+08:00
Description: ""
Tags: [DesignPattern]
Categories: [C++]

---

![5XjNhm3hz7U](https://cdn.jsdelivr.net/gh/chongg039/blog-pic-repo@master/uPic/5XjNhm3hz7U.jpg#center)

闲话少说直接进入正题。

### 备忘录模式(Memento Pattern)

备忘录模式就是可以维护一个对象的内部状态，并在对象的外部保存这个状态。这样就可以随时将对象恢复成保存的那个状态，就像吃后悔药一样，属于一个"undo"操作。

看下UML图，[来源](https://stackoverflow.com/questions/45867006/how-to-implement-memento-pattern-in-kotlin)：

![BHSqCR](https://cdn.jsdelivr.net/gh/chongg039/blog-pic-repo@master/uPic/BHSqCR.jpg#center)


```c++
class Memento
{
public:
    explicit Memento(std::string state);

    const std::string &get_state() const;

    void set_state(const std::string &state);
private:
    std::string state_;
};

// 管理者
class Caretaker
{
public:
    ~Caretaker();

public:
    Memento *get_memento() const;

    void set_memento(Memento *memento);

private:
    Memento *memento_;
};

// 发起人
class Originator
{
public:
    const std::string &get_state() const;

    void set_state(const std::string &state);

    Memento *CreateMemento();

    void SetMemento(Memento *memento);

    void Show();

private:
    std::string state_;
};

Memento::Memento(std::string state)
        : state_(std::move(state))
{}

const std::string &Memento::get_state() const
{
    return state_;
}

void Memento::set_state(const std::string &state)
{
    state_ = state;
}

Caretaker::~Caretaker()
{
    if (memento_) {
        delete memento_;
        memento_ = nullptr;
    }
}

Memento *Caretaker::get_memento() const
{
    return memento_;
}

void Caretaker::set_memento(Memento *memento)
{
    memento_ = memento;
}

const std::string &Originator::get_state() const
{
    return state_;
}

void Originator::set_state(const std::string &state)
{
    state_ = state;
}

Memento *Originator::CreateMemento()
{
    return new Memento(state_);
}

void Originator::SetMemento(Memento *memento)
{
    state_ = memento->get_state();
}

void Originator::Show()
{
    std::cout << "state is: " << state_ << std::endl;
}
```

看下效果：

```c++
int main()
{
    Originator *ori = new Originator();
    ori->set_state("On");
    ori->Show();

    // 保存状态
    Caretaker *care = new Caretaker();
    care->set_memento(ori->CreateMemento());

    ori->set_state("Off");
    ori->Show();

    // 恢复状态
    ori->SetMemento(care->get_memento());
    ori->Show();

    delete care;
    delete ori;
    return 0;
}
```

![xsBo2e](https://cdn.jsdelivr.net/gh/chongg039/blog-pic-repo@master/uPic/xsBo2e.png#center)

理论上，将Originator声明为Memento的友元类，是一种更明智的方式，即备忘录只能Originator访问，其他类不能访问。

### 中介者模式(Mediator Pattern)

对象间如何通信始终是系统设计中令人头痛的问题，互相通信的类需要调用另外一个对象的具体方法。当系统规模小是可行的，一旦这种相互通信变多，就容易像一团麻线那样剪不断理还乱。为了处理这种情况，我们自然希望有一个类充当中间人的角色，每个具体的对象只需要和这个中介者通信，具体的方法交由中介者处理，系统也由此解耦。

直接看UML：

![9yzHNZ](https://cdn.jsdelivr.net/gh/chongg039/blog-pic-repo@master/uPic/9yzHNZ.jpg#center)

```c++
class Colleague;

class ConcreteColleagueA;

class ConcreteColleagueB;

// 抽象中介者
class Mediator
{
public:
    virtual ~Mediator() = default;

    virtual void SendMsg(const std::string &msg, Colleague *colleague) = 0;
};

class ConcreteMediator : public Mediator
{
public:
    void set_colleague(ConcreteColleagueA *colleague_a);

    void set_colleague(ConcreteColleagueB *colleague_b);

    void SendMsg(const std::string &msg, Colleague *colleague) override;

private:
    ConcreteColleagueA *colleague_a_;
    ConcreteColleagueB *colleague_b_;
};

// 抽象同事
class Colleague
{
public:
    explicit Colleague(Mediator *mediator) : mediator_(mediator)
    {};

    virtual ~Colleague() = default;

protected:
    Mediator *mediator_;
};

class ConcreteColleagueA : public Colleague
{
public:
    explicit ConcreteColleagueA(Mediator *mediator);

    void Send(const std::string &msg);

    void Notify(const std::string& msg);
};

class ConcreteColleagueB : public Colleague
{
public:
    explicit ConcreteColleagueB(Mediator *mediator);

    void Send(const std::string &msg);

    void Notify(const std::string& msg);
};

void ConcreteMediator::set_colleague(ConcreteColleagueA *colleague_a)
{
    colleague_a_ = colleague_a;
}

void ConcreteMediator::set_colleague(ConcreteColleagueB *colleague_b)
{
    colleague_b_ = colleague_b;
}

void ConcreteMediator::SendMsg(const std::string &msg, Colleague *colleague)
{
    if (colleague == colleague_a_) {
        colleague_b_->Notify(msg);
    } else {
        colleague_a_->Notify(msg);
    }
}

ConcreteColleagueA::ConcreteColleagueA(Mediator *mediator)
        : Colleague(mediator)
{}

void ConcreteColleagueA::Send(const std::string &msg)
{
    mediator_->SendMsg(msg, this);
}

void ConcreteColleagueA::Notify(const std::string &msg)
{
    std::cout << "colleague A received: " << msg << std::endl;
}

ConcreteColleagueB::ConcreteColleagueB(Mediator *mediator)
        : Colleague(mediator)
{}

void ConcreteColleagueB::Send(const std::string &msg)
{
    mediator_->SendMsg(msg, this);
}

void ConcreteColleagueB::Notify(const std::string &msg)
{
    std::cout << "colleague B received: " << msg << std::endl;
}
```

这里我们构造两种类型的Colleague让他们互相通信看看：

```c++
int main()
{
    ConcreteMediator *mediator = new ConcreteMediator();
    ConcreteColleagueA *a = new ConcreteColleagueA(mediator);
    ConcreteColleagueB *b = new ConcreteColleagueB(mediator);

    mediator->set_colleague(a);
    mediator->set_colleague(b);

    a->Send("im a");
    b->Send("im b");

    delete a;
    delete b;
    delete mediator;

    return 0;
}
```

![jQQKg8](https://cdn.jsdelivr.net/gh/chongg039/blog-pic-repo@master/uPic/jQQKg8.png#center)

Mediator模式将多对多通信转化为了多对一通信，各对象之间不必维护自己的通信协议，降低了系统的复杂性。同时将控制集中起来管理，符合单一职责与集中控制原则。
