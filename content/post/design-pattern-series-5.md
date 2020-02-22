---
title: "[设计模式系列]装饰模式与组合模式"
date: 2020-02-22T11:01:40+08:00
Description: ""
Tags: [DesignPattern]
Categories: [C++]

---

![tk9RQCq5eQo](https://cdn.jsdelivr.net/gh/chongg039/blog-pic-repo@master/uPic/tk9RQCq5eQo.jpg#center)

这篇来学习一下装饰模式与组合模式，也是结构型模式中的内容。

### 装饰模式(Decorator Pattern)

顾名思义，装饰模式就是往一个现有的产品上添加功能部件，增加一些新的额外职责，将这个被嵌入的对象称为职责。注意这不是对所有的类都进行添加，而是对某些实例化的对象进行添加。并且，所有的装饰应该继承同一个装饰接口。

与Bridge模式很相似，侧重的也是单一职责与组合代替继承。考虑和之前相同的例子，对于题干Stem和解题过程，可以将抽取实体和关系的行为与其分离，作为行为装饰在具体的对象中。

```c++
// sentence基类，将装饰行为与其分离
class Sentence
{
public:
    virtual ~Sentence() = default;

    virtual void ShowDecorate() = 0;
};

// stem子类实例
class Stem : public Sentence
{
public:
    void ShowDecorate() override;
};

// solution子类实例
class Solution : public Sentence
{
public:
    void ShowDecorate() override;
};

// 装饰行为基类
class Decorator : public Sentence
{
public:
    explicit Decorator(Sentence *sentence);

    ~Decorator() override;

    void ShowDecorate() override;

private:
    Sentence *sentence_;
};

// 装饰类EntityDecorator
class EntityDecorator : public Decorator
{
public:
    explicit EntityDecorator(Sentence *sentence);

    void ShowDecorate() override;

private:
    void ExtractEntity();
};

// 装饰类RelationDecorator
class RelationDecorator : public Decorator
{
public:
    explicit RelationDecorator(Sentence *sentence);

    void ShowDecorate() override;

private:
    void ExtractRelation();
};

void Stem::ShowDecorate()
{
    std::cout << "stem's decorator" << std::endl;
}

void Solution::ShowDecorate()
{
    std::cout << "solution's decorator" << std::endl;
}

Decorator::Decorator(Sentence *sentence)
        : sentence_(sentence)
{}

Decorator::~Decorator()
{
    if (sentence_) {
        delete sentence_;
        sentence_ = nullptr;
    }
}

void Decorator::ShowDecorate()
{
    sentence_->ShowDecorate();
}

EntityDecorator::EntityDecorator(Sentence *sentence)
        : Decorator(sentence)
{}

void EntityDecorator::ShowDecorate()
{
    Decorator::ShowDecorate();
    ExtractEntity();
}

void EntityDecorator::ExtractEntity()
{
    std::cout << "extract some entities" << std::endl;
}

RelationDecorator::RelationDecorator(Sentence *sentence)
        : Decorator(sentence)
{}

void RelationDecorator::ShowDecorate()
{
    Decorator::ShowDecorate();
    ExtractRelation();
}

void RelationDecorator::ExtractRelation()
{
    std::cout << "extract some relations" << std::endl;
}
```

注意装饰器基类要继承Sentence接口的方法，现在我们的程序就可以这么写：

```c++
int main()
{
    Sentence *stem = new Stem;
    // 抽取实体，stem已经被接管
    Sentence *stem_e = new EntityDecorator(stem);
    // 抽取关系，stem_e已经被接管
    Sentence *stem_e_r = new RelationDecorator(stem_e);

    stem_e_r->ShowDecorate();

    // stem_e_r需要手动释放
    delete stem_e_r;

    return 0;
}
```

装饰模式对于动态的添加一些组件可以非常方便地实现。

### 组合模式(Composite Pattern)

GOF将组合模式定义为：将对象组合成树形结构以表示“部分整体”的层次结构，组合模式使得用户对单个对象和组合对象的使用具有一致性。

树形结构就是数据结构中的树，从根节点开始的树和每个子树理应具备一致的结构与功能。一般来说，需要声明一个公有的接口类实现所有一致的行为。且对于所有的叶子节点，不能再有子节点。

UML图来自Wikipedia：

![Kp25GW](https://cdn.jsdelivr.net/gh/chongg039/blog-pic-repo@master/uPic/Kp25GW.jpg#center)

[Veaxen](https://www.veaxen.com/c%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F10-%E7%BB%84%E5%90%88%E6%A8%A1%E5%BC%8F.html)的这篇博客中提到了一个总公司与分公司的案例，很贴切，我暂时没有想到这个设计模式一个比较好的现实案例，感兴趣的朋友可以去他的博客下面参考一下。

### 总结

Decorator模式可以在不生成具体子类的情况下灵活添加新的功能组件，与之相对的是Composite模式则侧重构建、表示一些具体的类，这些类在部分和整体上保持了一致性。