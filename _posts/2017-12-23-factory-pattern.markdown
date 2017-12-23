---
layout:     post
title:      "设计模式 -- 把生成实例交给子类"
subtitle:   " \"Factory Method Pattern\""
date:       2017-12-23 15:00:00
author:     "Aaron"
header-img: "img/post-bg-2015.jpg"
catalog: false
tags:
    - 设计模式
---

## 正文

工厂模式同样是一个“脍炙人口”的设计模式。今天 Aaron 选择紧接着讲工厂模式，是因为工厂模式和之前讲到的模板模式密不可分，或者说，工厂模式本身就是一种模板模式的应用。

### 1. 概述
通俗点说，利用 Template mothod 模式来构建实例的工厂，就是 Factory method 模式。在工厂模式中，父类决定实例的生成方式，但不决定所要生成的具体的类，具体的处理全部由子类负责。这样就可以将生成实例的框架和实际负责生成实例的类解耦。

<br />

### 2. 模式类图

<img class="shadow" src="/img/in-post/factorypattern/factory-1.png" width="800">
<br />

### 3. 模式角色

> * **Creator**

> * **Product**

> * **ConcreteProduct**

> * **ConcreteCreator**

<br />

### 4. 代码示例
```java
```

<br />

### 5. 延展阅读