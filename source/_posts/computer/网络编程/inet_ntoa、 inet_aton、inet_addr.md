---
title: inet_ntoa、 inet_aton、inet_addr
date: 2021-11-17 00:00:00
updated: 2021-11-17 12:00:00
email: 956954649@qq.com
tags:
    - 学习
    - 生活
categories:
    - 艾小艾的网络编程
---

# inet_addr()

简述：将一个点间隔地址转换成一个in_addr。

```c
#include <winsock.h>
　　unsigned long PASCAL FAR inet_addr( const struct FAR* cp);
```

本函数解释cp参数中的字符串，这个字符串用Internet的“.”间隔格式表示一个数字的Internet地址。返回值可用作Internet地址。所有Internet地址<u>以网络字节顺序返回(字节从左到右排列)。</u>

Internet地址用“.”间隔的地址可有下列几种表达方式：

a.b.c.d，a.b.c，a.b，a

> 当四个部分都有定值时，每个都解释成一个字节数据，从左到右组成Internet四字节地址。请注意，当一个Internet地址在Intel机器上表示成一个32位整型数时，则上述的字节为“d.c.b.a”。这是因为Intel处理器的字节是从右向左排列的。
> 
> <mark>请注意：</mark>只有Berkeley支持下述表达法，Internet其余各处均不支持。考虑到与软件的兼容性，应按规定进行使用:
> 
> ~~对一个三部分地址，最后一部分解释成16位数据并作为网络地址的最右两个字节。这样，三部分地址便很容易表示B组网络地址，如“128.net.host”。~~
> 
> ~~对一个两部分地址，最后一部分解释成24位数据并作为网络地址的最右三个字节，这样，两部分地址便很容易表示C组网络地址，如“net.host”。~~
> 
> ~~对仅有一个部分的地址，则将它的值直接存入网络地址不作任何字节的重组。~~

返回值：

若无错误发生，inet_addr()返回一个无符号长整型数，其中以适当字节顺序存放Internet地址。如果传入的字符串不是一个合法的Internet地址，如“a.b.c.d”地址中任一项超过255，那么inet_addr()返回INADDR_NONE。

# **inet_ntoa()**

简述：将网络地址转换成“.”点隔的字符串格式。

```c
#include <winsock.h>
char FAR* PASCAL FAR inet_ntoa( struct in_addr in);
```

本函数将一个用in参数所表示的Internet地址结构转换成以“.” 间隔的诸如“a.b.c.d”的字符串形式。请注意inet_ntoa()返回的字符串存放在WINDOWS套接口实现所分配的内存中。应用程序不应假设该内存是如何分配的。在同一个线程的下一个WINDOWS套接口调用前，数据将保证是有效。

返回值：若无错误发生，inet_ntoa()返回一个字符指针。否则的话，返回NULL。其中的数据应在下一个WINDOWS套接口调用前复制出来。

# **inet_aton()**

<mark>一般都不想用这个函数</mark>

简述：inet_aton是一个改进的方法来将一个字符串IP地址转换为一个32位的网络序列IP地址。

```c
#include <sys/socket.h>
#include <netinet/in.h>
#include <arpa/inet.h>
int inet_aton(const char *string, struct in_addr *addr);
```

inet_aton函数接受两个参数。参数描述如下：

1. 输入参数string包含ASCII表示的IP地址。

2. 输出参数addr是将要用新的IP地址更新的结构。

如果这个函数成功，函数的返回值非零。如果输入地址不正确则会返回零。使用这个函数并没有错误码存放在errno中，所以他的值会被忽略。

对于这个函数有一点迷惑的就是这个函数调用所需要的两个参数。如果我们定义了一个AF_INET套接口地址：

```c
struct sockaddr_in adr_inet;    /* AF_INET */
```

提供给inet_aton函数调用的参数指针为 &adr_inet.sin_addr

参考：

[inet_ntoa、 inet_aton、inet_addr_mb5fdcaee2972df_51CTO博客](https://blog.51cto.com/u_15061952/4233674)
