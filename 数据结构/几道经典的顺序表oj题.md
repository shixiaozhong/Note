![在这里插入图片描述](https://img-blog.csdnimg.cn/48e1ac29586e409599973bc048e47f76.jpg#pic_center)

@[toc]
### 前言
上次发布了一篇讲解顺序表的博客，博客链接[c语言实现顺序表(初阶数据结构)](https://blog.csdn.net/qq_52906742/article/details/119652378?spm=1001.2014.3001.5502)最后答应大家的oj题讲解来了。

题目都是力扣上的，这个网站想必大家都有所了解，是一个很优秀的刷题网站，刷题可以帮我们巩固知识，是非常有必要的。

题目不多，只有三题。
<font color="Red">第一题详细说，让大家知道算法题的思路分析和做法。</font>

网站链接[力扣](https://leetcode-cn.com/)
### 1.移除元素
题目链接[移除元素](https://leetcode-cn.com/problems/remove-element/)
![在这里插入图片描述](https://img-blog.csdnimg.cn/3b43ae9650714c7882aaeb721bf07f25.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzUyOTA2NzQy,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/b3398caed59e4d0d8bdd3fc8403c91c4.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzUyOTA2NzQy,size_16,color_FFFFFF,t_70)
函数接口：
![在这里插入图片描述](https://img-blog.csdnimg.cn/2c0462494b284f8fa7b28c7f81cd96b2.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzUyOTA2NzQy,size_16,color_FFFFFF,t_70)
<font color="Red">nums为数组首地址，numsSize为原数组长度，val为指定的删除的值，返回的是数组删除后的长度</font>

****
这是题目的描述，我们可以看到<font color="Red">题目的大致意思就是删除数据，删除数值为指定值val的元素</font>。
看到这里，我们立马会想到顺序表的指定位置删除，采用的是覆盖的操作。
这里会有第一个思路
>思路一：<font color="Red">覆盖删除(时间换空间)</font>
![在这里插入图片描述](https://img-blog.csdnimg.cn/3337ef70967a4560b0216669d5a00da4.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzUyOTA2NzQy,size_16,color_FFFFFF,t_70)
>空间复杂度：O(1)
>时间复杂度：O(N^2)
><font color="Red">这是一种以时间换空间的做法</font>
****
>思路二：<font color="Red">创建一个新数组(以空间换时间)</font>
>![在这里插入图片描述](https://img-blog.csdnimg.cn/d37255b05de242d89281e072a982e3ae.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzUyOTA2NzQy,size_16,color_FFFFFF,t_70)
>时间复杂度：O(N)
>空间复杂度：O(N)
***
其实这两种做法都是常规做法，这里有没有什么巧妙的方法呢？使性能最优，答案是有的
>思路三：<font color="Red">双指针做法</font>
>![在这里插入图片描述](https://img-blog.csdnimg.cn/772adfd4e7e94110a9df46b93118c79c.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzUyOTA2NzQy,size_16,color_FFFFFF,t_70)

>时间复杂度：O(N)
>空间复杂度：O(1)
<font color="Red">这个思路就是最优的了</font>
****
下面给大家写写最优思路的代码
```c
int removeElement(int* nums, int numsSize, int val){
    //双指针法
    //大致思路就是把不是val的数值全部排到数组前面去
    int* src = nums;
    int* dst = nums;

    while(src - nums < numsSize)
    {
        if(*src != val)//找到不是val的数据后
        {
            *dst = *src;//拷贝到dst的位置
            //两个指针全部后走
            dst++;
            src++;
        }
        else
        {
        	//是val时只有src走，保证dst所指为val的位置
            src++;
        }
    }
     //dst最后指向的是最后一个拷贝过来的非val值，
     //所以dst-nums就是所需要的数组长度
     return dst - nums;
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/05bb9b0778114181b95cb7b5b3269999.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzUyOTA2NzQy,size_16,color_FFFFFF,t_70)
成功！！！，如果还是理解不了，欢迎私信，单独讲解。
****
### 2.删除有序数组中的重复项
****
题目链接[删除有序数组中的重复项](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-array/)
这个画图其实不太好讲，最好是做动图，但是小编还没有学会。不过已经在学了

这里就给大家看看代码,讲解都附在代码中了，不懂得可以私信我。
```c
int removeDuplicates(int* nums, int numsSize){
    //双指针解法
    //大致想法就是将所有的出现过的不同的数字从前到后依次排列到数组

    //src指针往后走，寻找所有出现的不相同的数
    //并且借助dst指针全部依次排列

     //刚开始两指针都指向数组首地址
    //1.判断src所指向的值是否和dst相同
    //(i)相同则src++，往后寻找
    //(ii)不相同则dst++(目的是保留已经寻找到的值)，然后将src所指向的值赋给dst
    //循环往复，直到src找到尾
    int* src = nums;
    int* dst = nums;
    if(numsSize == 0)//数组为空的特殊情况
        return 0;
    while(src - nums < numsSize)
    {
        if(*src == *dst)
        {
            src++;
        }
        else
        {
            dst++;
            *dst = *src;
        }
    }
    return dst - nums + 1;
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/741364f9e97646068796064e35481240.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzUyOTA2NzQy,size_16,color_FFFFFF,t_70)
成功！！！
****
### 3.合并两个有序数组
****
题目链接[合并两个有序数组](https://leetcode-cn.com/problems/merge-sorted-array/)
讲解都在代码中
```c
void merge(int* nums1, int nums1Size, int m, int* nums2, int nums2Size,
 int n){
	//思路：
    //从两个数组的最大值开始比较，较大的依次放到nums1的最后面(借助dst),
    //较大的继续往前走，再比较，循环往复，直到遍历完任一数组。
    
    //接下来需要讨论谁先遍历完，如果nums2先遍历完，
    //则表明nums2中的所有数已经全部添加到了nums1中。
    //如果nums1先遍历完，则nums2中还有数据没有添加到nums1中，需要额外操作
    
    int i1 = m - 1;//指向nums1中的最大值
    int i2 = n - 1;//指向nums2中的最大值
    int dst = nums1Size - 1;//指向nums1的最大下标处

    while(i1 >= 0 && i2 >= 0)//有一个遍历完了就停止
    {
        //谁大谁就--(往前走)。每比较一次，赋值完，dst也要往前走一步
        if(nums1[i1] > nums2[i2])
        {
            nums1[dst--] = nums1[i1--];
        }
        else
        {
            nums1[dst--] = nums2[i2--];
        }
    }
    //将nums2中未添加的数添加到nums1中
    while(i2 >= 0)
    {
        nums1[dst--] = nums2[i2--];
    }
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/15f22a3a47d84d8795d9222b2047ca54.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzUyOTA2NzQy,size_16,color_FFFFFF,t_70)
成功！！！
****

### 结语
****
这三道题都是力扣中的简单题，难度不大，但是做法都是比较巧妙的，这也看出了题目的灵活性，需要寻找最优的做法。这里推荐大家多看看题解，看题解是很有必要的，可以学习到一些大牛的优秀做法。
这里顺序表就结尾了，<font color="Red">小编接下来更新的是链表的相关知识</font>，欢迎继续关注，如果觉得还行的，可以一键三连！！！
![在这里插入图片描述](https://img-blog.csdnimg.cn/94f84e19f04a484eb20cb7b86bc84cc3.png#pic_center)

