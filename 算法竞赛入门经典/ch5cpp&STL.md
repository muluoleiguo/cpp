C＋＋是一门庞大的语言，大多数语言特性和库函数在算法竞赛中都是用不到（或者可以避开）的。而且算法竞赛有它自身的特点，即使对于资深C＋＋程序员来说，如果缺乏算法竞赛的经验，也很难总结出一套适用于算法竞赛的知识点和实践指南。因此，即使你已经很熟悉C＋＋语言，但笔者仍建议花一些时间浏览本章的内容，相信会有新的收获。

## 5.1 从C到C＋＋

C语言是一门很有用的语言，但在算法竞赛中却不流行，原因在于它太底层，缺少一些“实用的东西”。



### 5.1.1 C＋＋版框架

stdio.h仍然存在，但是C＋＋中推荐的头文件是cstdio。类似地，string.h变成了cstring，math.h变成了cmath，ctype.h变成了cctype。带.h后缀的头文件依然存在，但并不被C＋＋所推荐使用。

```c++
#include<iostream>
#include<algorithm>
using namespace std;
const int maxn = 100 + 10;
int A[maxn];
int main() {
long long a, b;
while(cin >> a << b) {
cout << min(a,b) << "\n";
}
return 0;
}
```

当然，C＋＋流也不是完美的，其最大缺点就是运行太慢，以至于很多竞赛题目会在题面中的显著位置注明：本题的输入量很大，请不要使用C＋＋的流输入。

声明数组时，数组大小可以使用const声明的常数（这在C99中是不允许的）。在C＋＋中，这种写法更为推荐，因此本书后面的代码中一律采用这样的写法，而不是用#define声明常数。

C＋＋中的数据类型和C语言很接近，最显著的区别是多了一个bool来表示布尔值

### 5.1.2 引用

如果在参数名之前加一个“&”符号，就表示这个参数按照传引用（by reference）的方式传递，而不是C语言里的传值（by value）方式传递。这样，在函数内改变参数的值，也会修改到函数的实参。

提示5-4：C＋＋中的引用就是变量的“别名”，它可以在一定程度上代替C中的指针。例如，可以用“传引用”的方式让函数内直接修改实参。

### 5.1.3 字符串

string类型往往是更好的选择。例如，C＋＋的cin/cout可以直接读写string类型，却不能读写字符数组；string类型还可以像整数那样“相加”，而在C语言里只能使用strcat函数。string有很多方便的函数和运算符，但速度有些慢。

考虑这样一个题目：输入数据的每行包含若干个（至少一个）以空格隔开的整数，输出每行中所有整数之和。如果只能使用字符与字符数组，一般有两种方案：一是使用getchar( )边读边算，代码较短，但容易写错，并且相对较难理解；二是每次读取一行，然后再扫描该行的字符，同时计算结果。如果使用C＋＋，代码可以很简单。

```c++
#include<iostream>
#include<string>
#include<sstream>
using namespace std;
int main() {
string line;
while(getline(cin, line)) {
int sum = 0, x;
stringstream ss(line);
while(ss >> x) sum += x;
cout << sum << "\n";
}
return 0;

```

string类在string头文件中，而stringstream在sstream头文件中。首先用getline函数读一行数据（相当于C语言中的fgets，但由于使用string类，无须指定字符串的最大长度），然后用这一行创建一个“字符串流”——ss。接下来只需像读取cin那样读取ss即可。

提示5-6：可以把string作为流进行读写，定义在sstream头文件中。
虽然string和sstream都很方便，但string很慢，sstream更慢，应谨慎使用

### 5.1.4 再谈结构体

**提示5-7**：C＋＋中的结构体除了可以拥有成员变量（用a.x的方式访问）之外，还可以拥有成员函数（用a.add（1，2）的方式访问）。为了简单起见，本书中只使用struct而不使用class，但struct的很多概念和写法同样适用于class。

**提示5-8**：C＋＋中的结构体可以有一个或多个构造函数，在声明变量时调用。
**提示5-9**：C＋＋中的函数（不只是构造函数）参数可以拥有默认值。
**提示5-10**：在C＋＋结构体的成员函数中，this是指向当前对象的指针。

```c++
ostream& operator << (ostream &out, const Point& p) {
out << "(" << p.x << "," << p.y << ")";
return out;
}
//定义结构体的流输出方式
```

### 5.1.5 模板

```c++
template<typename T>
T sum(T* begin, T* end) {
T *p = begin;
T ans = 0;
for(T *p = begin; p != end; p++)
ans = ans + *p; //注意写成+=需要重载操作符
return ans;
}

```

```c++
template <typename T>
struct Point {
T x, y;
Point(T x=0, T y=0):x(x),y(y) {}
};
//然后把“+”和“<<”的代码也稍加改变：
template <typename T>
Point<T> operator + (const Point<T>& A, const Point<T>& B) {
return Point<T>(A.x+B.x, A.y+B.y);
}
template <typename T>
ostream& operator << (ostream &out, const Point<T>& p) {
out << "(" << p.x << "," << p.y << ")";
return out;
}
```



## 5.2 STL初步

