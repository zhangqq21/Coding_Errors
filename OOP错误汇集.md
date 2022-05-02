# **OOP错误汇集**

**1.**关于宏定义的用法（就是用标识符表示一个常量）：

​	1该常量可以为一个数值常量（int,single,double）、也可以是字符常量、字符串常量、布尔常量等（相应的变量也行，，，是devc++的bug?）

​    数值常量直接赋即可，而字符常量需加‘’，字符串常量需加“”；而对相应的变量，只需写入相应的变量名即可，且当变量值改变，相应标识符对应的值也发生改变。

​    2其作用机理大致为：如果是常量，则在后面的程序中直接当作代替的“字符串”代入即可。如“#define N ’a’”,则在后文则直接代入‘a’。如果是变量（非常量），则搜索前文中有无这一变量，有则转换成相同的数值和数据类型再代入，如下：则后面用到时直接代入变量a即可。（故而随值而改变)

```c++
int a=123;
#define N a
```

​	3错误样例：

```c++
int a; #include <fstream>
int a; #define N a
#define N =5
#define N 5;
```

​	4字符常量与字符串常量：

```c++
#define N 'a'
#define N "hello world"
```

​	5尤其注意，若是替换的不只是一个量，而是一个式子，则要用（）括起，否则会出错，如下：

```c++
#define N 1+2			//错误
#define N (1+2)			//正确
```

若是按第一种，则N*N=5错误；第二种的结果为9正确

​	6对含参的宏定义，尤其注意，把所有的单个参数都加一个括号，且把整体式子也得加括号，且运算时的括号也要注意。如下：

```c++
#define average(a,b) (((a)+(b))/2)
#define M (a,b) a*b+b 
```

​		及，标识符和参数表之间不能有括号！如第二行，会编译出错(认为是无参数)。	

​	7用“#undef + 标识符”来解除宏定义，如下：(可以用在主函数中)

```c++
	#undef N
```

​	8宏定义的优点：1方便程序的修改 2提高程序的效率（可以代替相对简单的函数调用）

**2.**关于类型转化：将int类型的数值赋给double,single，则按原数值赋入，只是数据类型改变；而将double,single类型赋给int，则按正数向下取整，负数向上取整的规则进行；

同时，初始化的规则亦是如此，如下：输出0；

```c++
int a=0;
double b=1.234;
a=b;
cout<<a<<endl;
```

**3.**关于"#ifndef,#ifdef,#endif"的用法：

​	1【注】：头文件如果使用这种操作，那么不能在里面定义变量或者函数，会出错。但是定义宏、定义类和定义结构都可以。

​	==改：==好像又可以了...只要在编译时别把头文件包进去就行

```c++
#ifndef x                 //先测试x是否被宏定义过
#define x
   程序段1blabla~    //如果x没有被宏定义过，定义x，并编译程序段 1
#endif   
　　程序段2blabla~　　 //如果x已经定义过了则编译程序段2的语句，“忽视”程序段 1
```

​	2 还可以用来debug，如当程序出问题时，可以自动define DEBUG，然后进入DEBUG模式，输出一些数值。

**4.**在单个文件中

```c++
#define x  	//不赋值（相当于int x;/char x;），在源代码中替换x，相当于什么也不打。和变量定义不同，这个没有默认初值！！！但是算def过了，无论是只在一个单一文件里，或者由头文件包含着，只要出现了一次#define x，就算宏定义过了，就可以执行#ifdef

	//
	cout<<x<<endl;
```

单独L1（不赋值）能编译过。在实际使用x时会编译出错（L3）（相当于“cout<< <<endl”）。

**5.**关于函数重载：函数的返回类型+函数名+函数参数表

​	1首先，需要函数名相同。其次，不同的重载函数之间必须==函数参数表的类型排序==不同（即如果是 int int double）,则重载的函数就不能是(int int double)!!! 其他的具体函数体、返回类型、参数名都没关系

​	2调用类型匹配 > 自动类型转换

​	推之：但凡出现了==函数二义性==都不能通过编译！！！(如缺省值：具体操作是把缺省值参数去掉，比对剩下的函数参数类型排序)

**6.**可以有string函数：

```c++
string ADD(string x,string y){
	return x+y;
}
```

