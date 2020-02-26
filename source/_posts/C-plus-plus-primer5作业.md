---
title: C_plus_plus_primer5作业
date: 2019-12-05 14:24:31
tags:
	- C
	- C++
---

## 2

### 2.3

```c++
int main()
{
	unsigned u = 10, u2 = 42;
	std::cout << u2 - u << std::endl; // 32
	std::cout << u - u2 << std::endl; // 10 - 42 = 10 + (2^32 - 42) = 4294967264

	int i = 10, i2 = 42;
	std::cout << i2-i << std::endl; // 32
    std::cout << i-i2 << std::endl; // -32
	std::cout << i - u << std::endl; // 整型转化为无符号 0
	std::cout << u - i << std::endl; // 整形转化位无符号 0
}
```



<!-- more -->

### 2.5

- （a）'a' ，L'a'  ，“ a” ，L“ a”  
- （b）10、10u，10L，10uL，012、0xC
- （c）3.14、3.14f，3.14L
- （d）10、10u，10.、10e-2

（a）：1字节字符，2字节字符，每个字符1字节的字符串，每个字符2字节的字符串

（b）：4个字节十进制，无符号4个字节十进制，长4个字节十进制，无符号长4个字节十进制，八进制，十六进制。

（c）：8个字节 double ，4个字节 float， 8个字节 long double。

（d）：十进制，无符号十进制，双精度  8字节，双精度 8字节。



### 2.6

```c++
int month = 9, day = 7;  // 整形
int month = 09, day = 07; // 被认为8进制 前者编译器声明无效 大于8
```

### 2.7

- (a) "Who goes with F\145rgus?\012"
- (b) 3.14e1L 
- (c) 1024f
- (d) 3.14L



（a）：Who goes with Fergus?  `\（8进制） or \x（16进制） ` 

（b）：long double

（c）：无效 必须小数

（d）：long double



```c++
std::cout << 2 << "\115\012";
std::cout << 2 << "\t\115\012";
```





### 2.10

> What are the initial values, if any, of each of the following variables?

```c++
std::string global_str; // 空
int global_int; // 0
int main()
{
   	// 未初始化 均不能使用
    
    int local_int; 
    std::string local_str;
}
```

### 2.11

- (a) extern int ix = 1024;
- (b) int iy;
- (c) extern int iz;

(a): definition. (b): definition. (c): declaration.



### 2.12

- (a) int double = 3.14 ; X
- (b) int _; 
- (c) int catch-22; X
- (d) int 1_or_2 = 1;  X
- (e) double Double = 3.14;

### 2.13


> What is the value of j in the following program?

```c++
int i = 42;
int main()
{
    int i = 100; //100
    int j = i; //100
}
```



### 2.14



> Is the following program legal? If so, what values are printed?

```c++
int i = 100, sum = 0;
for (int i = 0; i != 10; ++i)
    sum += i;
std::cout << i << " " << sum << std::endl; // 100 45 
```



### 2.15

- (a) int ival = 1.01; 
- (b) int &rval1 = 1.01;  X
- (c) int &rval2 = ival; 
- (d) int &rval3; X



### 2.16

> Which, if any, of the following assignments are invalid? If they are valid, explain what they do.

```
int i = 0, &r1 = i; double d = 0, &r2 = d;
```

- (a) r2 = 3.14159;
- (b) r2 = r1;
- (c) i = r2;
- (d) r1 = d;

### 2.17

> What does the following code print?

```c++
int i, &ri = i;
i = 5; ri = 10;
std::cout << i << " " << ri << std::endl; // 10 10
```

### 2.18

> Write code to change the value of a pointer. Write code to change the value to which the pointer points.

```c
int a = 0, b = 1;
int *p1 = &a, *p2 = p1;

// change the value of a pointer.
p1 = &b;
// change the value to which the pointer points
*p2 = b;
```



### 2.19

> Explain the key differences between pointers and references.

#### `definition:`

指针“指向”任何其他类型。

引用是对象的“另一个名称”。

#### `key difference:`

1. 引用是另一个已存在的对象的别称，指针自己本身属于对象
2. 初始化后，引用仍绑定到其初始对象。无法重新绑定引用以引用其他对象。可以分配和复制指针。
3. 引用始终获取引用最初绑定到的对象。一个指针可以在其生命周期内指向多个不同的对象。
4. a reference must be initialized. a pointer need **not be** initialized at the time it is defined.





### 2.20

> What does the following program do?

```c
int i = 42; // 定义 i
int *p1 = &i; *p1 = *p1 * *p1; // 定义一个指针指向i ，i的值等于 i*i
```



### 2.21

> Explain each of the following definitions. Indicate whether any are illegal and, if so, why.

```c
int i = 0;
```

- (a) double* dp = &i;  指针类型错误
- (b) int *ip = i;  初始化错误 一个是指针类型， 一个是字面量
- (c) int *p = &i; 



### 2.22

```c
if (p) // ... 如果指针不指向空
if (*p) // ... 如果指针指向的字面量不为 0
```



### 2.23

> Given a pointer p, can you determine whether p points to a valid object? If so, how? If not, why not?

否。因为需要更多信息来确定指针是否有效。

### 2.24

> Why is the initialization of p legal but that of lp illegal?

```c
int i = 42;
void *p = &i;  //void* 代表指向任何类型的指针
long *lp = &i;
```



### 2.25

 > Determine the types and values of each of the following variables.

  - (a) int* ip, i, &r = i;  |  ip是一个指针 指向int 类型， i是一个 int 类型的值， r是i的引用
  - (b) int i, *ip = 0;  |  i 是一个整形， ip是一个空指针
  - (c) int* ip, ip2; | ip 是一个空指针， ip2是一个整形



### `2.26`

> Which of the following are legal? For those that are illegal, explain why.

```c
const int buf;      
int cnt = 0;        
const int sz = cnt; 
++cnt;              
++sz;     // 不合法 常量
```



### 2.27



> Which of the following initializations are legal? Explain why.

