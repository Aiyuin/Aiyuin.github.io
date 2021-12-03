---
title: spckaddr_in.sockaddr,in_addr
date: 2021-11-17 00:00:00
updated: 2021-11-17 12:00:00
email: 956954649@qq.com
tags:
    - 学习
    - 生活
categories:
    - 艾小艾的生活日志
---

通用的socket地址:

```c
struct sockaddr {  
    unsigned short sa_family;     
    char sa_data[14];     
};  
```

具体到Internet   socket，用下面的结构，二者可以进行类型转换

```c
struct sockaddr_in {  
    short int sin_family;     
    unsigned short int sin_port;     
    struct in_addr sin_addr;     
    unsigned char sin_zero[8];     
};  
```

struct  in_addr就是32位IP地址。

```c
struct in_addr {  
    union{
         struct {
                u_char s_b1,s_b2,s_b3,s_b4;
          } S_un_b;
          struct{ 
                u_short s_w1,s_w2;
          } S_un_w;
          u_long S_addr; 
    } S_un;
    #define s_addr  S_un.S_addr
};  
```

填值的时候使用sockaddr_in结构，而作为函数（如socket, listen, bind等）的参数传入的时候转换成sockaddr结构就行了，毕竟都是16个字符长。

参考：

[sockaddr_in , sockaddr , in_addr区别_jackychu的专栏-CSDN博客_in_addr](https://blog.csdn.net/jackychu/article/details/4461927)
