---
layout:     post
title:      "设计模式 -- 把生成实例交给子类"
subtitle:   " \"Factory Method Pattern\""
date:       2017-12-23 15:00:00
author:     "Aaron"
header-img: "img/post-bg-2015.jpg"
catalog: false
tags:
    - 设计模式
---

## 正文

工厂模式同样是一个“脍炙人口”的设计模式。今天 Aaron 选择紧接着讲工厂模式，是因为工厂模式和之前讲到的模板模式密不可分，或者说，工厂模式本身就是一种模板模式的应用。

### 1. 概述
通俗点说，利用 Template mothod 模式来构建实例的工厂，就是 Factory method 模式。在工厂模式中，父类决定实例的生成方式，但不决定所要生成的具体的类，具体的处理全部由子类负责。这样就可以将生成实例的框架和实际负责生成实例的类解耦。

<br />

### 2. 模式类图

<img class="shadow" src="/img/in-post/factorypattern/factory-1.png" width="600">
<br />

### 3. 模式角色

> **Product**
Product 角色是一个抽象类。它定义了在 Factory method 模式中生成的实例所持有的 API。而具体的处理逻辑则完全由其子类 ConcreteProduct 角色来决定。

> **Creator**
Creator 角色是负责生成 Product 角色的抽象类，但是具体的处理则由子类 ConcreteCreator 来完成。Creator 对 ConcreteCreator 一无所知，它只需要知道调用 Product 角色和生成实例的方法就可以生成 Product 的实例。
**不用 new 关键字，而是用调用生成实例的方法来生成实例，就可以防止父类与其他类耦合**

> **ConcreteProduct**
ConcreteProduct 角色决定了具体的产品逻辑。

> **ConcreteCreator**
ConcreteCreator 角色负责生成具体的产品实例。

<br />

### 4. 代码示例

我们先定义一个 Product 角色，作为一个抽象类，它只定义要处理的接口。

> Product.java
```java
public abstract class Product {
    public abstract void use();
}
```

接着我们需要一个 Factory 角色用以生成 Product 角色的实例。在这里，我们就使用了 Template method 模式。 create 方法作为模板方法，决定了生成 Product 实例的处理流程包括 createProduct 和 registerProduct 两步。
而 createProduct 和 registerProduct 两个抽象方法的具体实现则完全由 Factory 的子类决定。

> Creator.java
```java
public abstract class Factory {
    public Product create(String owner){
        Product p = createProduct(owner);
        registerProduct(p);
        return p;
    }

    public abstract Product createProduct(String owner);

    public abstract void registerProduct(Product p);
}
```

有了以上两个抽象类，我们现在就需要为他们添加实现子类，负责具体逻辑的实现。

> IDCard.java
```java
public class IDCard extends Product {
    private String owner;
    private int serial;

    IDCard(String owner, int serial) {
        this.owner = owner;
        this.serial = serial;
        System.out.println("create card for " + owner + "(" + serial + ")");
    }

    @Override
    public void use() {
        System.out.println(owner + ": use this card (" + serial + ")");
    }

    public String getOwner() {
        return owner;
    }

    public int getSerial() {
        return serial;
    }
}
```

> IDCardFactory.java
```java
public class IDCardFactory extends Factory {
    public int serial = 1000;
    private Map owners = new HashMap();

    @Override
    public Product createProduct(String owner) {
        return new IDCard(owner, serial++);
    }

    @Override
    public void registerProduct(Product idCard) {
        owners.put(((IDCard) idCard).getOwner(), ((IDCard) idCard).getSerial());
    }

    public Map getOwners() {
        return owners;
    }
}
```

最后，我们尝试使用 Factory 角色生成实例，并调用其 API。
```java
// 里式替换
Factory factory = new IDCardFactory();

// 只需要问工厂生成实例
Product idCard1 = factory.create("Aaron");
Product idCard2 = factory.create("Tom");
Product idCard3 = factory.create("Becky");

idCard1.use();
idCard2.use();
idCard3.use();
```

<br />

输出结果
```
create card for Aaron(1000)
create card for Tom(1001)
create card for Becky(1002)
Aaron: use this card (1000)
Tom: use this card (1001)
Becky: use this card (1002)
```

### 5. 延展阅读