```c
int i = -1, &r = 0;         // r必须引用一个对象
int *const p2 = &i2;        // 一个常量指针指向 i2
const int i = -1, &r = 0;   // legal.
const int *const p3 = &i2;  // legal.
const int *p1 = &i2;        // legal
const int &const r2;        // illegal, r2 is a reference that cannot be const.
const int i2 = i, &r = i;   // legal.
```





### 2.28

> Explain the following definitions. Identify any that are illegal.

```c
int i, *const cp;       // 整形i和 指向int 型的指针cp ， 不合法 cp必须被初始化 
int *p1, *const p2;     // illegal, p2 must initialize.
const int ic, &r = ic;  // illegal, ic must initialize.
const int *const p3;    // illegal, p3 must initialize.
const int *p;           // legal.指针指向一个的整形，不可修改
```



### 2.29

> Uing the variables in the previous exercise, which of the following assignments are legal? Explain why.

```c
i = ic;     // legal.
p1 = p3;    // 不合法，p1是整形指针， p3 是常量整形指针
p1 = &ic;   // 不合法，ic是常量整形
p3 = &ic;   // 不合法 , p3 是常量指针
p2 = p1;    // 不合法 ， p2 是常来给你指针
ic = *p3;   // 不合法， ic 是常量整形
```



### 2.30



> For each of the following declarations indicate whether the object being declared has top-level or low-level const.

```c
const int v2 = 0; int v1 = v2;
int *p1 = &v1, &r1 = v1;
const int *p2 = &v2, *const p3 = &i, &r2 = v2; // p3是顶层 + 底层
```



### 2.31

> Given the declarations in the previous exercise determine whether the following assignments are legal. Explain how the top-level or low-level const applies in each case.

```c
r1 = v2; // 合法 普通赋值
p1 = p2; // 不合法 p1 是整形指针 ,p2是 常量整形指针
p2 = p1; // 合法 ，反之可以
p1 = p3; // 不合法 ，同2
p2 = p3; // 合法
```



### 2.32

> Is the following code legal or not? If not, how might you make it legal?

```c
int null = 0, *p = null; // 	int null = 0, *p = nullptr;
//// 	int null = 0, *p = &null;
```



### 2.33

```c
a=42; // set 42 to int a.
b=42; // set 42 to int b.
c=42; // set 42 to int c.
d=42; // ERROR, d is an int *. correct: *d = 42;
e=42; // ERROR, e is an const int *. correct: e = &c;
g=42; // ERROR, g is a const int& that is bound to ci.
```





### 2.34

> Write a program containing the variables and assignments from the previous exercise. Print the variables before and after the assignments to check whether your predictions in the previous exercise were correct. If not, study the examples until you can convince yourself you know ￼￼what led you to the wrong conclusion.





### 2.35

> Determine the types deduced in each of the following definitions. Once you’ve figured out the types, write a program to see whether you were correct.

```c
const int i = 42;
auto j = i; const auto &k = i; auto *p = &i; const auto j2 = i, &k2 = i;
```

j is int. k is const int&. p is const int *. j2 is const int. k2 is const int&.



### 2.36

> Assignment is an example of an expression that yields a reference type. The type is a reference to the type of the left-hand operand. That is, if i is an int, then the type of the expression i = x is int&. Using that knowledge, determine the type and value of each variable in this code:

```c
int a = 3, b = 4;
decltype(a) c = a;
decltype((b)) d = a;
++c;
++d;
```

`c` is an int, `d` is a reference of `a`. all their value are `4`.



### 2.37

> In the following code, determine the type of each variable and the value each variable has when the code finishes:

```c
int a = 3, b = 4;
decltype(a) c = a; // int
decltype( a=b ) d = a; // int (a = b) = int&
// d 是a的别名 ， c是 a的赋值
```




 ### 2.38

> Describe the differences in type deduction between decltype and auto. Give an example of an expression where auto and decltype will deduce the same type and an example where they will deduce differing types.

The way `decltype` handles top-level const and references differs **subtly** from the way `auto` does. Another important difference between `decltype` and `auto` is that the deduction done by decltype depends on the **form** of its given expression.







### 2.39

> Compile the following program to see what happens when you forget the semicolon after a class definition. Remember the message for future reference.

```c
struct Foo { /* empty  */ } // Note: no semicolon -》 ;
int main()
{
    return 0;
}
```

报错

### 2.40

```c
struct Sale_data
{
    std::string bookNo;
    std::string bookName;
    unsigned units_sold = 0;
    double revenue = 0.0;
    double price = 0.0;
    //...
}
```



### 2.41



## 3

### 3.1

```c++
#include <iostream>

using std::cout;
using std::endl;

int main()
{
    int sum = 0;
    for (int val = 1; val <= 10; ++val) sum += val;
    cout << "Sum of 1 to 10 inclusive is " << sum << endl;
	return 0;

}
```





### 3.2

```c
#include <iostream>
#include <string>
using std::string;
using std::cout;
using std::cin;
int main()
{
	string input;
	cin >> input;
	cout << input;
	return 0;
}
```



```c++
#include <iostream>
#include <string>
using std::string;
using std::cout;
using std::cin;
int main()
{
	string input;
	getline(cin, input);
	//cin >> input;
	cout << input;
	return 0;
}
```

### 3.3

> Explain how whitespace characters are handled in the string input operator and in the `getline` function.

- For code like `is >> s`, input is separated by whitespaces while reading into string `s`.
- For code like `getline(is, s)` input is separated by newline `\n` while reading into string `s`. Other whitespaces are ignored.
- For code like `getline(is, s, delim)`input is separated by `delim` while reading into string `s`. All whitespaces are ignored.



### 3.4

```c++
#include <iostream>
#include <string>
using std::string;
using std::cout;
using std::cin;
int main()
{
	string s, s1;
	cin >> s >> s1;
	cout << (s > s1) ? s : s1;
	return 0;
}
```



### 3.5

```c++

#include <iostream>
#include <string>
using std::string;
using std::cout;
using std::cin;
using std::endl;
int main()
{
	string s;
	string temp;
	while (cin >> temp)
	{
		s += temp;
	}
	cout << s << endl;
	return 0;
}
```



