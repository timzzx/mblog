---
title: "数据结构和算法学习理解C语言实现(二)"
date: 2018-10-25T14:07:37+08:00
categories: [数据结构和算法]
tags: [2018-10]
TableOfContents: true
---

# 线性表、队列和栈

在C语言数据之间一般存在3种疾病结构

+ 线性结构：数据元素间是一对一关系
+ 树形结构：数据元素间是一对多关系
+ 网状结构：数据元素间是多对多关系

## 线性表

 线性表是一种最基本、最简单、最常用的数据结构。在实际应用中，线性表都是以栈、队列、字符串、数组等特殊线性表的形式来使用的。因为这些特殊线性表都具有自己的特性，所以掌握这些特殊线性表的特性，对于数据运算的可靠性和提高操作效率是至关重要的。

线性结构的特征

+ 集合中必存在唯一的“第一元素”和唯一的“最后元素”。
+ 除最后元素之外，均有唯一的后继；除第一元素之外，均有唯一的前驱。

线性表的基本操作如下

+ 用Sernull(L):置空表。
+ 用Length(L):求表长度和表中各元素个数。
+ Get(L, i):获取表中第i个元素（1<=i<=n）
+ Prior(L, i):获取i的前驱元素。
+ Next(L, i):获取i的后继元素。
+ Locate(L, x):返回指定元素在表中的位置。
+ Insert(L, i, x):插入新元素。
+ Delete(L,x):删除已存在元素。
+ Empty(L):判断表是否为空。

线性表的结构特点

+ 均匀性：虽然不同数据表的数据元素是各种各样的，但同一线性表的各数据元素必须有相同的类型和长度。
+ 有序性：各数据元素在线性表中的位置只取决于他们的序。数据元素之前的相对位置是由线性的，即存在唯一的“第一个”和“最后一个”数据元素，除了第一个和最后一个外，其他元素前面只有一个数据元素直接前驱。

在实际应用中，有两种实现线性表数据元素存储功能的方法，分别是顺序存储结构和链式存储结构。

顺序表操作的功能有以下几种

1）计算顺序表的长度

数组的最小索引是0，顺序表的长度就是数组中最后一个元素的索引last加1.

2） 清空操作

清空操作是指清除顺序表中的数据元素，最终目的是使顺序表为空，此时last等于-1.

3） 判断线性表是否为空

当顺序表的last为-1时表示顺序表为空，此时会返回true，否则返回false表示不为空。

4） 判断顺序表是否为满

当顺序表为满时last值等于maxsize-1，此时会返回true，如果不为满则返回false。

5） 附加操作

在顺序表没有满的情况下进行附加操作，在表的末端添加一个新元素，然后使顺序表的last加1.

6） 插入操作

在顺序表中插入数据的方法非常简单，只需要在顺序表的第i个位置插入一个值为item的新元素即可。

在顺序表插入一个新数据元素的基本步骤如下。

+ 判断顺序表的状态，判断是否已满和插入的位置是否正确，当表满或插入的位置不正确时不能插入。
+ 当表未满直插入的位置正确时，将an~ai依次向后移动，为新的数据元素空出位置。在算法中用循环来实现。
+ 将新的数据元素插入到空出的第i个位置上
+ 修改last值以修改表长，使其仍指向顺序表的最后一个数据元素

7） 删除操作

可以删除顺序表中的第i个数据元素

在顺序表中删除一个数据元素的基本流程如下。

+ 判断顺序表是否为空，判断删除的位置是否正确，当为空或删除的位置不正确时不能删除
+ 如果表为空和删除的位置正确，则将ai+1~an依次向前移动，在算法中用循环来实现移动功能
+ 修改last值以修改表长，使它仍指向顺序表的最后一个数据元素。

8） 获取表元

通过获取表元运算可以返回顺序表中第i个数据元素的值

9） 按值进行查找

