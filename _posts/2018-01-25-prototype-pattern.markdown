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

Manager 类扮演了 Client 的角色。在这个例子中，TODO。。。

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













