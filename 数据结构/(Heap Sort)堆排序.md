[TOC] 
### 堆的基本介绍
堆(Heap)是计算机科学中一类特殊的数据结构的统称。
堆通常是一个可以被看做一棵<font color ="Red">完全二叉树</font>的数组对象。**把它的所有元素按照完全二叉树的顺序存储方式存储在一个一维数组中，并满足任意一个父亲节点的值一定小于等于(大于等于)孩子节点的值**。

堆的两条性质：
> * <font color ="Red">堆中某个节点的值总是不大于或不小于其父节点的值</font>
> * <font color ="Red">堆总是一棵完全二叉树</font>

堆的分类：
 > * 小根堆(小堆)： <font color ="Blue">根节点最小的堆</font>
 > * 大根堆(大堆)： <font color ="Blue">根结点最大的堆</font>

![在这里插入图片描述](https://img-blog.csdnimg.cn/f8428619effa467c873e334ee55f1729.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA57qi6LGG6aWt,size_20,color_FFFFFF,t_70,g_se,x_16) 
****
### 堆排序的实现
既然是堆排序，我们必须要建堆，在堆的基础上来进行排序。

那么首先是建堆，那我们如何让一堆无序的数字来让它形成堆呢？

这就首先需要了解一个堆排序的基本算法——<font color ="Red">向下调整算法</font>
***
#### 建堆(向下调整算法)
首先我们给出一个数组，逻辑上看做一颗完全二叉树。
`int array[] = {27,15,19,18,28,34,65,49,25,37};`
![在这里插入图片描述](https://img-blog.csdnimg.cn/c773a61407cf4259bf8a95eb2da2e720.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA57qi6LGG6aWt,size_20,color_FFFFFF,t_70,g_se,x_16)


这是一组**特殊数据**，但这组数据正好满足了向下调整算法的条件

**向下调整算法有一个前提**：<font color="Red">左右子树必须是一个堆，才能调整。</font>

我们可以通过<font color ="Red">从根节点开始的向下调整算法</font>可以把它调整成一个小堆。

我们观察数据可以发现只有根节点的数据不满足小堆，而左右子树都是满足的，所以我们需要从根节点来一步一步往下调整，使之形成一个小堆。

调整的依据就是小堆的条件：<font color ="Red">任意父节点都是小于等于孩子节点的。</font>

**向下调整算法**：
>  1.从根节点开始，不断向下调整
>  2.选出左右孩子中的较小的，跟父亲比较
>    * 如果比父亲小，就和父亲交换
>   * 如果比父亲大，就停止交换，此时已经构成堆了
>   
>  3.最坏交换到孩子为叶节点时停止

![在这里插入图片描述](https://img-blog.csdnimg.cn/78b0503e20eb4c6483629e0a130e4398.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA57qi6LGG6aWt,size_20,color_FFFFFF,t_70,g_se,x_16)

代码实现：
```c
//交换
void swap(int* px, int *py)
{
	int tmp = 0;
	tmp = *px;
	*px = *py;
	*py = tmp;
}

//向下调整算法，条件：左右子树都是堆

//先假设建为小堆(大堆就做相应修改)

//a为数组首地址，n为数据个数，parent为父节点
void AdjustDown(int* a, int n, int parent)
{
	int child = parent * 2 + 1;//先默认指向左孩子
	
    //结束条件为child的下标小于n，当child为叶子节点时，下一步child就是大于n的，循环结束
	while (child < n)
	{
		//找到左右孩子中小的那个，使child为较小的那个孩子
		if ((child + 1 < n) && (a[child + 1] < a[child]))
		{
			child++;//右孩子的下标默认比左孩子多一
		}

		//1.如果小的孩子比父亲小，则交换，继续向下调整
		//2.如果小的孩子比父亲大，结束调整

		if (a[child] < a[parent])
		{
			//交换
			swap(&a[child], &a[parent]);
			parent = child;
			child = parent * 2 + 1;
		}
		else
		{
			break;
		}
	}
}
```
***
向下调整算法已经讲完了，但是我们会发现这样是很鸡肋的，只能在特定的情况下形成堆，那么如何保证一般情况下都可以形成堆呢？                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               

先给一棵不满足左右子树都是堆的完全二叉树，`int arr[] = {12, 34, 45, 56, 14, 20, 90, 67, 42, 74};`
我们将这个完全二叉树调成小堆。

主要思路：
> <font color="red">1.从倒数第一个非叶子节点开始向下调整
> 2.调整到根节点时停止</font>     

![在这里插入图片描述](https://img-blog.csdnimg.cn/d5fdf227b7ed44ad9edeabfcc9b6f167.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA57qi6LGG6aWt,size_20,color_FFFFFF,t_70,g_se,x_16)
代码实现：
```c
//建小堆
//i的初始值为第一个非叶子结点的下标
for (int i = (n - 1 - 1) / 2; i >= 0; i--)
{
	//从倒数第一个非叶子节点开始向下调整，直到根节点
	AdjustDown(a, n, i);
}
//调整完毕后得到的是一个小根堆
```
经过向下调整算法的操作，该一般完全二叉树已经形成了一个堆，那现在该如何排序呢？
***
#### 排序的实现
排序的实现，就要考虑是排升序，还是排降序了，建堆又有建大堆和建小堆的区分，那么如何选择可以最好呢？
> <font color="GoYellow">排升序：建大堆更好,建小堆也可以，无法体现建堆带来的价值 。排降序：建小堆更好，同理</font>

那么如何来进行排序呢？

> <font color="Blue">1.已经建好堆了，第一个值为最小(如果为大堆，则第一个值最大),将第一个值和最后一个值交换，储存起来
> 2.再除去最后一个数，从根节点进行向下调整，重复操作，依次得到最小值储存，直到只剩下最后一个数。</font>

![在这里插入图片描述](https://img-blog.csdnimg.cn/a4c05464df9344269e97a5be190cb34d.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA57qi6LGG6aWt,size_20,color_FFFFFF,t_70,g_se,x_16)
![在这里插入图片描述](https://img-blog.csdnimg.cn/4b5383bf60624d1e86a433f5adcd3e47.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA57qi6LGG6aWt,size_20,color_FFFFFF,t_70,g_se,x_16)
代码实现：
```c
int end = n - 1;
	while (end > 0)
	{
		swap(&a[0], &a[end]);//将最小值与数组后续元素交换，储存到数组后面，形成降序
		AdjustDown(a, end, 0);//再依次调整得到最小值
		end--;
	}
```
***
### 堆排序完整代码
**动图：**
![在这里插入图片描述](https://img-blog.csdnimg.cn/6de61f6041f645cfaa5de7204585200b.gif#pic_center)
**代码：**

```c
#include<stdio.h>

//交换
void swap(int* px, int *py)
{
	int tmp = 0;
	tmp = *px;
	*px = *py;
	*py = tmp;
}

//向下调整算法，条件：左右子树都是堆

//先假设为小堆(大堆就做相应修改)
void AdjustDown(int* a, int n, int parent)
{
	int child = parent * 2 + 1;//默认指向左孩子

	while (child < n)
	{
		//找到左右孩子中小的那个
		if ((child + 1 < n) && (a[child + 1] < a[child]))
		{
			child++;//右孩子的下标默认比左孩子多一
		}

		//1.如果小的孩子比父亲小，则交换，继续向下调整
		//2.如果小的孩子比父亲大，结束调整

		if (a[child] < a[parent])
		{
			//交换
			swap(&a[child], &a[parent]);
			parent = child;
			child = parent * 2 + 1;
		}
		else
		{
			break;
		}
	}
}

void HeapSort(int* a, int n)
{
	//建小堆
	for (int i = (n - 1 - 1) / 2; i >= 0; i--)
	{
		//从倒数第一个非叶子节点开始向下调整，直到根节点
		AdjustDown(a, n, i);
	}
	//调整完毕后得到的是一个小根堆

	//排降序
	int end = n - 1;
	while (end > 0)
	{
		swap(&a[0], &a[end]);//将最小值与数组后续元素交换，储存到数组后面，形成降序

		AdjustDown(a, end, 0);//再依次调整得到最小值

		end--;
	}
}

int main()
{
	int a[] = { 12, 34, 45, 56, 14, 20, 90, 67, 42, 74 };

	//堆排序 
	HeapSort(a, sizeof(a) / sizeof(int));

	//打印数据
	for (int i = 0; i < sizeof(a) / sizeof(int); i++)
	{
		printf("%d ", a[i]);
	}
	return 0;
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/b3d18968fa274c09bc1b11dc42b91940.png) 
成功！！！
***
### 建堆的时间复杂度分析
![在这里插入图片描述](https://img-blog.csdnimg.cn/06612a04a83a486baf415761a66f9316.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA57qi6LGG6aWt,size_20,color_FFFFFF,t_70,g_se,x_16)  
**所以堆排序的时间复杂度为O(N*log2N)**(2为底数)