**7.**typeid的使用（待补充）：

```c++
#include <typeinfo>			//头文件
——————
	int n=123;				//具体操作
	const type_info &ninfo =typeid (n);
	cout<<ninfo.name()<<endl;    
```

**8.**结构体可以没有结构名而只有变量名，如：（类比一下，类定义也可以）

```C++
struct {
	char name[20];
	int age;
	char address[20];
}stu;
=====
//类同理
class {
public :
    int year;
    char sex;
private :
    void addmem();
}stu1,stu2;    
```

**9.**关于for 循环中“：”的使用：

```c++
int arr[10+5];
for (int i=0;i<=9;i++){
    arr[i]=i; 
} 

for (int i : arr){			
    cout<<i<<endl;			//注意：此处使用“i”，相当于是arr[i]!
}
//相当于下
for (int i=0;i<10+5;i++){
    cout<<arr[i];
}
```

输出的结果为0到9加上一群乱码，共15个数。即遍历arr数组中的每一个元素（无论是否赋值）。char数组类似（且单独的for (auto cha : "hello world")也行）。

**9.**学会将代码更美观(from haowen)，如下：

```c++
int a, b, c;		//在多个定义时，“,”后加个空格；或者说多个","后都加空格
int ans = 0;		//"="两侧加空格
for (int i = 1; i <= 123; i++)		//在";"后加空格，在"<=""=="等比较符号前后加空格
a = a + b;			//"+""-"等运算符前后加空格
cout << ans;		//"<<"前后加空格
if (a > 1 || b < 123)				//在逻辑运算符"||"等前后加空格
for (int i = 1; i <= 123; i++) {	//在for的条件括号()两边加空格
    cout << i << endl;	
}
	cout << "i = " << i;	// i = 4	这样的注释相当于是说明会输出的内容，一种注释方式
```

**10.**一个string 的name可以作为输入变量，可以多次被输入，从而简化代码。其他类型的也是如此。如：

```c++
string name;
for (int i = 1; i <= 123; i++) {
    cin << name;
    oop.addStudent(name);
}
```

11.通过while循环可实现操作系统/命令行中命令的不断输入，如：

```c++
while (cin >> order) {
    if (order == "xxx") {		//具体的命令
        ...
    }
    else if (...) {
        ...
    }
    ...
    else if (...) {				//设置结束的语句
        break;
    }
}
```

12.神奇的初始化方式：

```c++
double b {0.3};
int a {12};
//上述定义相当于
double b = 0.3;
int a = 12;
```

13.关于引用：某一变量的==别名==，即该内存空间有多个名字，每个名字都指向这一变量。1.多个名字地址相同（因变量相同） 2.多个名字可相互替代，数值相同 3.引用不可中途修改 4.可代替结构成员，从而简化 5.相当于一个int类型

```c++
//引用和指针的区别：
int & v0 = v1;	//引用
int *v0 = &v1;	//指针
//关于函数传参时
int ADD(int x[]);
int ADD(int *x);	//以上这两个是一维指针传参

int ADD(int& x);	//这个是传进去一个引用参数，相当于传进去一个int值!!!(所以swap函数这么传才能够实现交换)
======
//拓一下引用传参（是真正可以改变传进去的变量值，而不仅仅是改变形参）如：
void set_time(Time& t)	//需要在形参对应的数据结构后加上‘&’ 
{ 
cin>>t.hour;
cin>>t.minute;
cin>>t.sec;
} 
//对应的声明是：
void set_time(Time& t);
or void set_time(Time&);
```

14.引用也可以作为函数的返回值。这个返回值也指向某一个内存空间（但不是函数中定义的临时变量或者形式参数）。如下：

```c++
int a[3] = {1, 2, 3};
int& geta(int i) {
    return a[i];
}
======
	cout << geta(1);	//geta(1)相当于a[1]的引用，对geta(1)操作，相当于对a[1]操作
	geta(1) ++;			//a[1]也++
```

15.面向对象：一种写大型工程代码的语法，使书写时更符合人的思维，更为简化和清晰

16.在类中，如果不声明是private 还是public，那么默认为private。

17.类的指针也和结构体的指针相似，可以用"->"来访问，如下：

