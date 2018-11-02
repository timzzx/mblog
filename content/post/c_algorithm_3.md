---
title: "数据结构和算法学习理解C语言实现(三)"
date: 2018-10-30T14:35:43+08:00
categories: [数据结构和算法]
tags: [2018-11]
TableOfContents: true
---

# 树

“树”主要有根、干、枝、叶组成。在数据结构中树被引申为一个集合以及在该集合上定义的一种关系构成，包括根节点和若干课子树。

单个节点是一棵树，树根就是该节点本身。设T1,T2，···,Tk是数，它们的根节点分别为n1，n2，···，nk。如果用一个新节点n作为n1，n2，···，nk的父亲，得到一颗新树，节点n就是新树的根。称n1，n2，···，nk为一组兄弟节点，它们都是节点n的子节点，称n1，n2，···，nk为节点n的子树。由此可见数是由边连接起来的一系列节点，树的一个实例就是公司的组织机构图。

“树”需要满足如下两个条件

+ 有且仅有一个特定的称为根的节点
+ 其余的节点可分为m个互不相交的有限集合T1，T2，···，Tm，其中，每个集合又都是一棵树（子树）。

树的相关概念

+ 节点的度：是指一个节点的子树个数。
+ 树的度：一棵树中节点度的最大值。
+ 叶子（终端节点）：度为0的节点
+ 分支节点（非终端节点）：度不为0的节点。
+ 内部节点：除根节点之外的分支节点。
+ 孩子：将树中某个节点的子树的根称为这个节点的孩子。
+ 双亲：某个节点的上层节点称为该节点的双亲。
+ 兄弟：同一个双亲的孩子。
+ 路径：如果在树中存在一个节点序列k1，k2，···，kj，使得ki是ki+1的双亲（1≤i<j）,称该节点序列是从k1到kj的一条路径
+ 祖先：如果树中节点k到ks之间存在一条路径，则称k是ks的祖先。
+ 子孙：ks是k的子孙。
+ 层次：节点的层次是从根开始算起，第1层为根。
+ 高度：树中节点的最大层次称为树的高度或深度。
+ 有序数：将树中每个节点的各子树看成是从左到右有秩序的。
+ 无序数：有序树之外的称为无序数。
+ 森林：是n(n≥0)课互不想交的树的集合。

## 二叉树

二叉树是指每个节点最多有两个子树的有序树，通常将其两个子树的根分别称作“左子树”和“右子树”。

二叉树是节点的有限集，可以是空集，也可以是由一个根节点及两颗不相交的子树组成，通常将这两颗不想交的子树分别称作这个根的左子树和右子树。二叉树的特点。

+ 每个节点至多有两颗子树，即不存在度大于2的节点。
+ 二叉树的子树有左右之分，次序不能颠倒。
+ 二叉树的第i层最多有2的i-1次方个节点。
+ 深度为k的二叉树最多有2的k次方-1个节点。
+ 对任何一颗二叉树T，如果其终端节点树（即叶子节点树）为n0，度为2的节点树为n2，则n0=n2+1.

二叉树有5种基本形态

+ 空二叉树。
+ 只有一个根节点的二叉树。
+ 右子树为空的二叉树。
+ 左子树为空的二叉树。
+ 完全二叉树。

两种特殊的二叉树形态

+ 满二叉树：除了叶节点外，每一个节点都有左右子叶，并且叶节点都处在最底层的二叉树
+ 完全二叉树：只有最下层的两层节点度小于2，并且最下面一层的节点都集中在该层最左边的若干位置的二叉树。

二叉树的性质

1） 在二叉树中，第i层的节点总数不超过2的i-1次方

2） 深度为h的二叉树最少有h个节点，最多有2的h次方-1个节点（h≥1）。

3） 对于任意一颗二叉树来说，如果叶节点树为n0，且度数为2的节点总数为n2，则n0=n2+1.

4） 有n个节点的完全二叉树的深度为int(log2底n) + 1.

5） 存在一个有n个节点的完全二叉树，如果各节点用顺序方式存储，则在节点之间有如下关系。

