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

通常当我们定义一个数据结构的时候，会觉得将对其进行处理的逻辑放在数据结构的类本身中是一件理所应当的事情。然而当我们有多种“处理”方式时，
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
