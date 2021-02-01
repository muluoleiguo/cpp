## 数组

程序3-1 逆序输出

```c
#include<stdio.h>
#define maxn 105
int a[maxn];
int main()
{
	int x,n = 0;
    while(scanf("%d",&x)==1)
        a[n++]=x;
    for(int i=n-1;i>=1;i--)
        printf("%d",a[i]);
    printf("%d\n",a[0]);
    return 0;
}
```

**提示3-2**：在算法竞赛中，常常难以精确计算出需要的数组大小，数组一般会声明得稍大一些。在空间够用的前提下，浪费一点不会有太大影响。

**提示3-4**：比较大的数组应尽量声明在main函数外，否则程序可能无法运行

C语言的数组并不是“一等公民”，而是“受歧视”的。例如，数组不能够进行赋值操作：

`int a[maxn]，b[maxn];`

是不能赋值b＝a的

可以这样做：`memcpy（b，a，sizeof（int）*k）`。

使用memcpy函数要包含头文件string.h

如果需要把数组a全部复制到数组b中，可以写得简单一些：`memcpy（b，a，sizeof（a））`。



开灯问题。有n盏灯，编号为1～n。第1个人把所有灯打开，第2个人按下所有编号为2的倍数的开关（这些灯将被关掉），第3个人按下所有编号为3的倍数的开关（其中关掉的灯将被打开，开着的灯将被关闭），依此类推。一共有k个人，问最后有哪些灯开着？输入n和k，输出开着的灯的编号。k≤n≤1000。

样例输入：
7 3
样例输出：
1 5 6 7

用数组模拟即可

一个技巧在输出：为了避免输出多余空格，设置了一个标志变量first，可以表示当前要输出的变量是否为第一个。第一个变量前不应有空格，但其他变量都有。

```c
#include<stdio.h>
#include<string.h>
#define maxn 1010
int a[maxn];
int main()
{
    int n,k,first=1;
    memset(a,0,sizeof(a));
    scanf("%d%d",&n,&k);
    for(int i=1;i<=k;i++)	//k个人
        for(int j=1;j<=n;j++)	//n盏灯
            if(j%i==0)			
                a[j]=!a[j];
    for(int i=1;i<=n;i++)
        if(a[i])
        {
            if(first)
                first = 0;
            else
                printf(" ");
            printf("%d",i);
        }
    printf("\n");
    return 0;
}
```



## 3.2 字符数组

因为在C语言中，字符串其实就是字符数组——可以像处理普通数组一样处理字符串，只需要注意输入输出和字符串函数的使用

竖式问题。找出所有形如abc*de（三位数乘以两位数）的算式，使得在完整的竖式中，**所有数字都属于一个特定的数字集合**。输入**数字集合**（相邻数字之间没有空格），输出所有竖式。每个竖式前应有编号，之后应有一个空行。最后输出解的总数。具体格式见样例输出(输入集合 2、3、5、7)

**竖式其实就是小学的那种手算的过程，只不过这里用计算机输出**

样例输入：
2357 
样例输出：

![image-20210125225831174](C:\Users\55018\AppData\Roaming\Typora\typora-user-images\image-20210125225831174.png)

```c
#include<stdio.h>
#include<string.h>
int main()
{
    char s[20];
    char buf[99];
    scanf("%s",s);
    int abc,de;
    for(abc = 111;abc <=999;abc++)//尝试所有的abc和de
    {

        for(de =11;de <=99;de++)
        {
            int ok = 1;
            int x = abc * (de % 10);
            int y = abc * (de / 10);
            int z = abc * de;
            sprintf(buf,"%d%d%d%d%d",abc,de,x,y,z);//sprintf:输出到缓冲字符串buf中
            for(int k =0;k < strlen(buf);k++)          
                if(strchr(s,buf[k]) == NULL)//strchr查找字符串中首次出现字符buf(i)的位置 
                    ok = 0;     //如果在buf中有不在集合s中的数字，则不输出
            if(ok)
            printf("<%d>\n", ++count);
            printf("%5d\nX%4d\n-----\n%5d\n%4d\n-----\n%5d\n\n", abc, de, x, y, z);
        }
    }
    printf("The numbver of solutions = %d\n", count);
    return 0;
}

```



