---
title: "C指针知识点收集"
date: 2018-10-11T14:25:05+08:00
categories: [c语言]
tags: [2018-10]
---

# C指针知识点收集

#### 1.C使用内存
+ 静态/全局内存（静态声明的变量，全局变量也使用这部分内存。静态变量的作用域则局限在定义它们的函数内部）
+ 自动内存（函数内部声明，只在函数被调用时才创建，只在函数内部调用。）
+ 动态内存（内存分配在堆上，可以根据需要释放。）

#### 2.C的动态内存分配实际上就是通过使用指针实现的。malloc和free函数分别用来分配和释放动态内存。动态内存分配可以实现变长数组和数据结构（如链表和队列）。

#### 3.声明整数和一个整数指针

``` C
int num;
int *pi;
```
#### 4.星号两边的空白符无关紧要，下面声明都是等价的

```C
int* pi;
int * pi;
int *pi;
int*pi;
```

#### 5.阅读声明

```C
const int *pci;
```
倒过来读

1.pci是一个变量  const int *<b>pci</b>;<br />
2.pci是一个指针变量  const int <b>*pci</b>;<br />
3.pci是一个指向整数的指针变量  const <b>int *pci</b>;<br />
4.pci是一指向整数常量的指针个变量  <b>const int *pci</b>;<br />

#### 6.地址操作符&会返回操作数的地址。可以用这个操作符初始化pi指针
```C
num = 0;
pi = &num;
```
还可以这样初始化；
```C
int num;
int *pi = &num;
```

#### 7.虚拟内存和指针。程序使用的地址是虚拟地址。操作系统会在需要时把虚拟地址映射为物理内存地址。

#### 8.用间接引用操作符解引指针。间接引用操作符（*）返回指针变量指向的值，一般称为解引指针

#### 9.指向函数的指针
函数可以声明为指向函数。函数没有返回值。指针的名字叫foo
```C
void (*foo)();
```

#### 10.null的概念
+ null概念
+ null指针常量
+ NULL宏
+ ASCII字符NUL
+ null字符串
+ null语句

#### 11.void指针
void指针是通用指针，用来存放任何数据类型的引用.<br />
它有两个有趣的性质
+ void指针具有与char指针相同的形式和内存对齐方式
+ void指针和别的指针永远不会相等，不过，两个赋值为NULL的void指针是相等的。

任何指针都可以被赋给void指针，它可以被转换回原来的指针类型，这样的话指针的值和原指针的值时相等的。<br />
void指针只用做数据指针，而不能用函数指针。

#### 12.指针操作符
|操作符|名称|含义|
|---|---|---|
|*||用来声明指针|
|*|解引|用来解引指针|
|->|指向|用来访问指针引用的结构的字段|
|+|加|用于对指针做加法|
|-|减|用于对指针做减法|
|== !=|相等、不等|比较两个指针|
|> >= < <=|大于、大于等于、小于、小于等于|比较两个指针|
|(数据类型)|转换|改变指针的类型|

#### 13.给指针加上一个整数实际上加的数是这个整数和指针数据类型对应字节数的乘积

#### 14.一个指针减去另一个指针会得到两个地址的差值。这个差值通常没什么用，但可以判断数组中的元素顺序。

#### 15.比较指针的作用主要是判断数组元素的相对顺序

#### 16.指针的常见用法
+ 多层间接引用
+ 常量指针

#### 17.动态内存分配
+ 用malloc类的函数分配内存
+ 用这些内存支持应用程序
+ 用free函数释放内存
```C
int *pi = (int*) malloc(sizeof(int));
*pi = 5;
printf("*pi: %d\n", *pi);
printf("*pi: %d\n", *pi);
free(pi);
```
#### 18.内存泄露
+ 丢失内存地址
+ 应该调用free函数却没有调用（有时候也称为隐式泄露）

#### 19.动态内存分配函数
+ malloc
+ realloc
+ calloc
+ free

|函数|描述|
|---|---|
|malloc|从堆上分配内存|
|realloc|在之前分配的内存块的基础上，将内存重新分配为更大或者更小的部分|
|calloc|从堆上分配内存并清零|
|free|将内存块返回堆|

#### 20.用free函数释放内存
将已释放的指针赋值为NULL
```C
int *pi = (int*) malloc(sizeof(int));
···
free(pi);
pi = NULL;
```
ps:注意野指针，好习惯释放完指针后赋值为NULL

#### 21.程序栈是支持函数执行的内存区域，通常和堆共享。也就是说，它们共享同一块内存区域。程序栈通常占据这块区域的下部，而堆用的则是上不。
+ 调用函数时，函数的栈帧被推倒栈上，栈向上“长出”一个栈帧。当函数终止时，其栈帧从程序栈上弹出。栈帧所使用的内存不会被清理，但最终可能会被推到程序栈上的另一个栈帧覆盖。
+ 动态分配的内存来自堆，堆向下“生长”。随着内存的分配和释放，堆中会布满碎片。尽管堆是向下生长的，但这只是个大体方向，实际上内存可能在堆上的任意位置分配

