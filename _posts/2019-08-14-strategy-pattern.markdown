---
layout:     post
title:      "设计模式 -- 整体的替换算法"
subtitle:   " \"Strategy Pattern\""
date:       2019-08-14 15:00:00
author:     "Aaron"
header-img: "img/post-bg-2015.jpg"
catalog: false
tags:
    - 设计模式
---


---
## 正文

### 1. 概述

所谓“策略”（strategy），即程序中的“算法”。策略模式（strategy pattern）可以整体的替换算法的实现部分，使得我们可以轻松的以不同的算法去解决同一个问题。

<br />

### 2. 模式类图

<img class="shadow" src="/img/in-post/strategypattern/strategy-1.png" width="500">

<br />

### 3. 模式角色

> **Strategy （策略）**
Strategy 角色决定实现策略所必须的接口。

> **ConcreteStrategy （具体的策略）**
ConcreteStrategy 角色负责实现 Strategy 角色的接口。

> **Context （上下文）**
负责使用 Strategy 角色， Context中保存了 ConcreteStrategy 角色的实例，使用 ConcreteStrategy 角色的功能实现需求。

<br />

### 4. 代码示例

我们以猜拳游戏作为代码示例的实现需求。定义两种猜拳“策略”。第一种是，如果上次猜拳胜利，则此次使用相同的手势出拳。第二种略微复杂，根据上局猜拳胜利的概率，计算此次出拳的手势。

首先我们定义一个Hand类表示出拳。

> Hand.java

```java
public class Hand {
    public static final int HANDVALUE_S = 0;
    public static final int HANDVALUE_J = 1;
    public static final int HANDVALUE_B = 2;

    public static final Hand[] hand = {
            new Hand(HANDVALUE_S),
            new Hand(HANDVALUE_J),
            new Hand(HANDVALUE_B)
    };

    public static final String[] name = {
            "石头", "剪刀", "布"
    };

    private int handValue;

    private Hand(int handValue) {
        this.handValue = handValue;
    }

    public static Hand getHand(int handValue) {
        return hand[handValue];
    }

    public boolean isStrongerThan(Hand h) {
        return fight(h) == 1;
    }

    public boolean isWeakerThan(Hand h) {
        return fight(h) == -1;
    }

    private int fight(Hand h) {
        if (this == h) {
            return 0;
        } else if ((this.handValue + 1) % 3 == h.handValue) {
            return 1;
        } else {
            return -1;
        }
    }

    @Override
    public String toString() {
        return name[handValue];
    }

}

```

定义一个 Strategy 接口作为 Strategy 角色。它具有 nextHand() 和 study() API。 nextHand() 返回下次出拳手势。study() 用于决策下次出拳。

> Strategy.java

```java
public interface Strategy {
    Hand nextHand();

    void study(boolean win);
}

```

我们会有两种不同的策略，分别用 WinningStrategy 和 ProbStrategy 来实现

> WinningStrategy.java
```java
public class WinningStrategy implements Strategy {
    private boolean won;
    private Hand prevHand;
    private Random random;

    public WinningStrategy(int seed) {
        random = new Random(seed);
    }

    @Override
    public Hand nextHand() {
        if (!won) {
            prevHand = Hand.getHand(random.nextInt(3));
        }
        return prevHand;
    }

    @Override
    public void study(boolean win) {
        won = win;
    }
}
```

> ProbStrategy.java
```java
public class ProbStrategy implements Strategy {
    private Random random;
    private int prevHandValue = 0;
    private int currentHandValue = 0;
    private int[][] history = {
            {1, 1, 1},
            {1, 1, 1},
            {1, 1, 1}
    };

    public ProbStrategy(int seed) {
        random = new Random(seed);
    }

    @Override
    public Hand nextHand() {
        int bet = random.nextInt(getSum(currentHandValue));
        int handValue = 0;
        if (bet < history[currentHandValue][0]) {
            handValue = 0;
        } else if (bet < history[currentHandValue][0] + history[currentHandValue][1]) {
            handValue = 1;
        } else {
            handValue = 2;
        }
        prevHandValue = currentHandValue;
        currentHandValue = handValue;
        return Hand.getHand(handValue);
    }

    @Override
    public void study(boolean win) {
        if (win) {
            history[prevHandValue][currentHandValue]++;
        } else {
            history[prevHandValue][(currentHandValue + 1) % 3]++;
            history[prevHandValue][(currentHandValue + 2) % 3]++;
        }
    }

    private int getSum(int hv) {
        int sum = 0;
        for (int i = 0; i < 3; i++) {
            sum += history[hv][i];
        }
        return sum;
    }
}
```

我们需要一个 Context 角色来使用策略，定义 Player 来完成猜拳的操作。

> Player.java

```java
public class Player {
    private String name;
    private Strategy strategy;
    private int winCount;
    private int loseCount;
    private int gameCount;

    public Player(String name, Strategy strategy) {
        this.name = name;
        this.strategy = strategy;
    }

    public Hand nextHand() {
        return strategy.nextHand();
    }

    public void win() {
        strategy.study(true);
        winCount++;
        gameCount++;
    }

    public void lose() {
        strategy.study(false);
        loseCount++;
        gameCount++;
    }

    public void even() {
        gameCount++;
    }

    public String getName(){
        return name;
    }

    @Override
    public String toString() {
        return "[" + name + ": " + gameCount + " games, " + winCount + " win, " + loseCount + " lose]";
    }
}
```

最后需要一个 Main 方法来调用

> Main.java
```java
public class Main {

    public static void main(String[] args) {
        int seed1 = 98;
        int seed2 = 34;

        Player player1 = new Player("Aaron", new WinningStrategy(seed1));
        Player player2 = new Player("Tom", new ProbStrategy(seed2));

        for (int i = 0; i < 10000; i++) {
            Hand nextHand1 = player1.nextHand();
            Hand nextHand2 = player2.nextHand();

            if (nextHand1.isStrongerThan(nextHand2)) {
                System.out.println("Winner: " + player1.getName());
                player1.win();
                player2.lose();
            } else if (nextHand1.isWeakerThan(nextHand2)) {
                System.out.println("Winner: " + player2.getName());
                player1.lose();
                player2.win();
            } else {
                System.out.println("Even...");
                player1.even();
                player2.even();
            }
        }
        System.out.println("Total result: ");
        System.out.println(player1.toString());
        System.out.println(player2.toString());
    }
}

```

结果
```text
Winner: Aaron
Winner: Tom
Even...
Winner: Tom
Even...
Winner: Tom
Even...
Winner: Aaron
Even...

... ...

Winner: Aaron
Winner: Tom
Winner: Aaron
Winner: Tom
Winner: Tom
Winner: Tom
Winner: Tom
Total result: 
[Aaron: 10000 games, 3160 win, 3564 lose]
[Tom: 10000 games, 3564 win, 3160 lose]
```

<br />
