STL源码剖析



深度探索list



前置知识

操作符重载

++ 运算符，还可分为前缀 ++ 和后缀 ++ 运算符。 重载前缀++运算符 C++ 允许重载前缀运算符，以使表达式 ++b 能递增 b 的长度值，并返回结果对象。该运算符可以作为成员函数来重载，这使得它的单个形参是隐含的， 所以重载运算符不需要形参。

以下是Length类的一部分，它显示了 ++ 运算符的声明：



```c++
class Length
{
    private:
        int len_inches;
    public:
        //前缀++的声明
        Length operator++ ();
        //类的其余部分，省略
};
```

以下是运算符的实现语句，它将 len_inches 增加 1 并返回修改的对象：

```c++
Length Length::operator++()
{
    len_inches ++;
    return *this;
}
```

给定该重载之后，就可以使用很容易理解的 ++b 表达式了，它与 b.operator++() 是完全等效的。在程序中可以使用这两种表示法中的任何一种。 重载后缀++运算符 后缀递增运算符 b++ 也可以递增b的长度，但与前缀版本不同，**因为它返回对象在增加之前的值**。重载后缀递增运算符与重载前缀版本稍有不同。

以下是为 Length 类重载后缀递增运算符的函数：

```c++
Length Length::operator++(int)
{
    Length temp = *this;
    len_inches ++;
    return temp;
}
```

可以发现，第一个区别是该函数**有一个 int 类型的虚拟形参，这个形参在函数的主体中是不会被使用的，这只是一个约定，它告诉编译器递增运算符正在后缀模式下被重载**。**第二个区别是使用临时局部变量 temp 来获取递增之前对象的值。该值将被保存并在稍后由函数返回。**







iterator

需要用到很多操作符的重载

但是还是有没有理到精髓的东西

```c++
reference operator*() const//reference represents T& ,T is template
{
	return (*node).data;
}	

pointer operator->() const
{return &(operator*());}
//这里真心有一些混乱

```