### 3.6



```c++
int main()
{
	string s;
	cin >> s;
	for (auto& c : s) {
		c = 'X';
	}
	cout << s;
	return 0;
}
```

### 3.7

一个是引用，一个是赋值

### 3.8

略



### 3.9

> What does the following program do? Is it valid? If not, why not?

```c++
string s;
cout << s[0] << endl;
```

可以运行 但不显示



### 3.10

```c++
int main()
{
	string s;
	cin >> s;
	//cout << s[0] <<"1"<< endl;
	
	for (auto& c : s) {
		if (!ispunct(c))
		{
			cout << c;
		}
	}
	return 0;
}
```





### 3.11

> Is the following range for legal? If so, what is the type of c?

```c++
const string s = "Keep out!";
for (auto &c /*const char &c*/ : s){ /*... */ }
```

合法



### 3.12

> Which, if any, of the following vector definitions are in error? For those that are legal, explain what the definition does. For those that are not legal, explain why they are illegal.

```c++
vector<vector<int>> ivec;         // legal(c++11), vectors.
vector<string> svec = ivec;       // illegal, different type.
vector<string> svec(10, "null");  // legal, vector have 10 strings: "null".
```





### 3.13

```c++
vector<int> v1;         // size:0,  no values.
vector<int> v2(10);     // size:10, value:0
vector<int> v3(10, 42); // size:10, value:42
vector<int> v4{ 10 };     // size:1,  value:10
vector<int> v5{ 10, 42 }; // size:2,  value:10, 42
vector<string> v6{ 10 };  // size:10, value:""
vector<string> v7{ 10, "hi" };  // size:10, value:"hi"
```





### 3.14

> Read a sequence of ints from cin and store those values in a vector.

```c++
int main()
{
	vector<int> v;

	int in;
	while (cin>> in)
	{
		v.push_back(in);
	}
	return 0;
}

```





### 3.23



```c++
int main()
{
	vector<int> v(10);
	for (auto it = v.begin(); it != v.end(); it++) {
		*it = *it * 2;
	}
	return 0;
}
```



### 3.26

> In the binary search program on page 112, why did we write `mid=beg+(end-beg)/2;` instead of `mid=(beg+end) /2;`?

迭代器没有 + 运算符



### 3.27 

> Assuming txt_size is a function that takes no arguments and returns an int value, which of the following definitions are illegal? Explain why.

```c++
unsigned buf_size = 1024;

int ia[buf_size];   // 不是常量表达式 ，意味着在编译期间不能确定大小
int ia[4 * 7 - 14]; // 合法
int ia[txt_size()]; // 不合法 除非 txt_size() 是constexpr
char st[11] = "fundamental";  // 不合法，11的数组只能容纳10的字符串 额外有个\0
```



### 3.28

> What are the values in the following arrays?

```c++
string sa[10];      //all elements are empty strings
int ia[10];         //all elements are 0 全局变量默认为0

int main() 
{
    string sa2[10]; //all elements are empty strings
    int ia2[10];    //all elements are undefined 局部变量默认未定义
}
```





### 3.29

> List some of the drawbacks of using an array instead of a vector.

1. 大小在编译时期确定
2. 没有提供相应的功能
3. 有bug隐患





### 3.30

> Identify the indexing errors in the following code:

```c++
constexpr size_t array_size = 10;
int ia[array_size];
for (size_t ix = 1; ix <= array_size /* < */; ++ix)
      ia[ix] = ix;
```



### 3.31

```c
const int arr_size = 10;
int a[arr_size];

for (size_t i = 0; i != arr_size; i++)
{
	a[i] = i;
}
```


### 3.33

值未初始化



### 3.34

> Given that p1 and p2 point to elements in the same array, what does the following code do? Are there values of p1 or p2 that make this code illegal?

```c++
p1 += p2 - p1;
```

- It moves `p1` with the offset `p2 - p1`. After this statement, `p1` and `p2` points to the same address.
- Any legal value p1, p2 make this code legal.





### 3.35

```c++
int main()
{
    int size = 10;
    int arr[size];
    for (auto ptr = arr; ptr != arr + size; ++ptr) *ptr = 0;
	return 0;
}
```



### 3.36

```c++
int main()
{

    vector<int> vec1 = { 0, 1, 2 };
    vector<int> vec2 = { 0, 1, 2 };

    if (vec1 == vec2)
        cout << "The two vectors are equal." << endl;
    else
        cout << "The two vectors are not equal." << endl;

    return 0;
}
```



### 3.37

> What does the following program do?

```c++
const char ca[] = { 'h', 'e', 'l', 'l', 'o' };
const char *cp = ca; 
while (*cp) { 
    cout << *cp << endl;
    ++cp;
}
```

程序会一直打印到有\0为止



### 3.38

> In this section, we noted that it was not only illegal but meaningless to try to add two pointers. Why would adding two pointers be meaningless?

相减表示指针的偏移量

相加很难表示什么



### 3.39

略

### 3.40



```c++
int main()
{

	char s[50] ="hello", s1[50]= "world";
	//strcpy_s(s, s1); 数组赋值
	strcat_s(s, s1); // 数组添加
	cout << s;
	return 0;
}
```



### 3.41

```c++
int main()
{
    int arr[] = { 0, 1, 2, 3, 4, 5, 6, 7, 8, 9 };
    vector<int> v(begin(arr), end(arr));
    return 0;
```





### 3.42



### 3.43



```c++

int main()
{

	int ia[3][4] = {
		{0,1,2,3},
		{4,5,6,7},
		{8,9,10,11}
	};
    // 版本1
	for (int(&row)[4] : ia )
	{
		for (int col : row) {
			cout << col << endl;
		}
	}

	//版本3
	for (int (*p)[4]/*p是一个指针，指向4个int类型的数组 */  = ia ;  p != ia+3; ++p)
	{
		for (int* q = *p; q != *p + 4; ++q) {
			cout << *q;
		}
	}


	
	return 0;
}
```