所谓按值查找，是指在顺序表中查找满足给定值的数据元素。它就像住址的门牌号一样，这个值必须具体到xx单元xx室，否则会查找不到。按值查找就像word中的搜索功能一样，可以在繁多的文字中找到需要查找的内容。在顺序表中找到一个值的基本流程如下所示。

+ 从第一个元素起依次与给定值进行比较，如果找到，则返回在顺序表中首次出现与给定值相等的数据元素的序号，称为查找成功。
+ 如果没有找到，在顺序表中没有与给定值匹配的数据元素，返回一个特殊值表示查找失败

顺序表C语言例子

```C
#include<stdio.h>
#include<stdlib.h>

/**
 * 数组实现顺序表
 */
typedef int Status; //为数据类型声明别名，要加分号;
typedef int ElemType;
#define OK 1 //宏定义，不加分号;
#define ERROR 0
#define MAXSIZE 10
#define INCREMENT 10

typedef struct ArrayList   //顺序表三要素：元素基址、元素个数、表长
{
    ElemType *data; //数据元素基址
    int length; //顺序表当前元素个数
    int size; //顺序表表长
} ArrayList, *ArrayListPtr;

/**
 * 初始化构造一个空顺序表
 */
Status init(ArrayListPtr arrayListPtr)   //参数类型为指针变量
{
    arrayListPtr->data = (ElemType*)malloc(MAXSIZE*sizeof(ElemType)); //指针变量结构体引用成员变量使用->运算符;malloc函数申请的内存返回值要强转成对应存储格式的指针类型
    if(!arrayListPtr->data)
    {
        return ERROR;
    }
    arrayListPtr->length=0;
    arrayListPtr->size=MAXSIZE;
    return OK;
}

/**
 * 重置为空表；初始条件：顺序表存在
 */
Status clear(ArrayListPtr arrayListPtr)
{
    if(!arrayListPtr->data)
    {
        return ERROR;
    }
    arrayListPtr->length = 0;
    return OK;
}

/**
 * 销毁顺序表，回收内存空间；初始条件：顺序表存在
 */
Status destory(ArrayListPtr arrayListPtr)
{
    if(arrayListPtr->data)
    {
        free(arrayListPtr->data);
        arrayListPtr=NULL;
        return OK;
    }
    return ERROR;
}

/**
 * 根据位序返回元素
 * 思考：为什么要将返回结果作为参数传递进去而不是直接利用函数返回值传递？
 * 答：为了使函数更具有通用性，适应性更高，因为执行函数的结果有可能是正常返回指定的元素值，有可能查询失败而返回失败信息，此时如果用返回值类型做为函数返回值那么可能需要借助一些特殊值来区分失败的情况
 */
Status getElem(ArrayListPtr arrayListPtr, int pos, ElemType* e)
{
    if(pos < 1 || pos > arrayListPtr->length)
    {
        return ERROR;
    }
    *e = arrayListPtr->data[pos-1];
    return OK;
}

/**
 * 查找某个元素是否存在
 */
Status isLocate(ArrayListPtr arrayListPtr, ElemType e)
{
    int i = 0;
    if(!arrayListPtr->data)
    {
        return ERROR;
    }
    while(i < arrayListPtr->length)
    {
        if(arrayListPtr->data[i] == e)
        {
            return OK;
        }
        i++;
    }
    return ERROR;
}

/**
 * 判空
 */
Status isEmpty(ArrayListPtr arrayListPtr)
{
    return arrayListPtr->length == 0 ? 1 : 0;
}

/**
 * 插入元素到指定位置
 */
Status insert(ArrayListPtr arrayListPtr, int pos, ElemType e)
{
    ElemType* newBase;
    int i = 0;
    if(pos < 1 || pos > arrayListPtr->length+1)   //只能插入第一个位置到当前元素个数+1这之间的位置
    {
        printf("下标不合法，插入失败\n");
        return ERROR;
    }
    if(arrayListPtr->length == arrayListPtr->size)   //如果当前元素个数等于表长，则需要扩容
    {
        /*
        1）如果当前内存段后面有需要的内存空间，则直接扩展这段内存空间，realloc()将返回原指针。
        2）如果当前内存段后面的空闲字节不够，那么就使用堆中的第一个能够满足这一要求的内存块，将目前的数据复制到新的位置，并将原来的数据块释放掉，返回新的内存块位置。
        3）如果申请失败，将返回NULL，此时，原来的指针仍然有效。
        */
        newBase = (ElemType*)realloc(arrayListPtr->data, (arrayListPtr->size + INCREMENT)*sizeof(ElemType));
        arrayListPtr->data = newBase; //要将新首地址赋值给arrayList->data
        arrayListPtr->size += INCREMENT; //表长增加
    }
    for(i = arrayListPtr->length - 1; i >= pos-1; i--)
    {
        arrayListPtr->data[i+1] = arrayListPtr->data[i]; //后移操作
    }
    arrayListPtr->data[pos-1] = e; //插入操作
    arrayListPtr->length++;
    return OK;
}

/**
 * 删除指定位置元素，并用e返回
 */
Status deleteElem(ArrayListPtr arrayListPtr, int pos, ElemType* e)
//Status getElem(ArrayListPtr arrayListPtr, int pos, ElemType* e)
{
    int i;
    if(pos < 1 || pos > arrayListPtr->length)
    {
        return ERROR;
    }
    *e = arrayListPtr->data[pos-1];
    for(i = pos-1; i < arrayListPtr->length; i++)
    {
        arrayListPtr->data[i] = arrayListPtr->data[i+1];//前移操作
    }
    arrayListPtr->length--;
    return OK;
}

/**
 * 遍历顺序表
 */
void print(ArrayListPtr arrayListPtr)
{
    int i = 0;
    printf("\n顺序表表长 = %d, 元素个数 = %d,其元素分别为：",arrayListPtr->size, arrayListPtr->length);
    while(i<arrayListPtr->length)
    {
        printf("%d\t",arrayListPtr->data[i]);
        i++;
    }
    printf("\n");
}

void test1()
{
    ArrayList arrayList;
    ElemType e;
    ElemType* e1;
    //1.初始化
    init(&arrayList);
    printf("初始化顺序表：size = %d, length = %d\n",arrayList.size, arrayList.length);
    print(&arrayList);
    printf("\n是否为空表？%d\n", isEmpty(&arrayList));
    int i = 0;
    //2.插入元素
    while(i < 20)
    {
        insert(&arrayList, i+1, i);
        i++;
    }
    print(&arrayList);
    //3.根据位置返回元素

    getElem(&arrayList, 6, &e);
    printf("\n第6个元素是: %d\n", e);

    //4.查找某个元素是否存在
    printf("\n6是否存在于表中？ %d\n", isLocate(&arrayList, 6));
    printf("\n21是否存在于表中？ %d\n", isLocate(&arrayList, 21));
    printf("\n是否为空表？ %d\n", isEmpty(&arrayList));

    //5.删除元素
    deleteElem(&arrayList, 11, &e);
    printf("\n被删除的元素是：%d\n", e);
    printf("删除之后的元素序列为：");
    print(&arrayList);

    deleteElem(&arrayList, 11, &e);
    printf("\n被删除的元素是：%d\n", e);
    printf("删除之后的元素序列为：");
    print(&arrayList);
    //6.清空顺序表
    printf("执行清空之后:");
    clear(&arrayList);
    // insert(&arrayList, 1, 1); //执行清空之后仍可插入
    print(&arrayList);

    //7.销毁顺序表
    printf("执行销毁之后：");

    destory(&arrayList); //销毁只是回收了分配给线性表的空间，而arrayList变量依然存在，仍然可以访问arrayList.size等成员变量
    i = 0;
    while(i < 1)
    {
        insert(&arrayList, i+1, i);
        i++;
    }
    insert(&arrayList, 1, 6); //销毁之后再插入可能出现异常，因为地址已经被标记为可以被其他程序使用，当使用已经被占用的内存地址就会发生错误
    print(&arrayList);



}

```