C 库函数 **int sprintf(char \*str, const char \*format, ...)** 发送格式化输出到 **str** 所指向的字符串。

- **str** -- 这是指向一个字符数组的指针，该数组存储了 C 字符串。

- **format** -- 这是字符串，包含了要被写入到字符串 str 的文本。它可以包含嵌入的 format 标签，format 标签可被随后的附加参数中指定的值替换，并按需求进行格式化。

  format 标签属性是 `%[flags][width][.precision][length]specifier`

- **附加参数** -- 根据不同的 format 字符串，函数可能需要一系列的附加参数，每个参数包含了一个要被插入的值，替换了 format 参数中指定的每个 % 标签。参数的个数应与 % 标签的个数相同。

如果成功，则返回写入的字符总数，不包括字符串追加在字符串末尾的空字符。如果失败，则返回一个负数。

**提示3-8**：C语言中的字符型用关键字char表示，它实际存储的是字符的ASCII码。字符常量可以用单引号法表示。在语法上可以把字符当作int型使用。

**提示3-9**：在“scanf("%s", s)”中，不要在s前面加上“&”符号。如果是字符串数组chars[maxn] [maxl]，可以用“scanf("%s", s[i])”读取第i个字符串。注意，“scanf("%s", s)”遇到空白字符会停下来。



前用过printf和fprintf。没错！这3个函数是“亲兄弟”，printf输出到屏幕，fprintf输出到文件，而sprintf输出到字符串。多数情况下，屏幕总是可以输出的，文件一般也能写（除非磁盘满或者硬件损坏），但字符串就不一定了：应该保证写入的字符串有足够的空间。

多大才算足够大呢？答案是字符个数加1

函数strlen(s)的作用是获取字符串s的实际长度。strlen(s)返回的就是结束标记之前的字符个数

**提示3-12**：由于字符串的本质是数组，只能用strcpy(a,b),strcmp(a,b),strcat(a,b)来赋值、比较、连接，不能用=、==等运算符

提示3-13：滥用++、--、+=等可以修改变量值的运算符很容易带来隐蔽的错误。建议每条语句最多只用一次这种运算符，并且所修改的变量在整条语句中只出现一次。



## 3.3 竞赛题目选讲

例题3-1 TeX中的引号（Tex Quotes, UVa 272）

在TeX中，左双引号的"``",右双引号是"''"。输入一篇包含双引号的文章，你的任务是把它转换成TeX的格式。

```
样例输入："To be or not to be,"quoth the Bard,"that is the question".
样例输出： ``To be or not to be''quoth the Bard,``that is the question''.
```



本题的关键是，如何判断一个双引号是左双引号还是右双引号。方法很简单：使用一个标志变量即可。可是在此之前，需要解决另外一个问题：输入字符串。

之前学习了使用“scanf("%s")”输入字符串，但却不能在本题中使用它，因为它碰到空格或者TAB就会停下来。虽然下次调用时会输入下一个字符串，可是不知道两次输入的字符串中间有多少个空格、TAB甚至换行符。可以用下述两种方法解决这个问题：

第一种方法是使用“fgetc(fin)”，它读取一个打开的文件fin，读取一个字符，然后返回一个int值。为什么返回的是int而不是char呢？因为如果文件结束，fgetc将返回一个特殊标记EOF，它并不是一个char。如果把fgetc(fin)的返回值强制转换为char，将无法把特殊的EOF和普通字符区分开。如果要从标准输入读取一个字符，可以用getchar，它等价于fgetc(stdin)。



**提示3-14**：使用fgetc(fin)可以从打开的文件fin中读取一个字符。一般情况下应当在检查它不是EOF后再将其转换成char值。从标准输入读取一个字符可以用getchar，它等价于fgetc(stdin)。

fgetc和getchar将读取“下一个字符”，因此需要知道在各种情况下，“下一个字符”是哪个。如果用“scanf("%d", &n)”读取整数n，则要是在输入123后多加了一个空格，用getchar读取的将是这个空格；如果在“123”之后紧跟着换行，则读取到的将是回车符“\n”

这里有个潜在的陷阱：**不同操作系统的回车换行符是不一致的。**

**提示3-15**：在使用fgetc和getchar时，应该避免写出和操作系统相关的程序。

