Effective c++ 改善程序与设计

std::size_t 只是一个typedef 是c++计算个数（STL容器内元素个数等）时用的 unsigned 类型，同时也是vector,deque,string内operator []函数接受的参数类型。

别看做什么神奇的东西



c++对签名式的官方定义是不包含函数的返回类型，但是Effective c++ 看成一部分



definition 对对象 定义式是编译器为此对象拨发内存的地点

​					对函数或函数模板，定义式提供的代码本体

​					对class或类模板 ，定义式列出成员



被声明为explicit,这可阻止被用来执行隐式类型转换（implicit type conversions），但它们仍可被用来进行显式类型转换 （explicit type conversions）:

```c++
class B {
public:
	explicit B(int x = 0, bool b = true); //default构造函数；
}; 

class C {
public:
	explicit C（int x）; //不是 default构造函数
}；

void doSomething （B bObject）; //函数，接受一个类型为B的对象
B bObj1; //一个类型为B的对象
doSomething(bObj1); //没问题，传递一个B给doSomething函数
B bObj2(28); 	//没问题，根据int28建立一个B
				// （函数的bool参数缺省为true）
doSomething(28); 	//错误! DoSomething应该接受一个B,
					//不是一个int,而int至B之间
					//并没有隐式转换。
doSomething(B(28)); //没问题，使用B构造函数将int显式转换
					// （也就是转型，cast）为一个B以促成此一调用.
					// （条款27对转型谈得更多）

```

被声明为explicit的构造函数通常比其non-explicit兄弟更受欢迎，因为它们禁止编译器执行非预期（往往也不被期望）的类型转换。除非我有一个好理由允许构造函数被用于隐式类型转换，否则我会把它声明为explicit.



copy构造函数被用来“以同型对象初始化自我对象”，copy assignment操作符(=)被用来“从另一个同型对象中拷贝其值到自我对象”：

```C++
class Widget {
public:
Widget(); //default构造函数
Widget(const Widget& rhs);//copy 构造函数
Widget& operator=(const Widget& rhs); //copy assignment

};
Widget w1; //调用default构造函数
Widget w2(w1);//copy 构造函数
w1 = w2;//copy assignment

```

当你看到赋值符号时请小心，因为语法也可用来调用copy构造函数:
`Widget w3 = w2;` //调用 copy构造函数！

如果一个新对象被定义(例如以上语句中的w3),—定会有个**构造函数**被调用，不可能调用赋值操作。

如果没有新对象被定义(例如前述的wl = w2语句)，就不会有构造函数被调用，那么当然就是赋值操作被调用。



```c++
bool hasAcceptableQuality(Widget w);
Widget aWidget;
if (hasAcceptableQuality(aWidget))...

```

参数w是以by value方式传递给hasAcceptableQuality,所以在上述调用中aWidget被复制到w体内。这个复制动作由Widget的copy构造函数完成。意味“调用copy构造函数”。以by value传递用户自定义类型通常是个坏主意，Pass-by-reference-to-const往往是比较好的选择；C-like 类型pass by value 通常高效些。

你就是你自己的客户

lhs和rhs它们分别代表"left-hand side"(左手端)和”right-hand side'* (右手端)。
我常常以它们作为二元操作符(binary operators)函数如operator==和operator*的参数名称。



这些条款拿来背一背也不是什么非常大的问题

## **条款01:视C++为一个语言联邦**

今天的C++己经是个多重范型编程语言(multiparadigm programming
language),一个同时支持过程形式(procedural)、面向对象形式(object-oriented)、
函数形式(fiinctional)、泛型形式(generic)、元编程形式(metaprogramming)的语言。

## **条款 02:尽量以 const, enum, inline 替换 #define**

这通俗来讲宁可以编译器替换预处理器

请记住
■对于单纯常量，最好以const对象或enums替换#defineso
■对于形似函数的宏（macros），最好改用inline函数替换#defines。



宏看起来像函数，但不会招致函数调用(function call)带来的额外开销

//以a和b的较大值调用f
#define CALL_WITH_MAX(a, b) f((a) > (b) ? (a) : (b))

无论何时当你写出这种宏，你必须记住为宏中的所有实参加上小括号

