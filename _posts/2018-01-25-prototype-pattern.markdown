---
layout:     post
title:      "设计模式 -- 用复制方式生成实例"
subtitle:   " \"Prototype Pattern\""
date:       2018-01-25 19:00:00
author:     "Aaron"
header-img: "img/post-bg-2015.jpg"
catalog: false
tags:
    - 设计模式
---


## 正文

### 1. 概述

老实说 Aaron 想了半天要怎么给 Prototype 这个鬼畜的模式写个概述。因为在我有限的经验里，确实没有用到过这样的模式。但是作为创建型模式中很重要的一类，这边还是需要好好说明一下。

我们都知道可以用关键字 new 来指定类名生成实例，但是也有不适合通过指定类名来生成实例的情况，这个时候，我们的原型模式就要登场了。

原型模式的核心就是通过 Java 中的 clone 方法实现对实例的复制，来生成新的实例。它往往被应用在直接生成实例本身太过复杂的场景下。

<br />

### 2. 模式类图
<img class="shadow" src="/img/in-post/prototypepattern/prototype-1.png" width="500">

<br />

### 3. 模式角色

> **Prototype （原型）**
Prototype 角色负责定义用于复制现有实例来生产新实例的方法。

> **ConcretePrototype （具体的原型）**
ConcretePrototype 角色负责实现复制现有实例来生产新实例的方法。

> **Client （使用者）**
Client 角色负责使用复制实例的方法生成的新实例。


<br />

### 4. 代码示例

> Product.java

Product 类扮演了 Prototype 的角色。继承了 java.lang.Cloneable 接口。

```java
public abstract class Product implements Cloneable {
    public abstract void use(String s);

    public Product createClone(){
        Product p = null;
        try {
            p = (Product)clone();
        } catch (CloneNotSupportedException e) {
            e.printStackTrace();
        }
        return p;
    }
}
```

> MessageBox.java

MessageBox 类扮演了 ConcretePrototype 的角色。是 Product 抽象类的一个实现。

```java
public class MessageBox extends Product {
    private char decochar;

    public MessageBox(char decochar) {
        this.decochar = decochar;
    }

    @Override
    public void use(String s) {
        int width = s.getBytes().length;
        printLine(width);
        System.out.println(decochar + " " + s + " " + decochar);
        printLine(width);
        System.out.println("");
    }

    private void printLine(int width) {
        for (int i = 0; i < width + 4; i++) {
            System.out.print(decochar);
        }
        System.out.println("");
    }
}
```

> UnderlinePen.java

UnderlinePen 类也扮演了 ConcretePrototype 的角色。是 Product 抽象类的一个实现。

```java
public class UnderlinePen extends Product {
    private char ulchar;

    public UnderlinePen(char ulchar) {
        this.ulchar = ulchar;
    }

    @Override
    public void use(String s) {
        int width = s.getBytes().length;
        System.out.println("\"" + s + "\"");
        printUnderLine(width);
    }

    private void printUnderLine(int width) {
        System.out.print(" ");
        for (int i = 0; i < width; i++) {
            System.out.print(ulchar);
        }
        System.out.print(" ");
        System.out.println("");
    }
}
```

> Manager.java

Manager 类扮演了 Client 的角色。在这个例子中，register 方法将 Product 接口的实现类注册到一个 Map 中，create 方法将调用某个 Product 接口的实现类的 createClone 方法创建这个实例的备份。

```java
public class Manager {
    private Map showcase = new HashMap<>();

    public void register(String protoName, Product proto) {
        this.showcase.put(protoName, proto);
    }

    public Product create(String protoName) {
        Product p = (Product) this.showcase.get(protoName);
        return p.createClone();
    }
}
```

> Main.java

最后在 Main 方法中生成 Manager 的实例，在 Manager 的实例中注册 MessageBox 和 UnderlinePen 的实例。然后通过 create 方法克隆出 MessageBox 和 UnderlinePen 的实例。

```java
public class Main {
    private static final String STRONG_MESSAGE = "strong message";
    private static final String WARN_MESSAGE = "warn message box";

    public static void main(String[] args) {
        Manager manager = new Manager();

        UnderlinePen upen = new UnderlinePen('-');
        MessageBox mbox = new MessageBox('*');

        manager.register(STRONG_MESSAGE, upen);
        manager.register(WARN_MESSAGE, mbox);

        Product p1 = manager.create(STRONG_MESSAGE);
        p1.use("Hello world");
        Product p2 = manager.create(WARN_MESSAGE);
        p2.use("Hello world");

    }
}
```

### 5. 延展阅读

**clone 方法在哪里定义？**

细心的同学可能会发现，Product 抽象类虽然实现了 Cloneable 接口，但事实上并没有实现它的任何方法，而是直接调用了 clone() 方法。这是为什么呢？

因为提到 Cloneable 接口，很容易让人有一个误区，认为 clone 方法是声明在 Cloneable 接口中的。 但实际上， clone 方法是定义在 java.lang.Object 中的，所以所有的 Java 类都继承了 clone 方法。

Cloneable 接口中并没有声明任何方法。它只是被用来标记“可以使用 clone 方法进行复制”的。这样的接口被称为**标记接口( marker interface )**。













