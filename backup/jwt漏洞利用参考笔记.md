#### jwt漏洞利用参考文章
https://blog.csdn.net/qingzhantianxia/article/details/129104228

****

python jwt_tool.py 要解密的内容

![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762493404322-41d1e21f-b1fd-4586-bdb9-3f0adf8fea69.png)

**解密好的效果**

![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762493404292-86d2c65b-91cb-4883-86dc-2000544b8ead.png)

**<font style="color:rgb(255, 0, 1);">jwt使用 . 点进行隔开</font>**

**<font style="color:rgb(255, 0, 1);">分别是header payload signature这三个部分</font>**

eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJsZXZlbCI6ImFkbWluIiwicGFzc3dvcmQiOiIyYWM5Y2I3ZGMwMmIzYzAwODNlYjcwODk4ZTU0OWI2MyIsInVzZXJuYW1lIjoiam9lIn0.6j3NrK-0C7K8gmaWeB9CCyZuQKfvVEAl4KhitRN2p5k

#### **bp插件**


![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762493404695-4087db14-9e2f-4310-aa86-c118a3679a27.png)

![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762493404608-647bea61-152d-4486-854a-daf3f45b968d.png)

![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762493404663-b6634531-6d88-41f6-82d3-9272026e832c.png)

![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762493404680-46e83db6-eda0-4a96-b054-97ec61831767.png)

![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762493404585-cb7c1e9c-c042-416d-92e3-927f5cb9823e.png)

bp插件安装成功之后上面出现json web tokens

#### **靶场**
base64解密 

![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762493405334-2e8e48d2-977d-4d7f-8f34-2f1510d2e332.png)

