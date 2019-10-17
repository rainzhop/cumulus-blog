---
title: 『渣译』Tkinter简介 - Canvas组件
date: 2017-02-19 19:20:30
tags: [gui, tkinter, 渣译]
---

> 原文地址：<br>http://effbot.org/tkinterbook/canvas.htm

canvas组件为tkinter提供了结构化的图形绘制功能。其功能强大，既能够绘制点线面等图形，还能够创建出图形编辑器，甚至可以根据界面设计的需要实现出多种多样的自定义组件。

## canvas组件的使用场景

canvas是一个通用组件，主要用来显示和编辑图形。

canvas组件还被经常用来实现自定义组件。例如，可以在一个canvas上绘制并更新矩形框来实现一个进度条。

<!--more-->

## 样例

要在canvas上画东西，需要使用`create`方法来新增item。例如：

```
from Tkinter import *

master = Tk()

w = Canvas(master, width=200, height=100)
w.pack()

w.create_line(0, 0, 200, 100)
w.create_line(0, 100, 200, 0, fill="red", dash=(4, 4))

w.create_rectangle(50, 25, 150, 75, fill="blue")

mainloop()
```

注意，在canvas中新增的item会一直存在到你删除它。如果你想改变所画的东西，可以通过`coords`、`itemconfig`、`move`等方法来修改所绘制的item，也可以通过`delete`方法将其删除。例如：

```
i = w.create_line(xy, fill="red")

w.coords(i, new_xy) # change coordinates
w.itemconfig(i, fill="blue") # change color

w.delete(i) # remove

w.delete(ALL) # remove all items
```

## 一些概念

为了在canvas上显示东西，需要创建几个canvas item，这些item会被存放在一个栈中。默认情况下，新绘制的item会被画在所有已绘制item的最上边，即覆盖掉其他item。

### canvas item

canvas组件支持以下标准item：

* `arc`（弧、弦、扇形）
* `bitmap`
* `image`（`BitmapImage`实例或`PhotoImage`实例）
* `line`
* `oval`（圆或椭圆）
* `polygon`
* `rectangle`
* `text`
* `window`

弦、扇形、椭圆、多边形、矩形由边和内部区域组成，边和内部区域均可以被设置为透明的。

window item被用来在canvas上放置其他tkinter组件，对于这些item，canvas仅仅管理其几何属性。

item类型可以使用C或C++进行扩展，通过Python包的形式供tkinter使用。

### 坐标系统

canvas组件使用两种坐标系：window坐标系和canvas坐标系。windows坐标系以窗口左上角作为原点(0, 0)，而canvas坐标系则会明确指明item在画布上的位置。通过滚动canvas，可以指明要在window上显示canvas坐标系上的哪一部分区域。

`scrollregion`属性被用来限制canvas的滚动操作，可以这样对其进行设置：

```
canvas.config(scrollregion=canvas.bbox(ALL))
```

通过`canvasx`和`canvasy`方法可以将window坐标转换为canvas坐标，代码如下：

```
def callback(event):
    canvas = event.widget
    x = canvas.canvasx(event.x)
    y = canvas.canvasy(event.y)
    print canvas.find_closest(x, y)
```

### item标识符：句柄和标签

canvas组件提供了几个标识item的方法：

* item句柄（整数）
* 标签
* `ALL`
* `CURRENT`

item句柄是个整数值，用来标识canvas中的一个item。tkinter会为每个在canvas上新创建的item自动分配一个句柄。item句柄可以通过整数形式或字符串形式传递给canvas提供的各类方法。

标签是附属于item的一个象征性名称。标签是原始字符串，但不能包含空格。一个item可以没有标签，也可以拥有多个标签，且一个标签名可以用于多个item。标签被item所拥有，依附于item而存在。

要为一个item增加标签，可以使用`itemconfig`方法设置item的`tags`属性或通过`addtag_withtag`方法直接新增。item的`tags`属性可接受字符串或字符串元组作为入参。代码如下：

```
item = canvas.create_line(0, 0, 100, 100, tags="uno")
canvas.itemconfig(item, tags=("one", "two"))
canvas.addtag_withtag("three", "one")
```

要获取一个特定item的所有标签，可使用`gettags`方法。要通过标签获取相关item，可使用`find_withtag`方法。代码如下：

```
>>> print canvas.gettags(item)
('one', 'two', 'three')
>>> print canvas.find_withtag("one")
(1,)
```

canvas组件提供了两个预定义标签：

* `ALL` （或字符串`"all"`）其匹配canvas中的所有item。
* `CURRENT` （或字符串`"current"`）其匹配鼠标指针所指向的item。这可以在鼠标事件的处理中来找到触发事件的item。

### 打印

tkinter组件支持向PostScript打印机进行输出。

## 性能问题

canvas组件实现了一个直接的毁掉重来型的显示模式（straight-forward damage/repair display model，这..有啥官方翻译不...）。任何对canvas的修改和如`Expose`之类的外部事件都被认为是对屏幕的破坏。组件维护了一个`dirty rectange`来记录被破坏的区域。

当第一个破坏事件到来时，canvas注册一个待机任务（通过`after_idle`方法），该任务会在程序回到tkinter主循环时修复canvas。此外也可以通过调用`update_idletasks`来强制更新canvas。

在重绘canvas时，会先分配一个与`dirty rectangle`尺寸一样的像素图（一块内存）。之后遍历canvas中的item并对其中与`dirty rectangle`有重叠的item进行重绘。最后，这块像素图会被复制到显示屏上，同时存放像素图的内存被释放，这个复制过程在现代计算机上会非常快。

由于canvas仅使用了一个`dirty rectangle`，因此可以通过强制更新来得到更好的性能。例如，如果要同时改变画布中不同的几个区域，可以通过在改变后直接调用`update_idletasks()`来分别进行更新，以避免回到主循环进行重绘的时候`dirty rectangle`较大而不得不重绘大量其实并没有被改变的item。

## API参考手册

略