### 3.44

```c++
using int_array = int[4];
int main()
{

	int ia[3][4] = {
		{0,1,2,3},
		{4,5,6,7},
		{8,9,10,11}
	};
	// 版本1
	for (int_array&row: ia)
	{
		for (int col: row)
		{
			cout << col << endl;
		}
	}

    // 版本3
	for (int_array *p = ia /*p是一个指向 int[4] 类型的指针*/ ; p != ia + 3; p++)
	{
		for (int* q = *p; q != *p + 4; q++) {
			cout << *q;
		}
	}

	
	return 0;
}

```



### 3.45

```c++
int main()
{

	int ia[3][4] = {
		{0,1,2,3},
		{4,5,6,7},
		{8,9,10,11}
	};
	for (auto&row: ia)
	{
		for (auto col: row)
		{
			cout << col << endl;
		}
	}

	for (auto *p = ia ; p != ia + 3; p++)
	{
		for (auto * q = *p; q != *p + 4; q++) {
			cout << *q;
		}
	}

	
	return 0;
}
```





## 4

### 4.6

> Write an expression to determine whether an int value is even or odd.

```c
i  &  0x1 // 奇数true 偶数false
```



### 4.7

> What does overflow mean? Show three expressions that will overflow.

计算机中任何值都是采用补码， 如果 补码增大到占用符号位 

```c++
int main(){
    
    int a = INT_MAX + 1;    // 0x01111.. + 1
    int a = INT_MIN - 1;    // 0x10000.. - 1
    
}
```



### 4.8

> Explain when operands are evaluated in the logical `AND`, logical `OR`, and equality operators.

逻辑AND：仅当左侧为true时，才对第二个操作数求值。

 逻辑OR：仅当左侧为false时，才对第二个操作数求值

 等号运算符==：未定义。



### 4.9

> Explain the behavior of the condition in the following if:

```c++
const char *cp = "Hello World";
if (cp && *cp) // 如果cp不为空指针 并且 *cp 不为 '\0'
```







### 4.10



> Write the condition for a while loop that would read ints from the standard input and stop when the value read is equal to 42.





```c++
int main()
{
    int input;
    while (cin >> input)
    {
        if (input == 42) {
            break;
        }
        cout << input;
    }

    return 0;
}
```


### 4.11

> Write an expression that tests four values, a, b, c, and d, and ensures that a is greater than b, which is greater than c, which is greater than d.

```
a>b && b>c && c>d
```



### 4.12

> Assuming `i`, `j`, and `k` are all ints, explain what `i != j < k` means.

`i != j < k` is equivalent to `i != (j < k)`.



### 4.13

> What are the values of i and d after each assignment?

```c++
int i;   double d;
d = i = 3.5; // i = 3, d = 3.0
i = d = 3.5; // d = 3.5, i = 3
```



### 4.14

> Explain what happens in each of the if tests:

```c++
if (42 = i)   // complie error: expression is not assignable 赋值运算错误
if (i = 42)   // true.
```





### 4.15

> The following assignment is illegal. Why? How would you correct it?

```c++
double dval; int ival; int *pi;
dval = ival = pi = 0;
// pi is a pointer to int.
// can not assign to 'int' from type 'int *'
// correct it:
dval = ival = 0;
pi = 0;
```





### 4.16

> Although the following are legal, they probably do not behave as the programmer expects. Why? Rewrite the expressions as you think they should be.

```c++
if (p = getPtr() != 0)
if (i = 1024)
// why? always true. use an assigment as a condition.
// correct it
if ((p=getPtr()) != 0)
if (i == 1024)
```





### 4.17

> Explain the difference between prefix and postfix increment.



```c
int i = 1, j;
j = i++;
```

Here value of `j = 1` but `i = 2`. Here value of `i` will be assigned to `j` first then `i` will be incremented.





### 4.18

> What would happen if the while loop on page 132 that prints the elements from a vector used the prefix increment operator?

指向下一个值



### 4.19

> Given that ptr points to an int, that vec is a vector, and that ival is an int, explain the behavior of each of these expressions. Which, if any, are likely to be incorrect? Why? How might each be corrected?



```c++
ptr != 0 && *ptr++  // 检查ptr指针 并且 检查指针指向的值
ival++ && ival // check ival, and then check ival+1 whether equal zero.
vec[ival++] <= vec[ival] // 比较当前和下一个
```


### 4.20

> Assuming that iter is a vector::iterator, indicate which, if any, of the following expressions are legal. Explain the behavior of the legal expressions and why those that aren’t legal are in error.





```c++
*iter++;  // return *iter, then ++iter.
(*iter)++;  // 增值
*iter.empty() // 不合法 指针没有empty成员变量
iter->empty();  // indicate the iter' value whether empty.
++*iter;        // legal, string have not increment.
iter++->empty();//  指针没有empty成员变量
```



### 4.21

```c++
int main()
{
    vector<int> ivec{ 1, 2, 3, 4, 5, 6, 7, 8, 9 };

    for (auto i : ivec) {
        cout << ((i & 0x1) ? i * 2 : i) << " ";
    }
    cout << endl;
    
    return 0;
}
```



### 4.23

> The following expression fails to compile due to operator precedence. Using Table 4.12 (p. 166), explain why it fails. How would you fix it?

```c++
string s = "word";
string pl = s + s[s.size() - 1] == 's' ? "" : "s" ;
```



after:

```c++
string s = "word";
string pl = s + ( s[s.size() - 1] == 's' ?  "" : "s");
```



### 4.28

| 类型 | 大小(bytes) |
| ---- | ---- |
|  void:nullptr_t   |   4   |
| bool | 1 |
|bool:|1 |
|char:|1 |
|wchar_t:|2 |
|char16_t:|2 |
|char32_t:|4 |
|short:|2 |
|int:|4 |
|long:|4 |
|long long:|8|
|float:|4|
|double:|8|
|long double:|8|
|int8_t:|1|
|uint8_t:|1|
|int16_t:|2|
|uint16_t:|2|
|int32_t:|4|
|uint32_t:|4|
|int64_t:|8|
|uint64_t:|8|