还记得通讯录的项目的时候就是这中宏在 * ->等情况下调用没有括号而出了大问题



## 条款03:尽可能使用 const

```c++
char greeting[] = "hello";
const char* p = greeting; //const data
char* const p = greeting;	//const pointer
```



为了真正去理解，必须要有一定的阅读方法

**总结：如果关键字const出现在星号左边，表示被指物是常量；如果出现在星号右边，表示指针自身是常量；**

```c++
void fl（const Widget* pw）;
void f2（Widget const * pw）; //这两个是等效的
```





`int *&r = p; // r是一个对指针p的引用`

最简单的办法是从右向左阅读r的定义。离变量名最近的符
号(此例中是&r的符号&)对变量的类型有最直接的影响，因此r是一个引用。声明符的
其余部分用以确定r引用的类型是什么，此例中的符号*说明r引用的是一个指针。最后， 声明的基本数据类型部分指出r引用的是一个int指针。



```c++
int i = 42;
const int &r1 = i; //允许将const int&绑定到一个普通int对象上
```

所谓指向常量的指针或引用，不过是指针或引用“自以为是”罢了，它们觉得自己指向了常量，所以自觉地不去改变所指对象的值。



指针是对象而引用不是，因此就像其他对象类型一样，允许把指针本身定为常量。常
量指针（const pointer）必须初始化，而且一旦初始化完成，则它的值（也就是存放在指针
中的那个地址）就不能再改变了。**把*放在const关键字之前用以说明指针是一个常量**， 这样的书写形式隐含着一层意味，即不变的是指针本身的值而非指向的那个值：
int errNumb = 0;

`const double * const pip =   & pi ;` // pip是一个指向常量对象的常量指针

**用名词顶层const(top-level const)表示指针本身是个常量，而用名词底层const (low-level const)表示指针所指的对象是一个常量。**



STL迭代器系以指针为根据塑模出来，所以迭代器的作用就像个T*指针。



```c++
std::vector<int> vec;
const std::vector<int>::iterator iter =vec.begin();
			//iter的作用像个T* const
*iter = 10; //没问题，改变iter所指物
++iter;		//错误！ iter 是 const
std::vector<int>::const_iterator cIter =vec.begin();

//cIter的作用像个const T*
*cIter = 10;	//错误! *cIter 是 const
++cIter;		//没问题，改变cIter

```

令函数返回一个常量值，往往可以降低因客户错误而造成的意外，而又不至于
放弃安全性和髙效性。举个例子，考虑有理数的operator* 声明式：

```c++
class Rational { ... };
const Rational operator* （const Rational& lhs, const Rational& rhs）;

if (a * b = c){...} //喔欧，其实是想做一个比较(comparison==)动作！
//将operator*的回传值声明为const可以预防那个“没意思的赋值动作”

```



这个例子很有意思

```c++
class CTextBlock {
public:
char& operator[] （std::size_t position） const //bitwise const 声明，
{ return pText [position]; } 
private:
char* pText;
}；

```

operator[]实现代码并不更改pText.但是返回一个reference指向对象内部值，也就可以更改pText

```c++
const CTextBlock cctb ("Hello"); //声明一个常量对象
char* pc = &cctb[0];
*pc ='J'; //cctb现在有了 "Jello”这样的内容。
```





## **条款04:确定对象被使用前已先被初始化**

#### 构造函数的一个较佳写法是，使用所谓的member initialization list (成员初值列)替换赋值动作： 

```c++
class PhoneNumber { ... };
class ABEntry { //ABEntry =Address Book Entry

public:
ABEntry(const std::string& name, const std::string& address,
const std::list<PhoneNumber>& phones);
private:
std::string theName;
std::string theAddress;
std::list<PhoneNumber> thePhones;
int numTimesConsulted;
}； 

ABEntry: : ABEntry (const std:: string& name, const std:: string& address,
const std::list<PhoneNumber>& phones)
{
theName = name; //这些都是赋值(assignments)，
theAddress = address; //而不是始化(initializations)。
thePhones = phones;
numTimesConsulted = 0;
}
```

基于赋值的那个首先调用default构造函数为theName, theAddress和thePhones设初值，然后立刻再对它们赋予新值

