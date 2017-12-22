---
layout:     post
title:      "设计模式 -- 把具体处理交给子类"
subtitle:   " \"Singleton Pattern\""
date:       2017-12-22 21:00:00
author:     "Aaron"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - 设计模式
---

## 正文

### 1. 概述
可能你没有注意，但模板模式是被广泛应用在日常开发中的一种基本模式。事实上，当你使用继承或者多态概念的时候，就正在使用模板模式。

简单地说，Template method 就是将处理流程抽象出来，构成一个模板（父类）。模板并不关心具体的实现，而是把处理逻辑交给模板的实现（子类）。查看父类的代码是无法了解这些方法最终会如何进行处理，只有子类才决定具体的逻辑。但是无论子类如果处理，都会遵循父类的流程框架。

像这样**在父类中定义处理流程的框架，在子类中实现具体处理**的模式就被称为 Template method 模式。
<br />

### 2. 模式类图

<img class="shadow" src="/img/in-post/templatepattern/template-1.png" width="260">
<br />

### 3. 模式角色

> * **AbstractClass** AbstractClass 负责实现模板，同时负责声明模板中的抽象方法。这些抽象方法由子类 ConcreteClass 角色负责实现。
> * **ConcreteClass** ConcreteClass 负责实现 AbstractClass 角色中定义的抽象方法。
<br />

### 4. 代码示例

<br />

### 5. 延展阅读

<br />