第二种方法是使用“fgets(buf, maxn, fin)”读取完整的一行，其中buf的声明为char buf[maxn]。这个函数读取不超过maxn-1个字符，然后在末尾添上结束符“\0”，因此不会出现越界的情况。之所以说可以用这个函数读取完整的一行，是因为一旦读到回车符“\n”，读取工作将会停止，而这个“\n”也会是buf字符串中最后一个有效字符（再往后就是字符串结束符“\0”了）。只有在一种情况下，buf不会以“\n”结尾：读到文件结束符，并且文件的最后一个不是以“\n”结尾。尽管比赛的组织方应避免这样的情况（和输出文件一样，保证输入文件的每行均以回车符结尾），但正如刚才所说，选手应该把自己的程序写得更鲁棒。



**提示3-16**："fgets(buf, maxn, fin)"将读取完整的一行放在字符数组buf中。应当保证buf足够存放下文件的一行内容。除了在文件结束前没有遇到“\n”这种特殊情况外，buf总是以“\n”结尾。当一个字符都没有读到时，fgets返回NULL。

和fgetc一样，fgets也有一个"标准输入版"gets。遗憾的是，gets和它的"兄弟"fgets差别比较大：其用法是gets(s)，没有指明读取的最大字符数。这里就出现了一个潜在的问题：gets将不停地往s中存储内容，而不管是否存储得下！难道gets函数不去管s的可用空间有多少吗？确实如此。

**提示3-18**：C语言中的gets(s)存在缓冲区溢出漏洞，不推荐使用。在C11标准里，该函数已被正式删除。

本题的特点是：可以边读边处理，而不需要把输入字符串完整地存下来，因此getchar是一个不错的选择。



解答：

```assembly
#include<stdio.h>
int main()
{
	int c,q=1;
	while((c=getchar())!=EOF)
	{
		if(c=='"')
		{
			printf("%s", q?"``" : "''");
            q = !q;
		}
		else printf("%c", c);
	}
}
```



例题3-2 WERTYU（WERTYU, UVa10082）

把手放在键盘上时，稍不注意就会往右错一位。这样，输入Q会变成输入W，输入J会变成输入K等。键盘如图所示。

![image-20210125232802119](C:\Users\55018\AppData\Roaming\Typora\typora-user-images\image-20210125232802119.png)

输入一个错位后敲出的字符串（所有字母均大写），输出打字员本来想打出的句子。输入保证合法，即一定是错位之后的字符串。例如输入中不会出现大写字母A。

样例输入：
O S, GOMR YPFSU/

样例输出：
I AM FINE TODAY.



不得不说，这些竞赛题目挺有意思的

一个较好的方法是使用常量数组，下面是完整程序：(我是真的想不到这种)

```c
#include<stdio.h>
char s[]="`1234567890-=QWERTYUIOP[]\\ASDFGHJKL;'ZXCVBNM,./";
int main()
{
    int i,c;
    while((c=getchar())!=EOF)
    {
        for(i=1;s[i]&&s[i]!=c;i++);//找错位之后的字符在常量表中的位置
        	if(s[i])
                putchar(s[i-1]);//如果找到，则输出它的前一个字符
        	else putchar(c);
    }
}
```

**提示3-19**：善用常量数组往往能简化代码。定义常量数组时无须指明大小，编译器会计算。



例题3-3 回文词（Palindromes, UVa401）

输入一个字符串，判断它是否为回文串以及镜像串。输入字符串保证不含数字0。所谓回文串，就是反转以后和原串相同，如abba和madam。所有镜像串，就是左右镜像之后和原串相同，如2S和3AIAE。注意，并不是每个字符在镜像之后都能得到一个合法字符。在本题中，每个字符的镜像如图3-3所示（空白项表示该字符镜像后不能得到一个合法字符）。

![image-20210125233421335](C:\Users\55018\AppData\Roaming\Typora\typora-user-images\image-20210125233421335.png)

​																			图3-3 镜像字符

输入的每行包含一个字符串（保证只有上述字符。不含空白字符），判断它是否为回文串和镜像串（共4种组合）。每组数据之后输出一个空行。

样例输入：
NOTAPALINDROME
ISAPALINILAPASI
2A3MEAS
ATOYOTA
样例输出：

NOTAPALINDROME -- is not a palindrome.

ISAPALINILAPASI -- is a regular palindrome.

2A3MEAS -- is a mirrored string.

ATOYOTA -- is a mirrored palindrome.



既然不包含空白字符，可以安全地使用scanf进行输入。回文串和镜像串的判断都不复杂，并且可以一起完成。使用常量数组，只用少量代码即可解决这个看上去有些复杂的题目

```c
#include<stdio.h>
#include<string.h>
#include<ctype.h>
const char* rev="A 3 HIL JM O 2TUVWXY51SE Z 8 ";
const char* msg[]={"not a palindrome","a regular palindrome","a mirrored str"};
char r(char ch)
{	
    if(isalpha(ch)) return rev[ch-'A'];
    return rev[ch-'0'+25];
}