### 4.29

> Predict the output of the following code and explain your reasoning. Now run the program. Is the output what you expected? If not, figure out why.



```c++
int main()
{
	int x[10];
	int* p = x;
	cout << sizeof(x) / sizeof(*x) << endl; // 10:  40 /4
	cout << sizeof(p) / sizeof(*p) << endl; // 1： 指针大小为4B ，*p 为数组第一个整形 4B 
	return 0;
}
```



### 4.32

> Explain the following loop.

```c++
constexpr int size = 5;
int ia[size] = { 1, 2, 3, 4, 5 }; 
for (int *ptr = ia, ix = 0;
    ix != size && ptr != ia+size;
    ++ix, ++ptr) { /* ... */ } // 遍历数组
```





### 4.33

> Using Table 4.12 (p. 166) explain what the following expression does:

```c++
someValue ? ++x, ++y : --x, --y // (someValue ? ++x, ++y : --x), --y
```





### 4.34

> Given the variable definitions in this section, explain what conversions take place in the following expressions: (a) if (fval) (b) dval = fval + ival; (c) dval + ival * cval; Remember that you may need to consider the associativity of the operators.



```c++
if (fval) // fval converted to bool
dval = fval + ival;// float to  double, int to float to double
dval + ival * cval; //int to double ， c to int to double
```





### 4.35

> Given the following definitions,

```c++
char cval; int ival; unsigned int ui; float fval; double dval;
```

identify the implicit type conversions, if any, taking place:

```c++
cval = 'a' + 3; // a to int to char
fval = ui - ival * 1.0; // ival to double ,ui to double, to float
dval = ui * fval; // ui to float  to double
cval = ival + fval + dval;  // i to f to d
```



[^总结]: 从小往大转: char < int < unsigned int < float < double



### 4.36



> Assuming i is an int and d is a double write the expression i *= d so that it does integral, rather than floating-point, multiplication.

```c++
i = i * static_cast<int>(d)
```



### 4.37

> Rewrite each of the following old-style casts to use a named cast:



```c++
int i; double d; const string *ps; char *pc; void *pv;
pv = (void*)ps; // pv = const_cast<string*>(ps);
i = int(*pc);   // i = static_cast<int>(*pc);
pv = &d;        // pv = static_cast<void*>(&d);
pc = (char*)pv; // pc = static_cast<char*>(pv);
```





### 4.38

> Explain the following expression:

```c++
double slope = static_cast<double>(j/i); // int to double
```





## 6



### 6.2



```c
(a) string f() {  // return should be string, not int
          string s;
          // ...
          return s;
    }
(b) f2(int i) { /* ... */ }  // function needs return type
(c) int calc(int v1, int v1) { /* ... */ }  // 参数名重复
(d) double square (double x)  return x * x;   // 缺少括号
```





### 6.9



```c++
// Chapter6.h
int fact(int val);
```



```c++
// fact.cc
#include "Chapter6.h";
int fact(int val) {
	return 0;
};
```



```c++
// .cpp
#include "Chapter6.h"
int main()
{
	cout << fact(1);
	return 0;
}
```



### 6.10



```c++
void swap(int* a, int* b) {
	int p = *a;
	*a = *b;
	*b = *p;
}
```





### 6.16

```c++
bool is_empty(const string& s) { return s.empty(); }
```

添加const





### 6.19

(a) illegal, only one parameter. (b) legal. (c) legal. (d) legal.





### 6.22

```c++
void swap(int*& lft, int*& rht)
{
    auto tmp = lft;
    lft = rht;
    rht = tmp;
}
```



### 6.24

> Arrays have two special properties that affect how we define and use functions that operate on arrays: We cannot copy an array, and when we use an array it is (usually) **converted to a pointer**.

无错





### 6.36

```c++
string(&func())[10];
```



### 6.37



```c++
auto func()->string(*)[10];
```



### 6.39

(a):对

(b):错

(c):对





### 6.40

```c++
char* init(int ht = 24, int wd, char back); //默认实参不在形参列表的结尾 char* init( int wd, char back, int ht = 24);

```



### 6.43

都放在头文件中



### 6.46

不能 函数的返回类型和所有形参的类型必须要是constexpr





## 7

### 7.2

```c++
struct Sales_data {
	std::string bookNo;
	unsigned units_sold = 0;
	double revenue = 0.0;
    
    std::string isbn() const { return bookNo; };
	Sales_data& combine(const Sales_data&);
};

Sales_data& Sales_data::combine(const Sales_data& rhs)
{
	units_sold += rhs.units_sold;
	revenue += rhs.revenue;
	return *this;
}
```



### 7.4

```c++
struct Person {
	std::string name;
	std::string address;
};

```





### 7.5

可以 不修改只返回



### 7.15

```c++
struct Person {
	Person() = default;
	Person(const std::string name, const std::string address) :name(name), address(address) {};
	std::string name;
	std::string address;
};
```



### 7.17

访问级别不同 `struct`：public，`class`：private 



### 7.18

*封装*，即隐藏对象的属性和实现细节，仅对外公开接口

### 7.20





### 7.32

```c++
class Window_mgr {
public:
	void clear();

};
class Screen {
	friend void Window_mgr::clear();
};

```





### 7.33



```c++
Screen::pos Screen::size() const
{
    return height*width;
}
```





### 7.34

> 

```c++
dummy_fcn(pos height)
           ^
Unknown type name 'pos'
```



### 7.35





```c++
typedef string Type;
Type initVal(); // use `string`
class Exercise {
public:
	typedef double Type;
	Type setVal(Type); // use `double`
	Type initVal(); // use `double`
private:
	int val;
};

Type Exercise::setVal(Type /*该类型就是double*/ parm) { // 返回改为Exercise::Type
	val = parm + initVal();     // 实现initVal() 
	return val;
};
Exercise::Type Exercise::initVal(){} // 实现
```



### 7.36