```c++
class A {
public:	
	int num1;
	int num2;
	int num3;
	void doadd() {
		num3 = num1 + num2;
	}
};
=====
    A a1,*p;
	p = &a1;
	cout << p -> num1;	//或(*p).num1
```

18.关于enum的一些用法(一种有限选择式数据类型)：

```c++
enum GameResult {WIN, LOSE, TIE, CANSEL}; 
//->有点像struct结构体，其中大括号内是可能的赋值。其中若无特殊赋值，则这几个赋值依次对应int中的0,1,2,3
GameResult res1 = WIN; 		//类似int a;
GameResult res2 = TIE;
for (int i = res1; i <= res2; i++ ){
    cout << "res_now is " << i << endl;
} 							//此处强制转换
```

18.面向对象的思想感觉是把能捏成块的全部捏成块，从而简化思路，如下(把输入输出也捏成一块)：

```c++
void set_time(Time& t) //定义函数set_time，形参t是引用变量
{ 
cin>>t.hour;//输入设定的时间
cin>>t.minute;
cin>>t.sec;
} 
```

19.类的成员函数在定义时可以直接调用类中的数据成员，而不用再在形参中定义。

20.有意思的现象。粗略解释为定义类时，先执行的操作是，进行函数的声明（即便有定义）和各种数据的定义（无赋值），再进行函数的定义和数据变量的赋值等操作（？）：

```c++
class Time //定义Time类
{
public :	
	Time () {			//这里hour等尚未定义却还可以使用
		hour = 1;
		minute = 1;
		sec = 1;
	}
private :
	int hour;
	int minute = sec;	//这里也同样可以，，，（但主函数里这么干不行）	
	int sec;};
```

21.关于这个问题的答案：因为类中是一般化的构造函数，在定义时进行初始化。（问题见下）

```c++
class Student{
    ...
};
======
    Student stu (name);		//这是定义，同时也是初始化
=====
    Student stu;
	Student stu();
=====
    Student stu1 = stu2;
```

注：若是一般化的构造函数（不是缺省参数）且没有初始值，如第7、8行。都不能编过。但是如果是第10行那样，拿另一个来进行初始化则可以编过。	(这个问题可以通过构造函数重载来解决，但是只能初始化一次)

22.学会用参数初始化表：

```c++
Time::Time (int h,int m,int s) :hour(h), minute(m), sec(s) {}
```

23.exit函数的用法：

```c++
	exit (1);	//从此处直接结束程序。一般用于子函数中结束程序，而不用等到主函数结束。如：
void some_func () {
    ...
    exit (0);
    ...
}
```

24.通配符（*）的用法：（在Linux命令行（或一般的shell）中）

```c++
//*	匹配任意长度任意字符
//**	匹配任意级别目录(bash 4.0以上版本支持，shopt -s globstar)
//如具体的命令行中的运用：
1//git add *.txt		表示把以“.txt”结尾的文件全部加入到git的暂存仓库
2//	/etc/**/*.conf		表示"etc"文件夹的所有子文件夹中所有后缀为".conf"的文件
3// ex1.*				表示所有以"ex1."为前缀的文件
```

25.常用“global.h”来储存全局变量和全局函数的定义和声明（大项目中）；而用几个其他的cpp来储存函数。

26.可以先定义变量后声明，但好像不能先声明后定义变量？

27.命令行中编译时，“g++ -o 1.exe a.cpp b.cpp”，表示编译链接a,b两个cpp文件成为名为"1.exe"的程序

28.关于atoi函数的用法：将一个const char*数组转换成int，如果这个数组储存的是数字，就把char数组的数字转换成int类型的数字；如果含有非数字元素，则函数值为0。

```c++
	char str[] = "123456789";
	cout << atoi(str) <<endl;
	cout << sizeof (atoi(str));
//输出：
123456789
4
```

29.使用gdb的前提是得要把相应的"cpp"文件（不能是".o"）文件用下面的语句编译：

```
g++ -g ex.cpp -o ex.exe
```

然后才能使用gdb文件进行调试

30.![image-20220305161410352](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20220305161410352.png)

