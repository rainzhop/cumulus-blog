---
title: 『渣译』Linux内核链表的解释
date: 2014-12-19 00:00:00
tags: [链表, 渣译]
---

> 原文地址：[Linux Kernel Linked List Explained](https://isis.poly.edu/kulesh/stuff/src/klist/)
> 作者：Kulesh Shanmugasundaram

## 简介

C语言跟后来很多高级语言相比有一个最大的不同就是她没有内建一些好用的数据结构。不过，在Linux内核源码中有一个很好的循环链表的实现。

源码中的文件`include/linux/list.h`用C语言实现了一个非常好用的循环链表（后文中称`linuxlist`）。这个实现既高效又具有可移植性，这也是它被整合进内核的原因。当你在Linux内核中需要一个链表时就可以通过这个实现来将数据串起来，如果你想将它用在自己的应用程序中也几乎不需要对其进行什么修改就可以拿来用。

linuxlist的源代码：[list.h](/assets/list.h)

使用linuxlist的优点：

1. **类型无关**<br>这个链表可以被用来组织你想到的任意数据结构。
2. **可移植**<br>尽管我没有在所有平台上试过，但既然它都能被整合进内核，可移植性应该是没跑了。
3. **易用**<br>由于这个链表时类型无关的，不管链表中存储的数据是什么都可以用相同的函数对链表进行初始化、读取和遍历。
4. **易读**<br>宏和内联函数使得该文件中的代码优雅而易读。
5. **省时**<br>让你不用重复造轮子。使用这个链表可以将你从大量的调试和针对不同类型数据反复地创造链表中解放出来。
<!--more-->

linuxlist的实现与你曾见过的其他链表的实现可能都不同。通常，一个链表中会包含要被链接的元素，例如

```C
struct my_list{
	void *myitem;
	struct my_list *next;
	struct my_list *prev;
	};
```

而linuxlist给人的感觉是链表包含在了要被链接的元素中。例如，如果要创建一个链接` struct my_cool_list`的链表，则需要编码如下：

```C
struct my_cool_list{
	struct list_head list; /* 内核链表结构体 */
	int my_cool_data;
	void* my_cool_void;
	};
```

几点注意：

1. linuxlist存在于要链接的元素中；
2. 你可以将`struct list_head`放在要链接的结构体中的任何地方；
3. 你可以随意为`struct list_head`变量命名；
4. 你可以设计复合链表。

举个栗子，如下声明也是有效的：

```C
struct todo_tasks{
	char *task_name;
	unsigned int name_len;
	short int status;

	int sub_tasks;

	int subtasks_completed;
	struct list_head completed_subtasks;/* 链表结构体 */

	int subtasks_waiting;
	struct list_head waiting_subtasks; 	/* 另一个链表，所含元素可能相同也可能不同 */

	struct list_head todo_list;			/* 另一个链表 */
	};
```

OK，我们来看看linuxlist的结构在`include/linux/list.h`是如何定义的：

```C
struct list_head{
	struct list_head *next;
	struct list_head *prev;
	}
```

接下来我们来关注点细节。我们首先看看在我们自己的程序中应该怎样使用这个数据结构，然后我们再来研究这个数据结构是如何工作的。

---
## linuxlist的使用

熟悉linuxlist相关函数的最好方法莫过于阅读[list.h]({{ site.url }}/assets/list.h)文件，文件中的注释相当全面。

下面的代码是创建、增加、删除以及遍历链表的栗子：

```C
#include <stdio.h>
#include <stdlib.h>

#include "list.h"


struct kool_list{
	int to;
	struct list_head list;
	int from;
	};

int main(int argc, char **argv) {
	struct kool_list *tmp;
	struct list_head *pos, *q;
	unsigned int i;

	struct kool_list mylist;
	INIT_LIST_HEAD(&mylist.list);

	/* 向mylist中添加元素 */
	for(i = 5; i != 0; --) {
		tmp = (struct kool_list *)malloc(sizeof(struct kool_list));

		/* INIT_LIST_HEAD(&tmp->list);
		 *
		 * 这条语句会初始化动态分配的list_head，
		 * 当紧接着运行的函数是add_list()或其他类似的函数时可以省略这条语句，
		 * 因为在这些函数中，动态分配的list_head中的prev和next会被初始化。
		 */

		printf("enter to and from:");
		scanf("%d %d", &tmp->to, &tmp->from);

		/* 将新元素'tmp'添加到mylist中 */
		list_add(&(tmp->list), &(mylist.list));
		/* 也可以使用list_add_tail()函数将新元素添加到链表尾部
		 */
	}
	printf("\n");

	/* 现在你用有了一个struct kool_list类型的循环链表，
	 * 接下来我们来遍历这些元素并打印它们。
	 */

	/* list_for_each()是一个for循环的宏，
	 * 第一个参数用来记录循环次数，换句话说它指向了每次循环中当前元素中的list_head变量。
	 * 第二个参数指向了整个链表的头，该参数在这个宏中不会改变。
	 */

	printf("traversing the list using list_for_each()\n");
	list_for_each(pos, &mylist.lis) {

		 /* 此时： pos->next指向了下一个元素中的list变量，
 		  * pos->prev则指向了前一个元素中的list变量。
 		  * （这里所说的元素的类型是struct kool_list）
		  * 但我们要读取的不是list变量而是这个元素。
		  * 宏list_entry()就是用来读取元素的。
		  * 下一节"这是怎么做到的？"中解释了这是怎么做到的。
		  */

		 tmp = list_entry(pos, struct kool_list, list);

		 /* 给出一个指向struct list_head的指针、
		  * 它所在数据结构的类型以及它在这个数据结构中的名字（变量名），
		  * list_entry()会返回一个指向struct list_head指针所在的数据结构的指针。
		  * 例如，上边这次调用会返回一个指向struct kool_list的元素的指针。
		  */

		 printf("to = %d from = %d\n", tmp->to, tmp->from);

	}
	printf("\n");

	/* 由于这是个循环链表，你也可以以逆序遍历链表。
	 * 你所需要做的只是将'list_for_each'替换为'list_for_each_prev'，
	 * 其他部分保持不变。
	 * 你也可以通过list_for_each_entry()来直接遍历链表中的元素，如下。
	 */

	printf("traversing the list using list_for_each_entry()\n");
	list_for_each_entry(tmp, &mylist.list, list)
		 printf("to = %d from = %d\n", tmp->to, tmp->from);
	printf("\n");

	/* 现在我们来释放kool_list元素，由于我们会使用list_del()从链表中删除元素，
	 * 所以我们需要使用一个list_for_each()宏的更安全的版本list_for_each_safe()。
	 * 注意，在涉及到在循环中删除元素时你必须使用这个宏。
	 */

	printf("deleting the list using list_for_each_safe()\n");
	list_for_each_safe(pos, q, &mylist.lis) {
		 tmp= list_entry(pos, struct kool_list, list);
		 printf("freeing item to= %d from= %d\n", tmp->to, tmp->from);
		 list_del(pos);
		 free(tmp);
	}

	return 0;
}
```
---
## 这是怎么做到的？

没错，大部分的实现都相当普通，但却十分精巧。精巧之处就在于我们能够通过一个指向`struct list_head`的指针来获取到这个`struct list_head`所在的数据结构的地址。正如上一节看到的，这个戏法是由`list_entry()`宏来完成的。现在我们就来看看这个宏究竟做了什么。

```C
#define list_entry(ptr, type, member) \
	((type *)((char *)(ptr) - (unsigned long)(&((type *)0)->member)))
```
对于上一节的栗子，这个宏展开后的结果是：

```C
((struct kool_list *)((char *)(pos) - (unsigned long)(&((struct kool_list *)0)->list)))
```

这个问题困扰了不少人，但这确实是一个相当简单并且众所周知的小技巧。给定一个指向某个数据结构中的`struct list_head`的指针，宏`list_entry()`能够简单地算得指向这个数据结构的指针。为了达到这个目的，我们需要算出`list_head`在这个数据结构中的什么位置（相对于这个结构体起始位置的偏移量），然后将指向`list_head`的指针减去这个偏移量就可以得到这个数据结构的指针。

现在问题就变为如何计算这个偏移量了。假设有一个数据结构`struct foo_bar`，你想找到这个结构体中`foo`成员所在的偏移量，那么你需要这么做：

```C
(unsigned long)(&((struct foo_bar *)0)->boo)
```

即将内存地址0转换为一个指向某个结构体的地址（本栗子中是`struct foo_bar`）。然后取出你所感兴趣的成员的地址。这便是这个成员在结构体中的偏移量。而由于我们已经知道了这个成员的绝对地址，只需用该绝对地址减去这个偏移量，便可得到这个成员所在的结构体的起始地址。为了更好的理解这个问题，可以阅读一下如下代码：

```C
#include <stdio.h>
#include <stdlib.h>

struct foobar{
	unsigned int foo;
	char bar;
	char boo;
};

int main(int argc, char** argv){

	struct foobar tmp;

	printf("address of &tmp is = %p\n\n", &tmp);
	printf("address of tmp->foo = %p \t offset of tmp->foo = %lu\n", \
			&tmp.foo, (unsigned long) &((struct foobar *)0)->foo);
	printf("address of tmp->bar = %p \t offset of tmp->bar = %lu\n", \
			&tmp.bar, (unsigned long) &((struct foobar *)0)->bar);
	printf("address of tmp->boo = %p \t offset of tmp->boo = %lu\n\n", \
			&tmp.boo, (unsigned long) &((struct foobar *)0)->boo);

	printf("computed address of &tmp using:\n");
	printf("\taddress offset of tmp->foo: %p\n", \
			(struct foobar *) (((char *) &tmp.foo) - \
			((unsigned long) &((struct foobar *)0)->foo)));
	printf("\taddress and offset of tmp->bar: %p\n", \
		      (struct foobar *) (((char *) &tmp.bar) - \
		      ((unsigned long) &((struct foobar *)0)->bar)));
	printf("\taddress and offset of tmp->boo: %p\n", \
		      (struct foobar *) (((char *) &tmp.boo) - \
		      ((unsigned long) &((struct foobar *)0)->boo)));

	return 0;
}
```

这段代码输出的结果是：

```
address of &tmp is = 0xbfffed00

address of tmp->foo = 0xbfffed00   offset of tmp->foo = 0
address of tmp->bar = 0xbfffed04   offset of tmp->bar = 4
address of tmp->boo = 0xbfffed05   offset of tmp->boo = 5

computed address of &tmp using:
	address and offset of tmp->foo: 0xbfffed00
	address and offset of tmp->bar: 0xbfffed00
	address and offset of tmp->boo: 0xbfffed00
```

（完）