```c++
struct X {
	X(int i, int j) : base(i), rem( base /*修改为i*/ % j) { }
	int base, rem;
};
```





### 7.37

```c++
Sales_data first_item(cin);   // use Sales_data(std::istream &is) ; its value are up to your input.

int main() {
  Sales_data next;  // use Sales_data(std::string s = ""); bookNo = "", cnt = 0, revenue = 0.0
  Sales_data last("9-999-99999-9"); // use Sales_data(std::string s = ""); bookNo = "9-999-99999-9", cnt = 0, revenue = 0.0
}
```



### 7.38

```c++
Sales_data(std::istream &is = std::cin) { read(is, *this); }
```



### 7.39

不合法 调用将不明确



### 7.43





### 7.44

不合法 没有默认的构造方法



### 7.45

合法



### 7.46

- a) A class must provide at least one constructor. (**untrue**, "The compiler-generated constructor is known as the synthesized default constructor.") 默认的构造方法
- b) A default constructor is a constructor with an empty parameter list. (**untrue**, A default constructor is a constructor that is used if no initializer is supplied.What's more, A constructor that supplies default arguments for all its parameters also defines the default constructor) 
- c) If there are no meaningful default values for a class, the class should not provide a default constructor. (**untrue**, the class should provide.) 
- d) If a class does not define a default constructor, the compiler generates one that initializes each data member to the default value of its associated type. (**untrue**, only if our class does not explicitly define **any constructors**, the compiler will implicitly define the default constructor for us.) 如果class没有提供任何构造方法则会提供默认的构造方法



### 7.47

应该添加，避免类类型转换



### 7.49

(a) Sales_data &combine(Sales_data); // ok

(b) Sales_data &combine(Sales_data&); // [Error] no matching function for call to 'Sales_data::combine(std::string&)' (`std::string&` can not convert to `Sales_data` type.)  

(c) Sales_data &combine(const Sales_data&) const; // The trailing const mark can't be put here, as it forbids any mutation on data members. This conflicts with combine's semantics.



### 7.51

> vector 将其单参数的构造函数定义成 explicit 的，而string则不是，你觉得原因何在？

单个参数转换为vector 没有意义

char * -> string 在任何情况下都可以



### 7.52





> 使用2.6.1节的 Sales_data 类，解释下面的初始化过程。如果存在问题，尝试修改它。

初始化Sales_data

```c++
struct Sales_data {
    std::string bookNo;
    unsigned units_sold;
    double revenue;
};
```



### 7.54

constexpr 为常量表达式，它可以应用于变量, 以便在任何代码尝试修改该值时引发编译器错误

> **constexpr** *文本类型* *标识符* **=** *常量表达式* **;**
>
>  **constexpr** *文本类型* *标识符* **{** *常量表达式* **}** **;** 
>
> **constexpr** *文本类型* *标识符* **(** *params* **)** **;** 
>
> **constexpr** *ctor* **(** *params* **)** **;**

```c++
static constexpr int period = 30;
```



### 7.55

> 7.5.5节的 Data 类是字面值常量类吗？请解释原因。

不是。因为 std::string 不是字面值类型。



### 7.56

> 什么是类的静态成员？它有何优点？静态成员与普通成员有何区别？

A class member that is **associated with the class**, rather than with individual objects of the class type.



区别:

- 静态成员可以为不完全类型



### 7.58



```c++
//example.h
class Example {
public:
    // constexpr 可以初始化
    static double rate = 6.5; /* static constexpr double rate = 6.5; */
    // C++98 常整型、常枚举型的静态成员才能就地初始化
    static const int vecSize = 20;
    // 类中无法初始化静态成员
    static vector<double> vec(vecSize);
};
//example.c
#include "example.h"
double Example::rate; /*constexpr double Example::rate; 或者 const double Example::rate;*/
vector<double> Example::vec; /* vector<double> Example::vec(Example::vecSize); */
```





## 8



### 8.4

```c++
#include<iostream>
#include<string>
#include<vector>
#include<fstream>
using namespace std;
int main(){
  // string file = "a.txt";
  char file[10] = "a.txt";
  ifstream in(file);
  vector<string> vec;
  string line;
  if(in){
    while(getline(in,line)){
        vec.push_back(line) ;
    }
    in.close();
  }

  return 0;
};
```

### 8.5

```
in >> buf
```



### 8.12

聚合类 特点





### 8.14

未更改其值





## 9



### 9.1

> 对于下面的程序任务，vector、deque和list哪种容器最为适合？解释你的选择的理由。如果没有哪一种容器优于其他容器，也请解释理由。

- (a) 读取固定数量的单词，将它们按字典序插入到容器中。我们将在下一章中看到，关联容器更适合这个问题。
- (b) 读取未知数量的单词，总是将单词插入到末尾。删除操作在头部进行。
- (c) 从一个文件读取未知数量的整数。将这些数排序，然后将它们打印到标准输出。



(a):list 双链表

(b):deque

(c): vector 不需要进行插入删除操作。



### 9.2

> 定义一个list对象，其元素类型是int的deque。

```c++
std::list<std::deque<int>> l;
```



### 9.4

```c++
bool find(vector<int>::const_iterator begin, vector<int>::const_iterator end, int i)
{
	while (begin++ != end)
	{
		if (*begin == i) 
			return true;
    }	
    return false;
}
```



### 9.5

```c++
vector<int>::const_iterator find(vector<int>::const_iterator begin, vector<int>::const_iterator end, int i)
{
	while (begin != end)
	{
		if (*begin == i) 
			return begin;
		++begin;
    }	
    return end;
}
```



### 9.6

> 下面的程序有何错误？你应该如何修改它？

```c++
list<int> lst1;
list<int>::iterator iter1 = lst1.begin(),
					iter2 = lst1.end();
while (iter1 < iter2) /* iter1 != iter2 */
```

list 是双向链表， 因此迭代器可能不是连续的



### 9.7

> 为了索引int 的 vector中的元素，应该使用什么类型？

```c++
vector<int>::size_type
```



### 9.8