STL是指C＋＋的标准模板库（Standard Template Library）

### 5.2.1 排序与检索

**例题5-1** 大理石在哪儿（Where is the Marble？，Uva 10474）

现有N个大理石，每个大理石上写了一个非负整数。首先把各数从小到大排序，然后回答Q个问题。每个问题问是否有一个大理石写着某个整数x，如果是，还要回答哪个大理石上写着x。排序后的大理石从左到右编号为1～N。

样例输入：
4 1 

2 

3 

5 

1 

5 

5 2 

1 

3 

3 

3 

1 

2 

3 

0 0
样例输出：
CASE #1:
5 found at 4
CASE #2:
2 not found

3 found at 3

题目意思已经很清楚了：先排序，再查找。使用algorithm头文件中的sort和lower_bound很容易完成这两项操作，代码如下：

```c++
#include<cstdio>
#include<algorithm>
using namespace std;
const int maxn =10000;
int main(){
    int n,q,x,a[maxn],kase=0;
    while(scanf("%d%d",&n,&q)==2&&n){
        printf("CASE# %d:\n",++kase);
        for(int i=0;i<n;i++) scanf("%d",&a[i]);
        sort(a,a+n);
        while(q--){
			scanf("%d",&x);
        	int p = lower_bound(a,a+n,x)-a;//在已排序数组a中寻找x
            if(a[p]==x) printf("%d found at %d\n",x,p+1);
            else printf("%d not found\n",x);
        }
    }
    return 0;
}
```

sort使用数组元素默认的大小比较运算符进行排序，只有在需要按照特殊依据进行排序时才需要传入额外的比较函数。



提示5-11：algorithm头文件中的sort可以给任意对象排序，包括内置类型和自定义类型，前提是类型定义了“<”运算符。排序之后可以用lower_bound查找大于或等于x的第一个位置。待排序／查找的元素可以放在数组里，也可以放在vector里。

还有一个unique函数可以**删除有序数组中的重复元素**，

### 5.2.2 不定长数组：vector

vector就是一个不定长数组。不仅如此，它把一些常用操作“封装”在了vector类型内部。例如，若a是一个vector，可以用a.size( )读取它的大小，a.resize( )改变大小，a.push_back( )向尾部添加元素，a.pop_back( )删除最后一个元素。vector是一个模板类，所以需要用vector<int>a或者vector<double>b这样的方式来声明一个vector。Vector<int>是一个类似于inta[]的整数数组，而vector<string>就是一个类似于string a[ ]的字符串数组。vector看上去像是“一等公民”，因为它们可以直接赋值，还可以作为函数的参数或者返回值，而无须像传递数组那样另外用一个变量指定元素个数。

**例题5-2** 木块问题（The Blocks Problem，Uva 101）

从左到右有n个木块，编号为0～n-1，要求模拟以下4种操作（下面的a和b都是木块编号）。

* move a onto b：把a和b上方的木块全部归位，然后把a摞在b上面。
* move a over b：把a上方的木块全部归位，然后把a放在b所在木块堆的顶部。
* pile a onto b：把b上方的木块全部归位，然后把a及上面的木块整体摞在b上面。
* pile a over b：把a及上面的木块整体摞在b所在木块堆的顶部。
  遇到quit时终止一组数据。a和b在同一堆的指令是非法指令，应当忽略。

Sample Input
10
move 9 onto 1
move 8 over 1
move 7 over 1
move 6 over 1
pile 8 over 6
pile 8 over 5
move 2 over 1
move 4 over 9
quit
Sample Output
0: 0
1: 1 9 2 4
2:
3: 3
4:
5: 5 8 7 6
6:
7:
8:
9:

解：每个木块堆的高度不确定，所以用vector来保存很合适；而木块堆的个数不超过n，所以用一个数组来存就可以了。代码如下：

```c++
#include<cstdio>
#include<string>
#include<vector>
#include<iostream>
using namespace std;
const int maxn = 30;
int n;
vector<int> pile[maxn]; //每个pile[i]是一个vector
//找木块a所在的pile和height，以引用的形式返回调用者
void find_block(int a,int &p,int& h){
    for(p=0;p<n;p++)
        for(h=0;h<pile[p].size();h++)
            if(pile[p][h]==a) return;
}

//把第p堆高度为h的木块上方所有木块移动回原位
void clear_above(int p,int h){
    for(int i=h+1;i<pile[p].size();i++){
		int b=pile[p][i];
        pile[b].push_back(b); //把木块b放回原位
        
    }
    pile[p].resize(h+1);//pile只应保留下标0-h的元素
}

//把第p堆高度为h及其上方的木块整体移动到p2 堆的顶部
void pile_onto(int p,int h,int p2){
    for(int i=h;i<pile[p].size();i++)
        pile[p2].push_back(pile[p][i]);
    pile[p].resize(h);
}

void print() {
	for(int i = 0; i < n; i++) {
		printf("%d:", i);
		for(int j = 0; j < pile[i].size(); j++) printf(" %d", pile[i][j]);
			printf("\n");
	}
}

int main(){
    int a,b;
    cin>>n;
    string s1,s2;
    for(int i=0;i<n;i++) pile[i].push_back(i);
    while(cin>>s1){
        if(s1=="quit") break;
        cin>>a>>s2>>b;
		int pa,pb,ha,hb;
		find_block(a,pa,ha);
        find_block(b,pb,hb);
        if(pa==pb) continue;
        if(s2=="onto") clear_above(pb,hb);
        if(s1=="move") clear_above(pa,ha);
        pile_onto(pa,ha,pb);
    }
    print();
    return 0;
}
```

