---
layout:     post
title:      "设计模式 -- 容器与内容的一致性"
subtitle:   " \"Composite Pattern\""
date:       2019-08-20 13:30:00
author:     "Aaron"
header-img: "img/post-bg-2015.jpg"
catalog: false
tags:
    - 设计模式
---


---
## 正文

### 1. 概述

Composite 模式通常会被成为组合模式，而它同时也有另外一个名称，部分整体模式。虽然这个名称不那么高大上，但是却很直观的体现了这个设计模式的核心思想。
组合模式依据树形结构组合对象，用来表示部分以及整体结构。组成部分的每一个对象，会被当做单一的对象来处理。
组合模式模糊了简单对象和复杂对象的边界，使得程序可以统一的处理简单和复杂对象，使得业务逻辑与复杂对象的内部结构解耦。
以我们常见的计算机文件系统为例，具有“文件夹”和“文件”的概念。他们都是整个系统结构的组成部分，但是又有不同的属性。
文件只能被放入文件夹，所以本身只能是内容，而文件夹可以放入文件，也可以被放入上层文件夹，所以它既可以是内容，也可以是容器。
如果对这两种复杂程度不同的对象进行不同的处理，那么逻辑上会和对象内部结构强耦合，所以这里我们就用运用到组合模式，将文件夹和文件统一作为“目录条目”来处理。
这样我们就形成了统一的容器结构，使得整体结构中容器和内容具备一致性。

<br />

### 2. 模式类图

<img class="shadow" src="/img/in-post/compositepattern/composite-1.png" width="500">

<br />

### 3. 模式角色

> **Leaf （树叶）**
表示“内容”的角色。在该角色中，不能放入其他对象。

> **Composite （复合物）**
表示“容器”的角色，在该角色中，可以放入 Leaf 或者 Composite 角色。

> **Component**
使 Leaf 和 Composite 具有一致性的角色。 Component 是 Leaf 和 Composite 的父类。

> **Client**
使用 Composite 模式的角色。

<br />

### 4. 代码示例

这里我们就用上文中的文件系统为例，模拟一个文件结构。

首先我们定义一个“目录条目”，即 Component 角色。

> Entry.java


```java
public abstract class Entry {
    public abstract String getName();

    public abstract int getSize();

    public Entry add(Entry entry) throws FileTreatmentException {
        throw new FileTreatmentException();
    }

    public void printList() {
        printList("");
    }

    protected abstract void printList(String prefix);

    public String toString() {
        return getName() + "( " + getSize() + " )";
    }
}

```

然后定义“文件”和“文件夹”（或者称为“目录”）。文件即 Leaf 角色，文件夹即 Composite 角色。
可以注意到，File.java 和 Directory.java 中分别实现了 printList 方法，其内部实现逻辑差别很大。然而对于调用的 Client 角色来说，却是一个统一的接口，这就是组合模式的核心要义。Client 不需要关系不同对象的结构。


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
    protected void printList(String prefix) {
        System.out.println(prefix + "/" + this);
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

    protected void printList(String prefix) {
        System.out.println(prefix + "/" + this);
        for (Entry entry : directory) {
            entry.printList(prefix + "/" + name);
        }
    }
}
```

最后定义一个 Main 方法作为 Client 角色调用组合模式

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
            rootDir.printList();

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
            rootDir.printList();
        } catch (FileTreatmentException e) {
            e.printStackTrace();
        }

    }
}
```

结果
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

