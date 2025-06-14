---
title: "Power Canvas App: Looping marquee text"
date: 2025-06-14
draft: false
date: 2025-06-14T10:00:00+02:00
draft: true
tags:
  - Power Canvas app
  - Mid function
  - Mod function
categories:
  - Tutorials
---

Ever wanted to add dynamic content to your Power Apps to highlight key information?

This article I will explorer an approach for looping marquee text for a text input control.

The demonstration use Authoring Version: 3.25061.7,and enabled 'Modern controls and themes'.

![image-20250614154416783](/image-20250614154416783.png)

Effect first:

![](/loopingmarquee3.gif)

Prepare the app.

### 1. Insert controls in a blank screen：

- One text label control: TextCanvas1

  Text= `"While Montaigne's philosophy was admired and copied in France, none of his most immediate disciples tried to write essays."`

- One label control: TextInputCanvas1, which displays the text dynamically.

- Timer control，which is used for looping.

### 2. Use ***Left()*** 和***Right()*** to construct the dynamic content.

How to construct dynamic text box content? My first thought is to combine ***Left()***  and ***Right()*** to make it.

Reference article ：[Left, Mid, and Right functions](https://learn.microsoft.com/en-us/power-platform/power-fx/reference/function-left-mid-right)

Those two function have two main usage，one for string，another one for Single Column Table . Here we use the one for string.

Syntax:

**Left**( *String*, *NumberOfCharacters* ) 和 **Right**( *String*, *NumberOfCharacters* )

In order to construct dynamic content，***Left()*** +***Right()***  should be equal to  TextCanvas1.Text， so the length of the string/text will be our target.

And to get dynamic text，here we need timer control to manipulate strings.

[Timer](https://learn.microsoft.com/en-us/power-apps/maker/canvas-apps/controls/control-timer):  The default duration value of this control is 60000，as is 60 s.

We can set duration's value to equal the length of the text and multiple 100, meanwhile set repeat = true.

```
Len(TextCanvas1.Text)*100
```

Now  our **Right** formula is：

```
Right(
        TextCanvas1.Text,
        Len(TextCanvas1.Text) - Timer3.Value/100
)
```

The same as，**Left** formula：

```
 Left(
    TextCanvas1.Text,
    Timer3.Value / 100
)
```

Combine them together and assign it to TextInputCanvas1,Value =:

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

### 3. Use ***mid()*** and ***Mod()*** to build the content

**Mid**( *String*, *StartingPosition* [, *NumberOfCharacters* ] )

**Mod**( *Number*, *Divisor* )

We knew from previous that we can construct`*Timer1*.Value / 100` <= `Len(*TextCanvas1*.Text)`.

So：

```
Mod(Timer1.Value / 100, Len(TextCanvas1.Text)+1 )
```

The value of this formula will always be less than or equal to the length of the given string.

Considering that the second parameter of ***Mid()*** is at least 1, we introduce the ***Max()*** function.

```
 Mid(
    TextCanvas1.Text & "  " & TextCanvas1.Text, //duplicate with space buffer
    Max(1, Mod(Timer1.Value / 100, Len(TextCanvas1.Text)+1 )), 
    50 //can be ignored.
)
```

This one's effect：

![](/loopingmarquee2.gif)

### Summary：

In general, the rendering effect is not smooth enough. The scenario I can think of is probably putting a continuously scrolling notification at the top of an app.