数据结构的核心是vector<int>pile[maxn]，所有操作都是围绕它进行的。vector就像一个二维数组，只是第一维的大小是固定的（不超过maxn），但第二维的大小不固定。上述代码还有一个值得学习的技巧：输入一共有4种指令，但如果完全独立地处理各指令，代码就会变得冗长而且易错。更好的方法是提取出指令之间的共同点，编写函数以减少重复代码。

提示5-12：vector头文件中的vector是一个不定长数组，可以用clear( )清空，resize( )改变大小，用push_back( )和pop_back( )在尾部添加和删除元素，用empty( )测试是否为空。vector之间可以直接赋值或者作为函数的返回值，像是“一等公民”一样。



### 5.2.3 集合：set

集合与映射也是两个常用的容器。set就是数学上的集合——每个元素最多只出现一次。和sort一样，自定义类型也可以构造set，但同样必须定义“小于”运算符。

**例题5-3** 安迪的第一个字典（Andy's First Dictionary，Uva 10815）

输入一个文本，找出所有不同的单词（连续的字母序列），按**字典序**从小到大输出。单词不区分大小写。

Sample Input
Adventures in Disneyland
Two blondes were going to Disneyland when they came to a fork in the
road. The sign read: "Disneyland Left."
So they went home.
Sample Output
a
adventures
blondes
came
disneyland
fork
going
home
in
left
read
road
sign
so
the
they
to
two
went
were
when



解析:本题没有太多的技巧，只是为了展示set的用法：由于string已经定义了“小于”运算符，直接使用set保存单词集合即可。**注意，输入时把所有非字母的字符变成空格**，然后利用stringstream得到各个单词。

```c++
#include<iostream>
#include<string>
#include<set>
#include<sstream>
using namespace std;
set<string> dict; //string 集合
int main() {
	string s, buf;
	while(cin >> s) {
		for(int i = 0; i < s.length(); i++)
			if(isalpha(s[i])) s[i] = tolower(s[i]); 
        	else s[i] = ' ';
        stringstream ss(s);
		while(ss >> buf) dict.insert(buf);
	}
	for(set<string>::iterator it = dict.begin(); it != dict.end(); ++it)
	cout << *it << "\n";
	return 0;
}
```

<sstream> 定义了三个类：istringstream、ostringstream 和 stringstream，分别用来进行流的输入、输出和输入输出操作。

<sstream> 主要用来进行数据类型转换，由于 <sstream> 使用 string 对象来代替字符数组（snprintf方式），就避免缓冲区溢出的危险；而且，因为传入参数和目标对象的类型会被自动推导出来，所以不存在错误的格式化符的问题。简单说，相比c库的数据类型转换而言，<sstream> 更加安全、自动和直接。

上面的代码用到了set中元素**已从小到大排好序**这一性质，用一个for循环即可从小到大遍历所有元素。

谢谢侯捷老师

### 5.2.4 映射：map

map就是从键（key）到值（value）的映射。因为重载了[ ]运算符，map像是数组的“高级版”。例如可以用一个map<string，int>month_name来表示“月份名字到月份编号”的映射，然后用month_name["July"]=7这样的方式来赋值

**例题5-4** 反片语（Ananagrams，Uva 156）

输入一些单词，找出所有满足如下条件的单词：该单词不能通过字母重排，得到输入文本中的另外一个单词。在判断是否满足条件时，字母不分大小写，但在输出时应保留输入中的大小写，按字典序进行排列（所有大写字母在所有小写字母的前面）。

Sample Input
ladder came tape soon **leader** acme RIDE lone Dreis peat
ScAlE orb eye Rides **dealer** NotE derail LaCeS drIed
noel dire Disk mace Rob dries
#
Sample Output
Disk
NotE
derail
drIed
eye
ladder
soon

解析：把每个单词“标准化”，即全部转化为小写字母后再进行排序，然后再放到map中进行统计。代码如下：

```c++
#include<iostream>
#include<string>
#include<cctype>
#include<vector>
#include<map>
#include<algorithm>
using namespace std;
map<string,int> cnt;
vector<string> words;
//将单词s进行标准化
string repr(const string&s){
    string ans=s;
    for(int i=0;i<ans.length();i++)
        ans[i]=tolower(ans[i]);
    sort(ans.begin(),ans.end());
    return ans;
}
int main(){
    int n=0;
    string s;
    while(cin>>s){
        if(s[0]=='#') break;
        words.push_back(s);
        string r=repr(s);
        if(!cnt.count(r)) cnt[r]=0;
        cnt[r]++;
    }
    vector<string> ans;
    for(int i=0;i<words.size();i++)
        if(cnt[repr(words[i])]==1) ans.push_back(words[i]);
    sort(ans.begin(),ans.end());
    for(int i=0;i<ans.size();i++)
        cout<<ans[i]<<"\n";
    return 0;
}
```

