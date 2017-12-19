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

### 1. 概述
程序在运行中，通常会创建很多实例。然而如果当我们希望在程序中某个类只会存在一个时，就会有“生成单一实例”的需求。以 Java 为例，视窗系统 （Window system） 的类就是一个典型的例子，因为在一个简单系统里，只会有一个视窗实例。

当然，只要我们在编写程序时能多加注意，确保只调用一次 `new MyClass()`，自然可以达到只生成一个实例的结果。但是这样的控制太过主观，没有办法“确保”只有一个实例生成。

所以，我们要引入单例模式，来确保某个类的一个对象成为系统中的唯一实例。

**要点**

> - 某个类只能有一个实例
> - 它必须自行创建这个实例
> - 它必须自行向整个系统提供这个实例

<br />

### 2. 模式类图

<img class="shadow" src="/img/in-post/designpattern/singleton-1.png" width="260">
<br />

### 3. 模式角色

> * **Singleton**
在 Singleton 模式中，只有 Singleton 这一个角色。Singleton 角色有一个返回唯一实例的 static 方法。该方法总会返回同一个实例。

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
以上代码中，Singleton 类只会生成一个实例。它定义了 `static` 的 singleton 成员变量，并初始化了 Singleton 的实例。初始化的行为仅在该类在加载时进行一次。Singleton 类暴露了 `getSingleton()` 的成员方法，使得其他类可以调用这个实例。

Singleton 类的**构造函数是 private** 的，这是为了禁止从 Singleton 类外部条用构造函数。如果 Singleton 类以外的类调用构造函数 `new Singleton`，就会出现编译错误。显然，如果允许在外部调用，那么 Singleton 模式就没有意义了。

<br />

### 5. 延展阅读

**“懒汉式”和“饿汉式”**

所谓“懒汉”和“饿汉”的区别，在于创建单例对象的时间不同。前文的代码就是“饿汉式”的实现，无论你是否需要这个实例，Singleton 类都会在加载时创建它。

下面我们来看一下“饿汉式”的实现：
```java
public class Singleton {

    private static Singleton singleton = null;

    private Singleton() {
        System.out.println("Create a singleton");
    }

    public static Singleton getSingleton() {
        if (singleton == null) {
            singleton = new Singleton();
        }
        return singleton;
    }
}
```
以上代码中，Singleton 类只会在真正需要这个实例的时候才会创建这个它。


**多线程情况下使用 Singleton 模式**

```java
public class Singleton {

    private static Singleton singleton = null;

    private Singleton() {
        System.out.println("Create a singleton");
    }

    public static Singleton getSingleton() {
        if (singleton == null) {
            synchronized (Singleton.class) {
                if (singleton == null) {
                    singleton = new Singleton();
                }
            }
        }

        return singleton;
    }
}
```
在多线程的情况下，由于同一进程的多个线程共享同一片存储空间，所以可能导致 Singleton 模式被破坏。遇到这样的情况时，我们可以使用 Java 的 synchronize 机制有效避免了同一个数据对象被多个线程同时访问。












