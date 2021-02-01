Iterator Traits(机制，特性)

Iterator 需要遵循的原则

(初学一定是难于理解的，越是在这种时候就越是应该全身心的投入进去，付出时间足够了，脑子动起来了总是会达到一种相对较好的情况的)

```c++
iterator_category();//这是表示iterator需要遵守的规则，比如只能加，能+=大于一点数等
value_type //iterator 指向的数据类型
difference_type
//不同iterator之间的差距最多使用多少来表示,大部分的实现用ptrdiff_t(一般就够了，否crach)
```





算法Algorithms 提问然后Iterator 回答（每个iterator需要5个，但是有2个没有用，写源码的人想多了）

```c++
template<typename I>
inline void
algorithm(I first,I last){
    //省略内容
    I::iterator_category//1
    I::pointer//2 没用
    I::reference//3 没用
    I::value_type//4
    I::difference_type//5
}
```



```c++
template<class T,class Ref,class Ptr>
Struct __list_iterator{
    typedef bidirectional_iterator_tag
        iterator_category;//1 这里是双向的bidirec
    typedef T value_type;//2
    typedef Ptr pointer;//3
    typedef Ref reference;//4
    typedef ptrdiff_t difference_type;//5
}
```



但这种都是基于iterator是一个class的时候才能用

但native pointer（被视为一种退化的iterator）也可以作为迭代器



iterator traits 用于分离 class iterators 和non-class iterator

**解决计算机问题的尚方宝剑：加一个中介层。**

iterator traits **萃取机**



```c++
template<typename I,...>
void algorithm(...){
    typename iterator_traits<I>::value_type v1;
    //用iterator_traits间接去问
}

template<class I>
struct iterator_traits{
	typedef typename I::value_type value_type;
    //iterator_traits又去问I
}

//采用偏特化的办法分离出来
//traits 怎么知道传进来一个指针T*之后是什么类型的value_type呢？
//那value_type就是T咯，问题简单的解决了non-class iterator的问题
template <class T>
struct iterator_traits<T*>{
    typedef T value_type;
};

template <class T>
struct iterator_traits<const T*>{
    typedef T value_type;
};

//但收到是const *的时候返回还是T 是为什么呢？const 没有用啊
```







