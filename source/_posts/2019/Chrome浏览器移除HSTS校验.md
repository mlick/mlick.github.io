---
title: Chrome浏览器移除HSTS校验
categories:
  - 技术
tags:
  - 文章
date: 2019-12-28 17:34:30
---

如何解决超级烦人的证书不安全问题？

<!-- more -->

**现象**
------

针对现阶段,访问测试环境ingress域名时,  用http请求会强行跳转至https，出现如下图所示

![](/img/image2021-7-27_19-46-34.png)

然后输入 **thisunsafe**成功跳转

最后又会提示如下这个错误

![image-20230110183707107](/img/image-20230110183707107.png)

导致访问不了http请求。

  

  

**原因**
------

参考如下

[https://zh.wikipedia.org/wiki/HTTP%E4%B8%A5%E6%A0%BC%E4%BC%A0%E8%BE%93%E5%AE%89%E5%85%A8](https://zh.wikipedia.org/wiki/HTTP%E4%B8%A5%E6%A0%BC%E4%BC%A0%E8%BE%93%E5%AE%89%E5%85%A8)

  

**解决**
------

  

### **步骤一**

浏览器中输入  [chrome://net-internals/#hsts](chrome://net-internals/#hsts)   

  

### 步骤二

**Query HSTS/PKP domain**  中查询 有问题的域名

![image-20230110183835493](/img/image-20230110183835493.png)

如果查询的有结果，说明已经被HTST了，拉到最下面

** Delete domain security policies  **输入遇到问题的域名根域名  例如: **apaas-ppe1.eniot.io  **点击删除

注意保险起见输入域名 **xxx-dashboard.****apaas-ppe1.eniot.io** 再次删除一次

### 步骤三

再次在 查询 **Query HSTS/PKP domain**  中查询 

![image-20230110183905810](/img/image-20230110183905810.png)

如果出现 Not Found 表示已删除成功了，否则表示删除失败。

  

**最后**
------

再次访问 http:// 域名接口，理论上来说应该就能访问了。