关于makefile的机理：1.若直接一个make，则是执行第一行。2.若是与上次编译时某一指令对应的前提和相应的前提的前提...的相关文件都没有改变，则该指令下的所有指令都执行一次。（如若无改变，则若只make，则前3条都执行）3.若是有所改变（如改变func.cpp），则只执行1、3条。（其中机理：需查找所执行的命令的所有前提，溯游至最上流，把未更新的更新，并层层往下游递进和更新该更新的。若前提是命令，则将这个命令溯游至文件）

31.可用pragma once代替ifndef（更简洁）

32.`int *p = new int (3)`: 表示 new 一个值为3的变量，p 是它的地址。（new int () 是地址）

33.以下皆可编译通过：

```c++
	auto x = new int [10];	// auto 相当于 int* 
	auto *x = new int [10]; // auto 相当于 int 
	auto x = "123"			// auto 相当于 const char *
```

33.在函数重载中的函数参数的自动类型转换上，相关的计算需要考虑好，代入参数要将数据进行转换（如1.2代入 	`int` 是1），返回值也要考虑好转换。

34.在进行编译链接时，如果自定义的某个函数只有声明没有定义，或者无法根据声明找到错误，会出现链接错误`“undefined reference”`

35.在定义类中的构造函数时，定义在`public`下。

36.类中不能对`char`类型数组直接用指针赋值（如`str1 = str2`）,需要调用`strcpy`函数或者逐个赋值。

37.关于类中的构造函数：

```c++
	int num1;
	int num2;
	char str[30];
	A(char* STR, int NUM1, int NUM2) :num2(NUM2), num1(NUM1) {	//可以用冒号进行部分赋值，其他部分放在函数体中，且虽然按照变量声明的顺序进行赋值，但在不造成歧义的情况下（不相互赋值），在冒号后可以随意安排顺序
        strcpy(str, STR);
    }
```

38.写一个含类的大文件套路：

​	0.关于理解一个给出的代码程序，或许我可以找个案例去代入试试来看懂？得明白才能写好代码

1. 得要有main.cpp，类.h，类函数.cpp，其他函数.cpp这几类，先建好相应的文件，写好相应的注释。

   ==补充==：边看边画，把整个程序所需的类之间的组合和继承关系给画出来，相应的接口关系也给画出来（不过似乎只有组合的类之间有接口关系？）

   （并且似乎组合关系的话只有函数是接口？而继承的话是对根据数据生成函数?）

2. 在各个文件中写好相应的包含头文件方式和相应的注释，类的头文件里写好`pragma once`。

3. 写"类.h"中的各种结构（找清楚需要的变量（主函数、分函数、和类函数）和函数）：

​		（此处小补充一部分，如果题目给出基类，则看懂基类，写好注释；再找全派生类所需的 数据/函数；最后照着基类的接口写派生类

​			还有就是关于类的组合，应该先写主类还是次类呢？

​			先定次类？或者先写一些想得到的次类，再写主类，碰到需要的次类 数据/函数 再补充次类？）

​			1.==照着基类写派生类，照着主类写组合类==，若是都有则都照着

​			2.==照着组合类和该类的应用代码（如主函数代码）写主类==

```c++
class A{    
private:
    //数据
    int a;	//注释注明功用：记录当下的时间（或者通过函数名）
    ...
        
    //函数
    void func(){}	//给出相关的声明，注释函数的作用
    ...
        
public:
    //数据同上
    
    //构造和析构函数
    A(){};	//...
    ~A(){};	//...	
    
    //其他函数同上
}
```

1. 写"类.cpp"文件：先把要写的函数逐个摆出，再详写。
2. "其他函数"：同4。

39.如果一个类中有构造函数（有不含参数的情况），则在定义一个类或者类的数组（不带参数）时，都会进行相应的初始化。类数组或可按下面进行初始化：

```c++
    A a[3] = {(1, 2), (3, 4)};	//a[2]按照默认进行初始化
```

40.字符可`ch('\0')`如此进行初始化，而char数组可以`str("hello")`如此初始化。

41. `break` 语句只对 `for` 循环起作用，就是break for，而不是break if。且多重for循环时，只break含"break"的最内一层for结构

```c++
    for (int i = 1; i <= 123; i++) {
        if (2 > 1) {
            cout << "break" << 1;
            if (3 > 2) {
                cout << "break" << 2;
                break;
            }
        }
    }
```