> 为了读取string 的list 中的元素，应该使用什么类型？如果写入list，又应该使用什么类型？

```c++
list<string>::const_iterator // 读
list<string>::iterator // 写
```



### 9.9

> begin 和 cbegin 两个函数有什么不同？

`begin` 返回的是普通迭代器，`cbegin` 返回的是常量迭代器。



###  9.11

> 对6种创建和初始化 vector 对象的方法，每一种都给出一个实例。解释每个vector包含什么值。

```c++
vector<int> vec;    // 0
vector<int> vec(10);    // 0
vector<int> vec(10, 1);  // 10*1
vector<int> vec{ 1, 2, 3, 4, 5 }; // 1, 2, 3, 4, 5
vector<int> vec(other_vec); // 拷贝 other_vec 的元素
vector<int> vec(other_vec.begin(), other_vec.end()); // 拷贝 other_vec 的元素
```



### 9.12



> 对于接受一个容器创建其拷贝的构造函数，和接受两个迭代器创建拷贝的构造函数，解释它们的不同。

- 接受一个容器创建其拷贝的构造函数，必须容器类型和元素类型都相同。
- 接受两个迭代器创建拷贝的构造函数，只需要元素的类型能够相互转换，容器类型和元素类型可以不同。



### 9.13

> 如何从一个list初始化一个vector？从一个vector又该如何创建？编写代码验证你的答案。

```c++
vector<int> vec;
vector<double> vecd(vec.begin, vec.end);
```





### 9.14

> 编写程序，将一个list中的char * 指针元素赋值给一个vector中的string。

```c++
list<char *> l;
vector<string> sl;
sl.assign(l.cbegin(), l.cend());
```



### 9.15

> 编写程序，判定两个vector是否相等。

```c++
std::list<int>      li{ 1, 2, 3, 4, 5 };
std::vector<int>    vec2{ 1, 2, 3, 4, 5 };
std::vector<int>    vec3{ 1, 2, 3, 4 };
std::cout << (std::vector<int>(li.begin(), li.end()) == vec2 ? "true" : "false") << std::endl;
```



### 9.17

> 假定c1 和 c2 是两个容器，下面的比较操作有何限制？

```c++
	if (c1 < c2)
```

- c1 和 c2 必须是相同类型的容器并且保存相同类型的元素
- 元素类型要支持关系运算符





### 9.18

> 编写程序，从标准输入读取string序列，存入一个deque中。编写一个循环，用迭代器打印deque中的元素。

```c++
int main()
{
    deque<string> input;
    for (string str; cin >> str; input.push_back(str));
    for (auto iter = input.cbegin(); iter != input.cend(); ++iter)
        cout << *iter << endl;

    return 0;
}
```





### 9.19

> 重写上一题的程序，用list替代deque。列出程序要做出哪些改变。

只需要在声明上做出改变即可，其他都不变。

```c++
deque<string> input; 
//改为
list<string> input;
```





### 9.22

> 假定iv是一个int的vector，下面的程序存在什么错误？你将如何修改？

```c++
vector<int>::iterator iter = iv.begin(),
					  mid = iv.begin() + iv.size() / 2;
while (iter != mid /* iter++ != mid */)
	if (*iter == some_val)
		iv.insert(iter, 2 * some_val);
```



after：

```c++
while (iter != mid)
{
	if (*iter == some_val)
	{
		iter = v.insert(iter, 2 * some_val); // 迭代器 指向新添加的值
		++iter; 
    }
	++iter;
}
```





### 9.25

> 对于第312页中删除一个范围内的元素的程序，如果 elem1 与 elem2 相等会发生什么？如果 elem2 是尾后迭代器，或者 elem1 和 elem2 皆为尾后迭代器，又会发生什么？



不会删除



### 9.26

> 使用下面代码定义的ia，将ia 拷贝到一个vector和一个list中。是用单迭代器版本的erase从list中删除奇数元素，从vector中删除偶数元素。



```c++
vector<int> vec(ia, end(ia));
list<int> lst(vec.begin(), vec.end());

for (auto it = lst.begin(); it != lst.end(); )
	if (*it & 0x1) // 如果是奇数
		it = lst.erase(it);
	else 
		++it;

for (auto it = vec.begin(); it != vec.end(); )
	if (!(*it & 0x1))
		it = vec.erase(it);
	else
		++it;		
```





### 9.27

> 编写程序，查找并删除forward_list中的奇数元素。



```c++
#include <iostream>
#include <forward_list>

using namespace std;

int main()
{
	forward_list<int> flst = { 0, 1, 2, 3, 4, 5, 6, 7, 8, 9 };
	auto prev = flst.before_begin();
	auto curr = flst.begin();

	while (curr != flst.end())
	{
		if (*curr & 0x1) // 如果是奇数
		{
			curr = flst.erase_after(prev); // 移除当前 并指向下一个节点
		}
		else
		{
			prev = curr;
			++curr;
		}
	}
	return 0;
}
```



### 9.28



> 编写函数，接受一个forward_list和两个string共三个参数。函数应在链表中查找第一个string，并将第二个string插入到紧接着第一个string之后的位置。若第一个string未在链表中，则将第二个string插入到链表末尾。



```c++
void func(forward_list<string> &flst,const string &fs, const string &as) {
	auto prev = flst.before_begin();
	auto curr = flst.begin();

	while(curr != flst.end()) {
		if(*curr==fs){
			flst.insert_after(curr,as);
			return;
		}
		prev = curr;
		curr++;
	}
	flst.insert_after(prev,as);
};

```



### 9.29

> 假定vec包含25个元素，那么vec.resize(100)会做什么？如果接下来调用vec.resize(10)会做什么？



添加 75 个 0 元素

删除后面的90个元素



### 9.30

> 接受单个参数的resize版本对元素类型有什么限制（如果有的话）？

元素类型必须提供一个默认构造函数。



### 9.34

> 假定vi是一个保存int的容器，其中有偶数值也有奇数值，分析下面循环的行为，然后编写程序验证你的分析是否正确。



