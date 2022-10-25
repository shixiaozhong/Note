[TOC]

前言

> 本文有些概念参考了《数据结构、算法与应用 C++语言描述》这本书和维基百科，有些图片也是来自于维基百科。下面贴上维基百科的链接，有兴趣的同学可以去了解。[AVL树 - 维基百科，自由的百科全书 (wikipedia.org)](https://zh.wikipedia.org/wiki/AVL树)，电子书有需要的可以私信我。
>
> 本文的**主要内容是介绍AVL树的插入操作，尤其介绍旋转的情况和操作的具体实现。没有删除的具体介绍**。
>
> 需要完整代码，可以访问我的Gitee仓库[数据结构/AVL树/AVLTree.h -码云 - 开源中国 (gitee.com)](https://gitee.com/small__clock/algorithm/blob/master/数据结构/AVL树/AVLTree.h)

#### 一、AVL树

##### 1.1 定义

**如果搜索树的高度总是$O(log_N)$,我们就能保证查找、插入和删除的时间为$O(log_N)$,最坏情况下的高度为$O(log_N)$的树称为平衡树**。比较流行的一种平衡树是AVL树，AVL树得名于它的发明者[G. M. Adelson-Velsky](https://zh.m.wikipedia.org/wiki/格奥尔吉·阿杰尔松-韦利斯基)和[Evgenii Landis](https://zh.m.wikipedia.org/w/index.php?title=Evgenii_Landis&action=edit&redlink=1)。还有一种比较出名的平衡树叫红黑树，map和set的底层实现就是红黑树，后面会出一篇博客专门讲红黑树。

**二叉搜索树虽可以缩短查找的效率，但如果数据有序或接近有序二叉搜索树将退化为单支树，查找元素相当于在顺序表中搜索元素，效率低下。**因此，两位俄罗斯的数学家G.M.Adelson-Velskii和E.M.Landis在1962年发明了一种解决上述问题的方法：当向二叉搜索树中插入新结点后，如果能保证每个结点的左右子树高度之差的绝对值不超过1(需要对树中的结点进行调整)，即可降低树的高度，从而减少平均搜索长度。 

一棵AVL树或者是空树，或者是具有以下性质的二叉搜索树  

* 它的左右子树都是AVL树  
* 左右子树高度之差(简称平衡因子)的绝对值不超过1(-1/0/1)  

如果一棵二叉搜索树是高度平衡的，它就是AVL树。如果它有n个结点，其高度可保持在 ，搜索时间复杂度$ O(log_2N)$ 搜索时间复杂度$ O(log_2N)$。 

为什么平衡因子的差不是0呢？

因为在2，4等节点数量的情况下，不可能做到左右高度差相等，所以退而求其次，要求左右高度差不超过1。

---

##### 1.2 平衡因子

**AVL树一般用链表描述，下面提供的代码也就是链表结构实现的，使用的是三叉链结构**。为了简化插入和删除操作，我们为每一个节点增加一个平衡因子bf，**节点x的平衡因子bf(x)定义为：x的右子树高度 - x的左子树高度**。

注意这里有不同，在书籍和维基百科上都是左子树 - 右子树，因为自己的学习时就是这样定义的，不过没有大影响，只是注意后面平衡因子的值就行。

从AVL树的定义就可以知道，平衡因子的可能取值为-1、0和1，当出现了2或者-2时，就是出现了不平衡，需要通过旋转去调整。

**下图的平衡因子是右子树的高度 - 左子树的高度**。

![image-20220807114907133](http://shixiaozhong.oss-cn-hangzhou.aliyuncs.com/img/image-20220807114907133.png)

下面给出节点代码

```cpp
template<class K, class V>
struct AVLTreeNode
{
	//三叉链结构
	AVLTreeNode<K, V>* _left;
	AVLTreeNode<K, V>* _right;
	AVLTreeNode<K, V>* _parent;
	pair<K, V> _kv; // 这里存储的是一个pair
	int _bf; // 平衡因子 balance factor = 右子树高度 - 左子树高度

	//节点的构造函数
	AVLTreeNode(const pair<K, V> kv)
		:_left(nullptr)
		, _right(nullptr)
		, _parent(nullptr)
		, _kv(kv)
		, _bf(0)
	{}
};
```

---

#### 二、AVL树具体实现

##### 2.1 树的结构

```cpp
template<class K, class V>
class AVLTree
{
	typedef AVLTreeNode<K, V> Node;
public:
    //构造函数
	AVLTree()
		:_root(nullptr)
	{}
private:
    Node* _root; // 声明一个根
};
```

##### 2.2 插入新节点(:star:)

###### 2.2.1 插入位置问题

这里的插入和二叉搜索树是一样的，先找到插入位置，插入进去，并且更新平衡因子，如果不平衡再去进行调整。

```cpp
bool Insert(const pair<K, V>& kv)
{
    //插入节点
    if (_root == nullptr)
    {
        _root = new Node(kv);
        return true;
    }
    Node* parent = nullptr;
    Node* cur = _root;
	//寻找插入位置
    while (cur)
    {
        if (cur->_kv.first < kv.first)
        {
            parent = cur;
            cur = cur->_right;
        }
        else if (cur->_kv.first > kv.first)
        {
            parent = cur;
            cur = cur->_left;
        }
        else
        {
            return false;
        }
    }
    //插入节点
    cur = new Node(kv);
    if (parent->_kv.first > cur->_kv.first)
    {
        parent->_left = cur;
        cur->_parent = parent;
    }
    else
    {
        parent->_right = cur;
        cur->_parent = parent;
    }
    
    //插入完毕后，就需要控制树的平衡
    //...
}
```

---

###### 2.2.2 平衡因子更新问题

插入一个节点后，对这个新节点到祖先路径上所有节点都会有影响，所以还需要具体去分析

**平衡因子更新规则**

1. 插入更新的节点在父亲的左边，父亲平衡因子--
2. 插入更新的节点在父亲的右边，父亲平衡因子++
3. 父亲的平衡因子更新以后，是1或者-1，说明父亲所在子树的高度变了，需要继续向上更新
4. 如果父亲的平衡因子更新以后变为0，说明父亲所在子树的高度没变，不需要继续向上更新
5. 更新以后，父亲的平衡因子是2或者-2，说明父亲所在的子树已经不平衡了，需要旋转处理，让它平衡
6. 更新以后，到了根节点就不需要向上更新。

```cpp
bool Insert(const pair<K, V>& kv)
{
    //...
    //插入完毕后，就需要控制树的平衡
    //更新平衡因子
    while (parent)
    {
        if (cur == parent->_left)
            parent->_bf--;
        else
            parent->_bf++;
        
        //检查平衡因子
        //1、父亲所在子树的高度不变，不影响祖先，更新结束
        if (parent->_bf == 0)
        {
            break;
        }
        //2、父亲所在子树高度变了，继续向上更新
        else if (parent->_bf == 1 || parent->_bf == -1)
        {
            cur = parent;
            parent = parent->_parent;
        }
        //3、父亲所在子树出现不平衡，需要旋转处理
        else if (parent->_bf == 2 || parent->_bf == -2)
        {
            //旋转处理
            //...
        }
        else
        {
            assert(false);
        }
    }
    return true;
}
```

---

###### 2.2.3 平衡问题分析

如果插入一个节点后，导致树不满足左右子树高度差的绝对值小于等于1，就说这棵AVL树已经不平衡了。所以我们需要通过调整来使这棵树恢复平衡。我们**先分析下由插入操作导致不平衡的几种情形**：

1. 在不平衡树中，平衡因子的值限于-2， -1，0， 1， 2。	平衡因子的值是不会大于2或者小于-2的，因为在-2或者2时就已经需要去处理了。
2. 平衡因子为2的节点在插入前的平衡因子为1，类似的，平衡因子为-2的节点在插入前的平衡因子为-1。	这个没什么好解释的。
3. 只有从根到新节点的路径上的节点，其平衡因子在插入后才会发生改变。     在插入新节点后，会影响父亲子树的高度，所以父亲的平衡因子会受影响，同理父亲又是爷爷的子树，也会受到影响，所以会影响它所有祖先的平衡因子。一个插入会影响整条路径。
4. 假设A是离插入节点最近的祖先，其平衡因子是2或者-2，在插入前，从A到新插入节点的路径上，所有平衡因子都是0。    2或者-2一定是由1或者-1变化过来的，所以假设在A之前有不是0的节点，那么在插入后一定会先变成2或者-2。矛盾了，所以正确。

在树不平衡以后，我们就要想办法，让它平衡，**节点A的不平衡情况有两类：L型不平衡(插入节点在A的左子树中)和R型不平衡(插入节点在A的右子树中)。两种类型还可以细分**:

1. L型不平衡
    * LL型：在左子树的左子树中插入(单旋转)
    * LR型：在左子树的右子树中插入(双旋转)
2. R型不平衡
    * RR型：在右子树的右子树中插入(单旋转)
    * RL型：在右子树的左子树中插入(双旋转)

下图展示LL型和LR型，RR型和RL型对称的。

![](http://shixiaozhong.oss-cn-hangzhou.aliyuncs.com/img/旋转类型1.jpg)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         

---

##### 2.3 AVL树的旋转(:star:)

**我们把矫正LL型和RR型不平衡所做的转换称为单旋转，把矫正LR型和RL型的不平衡成为双旋转。对LR型不平衡所做的双旋转可以看作是RR型旋转加上LL型旋转，而对RL型双旋转就是LL型单旋转加上RR型单旋转**。

由于双旋转可以拆分为单旋转，所以在处理平衡时，重点关注单旋转。旋转的本质就是在遵循搜索树的规则情况下，让左右均衡，并且降低整棵树的高度。

**更新平衡因子的过程中，引发旋转的路径是直线就是单旋转，折线就是双旋转**。

---

###### 2.3.1 单旋转

我们先来看单旋转，先讨论LL型，**LL型就是在A的左子树的左子树去插入节点**。在B的左子树插入节点后，A的平衡因子变为了-2，树已经不平衡了，所以需要调整。同时需要注意旋转进行的条件，不同条件下，进行不同的旋转。

![](http://shixiaozhong.oss-cn-hangzhou.aliyuncs.com/img/LL型插入.jpg)

进行一次单旋转，注意这里的不平衡是左高右低，所以需要将右边下压，接近一样高，所以这个单旋转被称作右单旋。

> 树的**旋转方向**有很多不同的定义，有些定义彼此之间还存在冲突。有些人认为旋转方向应该反映节点的移动方向（左子树旋转到父节点的位置为右旋），有些人则认为旋转方向应该反映被旋转的子树是哪棵（左子树旋转到父节点的位置为左旋，与前一种说法反。这篇维基文章采用前者的定义：旋转方向为节点的移动方向。

---

**右单旋的条件**：

A的平衡因子为-2，B的平衡因子为-1，这种情况下进行右单旋。

**右单旋操作**：

1. 将B的右子树b作为A的左子树。
2. 让A作为B的右子树，使B作为新的父节点。

旋转完毕后注意平衡因子的变化，A和B都变为了0，树经过一次旋转就达到平衡了。

![](http://shixiaozhong.oss-cn-hangzhou.aliyuncs.com/img/右单旋.jpg)

下面看看代码实现，理论转化为代码才是最重要的。代码对标着图看，就很容易理解。过程看着不难，但是代码细节很多。

```cpp
//左边高，右单旋  函数调用
/*
if (parent->_bf == -2 && cur->_bf == -1) // 右单旋的条件
{
    RotateR(parent);
}
*/


//右单旋
void RotateR(Node* parent)
{
    Node* subL = parent->_left; // 对应着图中的B
    Node* subLR = subL->_right; // 对应着图中的b
	 
    //1. 将b作为A的左子树
    parent->_left = subLR;

    //判断subLR是不是空，如果不是空，就需要修改父亲，因为是三叉链结构
    if (subLR)
    {
        subLR->_parent = parent; // 修改b的父亲为A
    }

    //提前记录A的父亲，因为后面将B作为父节点时，需要向上连接
    Node* pparent = parent->_parent;

    subL->_right = parent; // 2. 将A作为B的右子树
    parent->_parent = subL; // 修改A的父亲为B

    //判断A是不是根
    if (parent == _root)
    {
        _root = subL; // 是根就将B作为新的根
        subL->_parent = nullptr; // B的父亲置空
    }
    else //不是根就连接新父亲
    {
        if (pparent->_left == parent)
            pparent->_left = subL;
        else
            pparent->_right = subL;
        
        //_parent指向新父亲
        subL->_parent = pparent;
    }

    //修改平衡因子
    parent->_bf = subL->_bf = 0;
}
```

---

RR型双旋转就是对称的，操作也是类似的，这里就直接贴个图。

**左单旋条件**：

A的平衡因子为2，B的平衡因子为1，这种情况下进行左单旋。

**左单旋操作**：

1. 将B的左子树b作为A的右子树。
2. 将A作为B的左子树，使B作为新的父节点。

<img src="http://shixiaozhong.oss-cn-hangzhou.aliyuncs.com/img/左单旋.jpg" style="zoom: 33%;" />

代码实现

```cpp
//右边高，左单旋
/*
else if (parent->_bf == 2 && cur->_bf == 1) // 左单旋的条件
{
    RotateL(parent);
}
*/


//左单旋
void RotateL(Node* parent)
{
    Node* subR = parent->_right; // 对应图中的B
    Node* subRL = subR->_left; // 对应图中的b
	//1. 将b作为A的右子树
    parent->_right = subRL;
    //判断b是不是为空
    if (subRL)
        subRL->_parent = parent; // 不为空就修改父亲
	
    //提前记录A的父亲，因为后面将B作为父节点时，需要向上连接
    Node* pparent = parent->_parent;
    subR->_left = parent; // 2. 将A作为B的左子树
    parent->_parent = subR; // 修改A的父亲
	
    //判断A是不是根节点 
    if (parent == _root)
    {
        _root = subR;  //是根就将B作为新的根
        subR->_parent = nullptr; // B的父亲置空
    }
    else //不是根就连接新父亲
    {
        if (pparent->_left == parent)
            pparent->_left = subR;
        else
            pparent->_right = subR;
        //_parent指向新父亲
        subR->_parent = pparent;
    }
    
    //修改平衡因子
    parent->_bf = subR->_bf = 0;
} 
```

下面贴一张动图，帮助理解

![](http://shixiaozhong.oss-cn-hangzhou.aliyuncs.com/img/单旋转动图.gif)

如果我们想测试代码的正确性我们可以设置单边树的情形。

![image-20220807173934323](http://shixiaozhong.oss-cn-hangzhou.aliyuncs.com/img/image-20220807173934323.png)

测试左单旋的情形，打开调试窗口，可以把图画出来，对比自己的结果看看，是不是对的。

测试右单旋就反过来，按654321来插入。旋转过程和答案在下图。

左单旋测试

<img src="http://shixiaozhong.oss-cn-hangzhou.aliyuncs.com/img/左单旋测试.jpg" style="zoom:33%;" />

右单旋测试

<img src="http://shixiaozhong.oss-cn-hangzhou.aliyuncs.com/img/右单旋测试.jpg" style="zoom:33%;" />

---

###### 2.3.2 双旋转

双旋转可以看作是单旋转的拆分，旋转类型有两种，LR型和RL型，也就对应着插入在不同的位置，这里也和单旋转一样，重点讲一种，另外一种是对称的。

我们来看LR型，也就是在左子树的右子树插入新节点，造成不平衡，然后再旋转处理。

**初始右子树为空的情况**

还有一种情况就是h==0时，开始只有两个节点，只能在B的右子树插入C，就是初始B的右子树为空的情况。最后平衡因子都为0。

![](http://shixiaozhong.oss-cn-hangzhou.aliyuncs.com/img/h等于0.jpg)

**h >= 1(初始右子树不为空)，即可以在右子树的左右子树插入**

<img src="http://shixiaozhong.oss-cn-hangzhou.aliyuncs.com/img/双旋转1.jpg" style="zoom: 50%;" />

**注意到这里在左子树的右子树插入的问题了吗？**

在C中插入时要注意分情况，是在左子树插入，还是在右子树插入，所得到的不平衡情况是不一样的，这里是和单旋转不一样的地方。单旋转在a中插入时，不管在a的左子树还是右子树，造成的不平衡情况是一样的。

**LR型双旋转的条件**：

A的平衡因子为-2，B的平衡因子为1。

**LR型双旋转的操作**：

1. 先以B为parent进行左单旋
2. 在以A为进行右单旋

可以注意到，不同情况两次旋转后的平衡因子变化情况是不同的。所以在写代码时一定要注意，分情况，看是在b中，还是在c中插入。

这里放一张维基百科上的动图帮助理解

<img src="http://shixiaozhong.oss-cn-hangzhou.aliyuncs.com/img/旋转动图.gif" style="zoom: 200%;" />

下面看看代码如何实现的

```cpp
//LR型，先左单旋，再右单旋
/*
else if (parent->_bf == -2 && cur->_bf == 1) // LR型的条件
{
    RotateLR(parent);
}
*/

//LR型双旋转
void RotateLR(Node* parent)
{
    Node* subL = parent->_left;
    Node* subLR = subL->_right;
    //记录插入后C的平衡因子，依据这个来完成最后平衡因子的改变
    int bf = subLR->_bf;
	
    //先以B左单旋
    RotateL(parent->_left);
    //再以A右单旋
    RotateR(parent);
	
    //依据C插入后的平衡因子的值来完成平衡因子的修改
    //1. 插入在小b的情况
    if (bf == -1)
    {
        subL->_bf = 0;
        parent->_bf = 1;
        subLR->_bf = 0;
    }
    //2. 插入在c的情况
    else if (bf == 1)
    {
        parent->_bf = 0;
        subL->_bf = -1;
        subLR->_bf = 0;
    }
    //3. 初始右子树为空的情况
    else if (bf == 0)
    {
        parent->_bf = 0;
        subL->_bf = 0;
        subLR->_bf = 0;
    }
    else
    {
        assert(false);
    }
}
```

关于RL型双旋转，就不做阐述，可以自己手画一下，和LR型是对称的。这里就直接上代码了。

```cpp
//RL型，先右单旋，再左单旋

/*
else if (parent->_bf == 2 && cur->_bf == -1)
{
    RotateRL(parent);
}
*/


void RotateRL(Node* parent)
{
    Node* subR = parent->_right;
    Node* subRL = subR->_left;
    
    int bf = subRL->_bf;

    RotateR(parent->_right);
    RotateL(parent);

    if (bf == 1)
    {
        subR->_bf = 0;
        parent->_bf = -1;
        subRL->_bf = 0;
    }
    else if (bf == -1)
    {
        parent->_bf = 0;
        subR->_bf = 1;
        subRL->_bf = 0;
    }
    else if (bf == 0)
    {
        parent->_bf = 0;
        subR->_bf = 0;
        subRL->_bf = 0;
    }
    else
    {
        assert(false);
    }
}
```

---

##### 2.4 检查AVL树是否平衡

AVL树的平衡条件就是所有的子树都需要满足左右子树的高度差的绝对值小于等于1。所以需要一个求树高度的函数

```cpp
//求树的高度
int Height(Node* root)
{
    if (root == nullptr)
        return 0;

    int leftHeight = Height(root->_left); // 左子树的高度
    int rightHeight = Height(root->_right); // 右子树的高度
    
    //求出树的高度
    return leftHeight > rightHeight ? leftHeight + 1 : rightHeight + 1;
}
```

树的高度现在知道了，下一步就是判断平衡了

```cpp
//判断平衡
bool _IsBalance(Node* root)
{
    if (root == nullptr)
        return true;

    int leftHeight = Height(root->_left);
    int rightHeight = Height(root->_right);
    //平衡因子的值是正确
    if (rightHeight - leftHeight != root->_bf)
    {
        cout << "平衡因子异常:"<<root->_kv.first <<endl;
    }
	//所有子树左右子树高度差是否满足条件
    return abs(rightHeight - leftHeight) < 2
        && _IsBalance(root->_left)
        && _IsBalance(root->_right);
}
```

#### 三、删除

AVL树的删除大概思路：

1. 按二叉搜索树的去删除
2. 更新平衡因子
3. 如果出现不平衡树，进行旋转，调整为平衡

对于删除的话，学习意义没有插入那么大，这里就简单了解一下就行了，如果想去深入学习，可以去看看《算法导论》或《数据结构-用面向对象方法与C++描述》殷人昆版。书中有代码参考。  

---

#### 四、总结

对于AVL树的学习，肯定不是那么容易的，对于旋转的理解肯定是要时间，在学习AVL树之前，我就已经学习过二叉查找树，对于AVL树的理解是很有帮助的，也可以多看看一些资料来补充。对于这篇文章，当然是我自己写的，可能存在一些疏漏，如果有问题的话，欢迎一起来探讨。