42.只编译不链接时，头文件需要存在才能编过。在命令行中编译链接时，可以不写头文件（写也可以）（在makefile中需考虑好），只需写cpp文件。

​	纠正：makefile中编译里==不能写头文件==，会出现`pragma once in main file`的报错。

43.

```c++
void hello() {
    cout << "hello world";
}
int main () {
    hello;		//这样虽然可以编过，但却没法正常调用函数，没有输出，故而需要带括号
    return 0;
}
```

44.

```c++
//关于#ifdef DEBUG(一个宏符号)的用法：有点好用
#include <iostream>
using namespace std;

int main () {
    #ifdef DEBUG		//这之间的代码段在如下命令行定义了"-DDEBUG"才会被编译，否则当作没看见
    cout << "debuding";
    #endif
    return 0;
}

需要如下的命令行：
    g++ test2.cpp -o test2.exe -g -DDEBUG
	-D+宏名 是在命令行中进行预宏定义
```

嗯...有点问题。在多文件里面，如果是先编译后链接的话，-DDEBUG是在编译期生成的，也只作用于编译时的cpp文件。也就是说下面这行定义的DEBUG只作用于main.cpp，而不作用于其他.o文件。所以哪个cpp里面要用到DEBUG宏，就在包含该cpp的那个编译命令里写-DDEBUG。

```
g++ -std=c++11 -o main main.cpp graph.o node.o -DDEBUG -g
```

45.这种代码可以通过编译，且运行时不会出错。

```c++
class A{
private: 
    int a;
    int b;
public: 
    A(int a, int b) : a(a), b(b) {}	//不会出现重复定义或者歧义，正常运行，那么写构造函数时就非常方便了
    //嗯...可能还是不太好，可以这样：
    A(int _a, int _b) :a(_a), b(_b) {}
    void print() {
        cout << "a = " << a << " b = " << b; 
    }
};
```

46.对运算符（+）重载：

```c++
//如果是用全局函数的形式，不能访问私有变量，只能访问公有（不是成员函数），如：
class A{
    int a;
    int b;
public: 
    A(int a, int b) : a(a), b(b) {}
};
A operator+(A& a1, A& a2) {
    A new_a(a1.a + a2.a, a1.b + a2.b);	//会说私有变量无法访问
    return new_a;
}
int main () {
    A a(1, 2);
    A b(2, 3);
    A c = a + b;
    return 0;
}
//而用成员函数的形式可以规避这个问题，如：
class A{
    int a;
    int b;
public: 
    A(int a, int b) : a(a), b(b) {}
    void print() {
        cout << "a = " << a << " b = " << b; 
    }
    A operator+(A& a1) {
        A new_a(a + a1.a, b + a1.b);		//不会出现错误
        return new_a;
    }
};
int main () {
    A a(1, 2);
    A b(2, 3);
    A c = a + b;
    return 0;
}
```

47.关于函数传参中的"const ..." 类型：传入的实参不必一定是"const ..."类型的，但在函数体中使用形参时，不能改变形参的值。（对于引用来说是保护数据的一种手段）

```c++
//
int ADD(const int a, const int b) {
    return a + b;
    //a = a + b;	//修改则会报错
    //return a;
}
//
    int a = 1;
    int b = 2;
    cout << ADD(1, 2);
    cout << endl << ADD(a, b);	//可以正常传
```

48.关于单目运算符（“!”）的用法：

 null和undefined直接转化为false
 字符串：空字符串转化为false，其他全为true
 数字：0和NaN转化为false，其他全为true

49.关于"++"前缀和后缀的功能和识别。

50.重载单目运算符时只能有一个形参。

51.默认构造函数：

```C++
A() = default;
//但不一定是赋“0”,可能是随机值
```

52.析构函数中不能有任何形式的参数

53.

```c++
    int* p[10];	//一个指针的数组，每个指向一个数或者数组
    p[1] = new int [3];	//指向数组
    p[1] = new int (3);	//指向一个数
```

54.类中的静态数据成员需在类外定义，但不能在头文件中定义，可以在主函数定义。

55.关于assert（int expression）断言函数的用法，如果是表达式结果为真则不输出，如果表达式结果为假则输出表达式：

```c++
#include <cassert>	//头文件
assert(1 == 2);	//file: [filename], line 2, 1 == 2
```