## 链表操作

顺序表有一个规定，即用连续的存储单元顺序存储线性表中的各元素。根据这个规定，当对顺序表进行插入和删除操作时，必须移动数据元素才能实现线性表逻辑上的相邻关系。这种操作会影响运行效率。要解决效率问题，需要获取链式存储结构。

链式存储结构不需要用地址连续的存储单元来实现，而是通过“链”建立起数据元素之间的次序关系。所以它不要求逻辑上相邻的两个数据元素在物理结构上也相邻，在插入和删除时不需移动元素，从而提高了运行的效率。链式存储结构主要有单链表、循环链表、双向链表、静态链表等几种形式。

```C
#include <stdio.h>
#include<malloc.h>
#define LEN sizeof(struct student )
#define NULL 0
struct student 
{
	int num;
	float score;
	struct student *next;
};
int n;
//建立链表
struct student *create(void)
{
	struct student *head, *p1,*p2;
	n=0;
	p1=p2=(struct student *)malloc(LEN);
	scanf("%d %f",&p1->next,&p1->score);
	head=NULL;
	while(p1->num!=0)
	{
		n=n+1;
		if(n==1) head=p1;    //p1是指向新开辟的结点
		else p2->next=p1;
		p2=p1;    //p2指向链表中的最后一个基点
		p1=(struct student *)malloc(LEN); //开辟新的结点，并且使p1指向他
		scanf("%d %f",&p1->num,&p1->score);
	}
	p2->next=NULL;
	return (head);
}
//删除结点
struct student *del(struct student  *head,long num)
{
	struct student *p1,*p2;
	
	if(head==NULL)   {printf("\nlist null!\n");goto end;}
	p1=head;
	while(num!=p1->num && p1->next!=NULL)   //p1指向不是所要找的结点，并且后面还有结点
	{
		p2=p1;
		p1=p1->next;      //p1后移一个及诶单
	}       //p1后移一个结点
	if(num==p1->num)     //找到了
	{
		if(p1==head)  head=p1->next;    //若p1指向的是首结点，把第二个结点的地址head
 
		else p2->next=p1->next;      //or 将下一个结点的地址赋给前一个结点地址
		printf("delete:%d\n",num);
		n=n-1;
		free(p1);
	}
	else  printf("%dnot been found !\n",num);
	end:
	return(head);
 
 
}
//插入学生信息
 struct student *insert(struct student *head ,struct student *stud)
{
	struct student *p0,*p1,*p2;
	p1=head;         //使p1指向第一个结点
	p0=stud;        //p0指向要插入的结点
	if(head==NULL)     //原来的链表是空表
	{
		head=p0,p0->next=NULL; //使p0指向的结点作为头结点
	}
	else
	{
		while ((p1->num>p1->num)&&(p1->next!=NULL))
		{
			p2=p1;     //使p2指向刚才p1指向的及诶按
			p1=p1->next;    //p1后移一个结点
		}
		if(p0->num<=p1->num)
		{
			if(head==p1)  head=p0;   //插到原来第一个结点之前
			else p2->next=p0;        //插到p2指向的结点之后
			p0->next=p1;
		}
		else
		{
			p1->next=p0;p0->next=NULL;    //插到最后的结点之后
		}
	}
	n=n+1;                            //结点数加1
	return (head);
}
//输出链表
 void print(struct student *head)
{
	struct student *p;
	p=head;
	if(head!=NULL)
		do
	{
		printf("%d %f\n",p->num,p->score);
		p=p->next;
 
	}
	while(p!=NULL);
}
//main函数
 void main()
{
	struct student *head,stu;
	long del_num;
	printf("Input records:\n");
	head=create();        //建立链表，返回头指针
	print(head);          //输出全部结点
 
	printf("\nInput the deleted number:\n");
	scanf("%ld",&del_num);       //输入要删除的学号
	while(del_num!=0)
	{
	head=del(head,del_num);      //删除后链表的头指针
	print(head);                       //输出全部结点
	printf("Input the deleted number:\n");
	scanf("%ld",&del_num); 
	}
 
	printf("\nInput the inserted record:\n");
	stu=(struct student *)malloc(LEN);
	scanf("%d %f",&stu->num,&stu->score);     //输入要插入的结点
	{
	head=insert(head,&stu);                 //插入一个结点，返回头指针的
	print(head);                                     //输出全部结点
	printf("\nInput the inserted record:\n");
	stu=(struct student *)malloc(LEN);
	scanf("%d %f",&stu->num,&stu->score);
	}
}

```

