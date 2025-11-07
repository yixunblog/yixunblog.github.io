### **课程大纲**
1、什么是CSRF漏洞？

2、CSRF案例分析

3、CSRF漏洞挖掘

4、CSRF漏洞防御

#### **什么是CSRF漏洞?**
**CSRF(XSRF)**

Cross-Site Request Forgery<font style="color:rgb(243, 50, 50);">跨站请求伪造</font>

##### **CSRF漏洞介绍：**
参考网址：

[<font style="color:rgb(0, 56, 132);">(121条消息) CSRF漏洞_不语.先生的博客-CSDN博客_csrf漏洞</font>](https://blog.csdn.net/weixin_39861994/article/details/121035470)

[<font style="color:rgb(0, 56, 132);">(121条消息) CSRF漏洞详解_poggioxay的博客-CSDN博客_csrf漏洞</font>](https://blog.csdn.net/m0_55854679/article/details/123077656)

CSRF是指利用受害者尚未失效的身份认证信息（ cookie、会话

等信息），诱骗其点击恶意链接或者访问包含攻击代码的页面，在受害人不知情的情况下

##### **CSRF实现流程**
![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762485364074-e2053d63-02cb-43ac-a327-18af838e943e.png)

![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762485364503-87b81288-9f80-4a41-9661-39207929be5c.png)

#### **案例演示：**
下面的两个.zip文件就是环境在小皮中创建网站

![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762485364078-651c73d9-5ceb-4361-aec2-6b0679f7f19a.png)

<font style="color:rgb(243, 50, 50);">仿真CSRF练习靶场</font>

通过cookies信息来制造跨站请求伪造

先通过index.html登陆入银行

用户名1100，密码123456

再打开http://localhost/bank/exciting.html

