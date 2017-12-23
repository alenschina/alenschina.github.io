---
layout:     post
title:      "设计模式 -- 把具体处理交给子类"
subtitle:   " \"Template Method Pattern\""
date:       2017-12-22 21:00:00
author:     "Aaron"
header-img: "img/post-bg-2015.jpg"
catalog: false
tags:
    - 设计模式
---

## 正文

可能你没有注意，但模板方法模式是被广泛应用在日常开发中的一种基本模式。事实上，当你使用继承或者多态概念的时候，就正在使用模板方法模式。

### 1. 概述
简单地说，Template method 就是将处理流程抽象出来，构成一个模板（父类）。模板并不关心具体的实现，而是把处理逻辑交给模板的实现（子类）。查看父类的代码是无法了解这些方法最终会如何进行处理，只有子类才决定具体的逻辑。但是无论子类如果处理，都会遵循父类的流程框架。

像这样**在父类中定义处理流程的框架，在子类中实现具体处理**的模式就被称为 Template method 模式。
<br />

### 2. 模式类图

<img class="shadow" src="/img/in-post/templatepattern/template-1.png" width="260">
<br />

### 3. 模式角色

> **AbstractClass** AbstractClass 负责实现模板，同时负责声明模板中的抽象方法。这些抽象方法由子类 ConcreteClass 角色负责实现。
> **ConcreteClass** ConcreteClass 负责实现 AbstractClass 角色中定义的抽象方法。
<br />

### 4. 代码示例

假设我们想将一段字符或者字符串循环打印5次，我们要怎么做呢？程序对于字符和字符串的打印处理是不同的，但是处理流程本身却是相同的（打印5次），这样的情况，就很适合使用模板方法模式。

我们先定义一个抽象类 AbstractDisplay，这个类会定义一个display方法，而这个方法会依次调用 open 、print 、 close 这3个方法。虽然这3个都被声明了，但是并没有方法体实现。在这里，调用抽象方法的 display 方法就是**模板方法**。

> AbstractDisplay.java

```java
public abstract class AbstractDisplay {
    public abstract void open();

    public abstract void print();

    public abstract void close();

    public final void display() {
        open();
        for (int i = 0; i < 5; i++) {
            print();
        }
        close();
    }

}
```

之后，我们为打印字符和字符串分别定义各自的实现类 CharDisplay 和 StringDisplay

> CharDisplay.java


```java
public class CharDisplay extends AbstractDisplay {
    private char ch;

    public CharDisplay(char ch) {
        this.ch = ch;
    }

    @Override
    public void open() {
        System.out.print("<<");
    }

    @Override
    public void print() {
        System.out.print(ch);
    }

    @Override
    public void close() {
        System.out.println(">>");
    }
}
```
调用方式
```java
AbstractDisplay charDisplay = new CharDisplay('A');
charDisplay.display();
```

输出结果为
```
<<AAAAA>>
```

> StringDisplay.java

```java
public class StringDisplay extends AbstractDisplay {
    private String string;

    private int width;

    public StringDisplay(String string) {
        this.string = string;
        this.width = string.getBytes().length;
    }

    @Override
    public void open() {
        printLine();
    }

    @Override
    public void print() {
        System.out.println("|" + string + "|");
    }

    @Override
    public void close() {
        printLine();
    }

    private void printLine() {
        System.out.print("+");
        for (int i = 0; i < this.width; i++) {
            System.out.print("-");
        }
        System.out.println("+");
    }
}
```
调用方式
```java
AbstractDisplay stringDisplay = new StringDisplay("Hello world");
stringDisplay.display();
```

输出结果为
```
+-----------+
|Hello world|
|Hello world|
|Hello world|
|Hello world|
|Hello world|
+-----------+
```
<br />


### 5. 延展阅读

**模板方法模式的优势**

Template method 的优点显而易见，由于在父类的模板方法中已经编写是算法（display），因此无需在每个子类中再去编写相同逻辑的算法。这就是所谓使逻辑处理通用化。

**抽象类的意义？**

对于抽象类，我们无法生成其实例。在初学抽象类的时候，有人会这样问：“无法生成实例的类有什么用呢？” 在理解 Template method 模式之后，大家应该能够稍微体会到抽象类的意义了吧。由于在抽象类中没有编写具体的实现，所以我们无法知道在抽象类中到底进行了什么处理。但是我们可以决定抽象方法的名字，然后通过调用使用了这些抽象方法的模板方法去编写处理。虽然具体的处理逻辑是由子类实现的，不过**在抽象类阶段确定处理的流程**非常重要。

**父类与子类的一致性**

在实例代码中，无论是 CharDisplay 的实例还是 StringDisplay 的实例，都是保存在 AbstractDisplay 类型变量中的，然后再来调用 display 方法。使用父类型保存子类型的优点是，即使没有用 instanceof 等指定子类的种类，程序也可以正常工作。这种原则成为**里氏替换原则**（The Liskov Substitution Priciple, LSP）。

<br />