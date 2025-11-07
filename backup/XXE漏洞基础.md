可参考网址：

[<font style="color:rgb(0, 56, 132);">(124条消息) XXE漏洞原理/防御_士别三日wyx的博客-CSDN博客_xxe原理利用防御</font>](https://blog.csdn.net/wangyuxiang946/article/details/118797832)

#### **课程大纲**
1、XML基础知识

2、什么是XXE

3、XXE利用方式

4、XXE防御

#### **xml基础知识**
<font style="color:rgb(243, 50, 50);">可扩展标记语言</font>

XML被设计用来传输和存储数据。

XML语言没有预定义的标签，允许作者定义自己的标签和自己的文档结构。

#### **xml用途**
**可做配置文件**

![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762492531993-75a77b44-9d22-4993-bc2d-5c2dcf2ad825.png)

**交换数据**

![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762492531659-282a662d-ced6-4b58-aa11-183bcc06ea42.png)

**xml内容**

![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762492532004-067b5482-b7b0-4046-80e5-3778beda350f.png)

**XML格式要求**

XML文档<font style="color:rgb(243, 50, 50);">必须有根元素</font>

XML文档<font style="color:rgb(243, 50, 50);">必须有关闭标签</font>

XML标签<font style="color:rgb(243, 50, 50);">对大小写敏感</font>

XML元素<font style="color:rgb(243, 50, 50);">必须被正确的嵌套</font>

XML属性<font style="color:rgb(243, 50, 50);">必须加引号</font>

**XML格式校验**

<font style="color:rgb(243, 50, 50);">DTD（Document Type Definition ）文档类型定义</font>

**DTD内容之元素**

![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762492531573-c9e4641c-8569-4760-b529-b67cbbe8a797.png)

**DTD内容之实体**

![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762492531764-bc75d12e-97f5-46b2-8f19-cb9d524f4e54.png)

** 实体**

![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762492532263-01fec0dc-a7e2-4ab1-b546-f1522790593e.png)

![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762492532658-ebfe1da6-27d6-480c-9c5f-384ea4ded363.png)

**外部实体引用协议**

![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762492532514-ff12fae7-374b-4ca4-81cf-fd0cd527ee1f.png)

**不同语言支持的协议**

![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762492532505-d1c4e545-8f87-4a64-aedd-3f58d5d8b77e.png)

**PHP扩展**

![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762492532495-990ecaad-876f-4b4e-a957-41d4bf5f27fe.png)

**完整的XML内容**

![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762492532973-3bbd7223-7e33-492f-8c5c-5c6ffa01f080.png)