## 先行先出的队列详解

先进先出队列严格按照“先来先得”原则，这一点和排队差不多。在买票时候需要排队，早来得先获得买票资格。计算机算法中的队列是一种特殊的线性表，它只允许在表的前端进行删除操作，在表的后端进行插入操作。队列是一种比较有意思的数据结构，最先插入的元素是最先被删除的；反之最后插入的元素是最后被删除的，因此队列又称为“先进先出”的线性表。进行插入操作的端称为队尾，进行删除操作的端称为对头。队列中没有元素时候，被称为空队列。

什么是队列

队列和栈一样，只允许在断点处插入和删除元素，循环队的入队算法如下。

+ tail=tail+1.
+ 如果tail=n+1，则tail=1.
+ 如果head=tai，即尾指针与头指针重合，则表示元素已装满队列，会施行“上溢”出错处理；否则Q（tail）=X，结束整个过程，其中X表示新的入出元素。

队列的基本操作如下。

1） InitQueue(&Q)

操作结果：构造一个空队列Q

2） DestroyQueue(&Q)

初始条件：队列Q已存在。

操作结果：销毁队列Q。

3） ClearQueue(&Q)

初始条件：队列Q已存在

操作结果：将队列Q重置为空队列。

4） QueueEmpty(Q)

初始条件：队列Q已存在