map.count(Key)返回值为1或者0，1返回存在，0返回不存在，返回的是布尔类型的值，因为在map类型中所有的数据的Key值都是不同的，所以被count的数要么存在1次，要么不存在

**提示5-13**：set头文件中的set和map头文件中的map分别是集合与映射。二者都支持insert、find、count和remove操作，并且可以按照从小到大的顺序循环遍历其中的元素。map还提供了“[]”运算符，使得map可以像数组一样使用。事实上，map也称为“关联数组“



### 5.2.5 栈、队列与优先队列

STL还提供3种特殊的数据结构：栈、队列与优先队列。所谓栈，就是符合“后进先出”（Last In First Out，LIFO）规则的数据结构，有PUSH和POP两种操作，其中PUSH把元素压入“栈顶”，而POP从栈顶把元素“弹出”，如图5-1所示。

提示5-14：STL的stack头文件提供了栈，用“stack<int>s”方式定义，用push( )和pop()实现元素的入栈和出栈操作，top( )取栈顶元素（但不删除）。

**例题5-5** 集合栈计算机（The Set Stack Computer，ACM/ICPC NWERC2006，UVa12096）

有一个专门为了集合运算而设计的“集合栈”计算机。该机器有一个初始为空的栈，并且支持以下操作。

* PUSH：空集“{}”入栈。
* DUP：把当前栈顶元素复制一份后再入栈。
* UNION：出栈两个集合，然后把二者的并集入栈。
* INTERSECT：出栈两个集合，然后把二者的交集入栈。
* ADD：出栈两个集合，然后把先出栈的集合加入到后出栈的集合中，把结果入栈。

每次操作后，输出栈顶集合的大小（即元素个数）。例如，栈顶元素是A={{}，{{}}}，下一个元素是B={{}，{{{}}}}，则：

* UNION操作将得到{{}，{{}}，{{{}}}}，输出3。
* INTERSECT操作将得到{{}}，输出1。
* ADD操作将得到{{}，{{{}}}，{{}，{{}}}}，输出3。

输入不超过2000个操作，并且保证操作均能顺利进行（不需要对空栈执行出栈操作）。After each test case there will be a line with ‘***’ (three asterisks).

```
Sample Input
2
9
PUSH
DUP
ADD
PUSH
ADD
DUP
ADD
DUP
UNION
5
PUSH
PUSH
ADD
PUSH
INTERSECT
Sample Output
0
0
1
0
1
1
2
2
2
***
0
0
1
0
0
***
```



解：本题的集合并不是简单的整数集合或者字符串集合，而是集合的集合。为了方便起见，此处为每个不同的集合分配一个唯一的ID，则每个集合都可以表示成所包含元素的ID集合，这样就可以用STL的set<int>来表示了，而整个栈则是一个stack<int>。

```c++
typedef set<int> Set;
map<Set,int> IDcache; //把集合映射成ID
vector<Set> Setcache; //根据ID 取集合
//查找给定集合x的ID。如果找不到，分配一个新ID
int ID (Set x) {
	if (IDcache.count(x)) return IDcache[x];
	Setcache.push_back(x); //添加新集合
	return IDcache[x] = Setcache.size() - 1;
}
//对任意集合s（类型是上面定义的Set），IDcache[s]就是它的ID，而Setcache[IDcache[s]]
//就是s本身。下面的ALL和INS是两个宏(10)：
#define ALL(x) x.begin(),x.end()
#define INS(x) inserter(x,x.begin())

```

```c++
stack<int> s; //题目中的栈
int n;
cin >> n;
for(int i = 0; i < n; i++) {
	string op;
	cin >> op;
	if (op[0] == 'P') s.push(ID(Set()));
	else if (op[0] == 'D') s.push(s.top());
	else {
	Set x1 = Setcache[s.top()]; s.pop();
	Set x2 = Setcache[s.top()]; s.pop();
	Set x;
	if (op[0] == 'U') set_union (ALL(x1), ALL(x2), INS(x));
	if (op[0] == 'I') set_intersection (ALL(x1), ALL(x2), INS(x));
	if (op[0] == 'A') { x = x2; x.insert(ID(x1)); }
	s.push(ID(x));
	}
	cout << Setcache[s.top()].size() << endl;
}

```

以下是STL algorithm的几个函数，使用的条件是有序容器，所以 vector在被sort了之后是可以使用的，set也是可以使用的。

set_difference 这个是求得在第一个容器中有，第二个容器中没有的。

set_intersection 求两个容器的交， set_union 求两个容器的并。

set_symmetric_difference 求两个容器的差。

**参数**：两个迭代器用来指定左操作数的集合范围，另两个迭代器用来作为右操作数的集合范围，还有一个迭代器用来指向结果集合的存放位置。



