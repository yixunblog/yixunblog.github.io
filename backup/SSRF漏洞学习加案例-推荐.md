#### <font style="color:rgba(0, 0, 0, 0.85);">0x01 SSRF漏洞简介</font>
**<font style="color:rgb(102, 102, 102);">1</font>****<font style="color:#000000;">.SSRF漏洞概述</font>**<font style="color:#000000;">  
</font><font style="color:#000000;">SSRF(Server-Side Request Forgery:服务器端请求伪造) 是一种由攻击者构造形成由服务端发起请求的一个安全漏洞。  
</font><font style="color:#000000;">一般情况下，SSRF攻击的目标是从外网无法访问的**内部系统**。（因为它是由服务端发起的，所以它能够请求到与它相连而与外网隔离的内网。也就是说可以利用一个网络请求的服务，当作跳板进行攻击）</font>

![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762572106138-3994b668-2012-45a5-bea0-6ca0da6053e2.png)

#### 2.SSRF漏洞产生原因
  
SSRF 形成的原因往往是由于服务端提供了从其他服务器应用获取数据的功能且没有对目标地址做过滤与限制。  
如：从指定URL地址获取网页文本内容，加载指定地址的图片，下载等。利用的就是服务端的请求伪造。ssrf是利用**存在缺陷的web应用作为代理**攻击远程和本地的服务器。

#### 3.容易出现SSRF的地方
1. 转码服务
2. 在线翻译
3. 图片加载与下载(通过URL地址加载或下载图片)
4. 图片、文章收藏功能
5. 网站采集、网页抓取的地方。
6. 头像的地方。(远程加载头像)
7. 一切要你输入网址的地方和可以输入ip的地方。

<img width="1005" height="91" alt="Image" src="https://github.com/user-attachments/assets/90c99d71-61ca-42a5-b2c5-f448cb52ec0d" />

#### 4.如何测试 SSRF 漏洞
<font style="color:#DF2A3F;">1.先验证对外请求能力，使用dnslog看看是否有http请求，总的来说，若只有DNS协议请求，存在 SSRF 的概率不大。</font>

 2.再换成src提供的靶子站或者内网ip地址127.0.0.1测试，如果这里是盲打ssrf就看看请求时间的变化。

再利用响应时间差异判断内网地址访问情况” 的思路，成功挖掘出盲 SSRF 漏洞。这种方法的核心是利用不同网络目标在被请求时响应时间的不同，来间接判断服务器是否能发起对特定内网地址的请求，从而在没有直接回显的情况下发现 SSRF 漏洞。

#### 5.SSRF 案例实战
参考别人的案例

##### <font style="color:rgba(0, 0, 0, 0.9);">案例:export-pdf-ssrf:</font>
<font style="color:rgba(0, 0, 0, 0.9);">这是关于pdf导出的ssrf技巧。我相信大家在很多场景下都遇见过关于导出功能的点。例如：</font>

<font style="color:rgb(85, 85, 85);">文章导出为pdf</font>

<font style="color:rgb(85, 85, 85);">项目导出为pdf</font>

![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762572618713-2688973d-cb39-4cca-9ccf-1a8e68637401.png)<font style="color:rgba(0, 0, 0, 0.9);">我在对一些资产进行访问观察时，发现了此处。这是一个将文章导出为pdf格式的功能点。这时我随便写下了一点内容，并点击导出后进行抓包。</font>![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762572648423-26e4f90a-c4bd-4291-9a16-92af336d2b70.png)

<font style="color:rgba(0, 0, 0, 0.9);">burp收到请求后，我观察此数据包，发现了一个非常有趣的参数：html，对此分析以后，发现这是后端将前端获取到的内容转换成html格式再传入后端导出为pdf格式的文件。</font>

<font style="color:rgba(0, 0, 0, 0.9);">ps：此包非常大，导致我的电脑接收的时候卡顿了10几秒，所以我并未截取原始数据包的截图。</font>

<font style="color:rgba(0, 0, 0, 0.9);">这时，我思考到了html中的iframe标签，</font>![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762572681514-18b3ff3d-14d9-47d0-a448-85b82fa7bf08.png)

