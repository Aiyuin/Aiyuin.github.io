---
title: ip数据包分片重组问题
date: 2021-11-17 00:00:00
updated: 2021-11-17 12:00:00
email: [956954649@qq.com](mailto:956954649@qq.com) tags:

- 学习
- 生活
  categories:
- 艾小艾的生活日志

---

```c
#include<stdio.h>
#include<stdlib.h>
#include<unistd.h>
#include<sys/types.h>
#include<sys/socket.h>
#include<netinet/in.h>
#include<netinet/ip.h>
#include<netinet/ip_icmp.h>
#include<signal.h>
#include<arpa/inet.h>
#include<sys/time.h>
#include<string.h>
#include<netdb.h>
#include<netinet/ether.h>

#define PACK_SIZE 4096

typedef struct ip_all{
    struct Node *ip;
    unsigned int ip_src;
    unsigned int ip_dst;
    unsigned short id;
    u_int8_t protocol;
}IP_ALL;

typedef struct Node{
    struct ip *ip;
    struct Node *next;
}Node;

struct ip_all *all[10];
int countall[10]={0};
int count=0;
int count_ture=0;

void print(struct ip *ip);
int check_ip(struct ip *ip,int num);
void to_ip_all(int num,struct ip *ip);
void to_ip_node(struct ip *ip,int num);
void chongzu();
void print_list();
void swich_H(Node *l,Node *p);

int main(int argc,char* argv[]){
    int sockfd;
    unsigned int heip;
    char recv_packet[PACK_SIZE];

    if(argc<2){
        printf("输入过滤包的ip地址");
        exit(EXIT_FAILURE);
    }

    heip=(unsigned int)inet_addr(argv[1]);

    if((sockfd=socket(AF_PACKET,SOCK_RAW,htons(ETH_P_ALL)))<0){
        perror("socket");
        exit(-1);
    }

    while(count<20){
        int check;

        if((check=recvfrom(sockfd,recv_packet,sizeof(recv_packet),0,NULL,NULL))<0) {
            continue;
        }

        count++;
        struct ip *ip = (struct ip*)(recv_packet+14);

        if(heip==ip->ip_src.s_addr){
            if(count_ture==0){
                to_ip_all(1,ip);
                count_ture++;
            }
            else{
                int i=0;
                int c_ip=0;
                while (i<count_ture)
                {
                    c_ip=check_ip(ip,i+1);
                    if(c_ip==1){
                        to_ip_node(ip,i+1);
                        break;
                    }
                    i++;
                }
                if(c_ip==0){
                    to_ip_all(i+1,ip);
                    count_ture++;
                }
            }
        }

        printf("%5d",count);
        print(ip);
    }
    print_list();
    printf("重组后\n");
    chongzu();
    print_list();
    return 0;
}

void print(struct ip *ip){
    u_short flags;
    u_short offset;
    flags=ntohs(ip->ip_off)&~IP_OFFMASK;
    offset=ntohs(ip->ip_off)&IP_OFFMASK;
    printf("\t%20s    \t%20s",inet_ntoa(ip->ip_src),inet_ntoa(ip->ip_dst));
    printf("\t%5d     \t%5d     \t%5d",ip->ip_tos,ntohs(ip->ip_len),ntohs(ip->ip_id));
    printf("\t%5d     \t%5d        \t%5d       \t%5d        \t%5d",flags,offset,ip->ip_ttl,ip->ip_ttl,ntohs(ip->ip_sum));
    printf("\n");
}

int check_ip(struct ip *ip,int num){
    unsigned int ip_src=ip->ip_src.s_addr;
    unsigned int ip_dst=ip->ip_dst.s_addr;
    u_short id=ip->ip_id;
    u_int8_t protocol=ip->ip_p;

    if((all[num]->ip_src==ip_src)&&(all[num]->ip_dst==ip_dst)&&( all[num]->id==id)&&(all[num]->protocol==protocol)){
        return 1;
    }
    else
        return 0;
}

void to_ip_all(int num,struct ip *ip){
    all[num]=(IP_ALL*)malloc(sizeof(IP_ALL));
    Node *q=(Node*)malloc(sizeof(Node));
    char *ip_buff=(char *)malloc(PACK_SIZE);
    memcpy(ip_buff,ip,PACK_SIZE-14);
    q->next=NULL;
    q->ip=(struct ip*)ip_buff;
    all[num]->ip_src=ip->ip_src.s_addr;
    all[num]->ip_dst=ip->ip_dst.s_addr;
    all[num]->id=ip->ip_id;
    all[num]->protocol=ip->ip_p;
    all[num]->ip=q;
    countall[num]++;
}

void to_ip_node(struct ip *ip,int num){
    Node *q=(Node*)malloc(sizeof(Node));
    char *ip_buff=(char *)malloc(PACK_SIZE);
    memcpy(ip_buff,ip,PACK_SIZE-14);
    Node *p;
    p=all[num]->ip;
    while (p->next!=NULL){
       p=p->next;
    }
    p->next=q;
    q->next=NULL;
    q->ip=(struct ip*)ip_buff;
    countall[num]++;
}

void print_list(){
    int i=1;
    for(;i<=count_ture;i++){
        Node *p;
        p=all[i]->ip;
        printf("第%d列数据包：\n",i);
        while (p->next!=NULL){
            print(p->ip);
            p=p->next;
        }
        print(p->ip);
    }
}

void chongzu(){
    int i=1;
    for(;i<=count_ture;i++){
        int j=1;
        while(j<count_ture){
            Node *p,*l;
            struct ip *ip;
            p=all[i]->ip;
            l=all[i]->ip;
            ip=l->ip;
            uint16_t max=ntohs(ip->ip_off)&IP_OFFMASK;
            p=p->next;
            while (p!=NULL){
                ip=p->ip;
                //flags=ntohs(ip->ip_off)&~IP_OFFMASK;
                uint16_t offset=ntohs(ip->ip_off)&IP_OFFMASK;
                if(offset<max){
                    max=offset;
                    swich_H(l,p);
                }
                l=l->next;
                p=p->next;
            }
            j++;
        }
    }
}

void swich_H(Node *l,Node *p){
    struct ip *ip;
    ip=l->ip;
    l->ip=p->ip;
    p->ip=ip;
}
```

