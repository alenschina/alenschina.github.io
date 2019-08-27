---
layout:     post
title:      "设计模式 -- 分离数据结构与处理数据"
subtitle:   " \"Visitor Pattern\""
date:       2019-08-26 10:00:00
author:     "Aaron"
header-img: "img/post-bg-2015.jpg"
catalog: false
tags:
    - 设计模式
---


---
## 正文

### 1. 概述

通常当我们定义一个数据结构的时候，会觉得将对其进行处理的逻辑放在数据结构的类本身中是一件理所应当的事情，这也很符合 OOP 设计的思路。然而当我们有多种“处理”方式时，
或者换句话说，当我们需要时常修改处理方式，或者添加新的处理方式时，我们就不得不频繁的修改数据结构类，这是我们不希望看到的。
于是我们引入了 Visitor 模式。在访问者模式中，**数据结构**与**数据处理**将被分离开来。我们编写不同的“访问者”类来访问数据结构，然后将对数据的处理逻辑交个“访问者”。
所以当有想的数据处理逻辑需要添加时，我们只需要定义新的“访问者”，并让数据结构类接受这个访问者即可。

<br />

### 2. 模式类图

<img class="shadow" src="/img/in-post/visitorpattern/visitor-1.png" width="500">

<br />

### 3. 模式角色

> **Visitor（访问者）**
Visitor 角色负责对数据结构中每一个具体的元素声明一个访问方法，即 visit(XXXX)，XXXX 指代某种具体元素。visit(XXXX)的具体实现由 ConcreteVisitor 角色实现。

> **ConcreteVisitor（具体的访问者）**
ConcreteVisitor 角色负责实现 Visitor 角色定义的接口。它将实现所有的 visit(XXXX)，即实现对所有 ConcreteElement 的处理逻辑。

> **Element（元素）**
Element 角色表示 Visitor 角色访问的对象。它会声明 accept 方法来接收访问者。

> **ConcreteElement**
ConcreteElement 角色负责实现 Element 角色定义的接口。

> **ObjectStructure（对象结构）**
ObjectStructure 角色负责处理 Element 角色的集合。

<br />

### 4. 代码示例
这里我们还是用 composite 模式的文件系统来举例。我们将定义文件系统的数据结构，但是与之前不同的是，将通过访问者来输出文件目录，而不是在数据结构中直接输出。

首先我们定义一个 Visitor，在其中声明对 File 和 Directory 的 visit 方法。

> Visitor.java

```java
public abstract class Visitor {
    public abstract void visit(File file);
    public abstract void visit(Directory directory);
}
``` 

定义 Element 接口，并在其中声明 accept 方法。

> Element.java

```java
public interface Element {
    void accept(Visitor v);
}
```

定义 Entry 类，这和 Composite 模式中的用法一样，只是在这里让它实现了 Element 接口，用于 visitor 模式。

> Entry.java

```java
public abstract class Entry implements Element {
    public abstract String getName();

    public abstract int getSize();

    public Entry add(Entry entry) throws FileTreatmentException {
        throw new FileTreatmentException();
    }

    public String toString() {
        return getName() + "( " + getSize() + " )";
    }
}
```

接下去的 File 和 Directory 类。作为数据结构本身，它们和 composite 模式中一样，但是对数据的输出不在由其本身提供功能，而是通过 accept 方法，交给 visitor 来处理。

> File.java

```java
public class File extends Entry {
    private String name;
    private int size;

    public File(String name, int size) {
        this.name = name;
        this.size = size;
    }

    @Override
    public String getName() {
        return name;
    }

    @Override
    public int getSize() {
        return size;
    }

    @Override
    public void accept(Visitor v) {
        v.visit(this);
    }
}
```

> Directory.java

```java
public class Directory extends Entry {
    private String name;
    private ArrayList<Entry> directory = new ArrayList();

    public Directory(String name) {
        this.name = name;
    }

    @Override
    public String getName() {
        return name;
    }

    @Override
    public int getSize() {
        int size = 0;
        for (Entry entry : directory) {
            size += entry.getSize();
        }
        return size;
    }

    public Entry add(Entry entry) {
        directory.add(entry);
        return this;
    }

    public Iterator iterator() {
        return directory.iterator();
    }

    @Override
    public void accept(Visitor v) {
        v.visit(this);
    }
}
```