int main()
{
    char s[30];
    while(scanf("%s",s)==1)
    {
        int len =strlen(s);
        int p=1,m=1;
        for(int i=0;i<(len+1)/2;i++)
        {
            if(s[i]!=s[len-1-i]) p=0; //不是回文串
            if(r(s[i])!=s[len-1-i])m=0;//不是镜像串
		}
        printf("%s -- is %s.\n\n",s,msg[m*2+p]);
    }
}
```

本题使用了一个自定义函数char r(char ch)，参数ch是一个字符，返回值是ch的镜像字符。这是因为该常量数组中前26项是各个大写字母的镜像，而后10个是数字1～9的镜像（数字0不会出现），所以需要判断ch是字母还是数字。

本题用isalpha来判断字符是否为字母，类似的还有idigit、isprint等，在ctype.h中定义。由于ASCII码表中大写字母、小写字母和数字都是连续的，如果ch是大写字母，则ch-'A'就是它在字母表中的序号（A的序号是0，B的序号是1，依此类推）；类似地，如果ch是数字，则ch-'0'就是这个数字的数值本身（例如'5'-'0'=5）。



**提示3-20**：头文件ctype.h中定义的isalpha、isdigit、isprint等工具可以用来判断字符的属性，而toupper、tolower等工具可以用来转换大小写。如果ch是大写字母，则ch-'A'就是它在字母表中的序号（A的序号是0，B的序号是1，依此类推）；类似地，如果ch是数字，则ch-'0'就是这个数字的数值本身。



例题3-4 猜数字游戏的提示（Master-Mind Hints, UVa 340）

实现一个经典"猜数字"游戏。给定答案序列和用户猜的序列，统计有多少数字位置正确（A），有多少数字在两个序列都出现过但位置不对（B）。
输入包含多组数据。每组输入第一行为序列长度n，第二行是答案序列，接下来是若干猜测序列。猜测序列全0时该组数据结束。n=0时输入结束。

样例输入：
4
1 3 5 5

1 1 2 3
4 3 3 5
6 5 5 1
6 1 3 5
1 3 5 5
0 0 0 0
10
1 2 2 2 4 5 6 6 6 9
1 2 3 4 5 6 7 8 9 1
1 1 2 2 3 3 4 4 5 5
1 2 1 3 1 5 1 6 1 9
1 2 2 5 5 5 6 6 6 7
0 0 0 0 0 0 0 0 0 0
0

样例输出：
Game 1:
	(1,1)
	(2,0)
	(1,2)
	(1,2)
	(4,0)
Game 2:
	(2,4) 

​	(3,2)
​	(5,0)
​	(7,0)



```c
#include<stdio.h>
#define maxn 1010
int main(){
    int n,a[maxn],b[maxn];
    int kase=0;
    while(scanf("%d",&n)==1&&n)	{//n=0时输入结束
        printf("Games: %d\n",++kase);
        for(int i=0;i<n;i++)	
            scanf("%d",&a[i]);		//a中保存正确的序列
        for(;;){
            int A=0,B=0;
            for(int i=0;i<n;i++){
                scanf("%d",&b[i]);
                if(a[i]==b[i]) A++;	//在猜测序列b中i位置如果猜对，则A++
            }
        if(b[0]==0) break; //正常的猜测序列没有0，判断一个数是0即可
            for(int d=1;d<=9;d++){
                int c1=0,c2=0;//统计数字d在答案序列和猜测序列中各出现多少次
                for(int i=0;i<n;i++){
                    if(a[i]==d) c1++;
                    if(b[i]==d) c2++;
                }
              	if(c1<c2) B+=c1; else B+=c2;//B=猜测序列和正确序列数字出现次数少的那个，
                							//减去正确的就是在猜和正都出现但不正确的
            }
            printf("   (%d,%d)\n", A, B-A);
        }
    }
    return 0;
}
```



例题3-5 生成元（Digit Generator, ACM/ICPC Seoul 2005, UVa1583）

如果x加上x的各个数字之和得到y，就说x是y的生成元。给出n（1≤n≤100000），求最小生成元。无解输出0。例如，n=216，121，2005时的解分别为198，0，1979。

假设所求生成元为m。不难发现m<n。换句话说，只需枚举所有的m<n，看看有没有哪个数是n的生成元。

可惜这样做的效率并不高，因为每次计算一个n的生成元都需要枚举n-1个数。有没有更快的方法？聪明的读者也许已经想到了：只需一次性枚举100000内的所有正整数m，标记“m加上m的各个数字之和得到的数有一个生成元是m”，最后查表即可。



我只能说，我有些笨了



```c
#include<stdio.h>
#include<string.h>
#define maxn 100005
int ans[maxn];
int main(){
    int T,n;
    memset(ans,0,sizeof(ans));
    for(int m=1;m<maxn;m++){
        int x=m,y=m;
        while(x>0){y+=x%10;x/=0}	//y就是生成元
        if(ans[y]==0||m<ans[y]) ans[y]=m; //ans数组内生成元ans[y]=x,x是y生成元且最小
    }
    scanf("%d",&T);
    while(T--){
        scanf("%d",&n);
        printf("%d\n",ans[n]);
    }
    return 0;
}
```



例题3-6 环状序列（Circular Sequence, ACM/ICPC Seoul 2004, UVa1584）

长度为n的环状串有n种表示法，分别为从某个位置开始顺时针得到。例如，图3-4的环状串有10种表示：

<img src="C:\Users\55018\AppData\Roaming\Typora\typora-user-images\image-20210127134010670.png" alt="image-20210127134010670" style="zoom:150%;" />

CGAGTCAGCT，GAGTCAGCTC，AGTCAGCTCG等。在这些表示法中，字典序最小的称为"最小表示"

输入一个长度为n（n≤100）的环状DNA串（只包含A、C、G、T这4种字符）的一种表示法，你的任务是输出该环状串的最小表示。例如，CTCC的最小表示是CCCT，CGAGTCAGCT的最小表示为AGCTCGAGTC。



【分析】
		本题出现了一个新概念：字典序。所谓字典序，就是字符串在字典中的顺序。一般地，对于两个字符串，从第一个字符开始比较，当某一个位置的字符不同时，该位置字符较小的串，字典序较小（例如，abc比bcd小）；如果其中一个字符串已经没有更多字符，但另一个字符串还没结束，则较短的字符串的字典序较小（例如，hi比history小）。字典序的概念可以推广到任意序列，例如，序列1, 2, 4, 7比1, 2, 5小。

​		学会了字典序的概念之后，本题就不难解决了：就像"求n个元素中的最小值"一样，**用变量ans表示目前为止，字典序最小串在输入串中的起始位置，然后不断更新ans。**

```c
#include<stdio.h>
#include<string.h>
#define maxn 105

