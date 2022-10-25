[TOC]

#### c/c++内存分布

C/C++ 的内存管理跟 JAVA 这些语言是不同的 —— JAVA 的程序不是直接跑在操作系统上的，JAVA 是在 JVM 虚拟机上运行。C/C++ 的程序是直接跑在 OS 上的，这也是为什么我们学习 C/C++ 要学习内存管理的原因，所以 C/C++ 的学习者需要对系统了解的更深。

```C++
int globalVar = 1;
static int staticGlobalVar = 1;

void Test()
{
	static int staticVar = 1;
	int localVar = 1;
	int num1[10] = { 1, 2, 3, 4 };
	char char2[] = "abcd";
	char* pChar3 = "abcd";
	int* ptr1 = (int*)malloc(sizeof(int) * 4);
	int* ptr2 = (int*)calloc(4, sizeof(int));
	int* ptr3 = (int*)realloc(ptr2, sizeof(int) * 4);
	free(ptr1);
	free(ptr3);
}
/*
选择题：
选项 : A.栈 B.堆 C.数据段(静态区) D.代码段(常量区)

globalVar在哪里？C     staticGlobalVar在哪里？C
staticVar在哪里？C      localVar在哪里？A
num1 在哪里？A
char2在哪里？ A         *char2在哪里？A(这里比较容易出错，char2实际上是把常量区的内容拷贝到数组中，"abcd"实际上还是在常									  量区，char2里的只是一份拷贝，pchar3是储存常量字符串"abcd"的地址)
pChar3在哪里？ A        *pChar3在哪里？D
ptr1在哪里？A           *ptr1在哪里？B
*/
```

<img src="http://shixiaozhong.oss-cn-hangzhou.aliyuncs.com/img/image-20220517130512193.png" alt="image-20220517130512193" style="zoom:67%;" />

1. **内核空间：** 放置操作系统相关的代码和数据。(用户不能直接进行操作 ------ 可以通过调用系统提供的 api 函数)。
2. 栈：又叫堆栈，非静态局部变量/函数参数/返回值等等，栈是向下增长的 。
3. **内存映射段**是高效的I/O映射方式，用于装载一个共享的**动态内存库**。用户可使用系统接口创建共享共享内存，做进程间通信。
4. **堆**用于程序运行时动态内存分配，堆是可以上增长的。
5. **数据段**–存储全局数据和静态数据。
6. **代码段**–可执行的代码/只读常量。

---

#### C语言中动态内存管理方式  

c语言中动态内存管理的方式会用几个常见的函数，malloc，calloc，realloc和free。

那么malloc，calloc和realloc的区别是什么呢？

malloc开辟空间不进行初始化，calloc可以进行初始化，realloc调整空间大小。

```c++
void Test ()
{
    int* p1 = (int*) malloc(sizeof(int));
    free(p1);
    int* p2 = (int*)calloc(4, sizeof (int));
    int* p3 = (int*)realloc(p2, sizeof(int)*10);
    if(!p3)
    {
        //处理错误
    }
    p2 = p3;
    free(p2);
}
```

---

#### C++内存管理方式

C语言内存管理方式在C++中可以继续使用，但有些地方就无能为力而且使用起来比较麻烦，因此C++又提出了自己的内存管理方式：通过**new**和**delete**操作符进行动态内存管理。对于malloc/free和new/delete，**一定要配套使用，可能会出问题**。在c++中，更建议使用new/delete。因为malloc/free能做的，new/delete也能做到，new/delete能做的，malloc/free不一定能做到。

注意：**malloc/free是函数，而new/delete是运算符**。

---

##### new和delete的实现原理

**内置类型**  

对于内置类型来说，malloc/free和new/delete是基本类似的，不同的地方是：new/delete申请和释放的是单个元素的空间，new[]和delete[]申请的是连续空间，而且new在申请空间失败时会抛异常。  

**自定义类型**

* new的原理
    1. 调用operator new函数申请内存空间。
    2. 在申请的空间上执行构造函数，完成对象的构造。  
* delete的原理
    1. 在空间上执行析构函数，完成对象中资源的清理工作。
    2. 调用operator delete函数释放对象的空间。
* new T[N]的原理
    1. 调用operator new[]函数，在operator new[]中实际调用operator new函数完成N个对象空间的申请。
    2. 在申请的空间上执行N次构造函数。
* delete[]的原理  
    1. 在释放的对象空间上执行N次析构函数，完成N个对象中资源的清理。
    2. 调用operator delete[]函数释放空间，实际在operator delete[]函数中调用operator delete函数来释放空间。