最后我们再定义 ListVisitor 类作为 ConcreteVisitor，实现对 File 和 Directory 的访问，输出数据。
可以注意到，在这个例子中，Directory 类同时也扮演了 ObjectStructure 角色。通过迭代器处理了 Element 角色的集合。

> ListVisitor.java

```java
public class ListVisitor extends Visitor {
    private String currentDir;

    @Override
    public void visit(File file) {
        System.out.println(currentDir + "/" + file);
    }

    @Override
    public void visit(Directory directory) {
        System.out.println(currentDir + "/" + directory);
        String originDir = currentDir;
        currentDir = currentDir + "/" + directory.getName();
        Iterator<Entry> iterator = directory.iterator();
        while (iterator.hasNext()) {
            Entry entry = iterator.next();
            entry.accept(this);
        }
        currentDir = originDir;
    }
}
```

通过 Main 方法调用后，我们会得到和 Composite 模式同样的输出结果。

> Main.java

```java
public class Main {
    public static void main(String[] args) {
        try {
            System.out.println("Making root entries");
            Directory rootDir = new Directory("root");
            Directory binDir = new Directory("bin");
            Directory tmpDir = new Directory("tmp");
            Directory usrDir = new Directory("usr");

            rootDir.add(binDir);
            rootDir.add(tmpDir);
            rootDir.add(usrDir);

            binDir.add(new File("vi", 10000));
            binDir.add(new File("latex", 20000));

            rootDir.accept(new ListVisitor());

            System.out.println("");
            System.out.println("Making user entries");
            Directory aaron = new Directory("aaron");
            Directory tom = new Directory("tom");
            Directory lucy = new Directory("lucy");

            usrDir.add(aaron);
            usrDir.add(tom);
            usrDir.add(lucy);

            aaron.add(new File("a.html", 100));
            tom.add(new File("b.txt", 400));
            tom.add(new File("c.xml", 200));
            lucy.add(new File("d.csv", 800));

            rootDir.accept(new ListVisitor());
        } catch (FileTreatmentException e) {
            e.printStackTrace();
        }

    }
}
```

结果：
```text
Making root entries
/root( 30000 )
/root/bin( 30000 )
/root/bin/vi( 10000 )
/root/bin/latex( 20000 )
/root/tmp( 0 )
/root/usr( 0 )

Making user entries
/root( 31500 )
/root/bin( 30000 )
/root/bin/vi( 10000 )
/root/bin/latex( 20000 )
/root/tmp( 0 )
/root/usr( 1500 )
/root/usr/aaron( 100 )
/root/usr/aaron/a.html( 100 )
/root/usr/tom( 600 )
/root/usr/tom/b.txt( 400 )
/root/usr/tom/c.xml( 200 )
/root/usr/lucy( 800 )
/root/usr/lucy/d.csv( 800 )
```

<br />


### 5. 延展阅读

**开闭原则**

其实简单地通过与 Composite 模式的对比，我们就能直观的发现 Visitor 模式需要更复杂的设计却只达到了相同的结果。那么为什么我们要引入 Visitor 模式呢？
这就涉及到软件开发的一个基本原则 -- 开闭原则（The Open-Closed principle, OCP）。所谓“开闭原则”，就是指 1. 对拓展是开放的；2. 对修改是关闭的。
在设计类的时候，我们需要考虑将来会因为业务的发展，而对其进行功能的拓展，这是很常见的操作。所以我们对拓展需要抱有开放的态度。
然而拓展往往意味着修改，而修改将给已有功能的完整性带来风险。所以我们应该尽量减少对已有代码的修改，对修改抱有关闭的态度。
显然，这两者是矛盾的，而访问者模式的提出就是为了解决这个问题，当然，也只是解决了一方面问题。访问者模式的优点在于可以自由的拓展 visitor 来满足新增的数据处理需求，但是与此同时却意味着一旦数据结构本身需要变更时，就会对所有的 Visitor 类造成影响，我们不得不修改所有的 Visitor 来适应这个变化，这就是访问者模式最大的问题。