---
layout:     post
title:      "设计模式 -- 简单窗口"
subtitle:   " \"Facade Pattern\""
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

随着业务复杂度的增加，系统程序中类的调用关系也会越来越复杂。这在日常工作中非常常见。如果放任不管，让大量的类散落在项目中，会对将来的拓展和维护造成大量麻烦。
为了解决这个问题，我们提出了 facade 模式。Facade 即窗口，这个模式的核心要义就是通过一个窗口角色，隐藏复杂的类调用关系，只暴露简洁的接口给 client。这样当 client 需要完成一段逻辑业务时，不需要关系业务内部的各个类是如何相互调用的。
用实际的例子类比，就像去饭店吃饭，客人只需要点菜下单，不需要考虑每一道菜用什么材料，找什么厨师来做。菜单就是面向客户的窗口，客户只需调用点菜的 API，就可以吃到美味的饭菜，而不需要考虑具体的制作过程。

<br />

### 2. 模式类图

<img class="shadow" src="/img/in-post/facadepattern/facade-1.png" width="500">

<br />

### 3. 模式角色

> **Facade（窗口）**
Facade 角色即窗口角色，向系统外部提供高层接口。

> **Client（请求者）**
Client 角色调用 Facade 角色定义的接口。（当然，严格来说 Client 并不是 Facade 模式中的角色）

> **构成系统的其他角色们**
组成系统的各个功能角色，它们并不关心 Facade 角色的存在，只执行自己的功能。Facade 角色负责调用它们组成完整的业务功能。

<br />

### 4. 代码示例

我们用一个打印 html 页面的场景举例。系统中包含从数据库读取数据的类，组装 html 页面内容的类。它们通过一个页面生成类整合。最终的 main 方法不用关心数据如何读取，html结构如何组装，只需要调用“生成页面”即可。

首先定义一个数据读取类。

> Database.java

```java
public class Database {
    private Database() {

    }

    public static Properties getProperties(String dbName) {
        String fileName = dbName + ".txt";
        Properties prop = new Properties();
        try {
            prop.load(new FileInputStream(fileName));
        } catch (IOException e) {
            e.printStackTrace();
            System.out.println("ERROR: " + fileName + " not found!");
        }
        return prop;
    }
}
```

同时定义数据源。这里的数据源只是一个 properties 文件。

> 

```properties
aaron@google.com=aaron
tom@google.com=tom
lucy@google.com=lucy
```

然后定义一个 Html 页面构造类。

> HtmlWriter.java

```java
public class HtmlWriter {
    private Writer writer;

    public HtmlWriter(Writer writer) {
        this.writer = writer;
    }

    public void title(String title) throws IOException {
        writer.write("<html>");
        writer.write("<head>");
        writer.write("<title>" + title + "</title>");
        writer.write("</head>");
        writer.write("<body>\n");
        writer.write("<h1>" + title + "</h1>\n");
    }

    public void paragraph(String msg) throws IOException {
        writer.write("<p>" + msg + "</P>\n");
    }

    public void link(String href, String caption) throws IOException {
        paragraph("<a href=\"" + href + "\">" + caption + "</a>\n");
    }

    public void mailto(String mailName, String userName) throws IOException {
        link("mailto:" + mailName, userName);
    }

    public void close() throws IOException {
        writer.write("</body>");
        writer.write("</html>\n");
        writer.close();
    }
}
```

接下来就是定义 facade 角色。我们创建一个 PageMarker 类。负责协调调用 Database 和 HtmlWriter，整合功能。

> PageMarker.java

```java
public class PageMaker {
    private PageMaker() {

    }

    public static void makeWelcomePage(String mailAddr, String fileName) {
        try {
            Properties mailProp = Database.getProperties("mailData");
            String userName = mailProp.getProperty(mailAddr);
            HtmlWriter writer = new HtmlWriter(new FileWriter(fileName));
            writer.title("Welcome to " + userName + "'s page!");
            writer.paragraph("欢迎来到" + userName + "的主页！");
            writer.paragraph("等待你的邮件哦");
            writer.mailto(mailAddr, userName);
            writer.close();

            System.out.println(fileName + " is created for " + mailAddr + " (" + userName + ")");
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

最后用 main 方法作为 client 调用 facade 角色给出的接口。

> Main.java

```java
public class Main {
    public static void main(String[] args) {
        PageMaker.makeWelcomePage("aaron@google.com", "home.html");
    }
}
```

结果：
```text
home.html is created for aaron@google.com (aaron)
```

```html
<html><head><title>Welcome to aaron's page!</title></head><body>
<h1>Welcome to aaron's page!</h1>
<p>欢迎来到aaron的主页！</P>
<p>等待你的邮件哦</P>
<p><a href="mailto:aaron@google.com">aaron</a>
</P>
</body></html>
```

<img class="shadow" src="/img/in-post/facadepattern/facade-2.png" width="500">