操作结果：若Q为空队列，则返回TRUE，否则返回FALSE

5） QueueLength(Q)

初始条件：队列Q已存在

操作结果：返回队列Q中数据元素的个数。

6） GetHead(Q,&e)

初始条件：队列Q已存在且非空

操作结果：用e返回Q中队头元素。

7） EnQueue(&Q, e)

初始条件：队列Q已存在。

操作结果：插入元素e为Q的新的队尾元素

8） DeQueue(&Q, &e)

初始条件：队列Q已存在且非空

操作结果：删除Q的队头元素，并用e返回其值

9） QueueTraverse(Q, visit())

初始条件：队列Q已存在且非空

操作结果：从队头到队尾依次对Q的每个数据元素调用函数visit(),一旦visit()失败，则操作失败

队列的基本操作

1）初始化队列Q的目的是创建一个队列。

2）入队的目的是将一个新元素添加到队尾，相当于到队列最后排队等候。

3）出队的目的是取出队头的元素，并同时删除该元素，使后一个元素成为队头。

4）获取队列第一个元素，即将队头的元素取出，不删除该元素，队头仍然是该元素

5）判断队列Q是否为空

顺序队列如下

```C
#include <stdio.h>

#define QueueSize 20
//typedef int DataType ;
#define DataType int
typedef struct {
    DataType data[QueueSize];
    int front;
    int rear;
    int count;
}Queue;

//初始化队列
void InitQueue(Queue *Q)
{
    Q->front = 0;
    Q->rear = 0;
    Q->count = 0;
}

//队列是否为空
int QueueEmpty(Queue *Q)
{
    return (Q->count == 0);
}

//队列是否满
int QueueFull(Queue *Q)
{
    return (Q->count == QueueSize);
}

//入队
void EnQueue(Queue *Q, DataType v)
{
    if(QueueFull(Q))
        printf("Error, the queue is full!");
    Q->data[Q->rear] = v;
    Q->rear = (Q->rear+1)%QueueSize; //循环，防止rear大于QueueSize；
    Q->count++;
}

//出队
DataType DeQueue(Queue *Q)
{
    DataType i;
    if(QueueEmpty(Q))
        printf("Error, the queue is empty");
    i = Q->data[Q->front];
    Q->front = (Q->front+1)%QueueSize; //循环
    Q->count--;
    return i;
}

//读队头元素，不改变对状态
DataType ReadQueue(Queue *Q)
{
    DataType i;
    if(QueueEmpty(Q))
        printf("Error, the queue is empty");
    i = Q->data[Q->front];
    return i;
}

int main()
{
    Queue Q, *Q1;
    int i = 1;
    InitQueue(&Q);
    while(i<=6)
    {
        EnQueue(&Q,i);
        i++;
    }

    printf("DeQueue: %d\n", DeQueue(&Q));
    printf("DeQueue: %d\n", DeQueue(&Q));
    printf("DeQueue: %d\n", ReadQueue(&Q));

    EnQueue(&Q,9);
    printf("The queue :");
    while(!QueueEmpty(&Q))
        printf("%d\t",DeQueue(&Q));

    Q1 = &Q;
    printf("\nThe length of the queue: %d", Q1->count);

    return 0;
}
```

