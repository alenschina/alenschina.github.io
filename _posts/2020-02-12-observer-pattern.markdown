### 1. 概述

观察者模式是一种被广泛应用的模式。Observer 即观察者，当被观察对象的状态变化时，就会通知观察者做出相应的处理。

所谓观察者并不是真的能够“主动”观察对象，而是对象做出通知行为。所以观察者模式也被称为 Publish-Subscribe （发布-订阅）模式。

观察者模式适合应用于需要针对对象状态做出相应处理的场景。

<br />

### 2. 模式类图

<img class="shadow" src="/img/in-post/observerpattern/observer-1.png" width="500">

<br />

### 3. 模式角色

> **Subject（观察对象）**
Subject 角色即被观察的对象，Subject 角色定义了**注册观察者**和**删除观察者**的方法。同时还声明了**获取现在状态**的方法

> **ConcreteSubject（具体的观察对象）**
ConcreteSubject 角色表示具体的观察对象。当自身状态发生变化时，会通知已经注册的观察者。

> **Observer（观察者）**
Observer 角色负责接收来自 Subject 角色的状态变化的通知。它需要声明一个接收通知的方法用以被观察对象调用。

> **ConcreteObserver（具体的观察者）**
ConcreteObserver 角色用于实现接收通知方法的具体实现。

<br />

### 4. 代码示例

我们定义一个会随机生成数字的被观察对象，以及两个会根据产生的数字做出不同处理的观察者。

> Observer.java

```java
public interface Observer {
    public abstract void update(NumberGenerator generator);
}
```

> NumberGenerator.java

```java
public abstract class NumberGenerator {
    private ArrayList observers = new ArrayList();

    public void addObserver(Observer observer) {
        observers.add(observer);
    }

    public void deleteObserver(Observer observer) {
        observers.remove(observer);
    }

    public void notifyObservers() {
        Iterator it = observers.iterator();
        while (it.hasNext()) {
            Observer o = (Observer) it.next();
            o.update(this);
        }
    }

    public abstract int getNumber();
    public abstract void execute();
}
```

> RandomNumberGenerator.java

```java
public class RandomNumberGenerator extends NumberGenerator {
    private Random random = new Random();
    private int number;

    @Override
    public int getNumber() {
        return number;
    }

    @Override
    public void execute() {
        for (int i = 0; i < 20; i++) {
            number = random.nextInt(50);
            notifyObservers();
        }
    }
}
```

> DigitalObserver.java

```java
public class DigitalObserver implements Observer {
    @Override
    public void update(NumberGenerator generator) {
        System.out.println("DigitalObserver: " + generator.getNumber());
        try {
            Thread.sleep(100);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

> GraphObserver.java

```java
public class GraphObserver implements Observer {
    @Override
    public void update(NumberGenerator generator) {
        System.out.println("GraphObserver： ");
        int count = generator.getNumber();
        for (int i = 0; i < count; i++) {
            System.out.print("*");
        }
        System.out.println("");
        try {
            Thread.sleep(100);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```