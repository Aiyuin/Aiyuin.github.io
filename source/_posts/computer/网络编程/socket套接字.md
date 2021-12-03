---
title: socket套接字选项问题
date: 2021-11-17 00:00:00
updated: 2021-11-17 12:00:00
email: 956954649@qq.com
tags:
    - 学习
    - 生活
categories:
    - 艾小艾的生活日志
---

raw_socket

```c
#include <sys/types.h>          /* See NOTES */
#include <sys/socket.h>

int socket(int domain, int type, int protocol);
```

原始套接字:套接字类型是SOCK_RAW

domain（family）套接字族可以是AF_INET、PF_INET、AF_PACKET和PF_PACKET

**_NET和_PACKET的区别:**（这是我之前为了寻找以太网帧头弄不懂的地方）

> 使用AF_INET，用户程序无法获得链路层数据，在接收发数据包时，以太网头部（以太网帧头）就被去掉，不能再现于数据包内。简单来说，使用AF_INET，是面向IP层的原始套接字；使用AF_PACKET，是面向链路层的套接字。

> 在捕获接受过来的ip分片时，由于用_INET域，使收过来的ip数据包，在以太网帧后被处理了，接受到的数据包被重组了，没有分片细节。使用_PACKET后，链路层的数据包不会配上层协议栈处理，直接被抓包，可以自己处理包细节。

Type就是socket的类型，对于AF_INET协议族而言有流套接字*(SOCK_STREAM)*、数据包套接字*(SOCK_DGRAM)*、原始套接字*(SOCK_RAW)*。

```c
/**

 * enum sock_type - Socket types

 * @SOCK_STREAM: stream (connection) socket

 * @SOCK_DGRAM: datagram (conn.less) socket

 * @SOCK_RAW: raw socket

 * @SOCK_RDM: reliably-delivered message

 * @SOCK_SEQPACKET: sequential packet socket

 * @SOCK_DCCP: Datagram Congestion Control Protocol socket

 * @SOCK_PACKET: linux specific way of getting packets at the dev level.

 *        For writing rarp and other similar things on the user level.

*/

enum sock_type {

    SOCK_STREAM = 1,

    SOCK_DGRAM  = 2,

    SOCK_RAW    = 3,

    SOCK_RDM    = 4,

    SOCK_SEQPACKET  = 5,

    SOCK_DCCP   = 6,

    SOCK_PACKET = 10,

};
```

如果第一个参数是AF_PACKET，第二个参数是SOCK_RAW，那么是面向链路层的套接字，第三个参数可以是：（socket(AF_INET, SOCK_RAW, 0)（这个会报错））

- ETH_P_IP - 只接收目的mac是本机的IP类型数据帧
- ETH_P_ARP - 只接收目的mac是本机的ARP类型数据帧
- ETH_P_RARP - 只接收目的mac是本机的RARP类型数据帧
- ETH_P_PAE - 只接收目的mac是本机的802.1x类型的数据帧
- ETH_P_ALL - 接收目的mac是本机的所有类型数据帧，同时还可以接收本机发出的所有数据帧，混杂模式打开时，还可以接收到目的mac不是本机的数据帧

如果是构造发包的话，使用AF_INET时，那么是面向IP层的套接字，protocol字段定义在netinet/in.h中，常见的包括IPPROTO_TCP、IPPROTO_UDP、IPPROTO_ICMP和IPPROTO_RAW。如果第三个参数是IPPROTO_TCP、IPPROTO_UDP、IPPROTO_ICMP，则我们所构造的报文从IP首部之后的第一个字节开始，IP首部由内核自己维护，首部中的协议字段会被设置为我们调用socket()函数时传递给它的protocol字段。也即，如果没有开启IP_HDRINCL选项，那么内核会帮忙处理IP头部。如果设置了IP_HDRINCL选项，那么用户需要自己生成IP头部的数据，其中IP首部中的标识字段和校验和字段总是内核自己维护。可以通过下面代码开启IP_HDRINCL：

```c
const int on = 1;
if(setsockopt(fd,SOL_IP,IP_HDRINCL,&on,sizeof(int)) < 0)
{
  printf("set socket option error!\n");
}
```

同时，如果第一个参数是AF_INET，最后一个IPPROTO_RAW的含义需要额外说明一下：

> 使用原始套接字编写底层网络程序时，最重要的决策之一是应用程序是否与传输级协议头，IP头一起构建。现在，告诉IP层不要预先添加自己的头的明显方法是调用setsockopt系统调用并设置IP_HDRINCL（包含头）选项。但是，并不总是存在此选项。在Net/3之前的版本中，没有IP_HDRINCL选项，并且没有内核预先添加自己的头的唯一方法是使用特定的内核补丁并将协议设置为IPPROTO_RAW。这些补丁最初是为4.3BSD和Net / 1制作的，以支持需要编写自己的完整IP数据报的Traceroute，因为它需要修改TTL字段。有趣的是，自从IP_HDRINCL出现以来，Linux和FreeBSD选择了不同的方式来继续“传统”。在Linux上将协议设置为IPPROTO_RAW时，默认情况下，内核会设置IP_HDRINCL选项，因此不会在其前面加上自己的IP头。

总结一下，对于socket的第一个参数，因为AF_INET、PF_INET、AF_PACKET和PF_PACKET中AF和PF基本等价，所以只需要区分_NET和_PACKET就行；第二个参数是一般是sock_raw；关于第三个参数，取决于第一个参数。

1. 新建socket时候指定的protocol(如：IPPROTO_TCP、IPPROTO_UDP)在发送时没有用到，并不会用指定的protocol来填充IP头部，因为我们设置了IP_HDRINCL来指明由我们自己来填充IP头部。
2. 发送的数据没有再经过IP层，最后直接调用dst_output发送数据报了，所以如果发送的数据报长度超过了MTU那么不会有IP分片产生，发送失败，返回EMSGSIZE错误码。 相反的如果没有设置IP_HDRINCL来由我们自己定义IP头部，那么流程会走到ip_append_data函数进行IP分片。
3. 发送的数据报没有经过TCP层

参考：

https://zhuanlan.zhihu.com/p/59327439