```c++
std::vector<int> set1 {1, 2, 3, 4, 5, 6};
std::vector<int> set2 {4, 5, 6, 7, 8, 9};
std::vector<int> result;
std::set_union(std::begin (set1), std::end(set1), //Range for set that is left operand
std::begin(set2), std::end(set2), // Range for set that is right operand
std::back_inserter(result));    // Destination for the result:1 2 3 4 5 6 7 8 9
```

set1 和 set2 中的初始值都是升序。如果它们都不是，那么在使用 set_union() 算法前，需要对 vector 容器排序。 back_inserter() 函数模板定义在 iterator 头文件中，它会调用传入参数的函数 push_back() 来返回一个 back_inserter_iterator 对象。所以，set1 和 set2 并集中的元素会被保存在 result 中。从并集运算得到的元素集合是容器元素的副本，因此这个运算并不会影响容器的原始内容。

当然，如果不保存运算结果；可以用一个流迭代器输出这些元素：

```c++
std::set_union(std::begin(set1), std::end(set1), std::begin(set2), std::end(set2),std::ostream_iterator<int> {std::cout, " "});
```

这里的目的地址是一个 ostream_iterator，它可以将结果输出到标准输出流中。

第二个版本的 set_union() 函数模板接收的第 6 个参数是一个用来比较集合元素的函数对象。下面是它可能的一些用法：

```c++
std:: set<int, std::greater<int>>set1 {1, 2, 3, 4, 5, 6}; // Contains 6 5 4 3 2 1
std:: set<int, std:: greater<int>>set2 {4, 5, 6, 7, 8, 9}; // Contains 9 8 7 6 5 4
std::set<int, std::greater<int>>result; // Elements in descending sequence
std::set_union(std::begin(set1), std::end(set1),std::begin(set2), std::end(set2),std::inserter(result, std::begin(result)), // Result destination: 9 8 7 6 5 4 3 2 1
std::greater<int>()); // Function object for comparing elements
```

这一次的集合是 set 容器中的元素。这些元素是用函数对象 greater<int> 排序过的，因此它们都是升序。set_union() 的最后一个参数是一个用来比较集合元素的 greater<int> 类型的实例。结果的存放位置是 result 容器的 inserter_iterator，容器会调用成员函数 insert() 来添加元素。不能对 set 容器使用 back_insert_iterator，因为它没有成员函数 push_back()。并集运算的结果是从两个集合得到的元素的副本的降序集合。

这两个版本的 set_union() 函数都会返回一个指向被复制元素段末尾后一个位置的迭代器。如果目的容器包含操作前的元素，这是很有用的。例如，如果目的容器是 vector 容器，set_union() 用 front_insert_iterator，如果用 back_inserter_iterator，可以用这个容器的结束迭代器，插入这个新元素，set_union() 返回的迭代器会指向第一个原始元素。





队列是符合“先进先出”（First In First Out，FIFO）原则的“公平队列”，

**提示5-15**：STL的queue头文件提供了队列，用“queue<int>s”方式定义，用push( )和pop( )进行元素的入队和出队操作，front( )取队首元素（但不删除）。

**例题5-6** 团体队列（Team Queue，UVa540）

有t个团队的人正在排一个长队。每次新来一个人时，如果他有队友在排队，那么这个新人会插队到最后一个队友的身后。如果没有任何一个队友排队，则他会排到长队的队尾。输入每个团队中所有队员的编号，要求支持如下3种指令（前两种指令可以穿插进行）。

* ENQUEUEx：编号为x的人进入长队。
* DEQUEUE：长队的队首出队。
* STOP：停止模拟。
  对于每个DEQUEUE指令，输出出队的人的编号。

Sample Input
2
3 101 102 103
3 201 202 203
ENQUEUE 101
ENQUEUE 201
ENQUEUE 102
ENQUEUE 202
ENQUEUE 103
ENQUEUE 203
DEQUEUE
DEQUEUE
DEQUEUE
DEQUEUE
DEQUEUE
DEQUEUE
STOP
2
5 259001 259002 259003 259004 259005
6 260001 260002 260003 260004 260005 260006
ENQUEUE 259001
ENQUEUE 260001
ENQUEUE 259002
ENQUEUE 259003
ENQUEUE 259004
ENQUEUE 259005
DEQUEUE
DEQUEUE
ENQUEUE 260002
ENQUEUE 260003
DEQUEUE
DEQUEUE
DEQUEUE
DEQUEUE
STOP
0
Sample Output
Scenario #1
101
102
103
201
202
203
Scenario #2
259001
259002
259003
259004
259005
260001



解析：本题有两个队列：每个团队有一个队列，而团队整体又形成一个队列。例如，有3个团队1，2，3，队员集合分别为{101，102，103，104}、{201，202}和{301，302，303}，当前长队为{301，303，103，101，102，201}，则3个团队的队列分别为{103，101，102}、{201}和{301，303}，团队整体的队列为{3，1，2}。代码如下：

