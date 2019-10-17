---
title: C++中指针和const关键字用在一起的一些例子
date: 2014-03-23 00:00:00
tags: C/C++
---

变量被`const`修饰意味着这个变量是一个常量，即不能修改。而将`const`关键字与指针用在一起时有几种情况需要分清楚：

> * 指向`const`对象的非`const`指针
> * 指向非`const`对象的`const`指针
> * 指向`const`对象的`const`指针

下边分别对这几种情况举例进行说明。

<!--more-->

### 指向`const`对象的非`const`指针:

    const int a = 0;
    const int *pa = &a;

这里`a`是一个`int`类型的`const`对象，`pa`是一个指向『`int`类型的`const`对象』的指针。需要注意，`pa`本身是一个非`const`的指针，它可以指向其他`int`类型的`const`对象，但它所指向的对象却是`const`的，即在这个例子中不能通过`pa`来修改`a`的值。

### 指向非`const`对象的`const`指针:

    double b;
    double *const pb = &b;

这里`b`是一个`double`类型的非`const`对象，`pb`是一个指向『`double`类型的非`const`对象』的`const`指针。需要注意，`pb`本身是`const`的，也就是说它所指向的目标不能更改，本例中即为`b`，但`b`是非`const`的，可以变化，也可以通过`pb`来修改`b`的值。

### 指向`const`对象的`const`指针:

    const int c = 0;
    const int *const pc = &c;

这里`c`是一个`int`类型的`const`对象，`pc`是一个指向『`int`类型的`const`对象』的`const`指针。需要注意，在这个例子中，`c`和`pc`都是`const`的，即都不能改变，既不能改变`pc`指向的目标，也不能改变`pc`所指向的对象`c`的值

总结一下，**声明指针时，在类型前边加`const`表示要指向的对象是`const`的，在`*`后边、指针变量名前边加`const`表示这个指针本身是`const`的。**

另外，要注意用`typedef`定义的指针类型和`const`一起使用时的情况，举例说明：

    typedef string *pstring;
    const pstring ps = &s;

这里定义`pstring`类型为指向`string`对象的指针这一类型，第二行不能简单地做文本替换从而理解为：

     const string *ps = &s;

这里应这样理解：

**`pstring`类型是指向`string`对象的指针，所声明的`ps`是一个`const`的`pstring`类型，即`ps`是指向`string`类型的对象的`const`指针**：

    string *const ps = &s;

（完）