```php
<iframe src="http://www.jd.com">
```

<font style="color:#DF2A3F;">IFRAME是HTML标签，作用是文档中的文档，或者浮动的框架(FRAME)。iframe元素会创建包含另外一个文档的内联框架</font>

<font style="color:rgba(0, 0, 0, 0.9);">是的我想到可以使用iframe标签包含一个地址，后端在处理此标签时，是否会代出内容后整理成pdf文件返回给我。</font>![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762572786643-ec2cb5b6-f644-4142-b416-e6a52e9f4215.png)

<font style="color:#DF2A3F;">事实可能并不为我所愿。他只返回了一个空的iframe框架给我。这时我依然并未放弃，因为我认为此功能点可能做过漏洞修复处理。</font>

<font style="color:rgba(0, 0, 0, 0.9);">这时我想起团队群内某位队员发过的一篇文章：</font><font style="color:#117CEE;">https://forum.butian.net/share/1497</font><font style="color:rgba(0, 0, 0, 0.9);">（我在熟读并背诵以后，我觉得可以尝试一下meta标签）,并且设置为0秒刷新请求。</font>

<font style="color:rgba(0, 0, 0, 0.9);"></font>

```php
<meta http-equiv="refresh" content="0;url=http://xxx.xxx.com" />
```

![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762572915598-6691eb65-ab08-4915-b01c-0a6b08dfb04b.png)



```php
{
"html": "<li>1</li><meta http-equiv=\"refresh\" content=\"0;url=http://ssrf.jd.local/c3f3f53c12674acdc9855f47b85299f0.html \"/><iframe src=\"http://ssrf.jd.local/c3f3f53c12674acdc9855f47b85299f0.html \"",
"exportType":2
}
```



![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762572998552-a3926c10-6429-4c5d-a494-88b839ccbce7.png)



<font style="color:rgba(0, 0, 0, 0.9);">当我拿着返回的pdf文件下载地址的时候，我发现成功访问了jd的ssrf测试地址；</font>

![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762573068319-31df9d71-b31a-4977-9d88-923727ac3ddb.png)

案例原文发于微信公众号（None安全团队）仅供学习参考：[https://mp.weixin.qq.com/s?__biz=MzIxOTk2Mjg1NA==&mid=2247485568&idx=1&sn=c5577184be31c63381c0caa652773c05&chksm=97d20409a0a58d1f35acd7746508fcb889247bb372953b506db4ace6015662ddad265d734e98&scene=126&sessionid=1689573443&key=2a83edf5b0c74434034683575b04ba4587fc5be1cb66907729e60823536f8fd835493d9855c9c64a003164fdd3b6018be050d247d792b326d3092cf739131f4bbecac37c51ffb3ada6eebbc8c04e57152bfe4832225807c2263adb2fbb4c757c1c8851f26195f3c99aee77813c95e73c253e63e64faad481899e7cab8307d6a4&ascene=15&uin=MzgxODQ4MjMz&devicetype=Windows+10+x64&version=63060012&lang=zh_CN&session_us=gh_e3af3e863724&countrycode=GY&exportkey=n_ChQIAhIQjI5FHWl3EiM6mCffDxKrERLvAQIE97dBBAEAAAAAAD3lBkd7JlEAAAAOpnltbLcz9gKNyK89dVj01%2Ft4q885nwvSVGdbTJQqJ5Zc%2BMakmeiogvmqj8INydhsr87YeWPkzETaxRiDR2xQguM%2F4zfFtTOFydSsebqip1VG8dA3M6h4IHtytUTSgQmpbP5pakAQVmUatKqbWA%2BPFYXLcqx0UFarl%2F2pMIkYY%2BFqnY%2BvuWXugulNE9PuheYrlFgm411zTgaxhAResFDO8e8IADDuKRxwDqGhre0pwPc7wPttsDN6yUA2gsEJ7aG%2FkQDVQGSvCsivCt1mj3758NZXmHQAy4%2BL&acctmode=0&pass](url)