//环状串s的表示法p是否比表示法q的字典序小
int less(const char* s,int p,int q){
    int n=strlen(s);
    for(int i=0;i<n;i++)
        if(s[(p+i)%n]!=s[(q+i)%n])
            return s[(p+i)%n]<s[(q+i)%n];
    return 0; //相等
}

int main(){
    int T;
    char s[maxn];
    scanf("%d",&T);
    while(T--){
        scanf("%s",s);
        int ans=0;
        int n=strlen(s);
        for(int i=1;i<n;i++)
            if(less(s,i,ans)) ans=i;
        for(int i=0;i<n;i++)
            putchar(s[(i+ans)%n]);
        putchar('\n');
    }
    return 0;
}
```

​	



### 3.4.1 进位制与整数表示

用ASCII编码表示字符。下面来探索一下字符在C语言中的表示。有些特殊的字符需要转义才能表达，`例如“\n”表示换行，“\\”表示反斜杠，“\"”表示引号，“\0”表示空字符，`

**提示3-21**：字符还可以直接用ASCII码表示。如果用八进制，应该写成：`“\o”，“\oo”或“\ooo”`（o为一个八进制数字）；如果用十六进制，应该写成“\xh”（h为十六进制数字串）。



在二进制中，8位最大整数就是8个1，即2^8^-1，用C语言写出来就是(1<<8)-1。注意括号是必需的，因为“<<”运算符的优先级没有减法高。