```c++
//开一个2g的空间,malloc失败返回的是NULL
	char* a = (char*)malloc((size_t)2 * 1024 * 1024 * 1024);
	if (!a)
	{
		cout << "malloc fail" << endl;
	}
	else
	{
		printf("malloc success: %p\n", a);
	}
	
	//new是申请失败抛异常的做法
	try
	{
		char* b = new char[10];
		printf("new success: %p\n", b);
	}
	catch(const exception e)
	{
		cout << e.what() << endl;
	}
```

<img src="http://shixiaozhong.oss-cn-hangzhou.aliyuncs.com/img/image-20220517144258704.png" alt="image-20220517144258704" style="zoom:67%;" />

---

##### operator new/operator delete

**new和delete是用户进行动态内存申请和释放的操作符，operator new 和operator delete是系统提供的全局函数**，new在底层调用operator new全局函数来申请空间，delete在底层通过operator delete全局函数来释放空间。  这个可以通过反汇编代码看出来。

<img src="http://shixiaozhong.oss-cn-hangzhou.aliyuncs.com/img/image-20220517144854529.png" alt="image-20220517144854529" style="zoom:67%;" />

可以看看operator new和operator delete的源码来看看这两个函数的实际实现。

```c++
/*
operator new：该函数实际通过malloc来申请空间，当malloc申请空间成功时直接返回；申请空间失败，
尝试执行空 间不足应对措施，如果改应对措施用户设置了，则继续申请，否则抛异常。
*/
void* __CRTDECL operator new(size_t size) _THROW1(_STD bad_alloc)
{
	// try to allocate size bytes
	void* p;
	while ((p = malloc(size)) == 0)
		if (_callnewh(size) == 0)
		{
			// report no memory
			// 如果申请内存失败了，这里会抛出bad_alloc 类型异常
			static const std::bad_alloc nomem;
			_RAISE(nomem);
		}
	return (p);
}

/*
operator delete: 该函数最终是通过free来释放空间的
*/
void operator delete(void* pUserData)
{
	_CrtMemBlockHeader* pHead;
	RTCCALLBACK(_RTC_Free_hook, (pUserData, 0));
	if (pUserData == NULL)
		return;
	_mlock(_HEAP_LOCK); /* block other threads */
	__TRY
		/* get a pointer to memory block header */
		pHead = pHdr(pUserData);
	/* verify block type */
	_ASSERTE(_BLOCK_TYPE_IS_VALID(pHead->nBlockUse));
    
    //这里是free释放空间
	_free_dbg(pUserData, pHead->nBlockUse);
	
    __FINALLY
		_munlock(_HEAP_LOCK); /* release other threads */
	__END_TRY_FINALLY
		return;
/*
free的实现
*/
#define free(p)		 _free_dbg(p, _NORMAL_BLOCK)
}
```

通过上述两个全局函数的实现知道，operator new实际也是通过malloc来申请空间，如果malloc申请空间成功就直接返回，否则执行用户提供的空间不足应对措施，如果用户提供该措施就继续申请，否则就抛异常。operator delete 最终是通过free来释放空间的。

---

#### 定位new表达式(placement-new )

定位new表达式是在**已分配的原始内存空间中调用构造函数初始化一个对象**。定位new表达式在实际中一般是配合内存池使用。因为内存池分配出的内存没有初始化，所以如果是自定义类型的对象，需要使用new的定义表达式进行显示调构造函数进行初始化。

```c++
class A
{
public:
	A(int a = 10)
	{
		_a = a;
	}
	~A()
	{

	}
private:
	int _a;
	int _b;
};

int main() 
{
	//开辟一块空间
	A* p = (A*)malloc(sizeof(A));
	//定位new调用构造函数进行初始化
	new(p)A(10);
	//调用析构
	p->~A();
	//将空间释放掉
	free(p);
	return 0;
}
```

---

  #### 内存泄漏

**内存泄漏**：内存泄漏指因为疏忽或错误造成程序未能释放已经不再使用的内存的情况。内存泄漏并不是指内存在物理上的消失，而是应用程序分配某段内存后，因为设计错误，失去了对该段内存的控制，因而造成了内存的浪费。    

**内存泄漏的危害**：长期运行的程序出现内存泄漏，影响很大，如操作系统、后台服务等等，出现内存泄漏会导致响应越来越慢，最终卡死。  

一个进程结束后，会把映射的内存都释放掉。那么内存泄漏好像也没啥事，因为进程正常结束，都会释放。

但是会有其他的一些场景危害很大的：

1. 进程没有正常结束，僵尸进程，尽可能会存在一些进程没有正常释放。
2. 长期运行的服务器，一般只有升级时才会停，内存泄漏会导致可用内存越来越小，程序越来越慢。
3. 物联网设备：扫地机器人，冰箱等，内存很小，经不起泄漏。

**C++需要主动释放内存。java不需要主动释放内存，java后台有垃圾回收机制，接管了内存释放**。

---

**如何预防内存泄漏呢**？

1. 智能指针。
2. 内存泄漏检测工具。