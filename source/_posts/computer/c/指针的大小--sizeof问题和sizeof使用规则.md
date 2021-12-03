---
title: 指针的大小--sizeof问题和sizeof使用规则
date: 2021-11-17 00:00:00
updated: 2021-11-17 12:00:00
email: 956954649@qq.com
tags:
    - 学习
    - 生活
categories:
    - 艾小艾的c学习
---

# 1、什么是sizeof

首先看一下sizeof在msdn上的定义：

> The sizeof keyword gives the amount of storage, in bytes, associated with a variable or a type (including aggregate types). This keyword returns a value of type size_t.

看到return这个字眼，是不是想到了函数？错了，sizeof不是一个函数，你见过给一个函数传参数，而不加括号的吗？sizeof可以，所以sizeof不是函数。网上有人说sizeof是一元操作符，但是我并不这么认为，因为sizeof更像一个特殊的宏，它是在编译阶段求值的。举个例子：

```cpp
cout<<sizeof(int)<<endl; // 32位机上int长度为4
cout<<sizeof(1==2)<<endl; // == 操作符返回bool类型，相当于 cout<<sizeof(bool)<<endl;
```

在编译阶段已经被翻译为：

```cpp
cout<<4<<endl;
cout<<1<<endl;
```

这里有个陷阱，看下面的程序：

```cpp
int a = 0;
cout<<sizeof(a=3)<<endl;
cout<<a<<endl;
```

输出为什么是4，0而不是期望中的4，3？？？就在于sizeof在编译阶段处理的特性。由于sizeof不能被编译成机器码，所以sizeof作用范围内，也就是()里面的内容也不能被编译，而是被替换成类型。=操作符返回左操作数的类型，所以a=3相当于int，而代码也被替换为：

```cpp
int a = 0;
cout<<4<<endl;
cout<<a<<endl;
```

所以，sizeof是不可能支持链式表达式的，这也是和一元操作符不一样的地方。

结论：不要把sizeof当成函数，也不要看作一元操作符，把他当成一个特殊的编译预处理。

# 2、sizeof的用法

sizeof有两种用法：

1. sizeof(object):也就是对对象使用sizeof，也可以写成sizeof object 的形式。

2. sizeof(typename):也就是对类型使用sizeof，注意这种情况下写成sizeof typename是非法的。
   
   下面举几个例子说明一下：

```cpp
int i = 2;
cout<<sizeof(i)<<endl; // sizeof(object)的用法，合理
cout<<sizeof i<<endl; // sizeof object的用法，合理
cout<<sizeof 2<<endl; // 2被解析成int类型的object, sizeof object的用法，合理
cout<<sizeof(2)<<endl; // 2被解析成int类型的object, sizeof(object)的用法，合理
cout<<sizeof(int)<<endl;// sizeof(typename)的用法，合理
cout<<sizeof int<<endl; // 错误！对于操作符，一定要加()
```

　　可以看出，加()是永远正确的选择。

　　结论：不论sizeof要对谁取值，最好都加上()。

# 3、数据类型的sizeof

## （1）C++固有数据类型

32位C++中的基本数据类型，也就char,short int(short),int,long int(long),float,double, long double

大小分别是：1，2，4，4，4，8, 10。

考虑下面的代码：

```cpp
cout<<sizeof(unsigned int) == sizeof(int)<<endl; // 相等，输出 1
```

unsigned影响的只是最高位bit的意义，数据长度不会被改变的。

结论：unsigned不能影响sizeof的取值。

## （2）自定义数据类型

typedef可以用来定义C++自定义类型。考虑下面的问题：

```cpp
typedef short WORD;
typedef long DWORD;
cout<<(sizeof(short) == sizeof(WORD))<<endl; // 相等，输出1
cout<<(sizeof(long) == sizeof(DWORD))<<endl; // 相等，输出1
```

结论：自定义类型的sizeof取值等同于它的类型原形。

## （3）函数类型

考虑下面的问题：

```cpp
int f1(){return 0;};
double f2(){return 0.0;}
void f3(){}
```

```cpp
cout<<sizeof(f1())<<endl; // f1()返回值为int，因此被认为是int
cout<<sizeof(f2())<<endl; // f2()返回值为double，因此被认为是double
cout<<sizeof(f3())<<endl; // 错误！无法对void类型使用sizeof
cout<<sizeof(f1)<<endl; // 错误！无法对函数指针使用sizeof
cout<<sizeof*f2<<endl; // *f2，和f2()等价，因为可以看作object，所以括号不是必要的。被认为是double
```

结论：对函数使用sizeof，在编译阶段会被函数返回值的类型取代，

# 4、指针问题

考虑下面问题：

```cpp
cout<<sizeof(string*)<<endl; // 4
cout<<sizeof(int*)<<endl; // 4
cout<<sizof(char****)<<endl; // 4
```

