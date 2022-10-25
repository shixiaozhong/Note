[TOC]

#### 可调用类型

类型定义的对象可以像函数一样去调用。

1. 函数指针		定义：int (*p)(int, int)。
2. 仿函数             类+重载operator()。
3. lambda表达式
4. 包装器

![image-20221019092657417](http://shixiaozhong.oss-cn-hangzhou.aliyuncs.com/img/image-20221019092657417.png)

sort的参数就有一个可调用类型的参数，可以传函数指针，仿函数啥的。

```cpp
// 一个简单的成绩类
class Grade
{
public:
	const char* _name;	// 名字
	int _score;			// 分数
	// 构造函数
	Grade(const char* name, int score)
		:_name(name)
		, _score(score)
	{}
	~Grade()	
	{}
};


//仿函数
struct Compare1
{

	// 重载()
	bool operator()(const Grade& a, const Grade& b)
	{
		return a._score > b._score;		// 按照成绩从大到小排序
	}
};

// 定义一个函数
bool Compare2(const Grade& a, const Grade& b)
{
	return a._score > b._score;			// 按照成绩从大到小排序
}


int main()
{
	Grade grade[5] = { {"小明", 95}, {"小李", 98}, {"小张", 93}, {"小红", 92}, {"小王", 90}};
	sort(grade, grade + 5, Compare2);		// 用函数指针
	sort(grade, grade + 5, Compare1());		// 用仿函数
	return 0;
}
```

可以发现这两种方式都有点麻烦，并且如果函数的个数比较多的话，并且命名如果不太规范的话，函数指针和仿函数就会很难用了。

---

#### lambda表达式

lambda表达式书写格式：**[capture-list] (parameters) mutable -> return-type { statement }**  。

1. [capture-list] : 捕捉列表，该列表总是出现在lambda函数的开始位置，编译器根据**[]**来判断接下来的代码是否为**lambda**函数，捕捉列表能够捕捉上下文中的变量供**lambda**函数使用。  
2. (parameters)：参数列表。与**普通函数的参数列表一致**，如果不需要参数传递，则可以连同()一起省略。  
3. mutable：默认情况下，lambda函数总是一个const函数，mutable可以取消其常量性。使用该修饰符时，参数列表不可省略(即使参数为空)。  
4. ->returntype：返回值类型。用追踪返回类型形式声明函数的返回值类型，没有返回值时此部分可省略。返回值类型明确情况下，也可省略，由编译器对返回类型进行推导。  
5. {statement}：函数体。在该函数体内，除了可以使用其参数外，还可以使用所有捕获到的变量。  

注意： 在lambda函数定义中，**参数列表和返回值类型都是可选部分，而捕捉列表和函数体可以为空**。因此C++11中最简单的**lambda**函数为：**[]{}**。  

```cpp
// 捕捉列表, 不传参数
int x = 1, y = 3;
auto swap1 = [](int& a, int& b) {
    int t = a;
    a = b;
    b = t;
};
swap1(x, y);


// 捕捉列表, 传参数
// lambda想要用外面的变量，就需要捕捉，而捕捉是以传值形式传递过来的，本质就是拷贝过来的，不能修改
// mutable的作用就是让传过来的值可以进行修改，但是修改的是传过来的值，不影响外面的对象
// 实际意义不大，除非你就是想内部修改，不修改外部的值。
auto swap2 = [x, y](int a, int b) mutable {
	int t = a;
	a = b;
	b = t;
};
swap2();	// 这里的代码实际上完不成交换，如果不使用mutable，a和b的内部交换都会有问题
```

lambda表达式的四种捕捉方式。捕捉列表描述了上下文中那些数据可以被lambda使用，以及使用的方式传值还是传引用，捕捉只能捕捉父作用域。  

* [var]：表示值传递方式捕捉变量var  
* [=]：表示值传递方式捕获所有父作用域中的变量(包括this)  
* [&var]：表示引用传递捕捉变量var  
* [&]：表示引用传递捕捉所有父作用域中的变量(包括this)  
* [this]：表示值传递方式捕捉当前的this指针  

注意：

1. 父作用域指包含lambda函数的语句块
2. 语法上捕捉列表可由多个捕捉项组成，并以逗号分割。例如比如：`[=, &a, &b]`：以引用传递的方式捕捉变量a和b，值传递方式捕捉其他所有变量。   
3. 在块作用域以外的lambda函数捕捉列表必须为空  
4. 在块作用域中的lambda函数仅能捕捉父作用域中局部变量，捕捉任何非此作用域或者非局部变量都
    会导致编译报错  
5. lambda表达式之间不能相互赋值，即使看起来类型相同。但是允许拷贝构造和赋值给函数指针。   

##### lambda表达式的本质

我们将lambda表达式看做一个函数，更准确一点是一个匿名函数。编译器是如何看待的呢？lambda表达式的本质其实是一个仿函数。

也就是类+operator()。写一个简单的lambda表达式，再写一个伪代码，简单描述一下编译器是如何翻译lambda表达式的。

```cpp
int x = 1, y = 2;
auto f = [](int a, int b) {
    return a + b;
};

//伪代码
class Sum
{
public:
    // ()运算符的重载
    int operator()(int a, int b)
    {
        return a + b;
    }
};

// 创建对象来调用
Sum sum;
sum(x, y);
```

---

#### 包装器

##### function包装器

function是一种函数包装器，也叫做适配器。它可以对可调用对象进行包装，C++中的function本质就是一个类模板，function的主要作用就是将不同类型的可调用对象变为同一种类，创建对象去使用。

```cpp
template <class T> function;     // undefined

template <class Ret, class... Args> 
class function<Ret(Args...)>;		//Ret表示返回值类型，(Args...)是参数类型，...表示是可变参数
```

function可以对可调用对象进行包装， 例如函数指针，lambda表达式，函数对象(仿函数)等。

为啥要引入包装器？

```cpp
// 看下面的代码
auto ret = func(x);
// 对于func，可能是哪些类型呢？函数名，函数指针，函数对象(仿函数)也有可能是lambda表达式的对象。这些都是可调用的类型


// 模板
template<class F, class T>
T use(F f, T t)
{
	return f(t);	// f是可调用对象，可能存在多种调用
}

int main()
{
    cout<<use(f, 10)<<endl;				// 函数调用
    cout<<use(functor(), 10)<< endl;	// 仿函数对象调用
    cout<<use([](int x){return x}, 10);	// lambda表达式调用
}
```

对于上面这三个调用，use函数还是模板函数，就会产生三份函数代码，是一种浪费，所以就需要包装器来改变。如果是传入包装器就是传入一种类型的对象，就只会创建一种函数。

```cpp
// function example

// a function:
int half(int x) 
{
	return x / 2;
}

// a function object class:
struct third_t {
	int operator()(int x)
	{
		return x / 3;
	}
};

// a class with data members:
struct MyValue {
	int value;

	// statci memnber function
	int static f(int x)
	{
		std::cout << x << std::endl;
		return 0;
	}
	// non-static member function
	int fifth()
	{
		return value / 5; 
	}
};

int main() {
	std::function<int(int)> fn1 = half;							// function
	std::function<int(int)> fn2 = &half;						// function pointer
	std::function<int(int)> fn3 = third_t();					// function object
	std::function<int(int)> fn4 = [](int x) {return x / 4; };	// lambda expression
	std::function<int(int)> fn5 = std::negate<int>();			// standard function object

	std::cout << "fn1(60): " << fn1(60) << '\n';
	std::cout << "fn2(60): " << fn2(60) << '\n';
	std::cout << "fn3(60): " << fn3(60) << '\n';
	std::cout << "fn4(60): " << fn4(60) << '\n';
	std::cout << "fn5(60): " << fn5(60) << '\n';

	// stuff with members:
	// 非静态成员函数需要&， 并且需要在参数中传递一个对象，去调用成员函数，类型可以是MyValue,也可以是MyValue&，深拷贝可以
	std::function<int(MyValue&)> value = &MyValue::value;  // pointer to data member		传引用
	std::function<int(MyValue&)> fifth = &MyValue::fifth;  // pointer to non-static  member function
	// 静态成员函数可以不需要取地址，因为没有this指针，所以也不需要传递类名
	std::function<int(int)> f = MyValue::f;  // static member function 

	MyValue sixty{ 60 };
	// 非静态的成员函数使用function包装器时需要传递一个对象，去调用成员函数
	std::cout << "value(sixty): " << value(sixty) << '\n';
	std::cout << "fifth(sixty): " << fifth(sixty) << '\n';

	return 0;
}
```

---

##### bind包装器

![image-20221019163051661](http://shixiaozhong.oss-cn-hangzhou.aliyuncs.com/img/image-20221019163051661.png)

bind也是一个函数包装器，是一个函数模板，生成一个新的可调用对象来“适应”原对象的参数列表。可以用来将用户提供的需要一个参数的函数转换成不需要参数的函数对象。绑定的值存储在函数对象内并且会被自动传递给指定的函数，bind包装器主要起到一个绑定的作用。

```cpp
class F
{
public:
	int f(int x, int y)
	{
		return x + y;
	}
};

int main()
{
	// 包装成员函数， 未采用bind绑定
	function<int(F, int, int)> func1 = &F::f;
	cout << func1(F(), 1, 2) << endl;
	
    // 绑定匿名对象
	function<int(int, int)> func2 = bind(&F::f, F(), placeholders::_1, placeholders::_2);
	cout << func2(1, 3) << endl;	// 4
	
    // 绑定参数,绑定匿名对象，且第二个参数绑定为10
	function<int(int)> func3 = bind(&F::f, F(), placeholders::_1, 10);
	cout << func3(1) << endl;	// 11
	
    // 绑定交换参数,第一个参数被placeholders::_2接收，作为函数中的第二个参数
    function<int(F, int, int)> func4 = bind(&F::f, placeholders::_1, placeholders::_2,      placeholders::_3);
	cout << func4(F(), 10, 5) << endl; //15  在函数中计算的是5 + 10，不是10 + 5， 如果是交换的话体现更明显
}
```