+ 如果i=1，则节点i为根，无父节点；如果i＞1，则父节点编号为trunc(n/2).
+ 如果2i≤n，则其左儿子（即左子树的根节点）的编号为2i；如果2i>n,则无左儿子。
+ 如果2i+1≤n，则其右儿子的节点编号为2i+1；如果2i+1＞n，则无右儿子。

6） 假设有n个节点，能构成h(n)种不同的二叉树，则h(n)为卡特兰数的第n项，h(n)=C(n,2n)/(n+1).

二叉树存储

二叉树是一种数据结构，任务--存储数据。在使用二叉树的存储数据时，一定要体现二叉树中各个节点之间的逻辑关系，即双亲和孩子之间的关系，只有这样才能项外人展示其独有功能。在应用中，会要求从任何一个节点能直接访问到其孩子，或直接访问到其双亲，或同时访问其双亲和孩子。

顺序存储结构

二叉树的顺序存储结构是指用一维数组存储二叉树的节点，并且节点的存储位置（下标）应该能体现节点之间的逻辑关系，即父子关系。因为二叉树本身不具有顺序关系，所以二叉树顺序存储结构需要利用数组下标来反映节点之间的父子关系。使用完全二叉树中节点的层序编号可以反映出节点之间的逻辑关系，并且这种反映是唯一的。对于一般的二叉树来说，可以增添一些并不存在的空节点，使之成为一颗完全二叉树的形式，然后再用一维数组顺序存储。

二叉树顺序存储的具体步骤如下

+ 将二叉树按完全二叉树编号。根节点的编号为1，如果某节点i有左孩子，则其左孩子的编号为2i；如果某节点i有右孩子，则其右孩子的编号为2i+1
+ 以编号作为下标，将二叉树中的节点存储到一维数组中。

链式存储结构

链式存储结构有两种，分别是二叉链存储结构和三叉链存储结构。二叉树的链式存储结构又称二叉链表，是指用一个链表来存储一颗二叉树。在二叉树中，每一个节点用链表中的一个链节点来存储。

操作二叉树

+ 定义链式结构
+ 初始化二叉树
+ 添加新节点到二叉树
+ 获取左、右子树
+ 获取状态
+ 进行查找操作
+ 清空二叉树

遍历二叉树

一棵非空二叉树由根节点及左、右子树这3个基本部分组成，所以在任何一个给定节点上，可以按某种次序执行如下3个操作

+ 访问节点本身(node, N)
+ 遍历该节点的左子树(left subtree, L)
+ 遍历该节点的右子树(right subtree, R)

3种操作有6种执行次序，分别是NLR,LNR,LRN,NRL,RNL,RLN.因为前3种次序与后3种次序对称。所以只讨论先左后右的前3种次序

+ NLR：即前序遍历，也称先序遍历，访问节点的操作发生在遍历其左右子树之前。
+ LNR：即中序遍历，访问节点的操作发生在遍历其左右子树之间
+ LRN：即后序遍历，访问节点的操作发生在遍历其左右子树之后

遍历算法

1）如果二叉树为非空，可以按照下面的顺序进行操作。

+ 先遍历左子树。
+ 然后访问根节点。
+ 最后遍历右子树。

2）如果二叉树为非空，可以按照下面的顺序进行操作。

+ 先访问根节点
+ 然后遍历左子树。
+ 最后遍历右子树。

3）如果二叉树为非空，可以按照下面的顺序进行操作

+ 先遍历左子树
+ 然后遍历右子树
+ 最后访问根节点

线索二叉树

线索二叉树是指n个节点的二叉链表中含有n+1个空指针域。利用二叉链表中的空指针域，存放指向节点在某种遍历次序下的前趋和后继节点的指针(这种附加的指针称为“线索”)。这种加上了线索的二叉链表称为线索链表，相应的二叉树称为线索二叉树。根据线索性质的不同，线索二叉树可分为前序线索二叉树、中序线索二叉树和后序线索二叉树3种

## 霍夫曼树

一些概念

+ 路径：从树中一个节点到另一个节点之间的分支构成这两个节点之间的路径。
+ 路径长度：路径上的分支数目称为路径长度。
+ 树的路径长度：从树根到每一个节点的路径长度之和
+ 节点的带权路径长度：从该节点到树根之间的路径长度与节点上权的乘积。
+ 树的带权路径长度：是树中所有叶子节点的带权路径长度的和