可以看到，不管是什么类型的指针，大小都是4的，因为指针就是32位的物理地址。

结论：只要是指针，大小就是4。（64位机上要变成8也不一定）。

顺便唧唧歪歪几句，C++中的指针表示实际内存的地址。和C不一样的是，C++中取消了模式之分，也就是不再有small,middle,big,取而代之的是统一的flat。flat模式采用32位实地址寻址，而不再是c中的 segment:offset模式。举个例子，假如有一个指向地址 f000:8888的指针，如果是C类型则是8888(16位, 只存储位移，省略段)，far类型的C指针是f0008888(32位，高位保留段地址，地位保留位移),C++类型的指针是f8888(32位，相当于段地址*16 + 位移，但寻址范围要更大)。

# 5、数组问题

考虑下面问题：

```cpp
char a[] = "abcdef";
int b[20] = {3, 4};
char c[2][3] = {"aa", "bb"};
```

```cpp
cout<<sizeof(a)<<endl; // 7
cout<<sizeof(b)<<endl; // 20*4
cout<<sizeof(c)<<endl; // 6
```

数组a的大小在定义时未指定，编译时给它分配的空间是按照初始化的值确定的，也就是7。c是多维数组，占用的空间大小是各维数的乘积，也就是6。可以看出，数组的大小就是他在编译时被分配的空间，也就是各维数的乘积*数组元素的大小。

结论：数组的大小是各维数的乘积*数组元素的大小。

这里有一个陷阱：

```cpp
int *d = new int[10];
cout<<sizeof(d)<<endl; // 4
```

d是我们常说的动态数组，但是他实质上还是一个指针，所以sizeof(d)的值是4。

再考虑下面的问题：

```cpp
double* (*a)[3][6];
cout<<sizeof(a)<<endl; // 4
cout<<sizeof(*a)<<endl; // 72
cout<<sizeof(**a)<<endl; // 24
cout<<sizeof(***a)<<endl; // 4
cout<<sizeof(****a)<<endl; // 8
```

a是一个很奇怪的定义，他表示一个指向 double*[3][6]类型数组的指针。既然是指针，所以sizeof(a)就是4。

既然a是执行double*[3][6]类型的指针，*a就表示一个double*[3][6]的多维数组类型，因此sizeof(*a)=3*6*sizeof(double*)=72。同样的，**a表示一个double*[6]类型的数组，所以sizeof(**a)=6*sizeof(double*)=24。***a就表示其中的一个元素，也就是double*了，所以sizeof(***a)=4。至于****a，就是一个double了，所以sizeof(****a)=sizeof(double)=8。

# 6、指针的大小--sizeof问题 指针的大小是问：一个指针变量占用多少内存空间？

分析：既然指针只是要存储另一个变量的地址，。注意，是存放一变量的地址，而不是存放一个变量本身，所以，不管指针指向什么类型的变量，它的大小总是固定的：只要能放得下一个地址就行！（这是一间只有烟盒大小的“房间”，因为它只需要入一张与着地址的纸条）。

存放一个地址需要几个字节？答案是和一个 int 类型的大小相同：4字节。

所以,若有：

```cpp
int* pInt;

char* pChar;

bool* pBool;

float* pFloat;

double* pDouble;
```

则: sizeof(pInt)、sizeof(pChar)、sizeof(pBool)、sizeof(pFloat)、sizeof(pDouble)的值全部为：4。

PS：你敢拆电脑吗？拆开电脑，认得硬盘数据线吗？仔细数数那扁宽的数据线由几条细线组成？答案：32条，正是 4 * 8。

# 附：sizeof和strlen的区别

❀第一个例子：
char* ss = "0123456789";
1、sizeof(ss)的结果是4,ss是指向字符串常量的字符指针
2、sizeof(*ss)的结果是1，*ss是第一个字符

❀第二个例子：
char ss[] = "01233456789";
1、sizeof(ss)结果是11，ss是数组，计算到'\0'的位置，因此是10+1
2、sizeof(*ss)结果是1，*ss是第一个字符

❀第三个例子
char ss[100] = "0123456789";
1、sizeof(ss)的结果是100，ss表示在内存中预分配的大小：100*1
2、strlen(ss)的结果是10，它的内部实现是用一个循环计算字符串的长度，直到'\0'为止。

❀第四个例子
int ss[100] = "0123456789";
1、sizeof(ss)的结果是400，ss表示在内存中的大小，为100*4
2、strlen(ss)错误，strlen的参数只能是char*，且必须是以'\0'结尾的。
注：笔者试验时无法对ss初始化
error message：
error C2075: 'ss' : array initialization needs curly braces

引用：

原文链接：https://blog.csdn.net/luguifang2011/article/details/39988627