补码表示法。计算机中的二进制是没有符号的。尽管123的二进制值是1111011，-123在计算机内并不表示为-1111011——这个“负号”也需要用二进制位来表示。

“正号和符号”只有两种情况，因此用一个二进制位就可以了。容易想到一个表示“带符号32位整数”的方法：用最高位表示符号（0：正数；1：负数），剩下31位表示数的绝对值。可惜，这并不是机器内部真正的实现方法。在笔者的机器上，语句“printf("%u\n",-1)”的输出是4294967295。把-1换成-2、-3、-4……后，很容易总结出一个规律：-n的内部表示是2^32^-n。这就是著名的“补码表示法”（Complement Representation）。



为什么计算机要用这样一个奇怪的表示方法呢？前面提到的“符号位+绝对值”的方法哪里不好了？答案是：运算不方便。试想，要计算1 + (-1)的值（为了简单起见，假设两个数都是带符号8位整数）。如果用“符号位+绝对值”法，将要计算00000001+10000001，而答案应该是00000000。似乎想不到什么简单的方法进行这个“加法”。但如果采用补码表示，计算的是00000001+11111111，只需要直接相加，并丢掉最高位的进位即可。
“符号位+绝对值”还有一个好玩的bug：存在两种不同的0：一个是00000000（正0），一个是10000000（负0）。这个问题在补码表示法中不会出现（想一想，为什么）。

+0 补码=反码=原码00000000

负数的补码形式**等于对应的正数的原码取反加一**。

 -0 原码10000000 反码 01111111 补码=+0反码+1 =11111111+1 =1 0000 0000(最高位1舍去)

或者是2^32^-0=1 0000 0000 最高位1在第9位了舍去



### 3.4.2 思考题

**题目1**（必要的存储量）：数组可以用来保存很多数据，但在一些情况下，并不需要把数据保存下来。下面哪些题目可以不借助数组，哪些必须借助数组？请编程实现。假设输入只能读一遍。

* 输入一些数，统计个数。
* 输入一些数，求最大值、最小值和平均数。
* 输入一些数，哪两个数最接近。
* 输入一些数，求第二大的值。
* 输入一些数，求它们的方差。
* 输入一些数，统计不超过平均数的个数。



**题目2**（统计字符1的个数）：下面的程序意图在于统计字符串中字符1的个数，可惜有瑕疵：

```c
#include<stdio.h>
#define maxn 10000000 + 10
int main() {
char s[maxn];
scanf("%s", s);
int tot = 0;
for(int i = 0; i < strlen(s); i++)
	if(s[i] == 1) tot++;
printf("%d\n", tot);
}
```

该程序至少有3个问题，其中一个导致程序无法运行，另一个导致结果不正确，还有一个导致效率低下。你能找到它们并改正吗？



### 3.4.3 黑盒测试和在线评测系统

黑盒测试。算法竞赛一般采取黑盒测试：事先准备好一些测试用例，然后用它们测试选手程序，根据运行结果评分。除了找不到程序（如程序名没有按照比赛规定取，或是放错位置）、编译错等连程序都没能运行的错误之外，一些典型的错误类型如下：

* 答案错（Wrong Answer，WA）。
* 输出格式错（Presentation Error，PE）。
* 超时（Time Limit Exceeded，TLE）。
* 运行错（Runtime Error，RE）。



在运行时，除了程序自身异常退出（例如，除0、栈溢出、非法访问内存、断言为假、main函数返回非0值）外，还可能是因为超过了评测系统的资源约束（如内存限制、最大输出限制）而被强制中止执行。有的评测系统会把这些情况和一般的运行错误区分开，但在多数情况下会统一归到“运行错”中。