```c++
#include<cstdio>
#include<queue>
#include<map>
using namespace std;
const int maxt=1000+10;
int main(){
	int t,kase=0;
    while(scanf("%d",&t)==1&&t){
        printf("Scenario #%d\n",++kase);
        //记录所有人的团队编号
        map<int,int> team;	//team[x]表示编号为x的人所在的团队编号
        for(int i=0;i<t;i++){
            int n,x;
            scanf("%d",&n);
            while(n--) { scanf("%d",&x); team[x]=i;}
        }
        //模拟
        queue<int> q,q2[maxt]; //q是团队的队列，而q2[i]是团队i成员的队列
        for(;;){
			int x;
            char cmd[10];
            scanf("%s",cmd);
            if(cmd[0]=='S') break;
            else if(cmd[0]=='D'){
                int t=q.front();
                printf("%d\n",q2[t].front());
                q2[t].pop();
                if(q2[t].empty()) q.pop();//团队t全体出队列
            }
            else if(cmd[0]=='E'){
                scanf("%d",&x);
                int t=team[x];
                if(q2[t].empty()) q.push(t);//团队t进入队列
                q2[t].push(x);
            }
        }
        printf("\n");
    }
    return 0;
}
```

优先队列是一种抽象数据类型（Abstract Data Type，ADT），行为有些像队列，但先出队列的元素不是先进队列的元素，而是队列中优先级最高的元素，这样就可以允许类似于“急诊病人插队”这样的事情发生。
STL的优先队列也定义在头文件<queue>里，用“priority_queue<int>pq”来声明。这个pq是一个“越小的整数优先级越低的优先队列”。由于出队元素并不是最先进队的元素，出队的方法由queue的front( )变为了top( )。自定义类型也可以组成优先队列，但必须为每个元素定义一个优先级。这个优先级并不需要一个确定的数字，只需要能比较大小即可。看到这里，是不是想起了sort？没错，只要元素定义了“小于”运算符，就可以使用优先队列。在一些特殊的情况下，需要使用自定义方式比较优先级，例如，要实现一个“个位数大的整数优先级反而小”的优先队列，可以定义一个结构体cmp，重载“( )”运算符，使其“看上去”像一个函数，然后用“priority_queue<int,vector<int>，cmp>pq”的方式定义。下面是这个cmp的定义：

```c++
struct cmp {
bool operator() (const int a, const int b) const { //a 的优先级比b小时返回true
return a % 10 < b % 10;
}
};
```

对于一些常见的优先队列，STL提供了更为简单的定义方法，例如，“越小的整数优先级越大的优先队列”可以写成“priority_queue<int，vector<int>，greater<int>>pq”。注意，最后两个“>”符号不要写在一起，否则会被很多（但不是所有）编译器误认为是“>>”运算符。 现在无所谓了

**提示5-16**：STL的queue头文件提供了优先队列，用“priority_queue<int>s”方式定义，用push( )和pop( )进行元素的入队和出队操作，top( )取队首元素（但不删除）。



**例题5-7** 丑数（Ugly Numbers，Uva 136）

丑数是指不能被2，3，5以外的其他素数整除的数。把丑数从小到大排列起来，结果如下：
1,2,3,4,5,6,8,9,10,12,15,…
求第1500个丑数。

解析：本题的实现方法有很多种，这里仅提供一种，即从小到大生成各个丑数。最小的丑数是1，而对于任意丑数x，2x、3x和5x也都是丑数。这样，就可以用一个优先队列保存所有已生成的丑数，每次取出最小的丑数，生成3个新的丑数。唯一需要注意的是，同一个丑数有多种生成方式，所以需要判断一个丑数是否已经生成过。代码如下：

```c++
#include<iostream>
#include<vector>
#include<queue>
#include<set>
using namespace std;
typedef long long LL;
const int coeff[3]={2,3,5};

int main(){

    priority_queue<LL,vector<LL>,greater<LL>> pq;
    set<LL> s;
    pq.push(1);
    s.insert(1);
    for(int i=1;;i++){
		LL x=pq.top();
        pq.pop();
        if(i==1500){
            cout<<"The 1500'th ugly number is "<<x<<".\n";
            break;
        }
        for(int j=0;j<3;j++){
            LL x2=x*coff[j];
            if(!s.count(x2)){s.insert(x2); pq.push(x2);}
        }
    }
    return 0;
}
```

### 5.2.6 测试STL

为了随机生成整数，先来看看随机数发生器。核心函数是cstdlib中的rand( )，它生成一个闭区间[0，RAND_MAX]内的均匀随机整数（均匀的含义是：该区间内每个整数被随机获取的概率相同），其中RAND_MAX至少为32767（2^15^-1），在不同环境下的值可能不同。严格地说，这里的随机数是“伪随机数”，因为它也是由数学公式计算出来的，不过在算法领域，多数情况下可以把它当作真正的随机数。



如何产生[0，n]之间的整数呢？很多人喜欢用rand( )%n产生区间[0，n-1]内的一个随机整数，姑且不论这样产生的整数是否仍然分布均匀，只要n大于RAND_MAX，此法就不能得到期望的结果。由于RAND_MAX很有可能只有32767这么小，在使用此法时应当小心。另一个方法是执行rand( )之后先除以RAND_MAX，得到[0，1]之间的随机实数，扩大n倍后四舍五入，得到[0，n]之间的均匀整数。这样，在n很大时“精度”不好（好比把小图放大后会看
到“锯齿”），但对于普通的应用，这样做已经可以满足要求了



