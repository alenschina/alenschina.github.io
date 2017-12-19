---
layout:     post
title:      "设计模式 -- 开篇"
subtitle:   " \"Singleton Pattern\""
date:       2017-12-19 21:00:00
author:     "Aaron"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - 设计模式
---


[憋bb，我要看正文](#build)

## 前言

Aaron 最近被 [萌姐](https://weibo.com/zhangmenggyl) 各种安利早起的好处，于是每天硬着头皮强行早起学习。这段时间在看《图解设计模式》（下注为《图解》）这本书，想好好把模式的概念巩固一下。所以本着“会教才算学会”的原则，决定把所学所见记录下来，供自己回顾，也供大家参考。这一系列将以《图解》为主干，结合线上各种资料，解读各种模式。

好了，Flag 已立，跪着也要写完。
<br/>

<p id = "build"></p>
---
## 正文

今天是第一期，我们先来看一看最简单易懂的一个模式：单例模式（singleton pattern）。如果你问任何一个有常识的程序猿有哪些设计模式，ta 一定能脱口而出“单例”，可见单例模式是多么深入人心。这不仅是因为单例模式本身容易理解，也因为它在日常开发中确实有着很高的出镜率。

### 1. 模式概述
程序在运行中，通常会创建很多实例。然而如果当我们希望在程序中某个类只会存在一个时，就会有“生成单一实例”的需求。以 Java 为例，视窗系统 （Window system） 的类就是一个典型的例子，因为在一个简单系统里，只会有一个视窗实例。

当然，只要我们在编写程序时能多加注意，确保只调用一次 `new MyClass()`，自然可以达到只生成一个实例的结果。但是这样的控制太过主观，没有办法“确保”只有一个实例生成。

所以，我们要引入单例模式，来确保某个类的一个对象成为系统中的唯一实例。

**要点**

- 某个类只能有一个实例
- 它必须自行创建这个实例
- 它必须自行向整个系统提供这个实例

<br />

### 2. 模式类图

![cmd-markdown-logo](/img/in-post/designpattern/single-1.png)

<br />

### 3. 模式角色


<br />

### 4. 代码示例


```java
public class Singleton {
    private static Singleton singleton = new Singleton();

    private Singleton() {
        System.out.println("Create a singleton");
    }

    public static Singleton getSingleton() {
        return singleton;
    }
}
```