对大多数类型而言，比起先调用也句w/t构造函数然后再调用copy assignment
操作符，单只调用一次copy构造函数是比较高效的，有时甚至高效得多。

同样道理，甚至当你想要也句构造一个成员变量， 你都可以使用成员初值列，只要指定无物(nothing)作为初始化实参即可。假设ABEntry有一个无参数构造函数，我们可将它实现如下：

```C++
ABEntry::ABEntry()
:theName(),
theAddress(),
thePhones(),
numTimesConsulted(0)
{ }
//调用theName的default构造函数； 为theAddress做类似动作； 为thePhones做类似动作；
//记得将numTimesConsulted显式初始化为0

```

如果成员变量在初始化列表中没有被指定初值的话，编译器会为用户自定义类型的成员变量自动调用default构造函数

但是**内置内型不会**,可是总之用成员列表，如果成员变量是const或references，它们就一定需要初值，不能被赋值

**C++有着十分固定的“成员初始化次序”。是的，次序总是相同：base classes更早于其derived classes被初始化，而class的成员变量总是以其 *声明* 次序被初始化。**

如果两个成员变量的初始化带有次序性。例如初始化array时需要指定大小，因此代表大小的那个成员变量必须先有初值。



#### C++对"定义于不同的编译单元内的non-local static对象”的初始化相对次序并无明确定义。




所谓static对象，其寿命从被构造出来直到程序结束为止，因此stack和heap-based对象都被排除。这种对象包括global对象、定义于namespace作用域内的对象、在classes内、在函数内、以及在file作用域内被声明为static的对象。函数内的static对象称为local static对象(因为它们对函数而言是local),其他static对象称为non-local static对象。程序结束时static对象会被自动销毁，也就是他们的析构函数会在main（）结束的时候自动调用。


所谓编译单元(translation unit)是指产出单一目标文件(single object file）的那些源码。基本上它是单一源码文件加上其所含入的头文件(#include files)。

现在，我们关心的问题涉及至少两个源码文件，每一个内含至少一个non-local static对象(也就是说该对象是global或位于namespace作用域内，抑或在class内或file作用域内被声明为static)。真正的问题是：如果某编译单元内的某个non-local static对象的初始化动作使用了另一编译单元内的某个non-local static对象，它所用到的这个对象可能尚未被初始化，因为C++对“定义于不同编译单元
内的non-local static对象”的初始化次序并无明确定义。



这是有原因的：决定它们的初始化次序相当困难，非常困难，根本无解。在其最常见形式，也就是多个编译单元内的non-localstatic对象经由“模板隐式具现化，implicit template instantiations”形成(而后者自己可能也是经由“模板隐式具现化”形成)，？？？？

不太懂这里模板隐式具现化什么意思，反正就是决定不了次序的意思呗。

那怎么办呢，用单例模式解决这个问题。

将每个non-local static对象搬到自己的专属函数内(该对象在此函数内被声明为static)。
这些函数返回一个reference指向它所含的对象。然后用户调用这些函数，而不直接
指涉这些对象。换句话说，non-local static对象被local static对象替换了。Design
Patterns迷哥迷姊们想必认出来了，这是**Singleton模式**的一个常见实现手法。

这个手法的基础在于：C++保证，函数内的local static对象会在“该函数被调
用期间” “首次遇上该对象之定义式”时被初始化。所以如果你以“函数调用”(返
回一个reference指向local static对象)替换“直接访问non-local static对象”
，你就获得了保证，保证你所获得的那个reference将指向一个历经初始化的对象

**更棒的是，如果你从未调用non-local static对象的“仿真函数”
，就绝不会引发构造和析构成本；真正的non-local static对象可没这等便宜！**

不用白不用



总结：

请记住
■为内置型对象进行手工初始化，因为C++不保证初始化它们。
■构造函数最好使用成员初值列(member initialization list)，而不要在构造函数
本体内使用赋值操作(assignment)。初值列列出的成员变量，其排列次序应该
和它们在class中的声明次序相同。
■为免除“跨编译单元之初始化次序”问题，请以1ocal static对象替换non-local
static 对象。