上面代码输出时，源IP地址和目的IP地址的输出是一模一样的。

一下代码是修改了print()函数的输出格式



```c
void print(struct ip *ip){
    u_short flags;
    u_short offset;
	flags=ntohs(ip->ip_off)&~IP_OFFMASK;
	offset=ntohs(ip->ip_off)&IP_OFFMASK;
	printf("\t%20s    ",inet_ntoa(ip->ip_src));
	printf("\t%20s    ",inet_ntoa(ip->ip_dst));
	printf("\t%5d     \t%5d     \t%5d",ip->ip_tos,ntohs(ip->ip_len),ntohs(ip->ip_id));
	printf("\t%5d     \t%5d	    \t%5d       \t%5d	    \t%5d",flags,offset,ip->ip_ttl,ip->ip_ttl,ntohs(ip->ip_sum));
	printf("\n");
}
```

> 目前不知道原因为什么这样，很遗憾，我也不懂

完整代码：

```c
#include<stdio.h>
#include<stdlib.h>
#include<unistd.h>
#include<sys/types.h>
#include<sys/socket.h>
#include<netinet/in.h>
#include<netinet/ip.h>
#include<netinet/ip_icmp.h>
#include<signal.h>
#include<arpa/inet.h>
#include<sys/time.h>
#include<string.h>
#include<netdb.h>
#include<netinet/ether.h>

#define PACK_SIZE 4096

typedef struct ip_all{
    struct Node *ip;
    unsigned int ip_src;
    unsigned int ip_dst;
    unsigned short id;
    u_int8_t protocol;
}IP_ALL;

typedef struct Node{
    struct ip *ip;
    struct Node *next;
}Node;

struct ip_all *all[10];
int countall[10]={0};
int count=0;
int count_ture=0;

void print(struct ip *ip);
int check_ip(struct ip *ip,int num);
void to_ip_all(int num,struct ip *ip);
void to_ip_node(struct ip *ip,int num);
void chongzu();
void print_list();
void swich_H(Node *l,Node *p);

int main(int argc,char* argv[]){
    int sockfd;
    unsigned int heip;
    char recv_packet[PACK_SIZE];

    if(argc<2){
        printf("输入过滤包的ip地址");
        exit(EXIT_FAILURE);
    }

    heip=(unsigned int)inet_addr(argv[1]);
 
    if((sockfd=socket(AF_PACKET,SOCK_RAW,htons(ETH_P_ALL)))<0){
        perror("socket");
	    exit(-1);
    }

    while(count<20){
        int check;

        if((check=recvfrom(sockfd,recv_packet,sizeof(recv_packet),0,NULL,NULL))<0) {
			continue;
	    }

	    count++;
        struct ip *ip = (struct ip*)(recv_packet+14);

        if(heip==ip->ip_src.s_addr){
            if(count_ture==0){
                to_ip_all(1,ip);
                count_ture++;
            }
            else{
                int i=0;
                int c_ip=0;
                while (i<count_ture)
                {
                    c_ip=check_ip(ip,i+1);
                    if(c_ip==1){
                        to_ip_node(ip,i+1);
                        break;
                    }
                    i++;
                }
                if(c_ip==0){
                    to_ip_all(i+1,ip);
                    count_ture++;
                }
            }
        }
       
        printf("%5d",count);
        print(ip);
    }
    print_list();
    printf("重组后\n");
    chongzu();
    print_list();
    return 0;
}

void print(struct ip *ip){
    u_short flags;
    u_short offset;
	flags=ntohs(ip->ip_off)&~IP_OFFMASK;
	offset=ntohs(ip->ip_off)&IP_OFFMASK;
	printf("\t%20s    ",inet_ntoa(ip->ip_src));
	printf("\t%20s    ",inet_ntoa(ip->ip_dst));
	printf("\t%5d     \t%5d     \t%5d",ip->ip_tos,ntohs(ip->ip_len),ntohs(ip->ip_id));
	printf("\t%5d     \t%5d	    \t%5d       \t%5d	    \t%5d",flags,offset,ip->ip_ttl,ip->ip_ttl,ntohs(ip->ip_sum));
	printf("\n");
}

int check_ip(struct ip *ip,int num){
    unsigned int ip_src=ip->ip_src.s_addr;
    unsigned int ip_dst=ip->ip_dst.s_addr;
    u_short id=ip->ip_id;
    u_int8_t protocol=ip->ip_p;

    if((all[num]->ip_src==ip_src)&&(all[num]->ip_dst==ip_dst)&&( all[num]->id==id)&&(all[num]->protocol==protocol)){
        return 1;
    }
    else
        return 0;
}

void to_ip_all(int num,struct ip *ip){
    all[num]=(IP_ALL*)malloc(sizeof(IP_ALL));
    Node *q=(Node*)malloc(sizeof(Node));
    char *ip_buff=(char *)malloc(PACK_SIZE);
    memcpy(ip_buff,ip,PACK_SIZE-14);
    q->next=NULL;
    q->ip=(struct ip*)ip_buff;
    all[num]->ip_src=ip->ip_src.s_addr;
    all[num]->ip_dst=ip->ip_dst.s_addr;
    all[num]->id=ip->ip_id;
    all[num]->protocol=ip->ip_p;
    all[num]->ip=q;
    countall[num]++;
}

void to_ip_node(struct ip *ip,int num){
    Node *q=(Node*)malloc(sizeof(Node));
    char *ip_buff=(char *)malloc(PACK_SIZE);
    memcpy(ip_buff,ip,PACK_SIZE-14);
    Node *p;
    p=all[num]->ip;
    while (p->next!=NULL){
       p=p->next;
    }
    p->next=q;
    q->next=NULL;
    q->ip=(struct ip*)ip_buff;
    countall[num]++;
}

void print_list(){
    int i=1;
    for(;i<=count_ture;i++){
        Node *p;
        p=all[i]->ip;
        printf("第%d列数据包：\n",i);
        while (p->next!=NULL){
            print(p->ip);
            p=p->next;
        }
        print(p->ip);
    }
}

void chongzu(){
    int i=1;
    for(;i<=count_ture;i++){
        int j=1;
        while(j<count_ture){
            Node *p,*l;
            struct ip *ip;
            p=all[i]->ip;
            l=all[i]->ip;
            ip=l->ip;
            uint16_t max=ntohs(ip->ip_off)&IP_OFFMASK;
            p=p->next;
            while (p!=NULL){
                ip=p->ip;
                //flags=ntohs(ip->ip_off)&~IP_OFFMASK;
	            uint16_t offset=ntohs(ip->ip_off)&IP_OFFMASK;
                if(offset<max){
                    max=offset;
                    swich_H(l,p);
                }
                l=l->next;
                p=p->next;
            }
            j++;
        }
    }
}

void swich_H(Node *l,Node *p){
    struct ip *ip;
    ip=l->ip;
    l->ip=p->ip;
    p->ip=ip;
}

```

参考：

外网：[c分片报文重组代码 - 超搜](https://chaosou.cc/uuz/02d7eabf81bbb4deb290d7f3b5dfbeb4d6dfb7d1d896d795e2.html)

[error：在预期整数时使用的聚合值](https://www.icode9.com/content-4-326745.html)
