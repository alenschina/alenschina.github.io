---
layout:     post
title:      "设计模式 -- 将关联零件组装成产品"
subtitle:   " \"Abstract Factory Pattern\""
date:       2018-01-04 21:00:00
author:     "Aaron"
header-img: "img/post-bg-2015.jpg"
catalog: false
tags:
    - 设计模式
---

[憋bb，我要看正文](#build)

Aaron 表示停更这么久实在是因为工作太忙，没有喘息时间来写blog。现在稍微缓过劲来，所以就继续我们的设计模式学习之旅。之前讲完了工厂方法模式，那今天就顺势讲一讲抽象工厂模式。
记得 Aaron 曾经在一次面试时被问到如何解读抽象工厂模式，当时也没能说出个大概。不少人也会有同样的感觉，觉得抽象工厂模式就是难以描述的东西。所以下面就让我们尝试啃啃这块难啃的骨头。

<br/>

<p id = "build"></p>
---
## 正文

### 1. 概述

其实大家经常挂在嘴边的工程模式分为三种：简单工厂模式，工厂方法模式，以及抽象工厂模式。前两种在之前的博文[工厂模式](/2017/12/23/factory-pattern)中已经涉猎，这边就不做赘述。简单来说，抽象工厂模式就是对工厂方法模式和抽象设计思想的一种结合。事实上能够足够理解这两种设计思想，抽象工厂的设计理念就是水到渠成的了。

Abstract factory 的设计中，不仅有“抽象工厂”，还有“抽象产品”和“抽象零件”。所有这些抽象出来的对象，都不考虑具体的实现，只关注接口、方法名称和签名等。看过之前博文的同学，是不是会觉得这些话很耳熟。没错，其实抽象工厂模式看似高深，其实不过是借用工厂的理念对抽象概念做了更复杂的组装而已。

<br />

### 2. 模式类图
<img class="shadow" src="/img/in-post/abstractfactorypattern/abstractfactory-1.png" width="400">

<br />

### 3. 模式角色

> **AbstractFactory**
AbstractFactory 角色负责定义用于生成抽象产品的接口。

> **AbstractProduct**
AbstractProduct 角色负责定义由 AbstractFactory 角色所生成的抽象零件及产品的接口。

> **ConcreteFactory**
ConcreteFactory 角色负责实现 AbstractFactory 角色定义的接口。

> **ConcreteProduct**
ConcreteProduct 角色负责实现 AbstractProduct 角色定义的接口。

<br />

### 4. 代码示例

Aaron 涉猎的代码太少，所以很少看到抽象工厂在实际项目里的使用。这边我们就用一个比较尴尬的代码示例（至少我是这么觉得的）来解释抽象工厂模式吧。这段代码的功能是将带有层次关系的链接的集合制作成HTML文件。HTML页面上的元素，例如超链接，就是所谓的零件；超链接的集合，也就是页面，就是所谓的产品，而我们的工厂就是负责制作HTML页面。

> Item.java

Item 类是 Link 类和 Tray 类的父类。caption 字段表示 item 名称。makeHTML 方法是抽象方法，需要子类来实现。

```java
public abstract class Item {
    protected String caption;

    public Item(String caption){
        this.caption = caption;
    }

    public abstract String makeHTML();
}
```

> Link.java

Link 类是抽象的表示超链接的类。url 字段保存超链接所指向的地址。

```java
public abstract class Link extends Item {
    protected String url;

    public Link(String caption, String url) {
        super(caption);
        this.url = url;
    }
}
```

> Tray.java

Tray 类是抽象的表示多个超链接的集合的类。通过 List 保存多个 Link 类。

```java
public abstract class Tray extends Item {
    protected ArrayList tray = new ArrayList();

    public Tray(String caption) {
        super(caption);
    }

    public void add(Item item){
        tray.add(item);
    }
}
```

> Page.java

Page 类是抽象的表示HTML页面的类。如果将 Link 和 Tray 类表示为抽象的“零件”，那么 Page 类就是抽象的“产品”了。这边可以注意一下，output 方法是一个 Template method 模式的应用。

```java
public abstract class Page {
    protected String title;
    protected String author;
    protected ArrayList content = new ArrayList();

    public Page(String title, String author){
        this.title = title;
        this.author = author;
    }

    public void add(Item item){
        content.add(item);
    }

    public void output() {
        try {
            String fileName = title+ ".html";
            Writer writer = new FileWriter(fileName);
            writer.write(this.makeHTML());
            writer.close();
            System.out.println(fileName + " created.");
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public abstract String makeHTML();
}
```

> Factory.java

getFactory 方法通过调用 Class 类的 forName 方法动态读取类的信息，接着使用newInstace 方法生成该类的实例。
createLink、createTray、createPage 方法是在抽象工厂中用于生成零件和产品的方法。

```java
public abstract class Factory {
    public static Factory getFactory (String className){
        Factory factory = null;
        try {
            factory = (Factory) Class.forName(className).newInstance();
        } catch (InstantiationException e) {
            e.printStackTrace();
        } catch (IllegalAccessException e) {
            e.printStackTrace();
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
        return factory;
    }

    public abstract Link createLink(String caption, String url);
    public abstract Tray createTray(String caption);
    public abstract Page createPage(String title, String author);
}
```

接下来就是各个抽象类的实现。负责零件、产品已经工厂的具体处理逻辑。Aaron 的懒人癌又开始了，就不一一细讲了。

> ListLink.java

```java
public class ListLink extends Link {
    public ListLink(String caption, String url) {
        super(caption, url);
    }

    @Override
    public String makeHTML() {
        return "<li><a href=\"" + url + "\">" + caption + "</a></li>\n";
    }
}
```

> ListTray.java

```java
public class ListTray extends Tray {
    public ListTray(String caption) {
        super(caption);
    }

    @Override
    public String makeHTML() {
        StringBuffer buffer = new StringBuffer();
        buffer.append("<li>\n=");
        buffer.append(caption + "\n");
        buffer.append("<ul>\n");
        Iterator it = tray.iterator();
        while (it.hasNext()) {
            Item item = (Item) it.next();
            buffer.append(item.makeHTML());
        }
        buffer.append("</ul>\n");
        buffer.append("</li>\n");
        return buffer.toString();
    }
}
```

> ListPage.java

```java
public class ListPage extends Page {
    public ListPage(String title, String author) {
        super(title, author);
    }

    @Override
    public String makeHTML() {
        StringBuffer buffer = new StringBuffer();
        buffer.append("<html><head><title>" + title + "</title></head>\n");
        buffer.append("<body>\n");
        buffer.append("<h1>" + title + "</h1>\n");
        buffer.append("<ul>\n");
        Iterator it = content.iterator();
        while(it.hasNext()){
            Item item = (Item)it.next();
            buffer.append(item.makeHTML());
        }
        buffer.append("</ul>\n");
        buffer.append("<hr><address>"+author+"</address>");
        buffer.append("</body></html>\n");

        return buffer.toString();
    }
}
```

> ListFactory.java

```java
public class ListFactory extends Factory {

    @Override
    public Link createLink(String caption, String url) {
        return new ListLink(caption, url);
    }

    @Override
    public Tray createTray(String caption) {
        return new ListTray(caption);
    }

    @Override
    public Page createPage(String title, String author) {
        return new ListPage(title, author);
    }
}
```