循环队列

```C
/* 队列的顺序存储结构(循环队列) */
#define MAX_QSIZE 5 /* 最大队列长度+1 */
typedef struct
{
 QElemType *base; /* 初始化的动态分配存储空间 */
 int front; /* 头指针，若队列不空，指向队列头元素 */
 int rear; /* 尾指针，若队列不空，指向队列尾元素的下一个位置 */
}SqQueue;
/* 循环队列的基本操作(9个) */
void InitQueue(SqQueue *Q)
{ /* 构造一个空队列Q */
 Q->base=malloc(MAX_QSIZE*sizeof(QElemType));
 if(!Q->base) /* 存储分配失败 */
  exit(OVERFLOW);
 Q->front=Q->rear=0;
}
void DestroyQueue(SqQueue *Q)
{ /* 销毁队列Q，Q不再存在 */
 if(Q->base)
  free(Q->base);
 Q->base=NULL;
 Q->front=Q->rear=0;
}
void ClearQueue(SqQueue *Q)
{ /* 将Q清为空队列 */
 Q->front=Q->rear=0;
}
Status QueueEmpty(SqQueue Q)
{ /* 若队列Q为空队列，则返回TRUE；否则返回FALSE */
 if(Q.front==Q.rear) /* 队列空的标志 */
  return TRUE;
 else
  return FALSE;
}
int QueueLength(SqQueue Q)
{ /* 返回Q的元素个数，即队列的长度 */
 return(Q.rear-Q.front+MAX_QSIZE)%MAX_QSIZE;
}
Status GetHead(SqQueue Q,QElemType *e)
{ /* 若队列不空，则用e返回Q的队头元素，并返回OK；否则返回ERROR */
 if(Q.front==Q.rear) /* 队列空 */
  return ERROR;
 *e=Q.base[Q.front];
 return OK;
}
Status EnQueue(SqQueue *Q,QElemType e)
{ /* 插入元素e为Q的新的队尾元素 */
 if((Q->rear+1)%MAX_QSIZE==Q->front) /* 队列满 */
  return ERROR;
 Q->base[Q->rear]=e;
 Q->rear=(Q->rear+1)%MAX_QSIZE;
 return OK;
}
Status DeQueue(SqQueue *Q,QElemType *e)
{ /* 若队列不空，则删除Q的队头元素，用e返回其值，并返回OK；否则返回ERROR */
 if(Q->front==Q->rear) /* 队列空 */
  return ERROR;
 *e=Q->base[Q->front];
 Q->front=(Q->front+1)%MAX_QSIZE;
 return OK;
}
void QueueTraverse(SqQueue Q,void(*vi)(QElemType))
{ /* 从队头到队尾依次对队列Q中每个元素调用函数vi() */
 int i;
 i=Q.front;
 while(i!=Q.rear)
 {
  vi(Q.base[i]);
  i=(i+1)%MAX_QSIZE;
 }
 printf("\n");
}
```

