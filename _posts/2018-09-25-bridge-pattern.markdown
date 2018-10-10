---
layout:     post
title:      "设计模式 -- 分离功能层次结构和实现层次结构"
subtitle:   " \"Bridge Pattern\""
date:       2018-09-25 15:00:00
author:     "Aaron"
header-img: "img/post-bg-2015.jpg"
catalog: false
tags:
    - 设计模式
---


---
## 正文


### 1. 概述

桥接模式（Bridge Pattern）是一种结构型设计模式。在理解桥接模式前，我们要先能够区分“类的功能层次结构”和“类的实现层次结构”。

举一个简单的例子，比如我们设计了一个程序，它有打印字符串的功能，同时它又需要分别能够在 windows 和 linux 上运行，并且打印不同的内容。那么假定我们有一个 Program 类，类中有一个print方法，那么我们会在 ProgramWinImpl 和 ProgramLiunxImpl 中分别实现 print 方法用于打印不同的内容，这就是我们上面说到的“类的实现层次结构”的拓展。假定现在我们需要给这个程序增加一个新功能 multiPrint，用于打印多行字符串，那么我们就会创建一个 MutilProgram 来继承 Program，并且增加 multiPrint 这个方法。这就是我们上面说到的“类的功能层次结构”的拓展。如果对这两种不同维度的拓展都使用简单的继承思想，那么我们的继承关系将会非常混杂、难以理解。这时，我们就需要引入桥接模式，帮助我们搭建“类的功能层次结构”和“类的实现层次结构”之间的桥梁。

<br />

### 2. 模式类图

<img class="shadow" src="/img/in-post/bridgepattern/bridge-1.png" width="500">

<br />

### 3. 模式角色

> **Abstraction （抽象化）**
Abstraction 角色位于“类的功能层次结构”的最上层。该角色保存了Implementor角色的实例，使用Implementor角色的方法定义基本功能。

> **RefinedAbstraction （改善的抽象化）**
RefinedAbstraction 角色负责在基础上增加新的功能。

> **Implementor （实现者）**
Implementor 角色位于“类的实现层次结构”的最上层。它定义了用于实现 Abstraction 角色的接口的方法。

> **ConcreteImplementor （具体实现者）**
ConcreteImplementor 角色负责实现 Implementor 角色中定义的方法。


<br />

### 4. 代码示例

*类的功能层次结构*

> Display.java

Display 类是“类的功能层次结构”的最上层，表示功能的抽象。它的 impl 字段保存了实现 Display 类的具体功能的实例。该实例通过 Display 的构造函数被传递给 Display 类，保存在 impl 字段中。impl 字段就是类的两个层次结构的“桥梁”。

```java
public class Display {
    private DisplayImpl impl;

    public Display(DisplayImpl impl) {
        this.impl = impl;
    }

    public void open() {
        this.impl.rawOpen();
    }

    public void print() {
        this.impl.rawPrint();
    }

    public void close() {
        this.impl.rawClose();
    }

    public void display() {
        open();
        print();
        close();
    }
}
```

> CountDisplay.java

CountDisplay 类是 Display 类的功能层次结构的拓展，它增加了“多次显示”的功能。

```java
public class CountDisplay extends Display {
    public CountDisplay(DisplayImpl impl) {
        super(impl);
    }

    public void multiDisplay(int times) {
        open();
        for (int i = 0; i < times; i++) {
            print();
        }
        close();
    }
}

```

*类的实现层次结构*

> DisplayImpl.java

DisplayImpl 是一个抽象类，只声明的对应 Display 类的三个抽象方法。

```java
public abstract class DisplayImpl {
    public abstract void rawOpen();

    public abstract void rawPrint();

    public abstract void rawClose();
}
```

> StringDisplayImpl.java

StringDisplayImpl 类最后实现了 Display 的所有功能。

```java
public class StringDisplayImpl extends DisplayImpl {
    private String string;
    private int width;

    public StringDisplayImpl(String string) {
        this.string = string;
        this.width = string.getBytes().length;
    }

    @Override
    public void rawOpen() {
        printLine();
    }

    @Override
    public void rawPrint() {
        System.out.println("|" + string + "|");
    }

    @Override
    public void rawClose() {
        printLine();
    }

    private void printLine() {
        System.out.print("+");
        for (int i = 0; i < width; i++) {
            System.out.print("-");
        }
        System.out.println("+");
    }
}
```

> Main.java

最后我们看一下如何调用 Display。

```java
public class Main {
    public static void main(String[] args){
        Display d1 = new Display(new StringDisplayImpl("hello world"));
        CountDisplay d2 = new CountDisplay(new StringDisplayImpl("hello world"));

        d1.display();
        d2.display();
        d2.multiDisplay(5);
    }

}
```

结果
```
+-----------+
|hello world|
+-----------+
+-----------+
|hello world|
+-----------+
+-----------+
|hello world|
|hello world|
|hello world|
|hello world|
|hello world|
+-----------+
```

<br />

### 5. 延展阅读

#### 继承是强关联，委托是弱关联

继承是一种拓展类的有效手段，但是其弊端就是使父类和子类之间有很强的关联性。如果我们想灵活的改变类之间的关系，那么使用“委托”的概念就会显得更合适。

以我们的代码示例为例，所谓“委托”，就是指 Display 类自身的方法并不自己直接处理逻辑，而是交由（委托） DisplayImpl 实现类来处理。
```
public void open() {
    this.impl.rawOpen();
}
```
而 DisplayImpl 类实在 Display 类的实例生成时才被作为参数传入的，所以 Display 类中方法的最终实现可以在此刻才被决定。这就是通过“委托”实现类功能的自由拓展。