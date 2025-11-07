[<font style="color:rgb(0, 56, 132);">https://www.bafangwy.com/study?courseNo=721§ionNo=76132</font>](https://www.bafangwy.com/study?courseNo=721&sectionNo=76132)

#### **注册：**
短信轰炸 

验证码安全问题 

密码爆破 

邮箱轰炸 

用户任意注册、批量注册 用户名枚举

 XSS（有框的地方就可以尝试插XSS，没有框也可以）

<font style="color:rgb(255, 0, 1);">一切前端和后端进行交互的时候都有可能出现xss漏洞</font>

#### **<font style="color:rgb(0, 0, 0);">登录</font>**
 短信轰炸、验证码安全问题、密码爆破、邮箱轰炸 

SQL注入 

撞库 

抓包把password字段修改为空值发送 认证凭证替换、比如返回的数据包中包含账号，修改账号就能登录到其他账号

Cookie仿冒 修改返回包的相关数据，可能会登陆到其他的用户

#### **找回密码：**
![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762485947057-08f53e96-2756-49c9-95d9-cf131d7d8867.png)

#### **会员系统：**
![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762485947016-bbe478ad-3d34-409b-84ec-adf7ebfc720b.png)

#### **传输过程**
 明文传输账户密码 

修改信息处无session/token导致csrf

 POST/COOKIE注入

#### **评论 **
POST注入

 存储型XSS 

无session/token导致CSRF

#### **验证码问题**
万能验证码

返回包中存在验证码 

删除验证码或者cookie中的值可以爆破账号密码

#### **短信轰炸**
![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762485947041-85d3bd0d-77e4-4d52-a5d4-e7ebb1fbf9e5.png)

#### **水平越权**
![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762485946982-5d55733d-2576-4d1f-900e-514dfd5bcc3c.png)

![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762485947171-58eebf23-63f1-4b68-8494-3f9971f4d2d6.png)

#### **垂直越权**
观察cookie中的session字段，可能某些字段或者参数代表身份，尝试修改

#### **数据泄露**
再找回密码处，填写数据后抓包查看返回信息，有可能存在敏感数据返回

#### **任意用户密码重置**
目前大部分都是在修改密码处参数修改

 有些是前端验证

#### **支付逻辑漏洞**
![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762485947431-12265efb-f56c-4308-b51d-9899f9bba9c0.png)

#### **登录绕过**
部分网站的身份验证放在了前端，因此只需要将response包中的相关字段进行修改，比如0改成1，false改成true，就可以登录任意用户账号

