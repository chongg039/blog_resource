---
title: "[设计模式系列]模板模式、状态模式和观察者模式"
date: 2020-02-25T10:22:41+08:00
Description: ""
Tags: [DesignPattern]
Categories: [C++]

---

![9ivMm671L48](https://cdn.jsdelivr.net/gh/chongg039/blog-pic-repo@master/uPic/9ivMm671L48.jpg#center)

### 模板模式(Template Pattern)

模板模式类似策略模式，都是将算法接口定义在抽象类中，具体的实现细节交给派生类去做。但是策略模式偏重组合，模板模式侧重继承。模板方法模式将重复的代码交给了父类，各自的子类只负责算法不同的细节部分。

```c++
class Abstract
{
public:
    virtual ~Abstract() = default;

    void TemplateMethod();

protected:
    virtual void Method1() = 0;

    virtual void Method2() = 0;
};

class ConcreteA : public Abstract
{
public:
    void Method1() override;

    void Method2() override;
};

class ConcreteB : public Abstract
{
public:
    void Method1() override;

    void Method2() override;
};

void Abstract::TemplateMethod()
{
    Method1();
    Method2();
}

void ConcreteA::Method1()
{
    std::cout << "concrete A use method 1" << std::endl;
}

void ConcreteA::Method2()
{
    std::cout << "concrete A use method 2" << std::endl;
}

void ConcreteB::Method1()
{
    std::cout << "concrete B use method 1" << std::endl;
}

void ConcreteB::Method2()
{
    std::cout << "concrete B use method 2" << std::endl;
}
```

### 状态模式(State Pattern)

有限状态自动机就是这样一个案例，对不同的状态执行不同的动作。一个很典型的就是switch/case语句，但switch/case没有实现状态逻辑和动作的分离，导致若条件很多时将会维护很冗长且复杂的代码。

状态模式主要解决的是当控制一个对象状态转换的条件表达式过于复杂时的情况。把状态的判断逻辑转移到表示不同状态的一系列类当中，可以把复杂的判断逻辑简化。

来自Wikipedia的State Pattern的UML图：

![n9HcaC](https://cdn.jsdelivr.net/gh/chongg039/blog-pic-repo@master/uPic/n9HcaC.jpg#center)

```c++
// 需要前置声明上下文，保存了当前的状态
class Context;

class State
{
public:
    virtual ~State() = default;

    virtual void Handle(Context *c) = 0;
};

class ConcreteStateA : public State
{
public:
    void Handle(Context *c) override;
};

class ConcreteStateB : public State
{
public:
    void Handle(Context *c) override;
};

// 上下文，一个处理请求的方法，一个设置下一状态的方法
class Context
{
public:
    explicit Context(State *state);

    ~Context();

    void Request();

    void SetState(State *new_state);

private:
    State *state_;
};

void ConcreteStateA::Handle(Context *c)
{
    std::cout << "handle state A" << std::endl;
    // 设置下一状态
    c->SetState(new ConcreteStateB());
    // 记得删除原有堆上的资源
    delete this;
}

void ConcreteStateB::Handle(Context *c)
{
    std::cout << "handle state B" << std::endl;
    c->SetState(new ConcreteStateA());
    delete this;
}

Context::Context(State *state)
        : state_(state)
{}

Context::~Context()
{
    if (state_) {
        delete state_;
        state_ = nullptr;
    }
}

void Context::Request()
{
    state_->Handle(this);
}

void Context::SetState(State *new_state)
{
    state_ = new_state;
}
```

一个避免内存泄漏的方法就是在每次重设状态之后，delete原有的状态。很显然这不是异常安全的代码，不过限于篇幅也不进行过多讨论。看下状态的变化：

```c++
int main()
{
    // 初始状态
    auto *init_state = new ConcreteStateA();
    auto *c = new Context(init_state);
    c->Request();
    c->Request();
    c->Request();

    delete c;
    return 0;
}
```

![EjDZtP](https://cdn.jsdelivr.net/gh/chongg039/blog-pic-repo@master/uPic/EjDZtP.png#center)

State模式很好地实现了对象的状态逻辑和动作实现的分离，状态逻辑分布在State的派生类中实现，而动作实现则可以放在Context类中实现（这也是为什么State派生类需要拥有一个指向Context的指针）。这使得两者的变化相互独立，改变State的状态逻辑可以很容易复用Context的动作，也可以在不影响State派生类的前提下创建Context的子类来更改或替换动作实现。

### 观察者模式(Observer Pattern)

终于到了重头戏了，观察者模式也称发布-订阅模式，是影响非常深远的一种设计模式。Observer 模式要解决的问题为：建立一个一（Subject）对多（Observer）的依赖关系，并且做到当“一”变化的时候，依赖这个“一”的多也能够同步改变。

看一下Wikipedia上的UML图：

![8AS5q4](https://cdn.jsdelivr.net/gh/chongg039/blog-pic-repo@master/uPic/8AS5q4.jpg#center)

对于发布者Subject，要同时提供观测者Observer的注册(register)和注销(unregister)方法，并提供一个供所有观测者同步消息的操作(notify)。对于一个具体的观测者，要提供自己重写抽象类的更新(update)方法，注意这个方法是在得到了notify通知后observer再去调用的。

```c++
class Observer;

// 发布者
class Subject
{
public:
    void RegisterObserver(Observer *observer);

    void UnRegisterObserver(Observer *observer);

    void NotifyObservers();

private:
    std::vector<Observer *> observers_;
};

// 具体发布者
class ConcreteSubject : public Subject
{
public:
    const std::string &get_subject_state() const;

    void set_subject_state(const std::string &subjectState);

private:
    std::string subject_state_;
};

// 观察者
class Observer
{
public:
    virtual ~Observer() = default;

    virtual void Update() = 0;
};

// 具体的观察者
class ConcreteObserver : public Observer
{
public:
    explicit ConcreteObserver(ConcreteSubject *subject);

    void Update() override;

    const std::string &get_observer_state() const;

    void set_observer_state(const std::string &observerState);

private:
    std::string observer_state_;
    ConcreteSubject *subject_;
};

void Subject::RegisterObserver(Observer *observer)
{
    observers_.push_back(observer);
}

void Subject::UnRegisterObserver(Observer *observer)
{
    auto iter = std::find(observers_.begin(), observers_.end(), observer);
    if (iter != observers_.end()) {
        observers_.erase(iter);
    }
}

void Subject::NotifyObservers()
{
    for (auto &observer : observers_) {
        observer->Update();
    }
}

ConcreteObserver::ConcreteObserver(ConcreteSubject *subject)
        : subject_(subject)
{}

void ConcreteObserver::Update()
{
    observer_state_ = subject_->get_subject_state();
    std::cout << "observer state change to: " << observer_state_ << std::endl;
}

const std::string &ConcreteObserver::get_observer_state() const
{
    return observer_state_;
}

void ConcreteObserver::set_observer_state(const std::string &observerState)
{
    observer_state_ = observerState;
}

const std::string &ConcreteSubject::get_subject_state() const
{
    return subject_state_;
}

void ConcreteSubject::set_subject_state(const std::string &subjectState)
{
    subject_state_ = subjectState;
}
```

MVC便是采用了观察者模式的一个具体实例。