需要注意的是，超时不一定是因为程序效率太低，也可能是其他原因造成的。例如，比赛规定程序应从文件读入数据，但所写程序却正在等待键盘输入。其他原因包括：特殊数据导致程序进入死循环、程序实际上已经崩溃却没异常退出等。

如果上述错误都没有，那么恭喜你，你的程序通过了测试。在ACM/ICPC中，这意味着你的程序被裁判接受（accepted，AC），而在分测试点的比赛中，这意味着你拿到了该测试点的分数。

在线评测系统（Online Judge，OJ）为平时练习和网上竞赛提供了一个很好的平台。

首先，要向读者介绍的是历史最悠久、最著名的OJ：西班牙Valladolid大学的UVaOJ，网址为http://uva.onlinejudge.org/。除了收录了早期的ACM/ICPC区域比赛题目之外，这里还经常邀请世界顶尖的命题者共同组织网上竞赛，吸引了大量来自世界各地的高手同场竞技。

其他著名的OJ包括国内的ZOJ（浙江大学）, POJ（北京大学），HDOJ（电子科技大学）、俄罗斯的SGU、Timus、波兰的SPOJ等。

最好用的还是VOJ啊

独立完成习题，**欲速则不达**

https://onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&category=827

UVa中的“ANSI C”是指C89编译器，请在提交时选择C++语言。

**习题3-1** 得分（Score, ACM/ICPC Seoul 2005, UVa1585）

给出一个由O和X组成的串（长度为1～80），统计得分。每个O的得分为目前连续出现的O的个数，X的得分为0。例如，OOXXOXXOOO的得分为1+2+0+0+1+0+0+1+2+3。

There is an objective test result such as “OOXXOXXOOO”. An ‘O’ means a correct answer of a problem
and an ‘X’ means a wrong answer. The score of each problem of this test is calculated by itself and
its just previous consecutive ‘O’s only when the answer is correct. For example, the score of the 10th
problem is 3 that is obtained by itself and its two previous consecutive ‘O’s.
Therefore, the score of “OOXXOXXOOO” is 10 which is calculated by “1+2+0+0+1+0+0+1+2+3”.
You are to write a program calculating the scores of test results.
**Input**
Your program is to read from standard input. The input consists of T test cases. The number of test
cases T is given in the first line of the input. Each test case starts with a line containing a string
composed by ‘O’ and ‘X’ and the length of the string is more than 0 and less than 80. There is no spaces
between ‘O’ and ‘X’.
**Output**
Your program is to write to standard output. Print exactly one line for each test case. The line is to
contain the score of the test case.
**Sample Input**
5
OOXXOXXOOO
OOXXOOXXOO
OXOXOXOXOXOXOX
OOOOOOOOOO
OOOOXOOOOXOOOOX
**Sample Output**
10
9
7
55
30

```c
#include <stdio.h>
#include <string.h>

#define maxn 85

char s[maxn];

int main()
{
    int T;
    scanf("%d",&T);
    for(int i=0;i<T;i++){
        scanf("%s",s);
        int len,sum=0,score=0;
        len=strlen(s);
        for(int i=0;i<len;i++){
            if(s[i]=='X')   score=0;
            else if(s[i]=='O'){
                score++;
                sum+=score;
            }
        }
        printf("%d\n",sum);
    }
}

```

**习题3-2** 分子量（Molar Mass, ACM/ICPC Seoul 2007, UVa1586）

给出一种物质的分子式（不带括号），求分子量。本题中的分子式只包含4种原子，分别为C, H, O, N，原子量分别为12.01, 1.008, 16.00, 14.01（单位：g/mol）。例如，C6H5OH的分子量为94.108g/mol。

习题见文件

