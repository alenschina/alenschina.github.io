---
layout:     post
title:      "设计模式 -- 装饰与被装饰物的一致性"
subtitle:   " \"Decorator Pattern\""
date:       2019-08-21 13:30:00
author:     "Aaron"
header-img: "img/post-bg-2015.jpg"
catalog: false
tags:
    - 设计模式
---


---
## 正文

### 1. 概述

假设我们有一个蛋糕，如果我们给它加上了奶油，就变成了奶油蛋糕，如果给它加上了草莓，就成了草莓蛋糕，如果加上巧克力，就是巧克力蛋糕了。这就是装饰蛋糕的场景。
我们的程序也是一样，可以给如同给蛋糕装饰一样，不断为其增加功能。这时候就需要装饰器模式出场了。也许你会觉得，我们用一般的继承不就能满足设计了吗？
那么请考虑这样的情况，我们有一块蛋糕，我们想加上奶油做出奶油蛋糕，然后又想要一块奶油草莓蛋糕，还想要一块奶油巧克力蛋糕，那么如果我们使用简单的继承设计，是否就会需要实现两个新的类型，却同时拥有奶油的属性？
在一个继承体系中，一般来说各子类应该是相斥的，比如汽车的子类可以是奥迪品牌，或者宝马品牌，这必然是两个互斥的子类，但是对汽车的装潢却是可以兼容的，无论是奥迪还是宝马汽车，都可以选择贴膜，或者换轮毂。
所以对这些可以兼容的功能的添加，就非常适合装饰器模式。其价值就在于装饰，而不影响被装饰物类本身的核心功能。

<br />

### 2. 模式类图

<img class="shadow" src="/img/in-post/decoratorpattern/decorator-1.png" width="500">

<br />

### 3. 模式角色

> **Component**
增加功能时的核心角色。上文书中的蛋糕就是 Component 角色。

> **ConcreteComponent**
实现 Component 角色定义接口的角色，也就是具体的被装饰物实例。

> **Decorator（装饰物）**
该角色具有 Component 角色相同的接口。**并且在其内部保存了被装饰对象 -- Component 角色**。Decorator 角色知道自己要装饰的对象。

> **ConcreteDecorator（具体的装饰物）**
具体实现装饰物的角色。

<br />

### 4. 代码示例

这里我们用装饰 String 输出的例子来说明装饰模式。我们会为字符串添加装饰的边框，代码非常直观的体现了装饰器模式的意义。

首先定义一个 Component 角色，这里创建一个抽象类。

> Display.java

```java
public abstract class Display {
    public abstract int getColumns();

    public abstract int getRows();

    public abstract String getRowText(int row);

    public final void show() {
        for (int i = 0; i < getRows(); i++) {
            System.out.println(getRowText(i));
        }
    }
}
```

接着定义一个 ConcreteComponent 角色，这就是那块蛋糕。

> StringDisplay.java

```java
public class StringDisplay extends Display {
    String string;

    public StringDisplay(String string) {
        this.string = string;
    }

    @Override
    public int getColumns() {
        return string.getBytes().length;
    }

    @Override
    public int getRows() {
        return 1;
    }

    @Override
    public String getRowText(int row) {
        if (row == 0) {
            return string;
        }
        return null;
    }
}
```

然后是 Decorator 角色，这里定义一个抽象类。

> Border.java

```java
public abstract class Border extends Display {
    protected Display display;

    protected Border(Display display) {
        this.display = display;
    }
}
```

最后定义两个 ConcreteDecorator 角色。分别实现不同的装饰边框。

> SideBorder.java

```java
public class SideBorder extends Border {
    private char borderChar;

    public SideBorder(Display display, char ch) {
        super(display);
        this.borderChar = ch;
    }

    @Override
    public int getColumns() {
        return 1 + display.getColumns() + 1;
    }

    @Override
    public int getRows() {
        return display.getRows();
    }

    @Override
    public String getRowText(int row) {
        return borderChar + display.getRowText(row) + borderChar;
    }
}
```

> FullBorder.java

```java
public class FullBorder extends Border {
    public FullBorder(Display display) {
        super(display);
    }

    @Override
    public int getColumns() {
        return 1 + display.getColumns() + 1;
    }

    @Override
    public int getRows() {
        return 1 + display.getRows() + 1;
    }

    @Override
    public String getRowText(int row) {
        if (row == 0 || row == display.getRows() + 1) {
            return "+" + makeLine('-', display.getColumns()) + "+";
        } else {
            return"|" + display.getRowText(row - 1) + "|";
        }
    }

    private String makeLine(char ch, int count) {
        StringBuffer sb = new StringBuffer();
        for (int i = 0; i < count; i++) {
            sb.append(ch);
        }
        return sb.toString();
    }
}
```

定义一个 Main 方法调用

> Main.java

```java
public class Main {
    public static void main(String[] args) {
        Display b1 = new StringDisplay("Hello world");
        Display b2 = new SideBorder(b1, '#');
        Display b3 = new FullBorder(b2);

        b1.show();
        b2.show();
        b3.show();

        Display b4 = new SideBorder(
                new FullBorder(
                        new FullBorder(
                                new SideBorder(
                                        new FullBorder(
                                                new StringDisplay("hello world")
                                        ), '*'
                                )
                        )
                ), '/'
        );
        b4.show();
    }
}
```

结果：
```text
Hello world
#Hello world#
+-------------+
|#Hello world#|
+-------------+
/+-----------------+/
/|+---------------+|/
/||*+-----------+*||/
/||*|hello world|*||/
/||*+-----------+*||/
/|+---------------+|/
/+-----------------+/
```