#### 22.栈帧的组织
栈帧由以下几种元素组成。
+ 返回地址（函数完成后要返回的程序内部地址）
+ 局部数据存储（为局部变量分配的内存）
+ 参数存储（为函数参数分配的内存）
+ 栈指针和基指针（运行时系统用来管理栈的指针）

#### 23.用指针传递数据
```C
void swapWithPointers(int* pnum1, int* pnum2) {
    int tmp;
    tmp = *pnum1;
    *pnum1 = *pnum2;
    *pnum2 = tmp;
}

int main() {
    int n1 = 5;
    int n2 = 10;
    swapWithPointers(&n1, &n2);
    return 0;
}
```

#### 24.返回指针
```C
int* allocateArray(int size, int value) {
    int* arr = (int*)malloc(size * sizeof(int));
    for(int i = 0; i < size; i++) {
        arr[i] = value;
    }
    return arr;
}

int* vector = allocateArray(5,45);
for(int i = 0; i < 5; i++) {
    printf("%d\n",vector[i]);
}

```
上面的代码是有问题注意1.指针用完没有释放，2在函数内创建的指针返回之后会在栈内弹出。

#### 25.传递指针的指针
> 将指针传递给函数时，传递的是值。如果我们想修改原指针而不是指针的副本，就需要传递指针的指针。

#### 26.实现自己的free函数
```C
void saferFree(void **pp) {
    if (pp != NULL && *pp != NULL) {
        free(*pp);
        *pp = NULL;
    }
}
```

#### 27.函数指针是持有函数地址的指针。这为我们以编译时未确定的顺序执行函数提供了另外一种选择，而不需要使用条件语句.函数指针对性能的影响要视具体情况而定。在表查询等场景中使用函数指针可以缓解性能问题

#### 28.使用函数指针

```C
int (*fptr1)(int);

int square(int num) {
    return num*num;
}

int n = 5;
fptr1 = square;
printf("%d squared is %d\n", n, fptr1(n));

// fptr1 = &square;也是一样的
```
还可以这么写
```C
typedef int (*funcptr)(int);
···
funcptr fptr2;
fptr2 = square;
printf("%d squared is %d\n", n, fptr2(n));
```

#### 29.传递函数指针

```C
int add(int num1, int num2) {
    return num1 + num2;
}

int subtract(int num1, int num2) {
    return num1 - num2;
}

typedef int (*fptrOperation)(int, int);

int compute() {
    return operation(num1, num2);
}

//使用
printf("%d\n",compute(add,5,6));
printf("%d\n",compute(sub,5,6));
```

#### 30.数组已经基本理解就不写了

#### 31.字符串本质就是数组也不提了

#### 32.我们可以使用C的结构体来表示数据结构元素，比如链表或数的节点，指针是把这些元素联系到一起的纽带。

#### 33.结构体声明的两种方式

```C
struct _person {
    char* firstName;
    char* lastName;
    char* title;
    unsigned int age;
}

typedef struct _person {
    char* firstName;
    char* lastName;
    char* title;
    unsigned int age;
} Person;

//声明
Person person;
//也可以声明一个Person指针并为它分配内存
Person *ptrPerson;
ptrPerson = (Person*) malloc(sizeof(Person));

//第一种声明，就用点表示法来访问其字段。
Person person;
person.firstName = (char*)malloc(strlen("Emily")+1);
strcpy(person.firstName,"Emily");
person.age = 23;

//如果使用结构体指针，就需要用箭头操作符
Person *ptrPerson;
ptrPerson = (Person*) malloc(sizeof(Person));
ptrPerson->firstName = (char*)malloc(strlen("Emily")+1);
strcpy(ptrPerson->firstName,"Emily");
person->age = 23;

//或者下面的方法（比较笨拙很少用）
Person *ptrPerson;
ptrPerson = (Person*) malloc(sizeof(Person));
(*ptrPerson).firstName = (char*)malloc(strlen("Emily")+1);
strcpy((*ptrPerson).firstName,"Emily");
(*ptrPerson).age = 23;
```
#### 34.结构体释放问题
在为结构体分配内存时，运行时系统不会自动为结构体内部的指针分配内存。类似的，当结构体消失时，运行时系统也不会自动释放结构体内部的指针指向的内存

#### 35.避免malloc/free开销
重新分配然后释放结构体会产生一些开销，可能导致巨大的性能瓶颈。解决这个问题的一种方法是为分配的结构体单独维护一张表。当用户不再需要某个结构体实例时，将其返回结构体池中。当我们需要某个实例时，从结构体池中获取一个对象。如果池中没有可用的元素，我们就动态分配一个实例。这种方法高效地维护一个结构体池，能按需使用和重复使用内存。

#### 36.用指针支持数据结构
+ 链表（单链表）
+ 队列（简单的先进先出队列）
+ 栈（简单的栈）
+ 树（二叉树）