##  后进先出栈

栈允许在同一端进行插入和删除操作，允许进行插入和删除操作的一端称为栈顶(top),另一端称为栈底(bottom).栈底是固定的，而栈顶是浮动的；如果栈中元素个数为零则被称为空栈。插入操作一般被称为入栈(Push),删除操作一般被称为出栈(Pop).

在栈中有两种基本操作，分别是入栈和出栈。

1）入栈（Push）

将数据保存到栈顶。在进行入栈操作前，先修改栈顶指针，使其向上移一个元素位置，然后将数据保存到栈顶指针所指向的位置。入栈（Push）操作的算法如下：

+ 如果TOP≥n，则给出溢出信息，进行出错处理。在进栈前首先检查栈是否已满，如果满则溢出；不满则进入下一步骤。
+ 设置TOP = TOP + 1，使栈指针加1，指向进栈地址。
+ S(TOP) = X,结束操作，X为新进栈的元素。

2）出栈（Pop）

将栈顶的数据弹出，然后修改栈顶指针，使其指向栈中的下一个元素。出栈（Pop）操作的算法如下：

+ 如果TOP≤0，则输出下溢信息，并进行出错处理。在退栈之前先检查是否已为空栈，如果是空则下溢信息，如果不空则进入下一步骤
+ X=S(TOP),退栈后的元素赋给X。
+ TOP = TOP - 1，结束操作，栈指针减1，指向栈顶。

栈的基本分类

1）顺序栈

顺序栈是栈的顺序存储结构的简称，是一个运算受限的顺序表，顺序栈是运算受限的顺序表

要注意3点

+ 顺序栈中元素用向量存放
+ 栈底位置是固定不变的，可以设置在向量两端的任意一个端点
+ 栈顶位置是随着进栈和退栈操作而变化的，用一个整型量top（通常称top为栈顶指针）来指示当前栈顶位置

顺序栈的基本操作

+ 进栈操作 进栈时，需要将S->top加1.
+ 退栈操作 在退栈时，需要将S->top减1.其中S->top<0表示此栈时一个空栈。当栈为空时，如果继续退栈运算将会产生溢出现象

顺序栈运算

顺序栈实现

```C
#include <stdio.h>

#define StackSize 20

//定义顺序栈结构
typedef struct{
    int data[StackSize];  //栈中数据
    int top;   //指示栈顶位置，为-1时栈为空栈
}SeqStack;

//初始化
void InitStack(SeqStack *S)
{
    S->top = -1;
}

//栈是否为空
int StackEmpty(SeqStack *S)
{
    if(S->top == -1)
        return 1;
    else
        return 0;
}

//栈是否已满
int StackFull(SeqStack *S)
{
    if(S->top == StackSize - 1)
        return 1;
    else 
        return 0;
}

//数据v压入栈
void Push(SeqStack *S, int v)
{
    if(StackFull(S))
        printf("Error,the stack is full");
    S->data[(++S->top)] = v;
}

//弹出栈顶数据
int Pop(SeqStack *S)
{
    int i;
    if(StackEmpty(S) == 1)
        printf("Error,the stack is empty");
    i = S->data[S->top];
    S->top--;
    return i;
}


int main()
{
    SeqStack St;    //这里不能直接声明*St，若声明指针则计算机只会给指针分配内存，而无法为堆栈分配数据存储的空间，因此会报错。
    int i = 0;
    InitStack(&St);
    while(i<=6)           //  将0-6压入栈中
    {
        Push(&St,i);
        i++;
    }
    printf("The top number of the stack is : %d\n",Pop(&St));    //弹出栈顶元素6
    Push(&St,12);
    Push(&St,10);
    printf("The top number of the stack is : %d\n",Pop(&St));  //弹出栈顶元素10
}

```

链栈实现代码