56.对完型填空类题，先捋清楚整个的思路，然后再依据这个思路找清楚各个文件里需要什么，再写代码。

57.这种错误是不能有中文字符（好像注释都不能有）

```
Traceback (most recent call last):
  File "/home/local_main_judger/judge_client/uoj_judger/data/45/checker.py", line 11, in <module>
    content = f.read()
  File "/usr/lib/python3.4/encodings/ascii.py", line 26, in decode
    return codecs.ascii_decode(input, self.errors)[0]
UnicodeDecodeError: 'ascii' codec can't decode byte 0xe8 in position 107: ordinal not in range(128)
```

58.函数中定义全局变量的方式：只能用static定义，且无论使用多少次函数，都只会定义一次。（第一次用函数时定义，main结束后再析构）

59.一般的函数和 const 函数的重载也可以：

```c++
void print(int a) {}
void print const(int a) {}
//这个不会报错，因为 const 变量只能调下面那个。而一般的变量会优先调上面那个
```

60.

```c++
A::A() {}		//类外定义构造函数
A::~A() {		//类外定义析构函数
  delete p;
}
```

61.数字键盘旁边的"-"和字母键盘上的"-"不一样，，，

62.vscode编译前得保存各种文件的变更，不然就按旧的来编，，，

63.考虑移动构造函数的原理：相当于把传入的右值参数的数据整个搬给新构造的变量（通过移动数据的地址），而不是拷贝。如果考虑用 move（左值） 函数来进行移动构造，那么传入的左值的数据就被搬空了，就作废了。

```c++
class A{
    int data;
    int* buf;
public:
    A(int _data) :data(_data) {
        buf = new int [10];
    }
    void print_buf() { cout << buf << endl; }
    A(A&& a1) {
        data = a1.data;
        buf = a1.buf;
        a1.buf = nullptr;
    }
};
int main() {
    A a1(123);
    a1.print_buf();	//0x1d1710
    A a2(move(a1));
    a2.print_buf();	//0x1d1710
    a1.print_buf();	//0
    return 0;
}
```

64.自定义的自动类型转换只有单向转换，双向则需要定义两次

65.有点复杂的东西：1.此中因为有 line 8 ,所以可以从int转换成complex类型的变量（相当于一种自动类型转换？）

```c++
class Complex
{  
    public:  
    int real;  
    int imag; 
    Complex():real(0),imag(0){}
    Complex(int r, int i):real(r),imag(i){cout<<"B";}
    Complex(int r): real(r),imag(0){cout<<"A";}	
    operator int(){
        return real;
    }
    Complex operator+(const Complex& c){
        return Complex(this->real+c.real,this->imag+c.imag);
    } 
};  

int main()
{
    Complex c(3,4);
    c=c+3.4;	//两种转换方式：1.c转换成int，再相加按照“A”构造一个c`,赋值给c; 2.3.4转换成c`,c + c`,再赋值给c
    cout<<c.real+c.imag;
    return 0;
}
```

66.继承的访问权限问题：

​	1.基类中的private数据对派生类内外皆不可访问。

​	2.对其他的数据，public 继承则对派生类的权限与对基类的权限相同；private 和 protected 继承的所有权限皆在类内，且类内皆可。

67.

```c++
      class A {
      public:
          int a=1;
      protected:
          int b=2;
      private:
          int c=3;
      };

      class B {
      public:
          int d=4;
      protected:
          int b=5;
      private:
          int e=6;
      };
      
      class C: public A, private B{
      public:
          void print() {
              cout << A::b << endl;	//当继承两个类，且两个类中有重名变量/函数时，需要加"T::"来说明
          }
      };