**提示5-19**：可以用cstdlib中的srand函数初始化随机数种子。如果需要程序每次执行时使用一个不同的种子，可以用ctime中的time（NULL）为参数调用srand。一般来说，只在程序执行的开头调用一次srand。

简单地说，种子是伪随机数计算的依据。种子相同，计算出来的“随机数”序列总是相同。如果不调用srand而直接使用rand( )，相当于调用过一次srand（1），因此程序每次执行时，将得到同一套随机数。

若要反复测试程序对不同随机数据的响应，需要每次得到的随机数不同。一个简单的方法是使用当前时间time（NULL）（在ctime中）作为参数调用srand。由于时间是不断变化的，每次运行时，一般会得到一套不同的随机数。之所以说“一般会”，是因为time函数返回的是自UTC时间1970年1月1日0点以来经过的“秒数”，因此每秒才变化一次。如果你的程序是由操作系统自动批量执行的，可能因为每次运行的间隔时间过短，导致在相邻若干次执行时time的返回值全部相同。一个解决办法是在测试程序的主函数中设置一个循环，做足够多次测试后再退出



```c++
vector<int> fill_random_int(int cnt) {
vector<int> v;
for(int i = 0; i < cnt; i++)
v.push_back(rand());
return v;
}

//实际上函数内的局部变量v中的元素需要逐个复制给调用者。而用传引用的方式调用，就避免了这些复制过程。

void fill_random_int(vector<int>& v, int cnt) {
v.clear();
for(int i = 0; i < cnt; i++)
v.push_back(rand());
}

```

**提示5-20**：把vector作为参数或者返回值时，应尽量改成用引用方式传递参数，以避免不必要的值被复制。

**提示5-21**：C＋＋支持函数重载，但函数的参数类型必须不同（不能只有返回值类型不同）。



写完了随机数发生器之后，就可以正式测试sort函数了，程序如下：

```c++
void test_sort(vector<int>& v) {
sort(v.begin(), v.end());
for(int i = 0; i < v.size()-1; i++)
assert(v[i] <= v[i+1]);
}

int main() {
vector<int> v;
fill_random_int(v, 1000000);
test_sort(v);
return 0;
}
```

新内容是上面的assert宏，其用法是“assert（表达式）”，作用是：**当表达式为真时无变化，但当表达式为假时强行终止程序，并且给出错误提示**。当然，上述程序也可以写成“if（v[i]>v[i＋1]）{printf（"Error：v[i]>v[i＋1]！\n"）；abort( )；}”，但assert更简洁，而且可以知道是由代码中的哪一行引起的，所以在测试时常常使用它。

set和map每次插入、查找和删除时间和元素个数的对数呈线性关系.尽管如此，在一些对时间要求非常高的题目中，STL有时会成为性能瓶颈，



## 5.3 应用：大整数类

如果运算结果真的很大，就需要用到所谓的高精度算法，即用数组来储存整数，并模拟手算的方法进行四则运算。

### 5.3.1 大整数类BigInteger

结构体BigInteger可用于储存高精度非负整数。

```c++
struct BigInteger(){
    static const int BASE = 100000000;
	static const int WIDTH=8;
    
    vector<int> s;
    BigInteger(long long num=0){*this=num;}
    BigInteger operator=(long long num){//赋值运算符
        s.clear();
        do{
            s.push_back(num%BASE);
            num/=BASE;
            
        }while(num>0);
        return *this;
        
    }
    BigInteger operator=(const string& str){
        s.clear();
        int x,len=(str.length()-1)/WIDTH+1;
        for(int i=0;i<len;i++){
			int end=str.length()-i*WIDTH;
            int start =max(0,end-WIDTH);
            sscanf(str.substr(start,end-start).c_str(),"%d",&x);
            s.push_back(x);
        }
        return *this;
    }
}
```

其中，s用来保存大整数的各个数位。例如，若是要表示1234，则s={4，3，2，1}。用vector而非数组保存数字的好处显而易见：不用关心这个整数到底有多大，vector会自动根据情况申请和释放内存。

上面的代码中还有赋值运算符，有了它就可以用x=123456789或者x="1234567898765432123456789"这样的方式来给x赋值了。

```c++
ostream& operator << (ostream &out,const BigInteger& x){
    out<<x.s.back();
    for(int i=x.s.size()-2;i<=0;i--){
        char buf[20];
        sprintf(buf,"%08d",x.s[i]);
        for(int j=0;j<strlen(buf);j++) out<<buf[j];
    }
    return out;
}
istream& operator >> (istream &in, BigInteger& x){
	string s;
    if(!(in>>s)) return in;
    x=s;
    return in;
}
```

这样，就可以用cin>>x和cout<<x的方式来进行输入输出了。怎么样，很方便吧？不仅如此，stringstream也“自动”支持了BigInteger，这得益于C＋＋中的类继承机制。简单地说，由于“>>”和“<<”运算符的参数是一般的istream和ostream类，作为“特殊情况”的cin/cout以及stringstream类型的流都能用上它。

