**个人看法：现在企业中xss可能比较少了，如果在挖掘中测试xss直接返回405或者403，500这种极有可能是他们加上了waf（web安全防火墙）。一般都是绕不过去我们可以尝试其他漏洞，或者找旁站！**

**1.XSS平台**

DVWA靶场通关-已完结（可参考）  [<font style="color:#003884;">https://blog.csdn.net/Yb_140/article/details/122632752</font>](https://blog.csdn.net/Yb_140/article/details/122632752)

** (XSS)(基于 DOM 的跨站脚本)DOM Based Cross Site Scripting**

**Low（低级）**

<font style="color:#f33232;">跨站脚本攻击，是指攻击者在页面中注入恶意的脚本代码，当受害者访问该页面时，恶意代码会在其浏览器上执行，需要强调的是，XSS不仅仅限于JavaScript，还包括flash等其它脚本语言。</font>

DOM是一个与平台、编程语言无关的接口，它允许程序或脚本动态地访问和更新文档内容、结构和样式，处理后的结果能够成为显示页面的一部分。DOM中有很多对象，其中一些是用户可以操纵的，如uRI ，location，refelTer等。客户端的脚本程序可以通过DOM动态地检查和修改页面内容，<font style="color:#ff0001;">它不依赖于提交数据到服务器端，而从客户端获得DOM中的数据在本地执行，如果DOM中的数据没有经过严格确认，就会产生DOM—based XSS漏洞。</font>

**<font style="color:#000000;">注入代码,直接弹窗显示</font>**

<font style="color:#f33232;">?default=<script>alert(11)</script></font>

![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762496437933-c28a959b-4908-43dd-b4e4-8abe954b82f7.png)

**Medium（中级）**

<font style="color:#f33232;">简单来说，就是过滤掉了“<script”</font>，当函数匹配到 <script 字符串的时候就会将URL后面的参数修正为 ?default=English

在这里可以通过onerror事件，在装载文档或图像的过程中如果发生了错误就会触发

**注入代码**

···
?default=</option></select><img src=x onerror=alert(1)>
···
![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762496438307-57376f53-9303-4087-a116-7a656d34c0c8.png)

**High（高级）**

<font style="color:#ff0001;">设置了白名单，只对default进行检查，可以使用&连接另一个自定义变量来绕过</font>

构造

**注入代码**

English&<script>alert(1)</script>

![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762496438641-4ff2d70a-ba57-42d3-98f2-f12a825d555f.png)

**Impassible(****<font style="color:#000000;">无动于衷</font>****)**

**这一关看来什么反应都没有，**

源码解析：

```php
<?php # Don't need to do anything, protction handled on the client side ?>
```

不需要做任何事情，保护由客户端处理

**(XSS)(反射跨站脚本)Reflected Cross Site Scripting **

<font style="color:#f33232;">是非持久型，参数型的跨站脚本</font>

**Low（低级）**

<font style="color:#f33232;">XSS攻击需要具备两个条件：</font>

需要向web页面注入恶意代码；

这些恶意代码能够被浏览器成功的执行

XSS反射型漏洞

反射型XSS的触发有后端的参与，而之所以触发XSS是因为后端解析用户在前端输入的带有XSS性质的脚本或者脚本的data URI编码，后端解析用户输入处理后返回给前端，由浏览器解析这段XSS脚本，触发XSS漏洞。

基本原理就是通过给别人发送带有恶意脚本代码参数的URL，当URL地址被打开时，特定的代码参数会被HTML解析，执行，如此就可以获取用户的COOIKE，进而盗号登陆。

<font style="color:#f33232;">服务器并没有对 name 参数做任何的过滤和检查。</font>

注入代码

<script>alert(1)</script> 进行尝试

![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762496438942-cbb6bf7f-00f9-4032-8096-b069c74e409f.png)

**Medium（中级）**

源码分析

```php
<?php
header ("X-XSS-Protection: 0");
// Is there any input?
if( array_key_exists( "name", $_GET ) && $_GET[ 'name' ] != NULL ) {
    // Get input
    //将输入中的<script>转化为空
    $name = str_replace( '<script>', '', $_GET[ 'name' ] );
    // Feedback for end user
    echo "<pre>Hello ${name}</pre>";
}
?>
```

<font style="color:#f33232;">会检查 name 参数中是否有 “< script >”，如果有则替换为空</font>

可以使用<font style="color:#f33232;">大小写绕过</font>

<sCript>alert(1)</ScRipt>

或者

<sc<script>ript>alert(1)</script>

![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762496439244-dce03b55-b8d4-46bf-b54a-0306bc8136aa.png)

**High（高级）**

源码分析

```php
<?php
header ("X-XSS-Protection: 0");
// Is there any input?
if( array_key_exists( "name", $_GET ) && $_GET[ 'name' ] != NULL ) {
    // Get input
    //使用通配符，完全匹配scriptN，所以有关script的标签全被过滤
    $name = preg_replace( '/<(.*)s(.*)c(.*)r(.*)i(.*)p(.*)t/i', '', $_GET[ 'name' ] );
    // Feedback for end user
    echo "<pre>Hello ${name}</pre>";
}
?>
```

preg_replace() 函数执行一个正则表达式的搜索和替换，**“*” **代表一个或多个任意字符，“i” 代表不区分大小写。也就是说 “< script >” 标签在这里被完全过滤了，但是我们可以通过其他的标签例如 img、body 等标签的事件或者iframe 等标签的 src 注入 JS 攻击脚本。

<font style="color:#f33232;">注入代码</font>

<img src="#" onerror=alert(1)>

![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762496439852-82408a42-b5e7-4c69-a64e-d43a4ed0cd0d.png)

**Impassible(****<font style="color:#000000;">无动于衷</font>****)**

<font style="color:#4D4D4D;">源码分析</font>

```php
<?php
// Is there any input?
if( array_key_exists( "name", $_GET ) && $_GET[ 'name' ] != NULL ) {
// Check Anti-CSRF token
checkToken( $_REQUEST[ 'user_token' ], $_SESSION[ 'session_token' ], 'index.php' );
// Get input

//转码
& （和号） 成为 &
" （双引号） 成为 "
' （单引号） 成为 '
< （小于） 成为 <
（大于） 成为 >$name = htmlspecialchars( $_GET[ 'name' ] );
// Feedback for end user
echo "<pre>Hello ${name}</pre>";

}
// Generate Anti-CSRF token
generateSessionToken();
?>
```

<font style="color:#4D4D4D;">htmlspecialchars() 函数用于把预定义的字符 "<" 和 ">" 转换为 HTML 实体，防止了我们注入 HTML 标签。htmlspecialchars 函数会将 < 和 > 转换成 html 实体而不是当做标签，所以我们插入的语句并不会被执行。</font>

**htmlspecialchars详解**

[<font style="color:#003884;">(120条消息) htmlspecialchars详解_The-Back-Zoom的博客-CSDN博客_html_special_chars</font>](https://blog.csdn.net/qq_53568983/article/details/123677113)

**(XSS)(存储跨站脚本) Stored Cross Site Scripting**

<font style="color:#F33232;">是持久性跨站脚本，</font><font style="color:#4D4D4D;">持久性体现在xss代码不是在某个参数中，</font><font style="color:#F33232;">而是写进数据库或文件等可以长久保存数据的介质中，</font><font style="color:#4D4D4D;">储存性xss通常发生在留言板中，我们可以在留言板留言，把恶意代码写进数据库中。</font>

**Low（低级）**

**在留言板中注入代码**

<font style="color:#4D4D4D;">注入代码</font>

<script>alert("hello")</script>

![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762496440134-f89724c8-7948-4158-9c0d-2020da2d26cb.png)

同样name框中也可以注入代码不过在这里做了限制需要修改前端的html代码进行注入

![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762496440425-baebbd00-3c7f-44a5-882b-d27afcfdd682.png)

一样注入成功！

![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762496440784-f49f7f9f-8c5c-49fc-a1c3-805efb6890c5.png)

再次访问此页面时，就会有次弹窗

成功后更新数据库

**Medium（中级）**

**这里我只挑了部分源码**

Message处使用了htmlspecialchars()函数，将字符全部转为了HTML实体，因此Message处无法使用XSS形成攻击。

<font style="color:#f33232;">name处做了长度限制，因此考虑使用抓包在BP中修改name的值，考虑使用双写或者大小写去绕过。</font>

```php
$message = strip_tags( addslashes( $message ) );
$message = ((isset($GLOBALS["___mysqli_ston"]) && is_object($GLOBALS["___mysqli_ston"])) ? mysqli_real_escape_string($GLOBALS["___mysqli_ston"],  $message ) : ((trigger_error("[MySQLConverterToo] Fix the mysql_escape_string() call! This code does not work.", E_USER_ERROR)) ? "" : ""));
$message = htmlspecialchars( $message );
```

<scRIPt>alert(1)</SCript>

<scr<script>ipt>alert(1)</script>

![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762496441118-bb06f883-b64f-4ac1-927b-c64b8217d9f9.png)

**High（高级）**

与11的High相同，script被过滤

使用BP改name即可

<img src=1 onerror=alert(1)>

**Impassible**

源码解析：

```php
<?php
if( isset( $_POST[ 'btnSign' ] ) ) {
// Check Anti-CSRF token
checkToken( $_REQUEST[ 'user_token' ], $_SESSION[ 'session_token' ], 'index.php' );
// Get input
$message = trim( $_POST[ 'mtxMessage' ] );
$name    = trim( $_POST[ 'txtName' ] );

// Sanitize message input
$message = stripslashes( $message );
$message = ((isset($GLOBALS["___mysqli_ston"]) && is_object($GLOBALS["___mysqli_ston"])) ? mysqli_real_escape_string($GLOBALS["___mysqli_ston"],  $message ) : ((trigger_error("[MySQLConverterToo] Fix the mysql_escape_string() call! This code does not work.", E_USER_ERROR)) ? "" : ""));
$message = htmlspecialchars( $message );

// Sanitize name input
$name = stripslashes( $name );
$name = ((isset($GLOBALS["___mysqli_ston"]) && is_object($GLOBALS["___mysqli_ston"])) ? mysqli_real_escape_string($GLOBALS["___mysqli_ston"],  $name ) : ((trigger_error("[MySQLConverterToo] Fix the mysql_escape_string() call! This code does not work.", E_USER_ERROR)) ? "" : ""));
$name = htmlspecialchars( $name );

// Update database
$data = $db->prepare( 'INSERT INTO guestbook ( comment, name ) VALUES ( :message, :name );' );
$data->bindParam( ':message', $message, PDO::PARAM_STR );
$data->bindParam( ':name', $name, PDO::PARAM_STR );
$data->execute();

}
// Generate Anti-CSRF token
generateSessionToken();
?>
```

<font style="color:#4D4D4D;">对Name和Message都使用了htmlspecialchars()函数做了过滤，还加了token值，进一步提高了安全性。</font>