```c++
iter = vi.begin();
while (iter != vi.end())
	if (*iter % 2) // 如果是偶数
		iter = vi.insert(iter, *iter); // 移到添加的位置
	++iter; // 移到原来的位置
```



循环不会结束



### 9.35

> 解释一个vector的capacity和size有何区别。

- capacity 的值表明，在不重新分配内存空间的情况下，容器可以保存多少元素
- 而 size 的值是指容器已经保存的元素的数量



### 9.36

> 一个容器的capacity可能小于它的size吗？

不可能。



### 9.37

链表，array是数组 固定大小





### 9.39

> 解释下面程序片段做了什么：



```c++
vector<string> svec;
svec.reserve(1024); //请求分配1024个string 内存
string word;
while (cin >> word)
	svec.push_back(word);
svec.resize(svec.size() + svec.size() / 2); // 扩容150% 的容量
```



### 9.40

> 如果上一题的程序读入了256个词，在resize之后容器的capacity可能是多少？如果读入了512个、1000个、或1048个呢？

- 如果读入了256个词，capacity 仍然是 1024
- 如果读入了512个词，capacity 仍然是 1024
- 如果读入了1000或1048个词，capacity 取决于具体实现。



### 9.41

> 编写程序，从一个vector初始化一个string。

```c++
vector<char> v{ 'h', 'e', 'l', 'l', 'o' };
string str(v.cbegin(), v.cend());
```





### 9.47



```
#include <iostream>
#include <string>

using namespace std;

int main()
{
    string numbers("0123456789");
    string s("ab2c3d7R4E6");

    cout << "numeric characters: ";
    // 查找s中包含任意子串中任意字符的位置
    for (int pos = 0; (pos = s.find_first_of(numbers, pos)) != string::npos; ++pos)
    {
        cout << s[pos] << " ";
    }
    // 查找s中不包含任意子串中任意字符的位置
    cout << "\nalphabetic characters: ";
    for (int pos = 0; (pos = s.find_first_not_of(numbers, pos)) != string::npos; ++pos)
    {
        cout << s[pos] << " ";
    }

    return 0;

}
```





### 9.48



> 假定name和numbers的定义如325页所示，numbers.find(name)返回什么？



返回 npos





### 9.49



> 如果一个字母延伸到中线之上，如d 或 f，则称其有上出头部分（ascender）。如果一个字母延伸到中线之下，如p或g，则称其有下出头部分（descender）。编写程序，读入一个单词文件，输出最长的既不包含上出头部分，也不包含下出头部分的单词。



```
#include <iostream>
#include <string>

using namespace std;

int main()
{
    string ad = "dfpg";
    
    string word = "ffaaaaaddssssggspp";
    
    int index = -1;
    int offset = 0;
    // 从第一个部不包含任意ad字符的索引开始查找
    for(auto i =  word.find_first_not_of(ad); i!= word.size() ; i++){
        
        int ofset = 0;
        // wd不在ad中 那么 ofset
        for(auto j = i ; ad.find_first_of(word[j])==-1;j++){
            ofset ++;
        }
        //　如果偏移量大于　已知偏移量　　
        if(ofset > offset){
            offset = ofset;
            index = i;
        }
    } 
    
    cout<< string(word,index,offset) ;

    return 0;
```

### 9.50



> 编写程序处理一个vector，其元素都表示整型值。计算vector中所有元素之和。修改程序，使之计算表示浮点值的string之和。



### 9.52



> 使用stack处理括号化的表达式。当你看到一个左括号，将其记录下来。当你在一个左括号之后看到一个右括号，从stack中pop对象，直至遇到左括号，将左括号也一起弹出栈。然后将一个值（括号内的运算结果）push到栈中，表示一个括号化的（子）表达式已经处理完毕，被其运算结果所替代。
>
> 这道题可以延伸为逆波兰求值，以及中缀转后缀表达式。





### 10.4

> 假定 v 是一个vector，那么调用 accumulate(v.cbegin(),v.cend(),0) 有何错误（如果存在的话）？

结果会是 int 类型。





### 10.5

## 

> 在本节对名册（roster）调用equal 的例子中，如果两个名册中保存的都是C风格字符串而不是string，会发生什么？

C风格字符串是用指向字符的指针表示的，因此会比较两个指针的值（地址），而不会比较这两个字符串的内容



### 10.10

> 你认为算法不改变容器大小的原因是什么？

算法的接口是迭代器，而插入器用来改变容器。这样的设计使得算法具有通用性。





### 12.8

> 下面的函数是否有错误？如果有，解释错误原因。

```c++
bool b() {
	int* p = new int;
	// ...
	return p; // 返回bool 值， 此内存无法被释放
}
```





### 12.9

> 解释下面代码执行的结果。

```c++
int *q = new int(42), *r = new int(100);
r = q; // r指向42 100 将不被释放
auto q2 = make_shared<int>(42), r2 = make_shared<int>(100);
r2 = q2; // r指向42， 100 将被释放
```





### 12.10

```c++
shared_ptr<int> p(new int(42)); //p指向 42 p引用=1
process(shared_ptr<int>(p)); //这个临时的shared_ptr 也会指向42 p引用=2 结束后=1
```



### 12.11

> 如果我们像下面这样调用 process，会发生什么？

```c++
process(shared_ptr<int>(p.get())); // 引用计数为0
```



### 12.12



> p 和 sp 的定义如下，对于接下来的对 process 的每个调用，如果合法，解释它做了什么，如果不合法，解释错误原因：



```c++
auto p = new int(); // p是一个隐士指针
auto sp = make_shared<int>(); // sp是一个智能指针
(a) process(sp); // 合法  引用计数为2
(b) process(new int()); // 不合法 不能从隐士指针转换为 智能指针
(c) process(p); // 不合法 不能从隐士指针转换为 智能指针
(d) process(shared_ptr<int>(p)); // 合法 但是结束后p为 空悬指针
```





### 12.13



> 如果执行下面的代码，会发生什么？

```c++
auto sp = make_shared<int>();
auto p = sp.get();
delete p; // sp为空悬指针
```