```c
#include<stdio.h>
#include<string.h>
#include<cctype>
#define maxn 85
char s[maxn];

int main(){
    int T;
    scanf("%d",&T);
    for(int i=0;i<T;i++){
        int c=0,h=0,o=0,n=0,len;
        char ch;
        scanf("%s",s);
        len=strlen(s);
        for(int i=0;i<len;i++){
            if(isalpha(s[i]))
                if(isdigit(s[i+1])){//下一位如果是数字
                    i++;
                    int j=i,num=0;
                    while(isdigit(s[i+1])){//i走到数字最后一位
                        i++;
                    }
                    int k=j;
                    for(;j<=i;j++){//数字字符转化为整形num
                        num*=10;
                        num=num+(s[j] - '0');
                    }
                    switch(s[k-1]){//根据数字前元素来加咯
                    case 'C':c+=num;break;
                    case 'H':h+=num;break;
                    case 'O':o+=num;break;
                    case 'N':n+=num;break;
                    default:break;
                    }
                }else	//下一位是字母或结束，相当于1
                    switch(s[i]){
                    case 'C':c++;break;
                    case 'H':h++;break;
                    case 'O':o++;break;
                    case 'N':n++;break;
                    }
            }
        printf("%.3f\n",c*12.01+h*1.008+o*16.00+n*14.01);
        //注意这里保留3位小数否则算是错误答案，还有键盘这个鬼东西害我CE两次，shit！
    }
    return 0;
}
```

**习题3-3** 数数字（Digit Counting , ACM/ICPC Danang 2007, UVa1225）22

把前n（n≤10000）个整数顺次写在一起：123456789101112…数一数0～9各出现多少次
（输出10个整数，分别是0，1，…，9出现的次数）。

```c
#include<stdio.h>
#include<string.h>
int main(){
    int T;
    int cnt[10];
    scanf("%d",&T);
    for(int i=0;i<T;i++){
        memset(cnt,0,sizeof(cnt));
        int n;
        scanf("%d",&n);
        for(int i=1;i<=n;i++){
            int num=i;
            while(num){
                cnt[num%10]++;
                num/=10;
            }
        }
        for(int i=0;i<9;i++) printf("%d ",cnt[i]);
        printf("%d\n",cnt[9]);

    }
    return 0;
}
```

**习题3-4** 周期串（Periodic Strings, UVa455）

如果一个字符串可以由某个长度为k的字符串重复多次得到，则称该串以k为周期。例如，abcabcabcabc以3为周期（注意，它也以6和12为周期）。输入一个长度不超过80的字符串，输出其最小周期。

```C
#include<stdio.h>
#include<string.h>
#define maxn 81
char s[maxn];

int main(){
    int T;
    scanf("%d",&T);
    while(T--){
        scanf("%s",s);
        int len=strlen(s),j;
        for(int i=1;i<=len;i++){
            if(!(len%i)){
                for(j=0;j<len;j++)
                    if(s[j]!=s[j%i]) break;
                if(j==len){
                    printf("%d\n",i);
                    break;
                }
            }
        }
        if(T) putchar('\n'); //没有这个格式错误，？为何
    }
    return 0;
}
```

习题3-5 谜题（Puzzle, ACM/ICPC World Finals 1993, UVa227）

有一个5*5的网格，其中恰好有一个格子是空的，其他格子各有一个字母。一共有4种指令：A, B, L, R，分别表示把空格上、下、左、右的相邻字母移到空格中。输入初始网格和指令序列（以数字0结束），输出指令执行完毕后的网格。如果有非法指令，应输出“This puzzle has no final configuration.”，例如，图3-5中执行ARRBBL0后，效果如图3-6所示。

<img src="C:\Users\55018\AppData\Roaming\Typora\typora-user-images\image-20210127171136575.png" alt="image-20210127171136575" style="zoom:50%;" />

​																			图3-5 执行ARRBBL0前 

<img src="C:\Users\55018\AppData\Roaming\Typora\typora-user-images\image-20210127171151407.png" alt="image-20210127171151407" style="zoom:50%;" />

​																			图3-6 执行ARRBBL0后

**（1）每个输出之间都有一行空格，但最后一个输出之前没有’\n’。
（2）‘0’之前移动序列有很多’\n’或空格，移动序列的非法只考虑越界，不考虑有其他字符。
（3）输入迷宫矩阵不用考虑有多个空格，样例中如果空格出现在最后一个，则是缺失的，输入时需要补齐。
（4）输出2个结果之间有空行，最后一个结果后无空行。
最后，使用getchar()注意容易多吃或少吃字符，建议判断每一个ch=getchar()。**



**习题3-8** 循环小数（Repeating Decimals, ACM/ICPC World Finals 1990, UVa202）

输入整数a和b（0≤a≤3000，1≤b≤3000），输出a/b的循环小数表示以及循环节长度。例如a=5，b=43，小数表示为0.(116279069767441860465)，循环节长度为21。