上述代码中还有两点需要说明。一是static const int BASE=100000000，其作用是声明一个“属于BigInteger”的常数。注意，这个常数不属于任何BigInteger类型的结构体变量，而是属于BigInteger这个“类型”的，因此称为静态成员变量，在声明时需要加static修饰符。在BigInteger的成员函数里可以直接使用这个常数（见上面的代码），但在其他地方使用时需要写成BigInteger：：BASE。

**提示5-24**：可以给结构体声明一些属于该结构体类型的静态成员变量，方法是加上static修饰符。静态成员变量在结构体外部使用时要写成“结构体名：：静态成员变量名”

### 5.3.2 四则运算

```c++
BigInteger operator + (const BigInteger& b) const {
BigInteger c;
c.s.clear();
for(int i = 0, g = 0; ; i++) {
if(g == 0 && i >= s.size() && i >= b.s.size()) break;
int x = g;
if(i < s.size()) x += s[i];
if(i < b.s.size()) x += b.s[i];
c.s.push_back(x % BASE);
g = x / BASE;
}
return c;
}

BigInteger operator += (const BigInteger& b) {
    *this = *this + b; return *this;
}
//减法、乘法和除法的原理类似，这里不再赘述，请读者参考代码仓库。
```

### 5.3.3 比较运算符

```c++
bool operator > (const BigInteger& b) const {
if(s.size() != b.s.size()) return s.size() < b.s.size();
for(int i = s.size()-1; i >= 0; i--)
if(s[i] != b.s[i]) return s[i] < b.s[i];
return false; //相等
}

```

一开始就比较两个BigInteger的位数，如果不相等则直接返回，否则直接从后往前比较（因为低位在vector的前面）。注意，这样做的前提是两个数都没有前导零，否则，很可能出现“运算结果都没问题，但一比较就出错”的情况。

只需定义“小于”这一个符号，即可用它定义其他所有比较运算符（当然，对于BigInteger这个例子来说，“ == ”可以直接定义为s == b.s，不过不具一般性）：

```c++
bool operator > (const BigInteger& b) const{ return b < *this; }
bool operator <= (const BigInteger& b) const{ return !(b < *this); }
bool operator >= (const BigInteger& b) const{ return !(*this < b); }
bool operator != (const BigInteger& b) const{ return b < *this || *this < b; }
bool operator == (const BigInteger& b) const{ return !(b < *this) && !(*this < b); }
```



## 5.4 竞赛题目举例

**例题5-8** Unix ls命令（Unix ls，UVa400）

输入正整数n以及n个文件名，排序后按列优先的方式，假设最长文件名有M个字符，则最右列宽至少M字符，其它列宽皆为M+2字符，而输出的总宽为60个字符，输出文件名前要先输出60个’-’字符。

分析：

​    找出M值，并计算出输出的行数和列数。设maxcol=60，则列数cols = (maxcol - M) / (M + 2) + 1，行数rows = (n - 1) / cols + 1。然后排序输出文件名。

```
Sample Input
10
tiny
2short4me
very_long_file_name
shorter
size-1
size2
size3
much_longer_name
12345678.123
mid_size_name
12
Weaser
Alfalfa
Stimey
Buckwheat
Porky
Joe
Darla
Cotton
Butch
Froggy
Mrs_Crabapple
P.D.
19
Mr._French
Jody
Buffy
Sissy
Keith
Danny
Lori
Chris
Shirley
Marsha
Jan
Cindy
Carol
Mike
Greg
Peter
Bobby
Alice
Ruben
Sample Output
------------------------------------------------------------
12345678.123 size-1
2short4me size2
mid_size_name size3
much_longer_name tiny
shorter very_long_file_name
------------------------------------------------------------
Alfalfa Cotton Joe Porky
Buckwheat Darla Mrs_Crabapple Stimey
Butch Froggy P.D. Weaser
------------------------------------------------------------
Alice Chris Jan Marsha Ruben
Bobby Cindy Jody Mike Shirley
Buffy Danny Keith Mr._French Sissy
Carol Greg Lori Peter
```

首先计算出M并算出行数，然后逐行逐列输出。代码如下：

```c++
#include<iostream>
#include<string>
#include<algorithm>
using namespace std;
const int maxcol=60;
const int maxn=100+5;
string filenames[maxn];

//输出字符串s，长度不足len时补字符extra
void print(const string& s,int len,char extra){
    cout<<s;
    for(int i=0;i<len-s.length();i++)
        cout<<extra;
}

int main(){
	int n;
    while(cin>>n){
		int M=0;
        for(int i=0;i<n;i++){
            cin>>filenames[i];
            M=max(M,(int)filenames[i].length());//STL的max
        }
        //计算列数cols和行数rows
        int cols=(maxcol-M)/(M+2)+1,rows=(n-1)/cols+1;
        print("",60,'-');
        cout<<"\n";
        sort(filenames,filenames+n);
        for(int r=0;r<rows;r++){
			for(int c=0;c<cols;c++){
				int idx=c*rows+r;
                if(idx<n) print(filenames[idx],c==cols-1?M:M+2,' ');
            }
            cout<<"\n";
        }
    }
    return 0;
}
```

需要花费大量的时间进行训练啊，暂时到这里先暂停一下