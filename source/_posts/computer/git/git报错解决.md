---
title: git报错解决
date: 2021-12-04 00:00:00
updated: 2021-12-04 12:00:00
email: 956954649@qq.com
tags:
    - 学习
    - 编程
categories:
    - [艾小艾的学习,计算机,问题,git]
---

### push

#### Git报错解决：fatal: unable to access ‘https://github.com/.......‘: OpenSSL SSL_read: Connection was reset

文章标签： [git](https://so.csdn.net/so/search/s.do?q=git&t=blog&o=vip&s=&l=&f=&viparticle=)

昨天还可以git push代码到远程仓库，今天git push时报了这个错：fatal: unable to access 'https://github.com/.......': OpenSSL SSL_read: Connection was reset, errno 10054

![](https://img-blog.csdnimg.cn/img_convert/73579e02a2e1f6421ba09fc18b52157e.png)

产生原因：一般是这是因为服务器的SSL证书没有经过第三方机构的签署，所以才报错

参考网上解决办法：解除ssl验证后，再次git即可

git config --global http.sslVerify "false"

![](https://img-blog.csdnimg.cn/img_convert/b252098f3c4afd2985b873d75be23ad8.png)
