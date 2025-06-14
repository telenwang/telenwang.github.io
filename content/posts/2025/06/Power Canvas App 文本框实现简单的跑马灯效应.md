---
title: "Power Canvas App: SharePoint List to populate Word Template"
date: 2025-06-14
draft: false
date: 2025-06-14T10:00:00+02:00
draft: true
tags:
  - Power Automate
  - SharePoint
  - Word
  - Automation
categories:
  - Tutorials
---

Power Canvas App: 文本框实现简单的跑马灯效应

本文将展示一种如何实现文本框跑马灯效应的方法.

本示例使用Authoring Version: 3.25061.7,并启用.

![image-20250614154416783](C:\Users\twg\AppData\Roaming\Typora\typora-user-images\image-20250614154416783.png)

先展示效果：

![](C:\Users\twg\Desktop\loopingmarquee3.gif)

准备程序.

### 1. 在空白屏幕插入以下控件：

- 一个文本标签: TextCanvas1

  Text= `"While Montaigne's philosophy was admired and copied in France, none of his most immediate disciples tried to write essays."`

- 一个文本框: TextInputCanvas1, 用来动态显示文本标签的文本.

- Timer控件，用来创建循环效果.

### 2. 使用***Left()*** 和***Right()*** 构造动态内容.

如何构造动态的文本框内容？ 我第一个想到的是使用***Left()*** 和***Right()*** 组合来构建.

官网参考：[Left, Mid, and Right functions](https://learn.microsoft.com/en-us/power-platform/power-fx/reference/function-left-mid-right)

这几个函数有两种用法，一种针对字符串，另一种针对单列表类型. 在这里我们只用字符串类型.

语法:

**Left**( *String*, *NumberOfCharacters* ) 和 **Right**( *String*, *NumberOfCharacters* )

为了构建动态值，***Left()*** +***Right()***  应该等于 TextCanvas1.Text， 所以我们要从字符串的长度着手.

为了让字符串的值动态的变化，引入timer控件来操作字符串.

[Timer](https://learn.microsoft.com/en-us/power-apps/maker/canvas-apps/controls/control-timer):  这个控件的默认持续时间是60000毫秒，也就是60秒.

我们可以设置duration的值为字符串的长度乘以100,然后repeat设置为true.

```
Len(TextCanvas1.Text)*100
```

这样我们的**Right**公式即为：

```
Right(
        TextCanvas1.Text,
        Len(TextCanvas1.Text) - Timer3.Value/100
)
```

同理，**Left**的公式为：

```
 Left(
    TextCanvas1.Text,
    Timer3.Value / 100
)
```

拼凑在一起赋给TextInputCanvas1的Value:

```
Right(
        TextCanvas1.Text,
        Len(TextCanvas1.Text) - Timer3.Value/100
) & "        " //add some spaces for buffer
& Left(
    TextCanvas1.Text,
    Timer3.Value / 100
)
```

### 3. 使用***mid()*** 和***Mod()***来构造动态内容

**Mid**( *String*, *StartingPosition* [, *NumberOfCharacters* ] )

**Mod**( *Number*, *Divisor* )

我们知道可以构建`*Timer1*.Value / 100` <= `Len(*TextCanvas1*.Text)`.

所以：

```
Mod(Timer1.Value / 100, Len(TextCanvas1.Text)+1 )
```

这个公式的值永远都会小于等于所给字符串的长度.

考虑到***Mid()***的第二个参数至少是1， 我们引入***Max()***函数.

```
 Mid(
    TextCanvas1.Text & "  " & TextCanvas1.Text, //多加一个作为缓冲
    Max(1, Mod(Timer1.Value / 100, Len(TextCanvas1.Text)+1 )), 
    50 //可忽略
)
```

效果如下：

![](C:\Users\twg\Desktop\loopingmarquee2.gif)

### 总结：

总体上来讲,渲染效果一般,不够丝滑. 我能想到的应用场景可能就是在程序顶部放个持续滚动的通知之类的.