```

68.

```c++
class Base{
public:
    void func() { cout << "1" << endl; }
    void func(int data) { cout << "2" << endl;}
};
class Derive :public Base{
public:
    using Base::func;	//使用这一基类函数的延续时，基类函数与派生类函数相比，派生类里没有的则沿用基类的，若派生类里有一样的则按派生类里的进行
    void func(int data) { cout << "3" << endl;}
    void func(double data) {cout << "4" << endl;}
};
int main() {
    Derive d1;
    d1.func();	//	1
    d1.func(1);	//	3
    d1.func(1.0);	//	4	
    return 0;
}
```

69.

```c++
class Animal{
    public:
    //Animal(){};
    Animal(int d){};
    void move(double d) { cout << "Animal move" << d << "km\n"; }
    void eat(double d) { cout << "Animal eat " << d << "g food\n"; }
};
class Bird: public Animal{	//构造派生类对象时，会调用基类对象的构造函数；如果没有显式说明，则调用基类的默认构造函数，显示说明则按显示说明中调用的构造函数进行调用；此处因为基类的默认构造函数已经被implicitly deleted，故而无法调用，编译出错
    public:
    using Animal::move;
    void move() { cout << "Bird move"; }
    void move(int i) { cout << "Bird move" << i << "m\n"; }
};

//显示说明则需要自己去写，如下：
class Bird: public Animal{
public:
    Bird(int i) : Animal(i) {}	//显式调用Animal(int i)
    -----
}
```

70.派生类的构造函数的成员初始化列表中，不可以包含基类数据成员的初始化。

​	原因：1基类中private的数据无法访问，2可能会出现调默认构造函数和自行初始化的矛盾

71.

```c++
class A{
  int data;
public:
  A() :data(1) {}
  ~A() {cout << "~A()";}
};
class B{
  A* array;
public:
  B() :array(new A [10]) {}
  ~B() { cout << "~B()"; delete[] array;}	//这里"delete[] array"会调用A的析构函数
};
//输出：~B()~A()~A()~A()~A()~A()~A()~A()~A()~A()~A()
```

71.

```c++
Node& Vector::operator [] (int pos);	//运算符重载时，格式是这样，“Vector”放中间
//记忆：“Vector::”都放在函数名字前
```

72.关于内存泄漏这件事：

在类中移动指针时，需要考虑是否会存在某些数据因为各种原因（如失去指针）而无法删除的情况，如：

目前遇到的赋值运算符和更换指针的函数里面出现的比较多

```c++
Vector & Vector::operator=(Vector && other){    //移动赋值函数
    capacity = other.capacity;
    len = other.len;
    if (array != other.array) {
        //delete[] array;		//不写这句会出现内存泄漏，原array指定的数据无法被删除
        array = other.array;
        other.array = nullptr;
    }
    return *this;
}
```

73.假如使用了 move 函数，则改换之后的变量的地址换成了 nullptr ，则在析构时则不删除任何东西。

```c++
void Vector::dilatation() { //将数组容量翻倍
    capacity = capacity * 2;
    Node* tmp = new Node [capacity];
    for (int i = 0; i < len; i++) {
        tmp[i] = move(array[i]);
    }
    delete[] array;	//如这里delete时，则被move的变量则不理会，而不会出现删到tmp的情况
    array = tmp;
    tmp = nullptr;
}
```

74.基类中的private数据，无论是什么数据类型，都不能被派生类调用，只能通过基类的protected和public接口间接调用

75.在写有关继承的基类和派生类时，应该先==弄清楚基类的接口是什么，函数功能是什么，写好注释==。很多时候为了防止代码冗余，不会设置重复的数据和函数。同时，基类也有着作为一个模板的功能，便于检查函数是否都具备了。

在具体书写时，还是得要按先前那样分析题目，看是否有数据或者函数遗漏的地方。或者如果太繁琐了就采取先写后补的思路。

==在写派生类时，看着接口写派生类函数。同样需考虑派生类需要什么样的函数==。可能一个重要原则是尽量不去 重载/覆盖 派生类的函数？如果是虚函数再 重载/覆盖 ？

76.看一个派生类，要同时看基类和派生类的代码。

77.组合是接口用于外，继承更多的是接口用于内。

78.一般不改基类数据/函数。

79.

```c++
Derived(int i) :Base(i) {}	//有趣的初始化写法
```

80.

```c++
SomberWeapon::SomberWeapon(string name) :Weapon(name){
    //Weapon(name); 错误
    cout << "Somber weapon " << get_name() << " was created." << endl;
}//派生类中的构造函数，对基类构造函数的调用：不能在函数体中调用，可以1在函数初始化列表中显式调用，2不用写，由编译器自动合成默认构造函数，在派生类构造函数前执行