```C
#include <stdio.h>
#include <stdlib.h>

#define Datatype int

//栈节点定义
typedef struct Lstack{
    Datatype data;
    struct Lstack *next;
}StackNode;

//栈顶指针定义,存储指向StackNode节点的指针的一种类型
typedef struct{
    StackNode *top;
}StackTop;               //类似于链表的头指针head；

//置栈空
void InitStack(StackTop *S)
{
    S->top = NULL;
}

//判断栈是否为空
int StackEmpty(StackTop *S)
{
    return(S->top == NULL);
}

//数据v压入栈
void Push(StackTop *S, Datatype v)
{
    StackNode *p;
    p = (StackNode *)malloc(sizeof(StackNode));//分配新的空间
    p->data = v;
    p->next = S->top;
    S->top = p;
}

//弹出栈顶数据
Datatype Pop(StackTop *S)
{
    Datatype i;
    StackNode *p;
    if(StackEmpty(S))
        printf("Error, the stack is empty!");
    p = S->top;
    i = S->top->data;
    S->top = S->top->next;
    free(p);
    return i;
}

//读取栈顶数据，不改变栈
Datatype StackPop(StackTop *S)
{
    Datatype i;
    if(StackEmpty(S))
        printf("Error, the stack is empty!");
    i = S->top->data;
    return i;
}


void main()
{
    StackTop St; //这里为链栈的声明和初始化，不能声明为指针，不然程序无法自动分配内存空间
    Datatype i=1;
    InitStack(&St);
    while(i<=6)
    {
        Push(&St,i);
        i++;
    }

    printf("The top number of the stack is : %d \n",Pop(&St));
    printf("The top number of the stack is : %d \n",Pop(&St));
    printf("The top number of the stack is : %d \n",StackPop(&St));
    printf("The top number of the stack is : %d \n",StackPop(&St));
}
```

## 问题

线性表插入操作的时间复杂度是多少？

> 在顺序表上实现插入操作的过程是一个插入并重新排序的过程。在这个过程中，时间主要消耗在数据的移动上。在第i个位置插入一个元素，从ai到an都要向后移动一个位置。插入操作的时间复杂度为O(n).

线性表删除操作的时间复杂度是多少？

> 这个问题和插入一样都是O(n)

线性表按值查找操作的时间复杂度是多少？

> 在顺序表中进行的按值查找实现了一个比较运算，比较的次数与给定值在表中的位置和表长有关。当给定值与第一个元素相等时，比较次数为1；当给定值与最后一个元素相等时，比较次数为n，平均次数就是(n+1)/2，时间复杂度为O(0).

```
/************************************************************************/ 
/* 以下 是 关于 线性 表链 接 存储（ 单 链 表） 操作 的 11 种 算法 */ 
/* 1. 初始化 线性 表， 即 置 单 链 表 的 表头 指针 为 空 */ 
/* 2. 创建 线性 表， 此 函数 输入 负数 终止 读取 数据*/ 
/* 3. 打印 链 表， 链 表 的 遍历*/
/* 4. 清除 线性 表 L 中的 所有 元素， 即 释放 单 链 表 L 中 所有 的 节点， 使之 成为 一个 空 表 */ 
/* 5. 返 回单 链 表 的 长度 */ 
/* 6. 检查 单 链 表 是否 为 空， 若为 空 则 返回 １， 否则 返回 0 */ 
/* 7. 返 回单 链 表中 第 pos 个 节点 中的 元素， 若 pos 超出 范围， 则 停止 程序 运行 */ 
/* 8. 从 单 链 表中 查找 具有 给定 值 x 的 第一个 元素， 若 查找 成功 则 返回 该 节点 data 域 的 存储 地址， 否则 返回 NULL */ 
/* 9. 把 单 链 表中 第 pos 个 节点 的 值 修改 为 x 的 值， 若 修改 成功 返回 １， 否则 返回 0 */ 
/* 10. 向 单 链 表 的 表头 插入 一个 元素 */ 
/* 11. 向 单 链 表 的 末尾 添加 一个 元素 */ 
/************************************************************************/
```
