### 1. 删除链表中等于给定值 val 的所有节点。
题目链接 [移除链表元素](https://leetcode-cn.com/problems/remove-linked-list-elements/)
![在这里插入图片描述](https://img-blog.csdnimg.cn/fa234296000140aba1c4231f171c9163.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA57qi6LGG6aWt,size_20,color_FFFFFF,t_70,g_se,x_16)
这个题有两个思路
><font color = "Red">1.直接在原链表的基础上删除值为val的节点
>2.创建一个新链表，将不是val的值的节点重新连接</font>
***

1.直接在原链表的基础上删除值为val的节点
```c
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     struct ListNode *next;
 * };
 */


struct ListNode* removeElements(struct ListNode* head, int val){
   //遍历每一个节点
   struct ListNode* cur = head;
   //保存要判断节点的前一个节点
   struct ListNode* prev = head;

   while(cur)
   {
       //保存当前要判断节点，便于free
       struct ListNode* f = cur;

       //当前节点为要删除的节点
       if(cur->val == val)
       {
           //待删节点为头节点
           if(cur == head)
           {
               //cur继续向后走一步
               cur = cur->next;
               //更新head
               head = cur;
           }
           //不是头节点
           else 
           {
               //prev直接指向待删节点的下一个节点，cur继续向后走
               prev->next = cur->next;
               cur = cur->next;
           }
           //将待删除的节点删除
           free(f);
       }
       //不是需要删除的
       else
       { 
           //prev和cur都往后走
           prev = cur;
           cur = cur->next;
       }
   }
   //返回操作后的链表
   return head;
}
```
***
2.创建一个新链表，将不是val的值的节点重新连接
```c
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     struct ListNode *next;
 * };
 */


struct ListNode* removeElements(struct ListNode* head, int val){
   //新链表的头节点
   struct ListNode* newhead = NULL;
   //遍历链表的每一个节点
   struct ListNode* cur = head;
   //保存新链表的尾节点，便于尾插
   struct ListNode* tail = NULL;

   while(cur)
   {
       //不是val就尾插到新链表
       if(cur->val != val)
       {
           //是第一个节点，做头节点
           if(tail == NULL)
           {
               newhead = tail = cur;
           }
           //不是就尾插
           else
           {
               tail->next = cur;
               tail = cur;
           }
           cur = cur->next;
       }
       else
       {
           cur = cur->next;
       }
   }
   //判断tail是不是空，如果是空，则意味着新链表为空，
   //不是空，tail->next需要置空，因为指向的是原链表中的一个val节点
   if(tail)
        tail->next = NULL;
   return newhead;
}
```
***
### 2.反转一个单链表。
 题目链接[反转链表](https://leetcode-cn.com/problems/reverse-linked-list/description/)
   ![在这里插入图片描述](https://img-blog.csdnimg.cn/87b3ab03a2f0434c95ffc9d5b452a904.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA57qi6LGG6aWt,size_20,color_FFFFFF,t_70,g_se,x_16)
这道题同样也有两个思路
><font color = "Red">1.通过三个指针来直接改变指向
>2.双指针不断头插后面的节点</font>
>
***

1.通过三个指针来直接改变指向
![在这里插入图片描述](https://img-blog.csdnimg.cn/4e6b872787324d71882ba7d469f214d1.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA57qi6LGG6aWt,size_20,color_FFFFFF,t_70,g_se,x_16)
![在这里插入图片描述](https://img-blog.csdnimg.cn/7bb2f19d534c4c1b9013229816f743c1.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA57qi6LGG6aWt,size_20,color_FFFFFF,t_70,g_se,x_16)
```c
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     struct ListNode *next;
 * };
 */


struct ListNode* reverseList(struct ListNode* head){
    //空链表或者只有一个节点时，直接返回head
    if(head == NULL || head->next == NULL)
        return head;
    //借助三个节点来改变指向
    struct ListNode* n1 = NULL;
    struct ListNode* n2 = head;
    struct ListNode* n3 = head->next;
    //条件为n2走到空停止
    while(n2)
    {
        //改变指向
        n2->next = n1;
        //向后迭代
        n1 = n2;
        n2 = n3;

        //判断n3，防止在最后时造成空指针访问
        if(n3)
            n3 = n3->next;
    }
    //n2走到空时，n1恰好在最后一个节点
    return n1;
}
```
***
2.双指针不断头插后面的节点
![在这里插入图片描述](https://img-blog.csdnimg.cn/3c2abdd804c841a68d60dc1df833ef17.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA57qi6LGG6aWt,size_20,color_FFFFFF,t_70,g_se,x_16)

![在这里插入图片描述](https://img-blog.csdnimg.cn/220fc6ff4e0d400388f1c699387bd0ef.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA57qi6LGG6aWt,size_20,color_FFFFFF,t_70,g_se,x_16)

```c
struct ListNode* reverseList(struct ListNode* head){
	 //空链表和只有一个节点是直接返回head
    if(head == NULL || head->next == NULL)
        return head;
    //新头节点
    struct ListNode* newhead = NULL;
    struct ListNode* cur = head;
    while(cur)
    {
        //保存当前节点的下一个节点
       struct ListNode* next = cur->next;
       //头插
       cur->next = newhead;
       //作为新头
       newhead = cur;
       //迭代
       cur = next;
    }
    return newhead;
}
```
***
### 3. 给定一个带有头结点 head 的非空单链表，返回链表的中间结点。如果有两个中间结点，则返回第二个中间结点。
题目链接[链表的中间节点](https://leetcode-cn.com/problems/middle-of-the-linked-list/description/)
![在这里插入图片描述](https://img-blog.csdnimg.cn/b21df61097634efc911b31748f9f07e6.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA57qi6LGG6aWt,size_20,color_FFFFFF,t_70,g_se,x_16)思路
><font color = "Red">用快慢指针(双指针)，一个指针走一步，一个指针走两步</font>

```c
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     struct ListNode *next;
 * };
 */


struct ListNode* middleNode(struct ListNode* head){
    //快慢指针，慢指针一次走一步，快指针一次走两步
    struct ListNode* slow = head;
    struct ListNode* fast = head;
    //两个判断条件，当节点数为奇数个时，最后fast是走到最后一个节点
    //当节点数是偶数个时，最后fast是走到空
    while(fast && fast->next)
    {
        slow = slow->next;
        fast = fast->next->next;
    }
    //最后slow指向的就是中间节点
    return slow;
}
```
***
### 4.输出该链表中倒数第k个结点
题目链接[链表中倒数第k个节点](https://www.nowcoder.com/practice/529d3ae5a407492994ad2a246518148a?tpId=13&&tqId=11167&rp=2&ru=/activity/oj&qru=/ta/coding-interviews/question-ranking)
![在这里插入图片描述](https://img-blog.csdnimg.cn/8638a9857ffb4f8abcf4ebc3cf1551b3.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA57qi6LGG6aWt,size_20,color_FFFFFF,t_70,g_se,x_16)

这题是上题的一个衍生题，也是双指针的做法。
思路
><font color="Red">让一个指针先走k步，在同步走，前面指针走到尾时，后面指针刚好在倒数第个节点</font>
***
```c
/**
 * struct ListNode {
 *	int val;
 *	struct ListNode *next;
 * };
 *
 * C语言声明定义全局变量请加上static，防止重复定义
 */

/**
 * 
 * @param pListHead ListNode类 
 * @param k int整型 
 * @return ListNode类
 */
struct ListNode* FindKthToTail(struct ListNode* pListHead, int k ) {
    //创建两个指针
    struct ListNode *slow,*fast;
    slow = pListHead;
    fast = pListHead;
    //快的先走k步
    while(k--)
    {
        if(fast == NULL)
            return NULL; 
        fast = fast->next;
    }
    //再同步走，直到走到尾
    while(fast)
    {
        slow = slow->next;
        fast = fast->next;
    }
    return slow;
}
```
***
### 5.合并两个有序链表
题目链接[合并两个有序链表](https://leetcode-cn.com/problems/merge-two-sorted-lists/description/)
思路
![在这里插入图片描述](https://img-blog.csdnimg.cn/90435a8a20ff48178bcfe270fa9538b9.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA57qi6LGG6aWt,size_20,color_FFFFFF,t_70,g_se,x_16)
思路
><font color="Red">创建一个新的链表,两个指针来比较值的大小，小的就尾插到新链表，最后将所有节点连接</font>
***
```c
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     struct ListNode *next;
 * };
 */


struct ListNode* mergeTwoLists(struct ListNode* l1, struct ListNode* l2){
    struct ListNode* n1 = l1;
    struct ListNode* n2 = l2;

    //如果两个链表中有空链表，不需要合并，直接返回另一条链表就可以
    if(n1 == NULL)
        return n2;
    if(n2 == NULL)
        return n1;

    //新链表的头尾指针
    struct ListNode* newhead, *tail;
    //创建一个哨兵位的头节点，便于尾插，最后要去除
    newhead = tail = (struct ListNode*)malloc(sizeof(struct ListNode));

    while(n1 && n2)
    {
        if(n1->val < n2->val)
        {
            tail->next = n1;
            tail = n1;
            n1 = n1->next;
        }
        else
        {
            tail->next = n2;
            tail = n2;
            n2 = n2->next;
        }
    }
    //将没有接上的链表连接上
    if(n1)
        tail->next = n1;
    if(n2)
        tail->next = n2;
        
    //free掉哨兵位的头节点
    struct ListNode* first = newhead->next;
    free(newhead);
    return first;
}
```
***
### 6.