SomberWeapon::~SomberWeapon() {
    //~Weapon(); 错误
    if (get_level() == 0) {
        cout << "Somber weapon " << get_name() << " was destroyed." << endl;
    }
    if (get_level() != 0) {
        cout << "Somber weapon " << get_name() << "+" << get_level() << " was destroyed." << endl;
    }
}//派生类析构不能写基类的析构进去，默认先派生类后基类
```

81.这样定义了一个指针数组，好像已经把数组的变量给初始化了？已经可以用指针访问了

```c++
A* array[100];	//定义一个数组，元素为100个指向A的指针
array[1] = &obj;	//可以直接给地址
array[2] = new A [10];	//可以new一个数组
array[3] = new A (1, 2); //可以new一个变量
```

82.

```c++
void T(T&& a);	//构造函数的参数是"T&& a"
void func(const T& a) {	
    T a1 = move(a);	//move(a)的类型是"const T&& a",不符合
}
```

83.

```c++
if (ptr) {	//如果ptr为nullptr或者已经被delete，则式子值为假，否则值为真
    cout << "not_null";
}
```

84.嗯...一个非常有意思的问题：`A *arr[100]`所选定的指针，即便A没有默认构造函数（已经有显式的了），即便arr指针没有指定变量，arr还是会指着一个A的变量，并且构造方式不明；

而又可以通过`arr[1] = new A (Parameters)`的方式把arr[1]构造出来。

85.虽然我们通过基类和派生类之间的种种繁琐的方式把数据给保护了起来，但是还是可以拷贝来改变其变量...

所以在这一层上，应该通过使用`const &`的方式来保护数据？

86.类内的非常量型共有类static类型数据可以在全局中访问和修改。

87.不是`main.cpp`文件的其他函数cpp文件也可以写函数，来简化代码，让思路更清晰。

88.对会出现数据换算的比较麻烦和繁琐的活，得纸上写清楚过程和公式，找几个样例测试过再写，不然出bug的概率太大了。

89.得学一下怎么实现代码重用了，不然写得太麻烦了：函数，模板，虚函数重载，和大体简化的思路。

90.对没有虚函数的类，相应的对象没有虚函数表（即没有一个指向虚函数表的指针，而一个指针为8个字节）。有虚函数的类，无论是一个还是多个，都有一个虚函数指针，即大小加8个字节。

91.

```c++
class Base{
public:
    Base() { func1(); }
    virtual void func1() {cout << "Base" << endl; }
};
Base a(); //执行顺序为，先执行Base，把所有东西都构造出来（比如虚函数），再执行函数体内的内容即 func1	
```

92.关于`final`的用法，用在类时表示该类不能被继承；用在函数时，该函数需是虚函数，且该函数不能再重写隐藏

93.虽然基类的指针可以访问派生类中的应有基函数，但是如果用派生类的对象或者指针也只能访问到派生类里的函数，基类的同函数被重写覆盖，基类的同名参数不同函数也被重写隐藏（如果不使用`using`关键词时）

94.纯虚函数并非没有函数体，只是在类内声明时要加一个`= 0`，再在类外实现。

95.不能把基类对象赋给派生类的指针，编译错误，会出现非法内存访问

96.构造函数：给指针new数组，并赋初值

97.类内虚函数在类外定义不能带`virtual`关键字

98.一个cpp里定义的函数如果不在另一个cpp里声明，则这个函数只是局部函数。（尽管如此，多个cpp里还是不能有重名同类函数，哪怕只是局部函数（确保无二义性）

99.一个优秀的注释，看着就可以把代码给码出来。可以考虑多用 调用、判断 等词语

100.一定得先看懂这个 程序/函数 ，再写代码qwq，来自某段看漏了一个参数导致怎么都捣鼓不出来的情况、

101.一个动态指针无法自行获取自己的数组大小，必得配一个argument num

# 出现的问题

1.移动赋值运算符，对右值，除了将指针变成 nullptr ，还需要将其他数据变成 0 吗？

2.在写派生类时，看着接口写派生类函数。同样需考虑派生类需要什么样的函数。可能一个重要原则是尽量不去改派生类的函数？如果是虚函数再改？

3.还有就是关于类的组合，应该先写主类还是次类呢？

​		先定次类？

4.

