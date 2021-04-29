# Web进阶类漏洞

## Java反序列化漏洞

### Java反序列化基础

序列化：将对象转换为字节序列的过程（ObjectOutputStream的writeObject()方法实现序列化）

反序列化：将字节序列还原为对象的过程（ObjectInputStream的readObject()方法实现反序列化）

序列化的作用：远程方法调用、便于通过网络传输对象至远程系统、便于存储对象在数据库或本地文件

### 反序列化:

- serialVersionUID是在JAVA序列化、反序列化时起作用的一个字段
- JAVA的序列化机制是通过判断类的serialVersionUID来验证版本一致性的
- 在进行反序列化时，JVM会把传来的字节流中的serialVersionUID与对应类的serialVersionUID进行比较，如果一致，则进行反序列化，否则就会出现异常
- 如果可序列化的类没有显示声明serialVersionUID值，会根据算法计算得到serialVersionUID，编译器实现不同可能导致计算的serialVersionUID而有所不同

#### 反序列化漏洞原理

Source接收不信任的数据进行反序列化

Sink可利用的Gadget Chains

#### 漏洞危害

RCE远程命令执行，DoS拒绝服务攻击，SSRF服务端请求伪造....

易受攻击协议：RMI（Remote Method Invocation远程方法调用）、JMX（Java Management Extension）、JMS（Java Messaging System）、AMF（Action Message Format）、Weblogic T3、Customer Application protocol（用户自定义协议）....

易受攻击组件：Weblogic、JBoss、WebSphere、Fastjson、Jenkins、JSF ViewState....

java反序列化漏洞工具：

ysoserial

该工具一个很好用的payload：URLDNS

![UrlDNS使用方法](E:\Typora\Image\UrlDNS使用方法.png)

另一个payload：CommonsCollections1

![CommonsCollections1使用方法](E:\Typora\Image\CommonsCollections1使用方法.png)

GadgetProbe

通过DNS盲打识别远程服务器classpath上的类，库和库版本

![image-20200713094006230](E:\Typora\Image\image-20200713094006230.png)

marshalsec

可以方便开启RMI和LDAP服务，还可以用于生成jackson、Xstream等payload

![image-20200713094747996](E:\Typora\Image\image-20200713094747996.png)

##### Shiro反序列化漏洞

Shiro识别：在请求的Cookie添加rememberMe=test，如果响应中包含rememberMe=deleteMe则判断为Shiro

默认没有回显，在不通外网的情况下，漏洞利用和就会很艰难了，但是可以考虑通过报错、web获取当前上下文对象、修改当前网络连接描述文件等方法获取回显

##### Fastjson反序列化漏洞

Tips：rmi限制比较多，建议用ldap

![image-20200713100208300](E:\Typora\Image\image-20200713100208300.png)

如果ldap能获取服务器访问请求，但是reference无法加载远程class文件，一般是由于JDK版本问题，需要考虑绕过JDK版本的安全限制

如果ldap能获取服务器访问请求，reference可以加载远程class文件，但是代码没有被成功执行，一般是本地编译POC的JDK版本和目标服务器版本不一致，本地编译换JDK版本即可

##### Weblogic反序列化漏洞

weblogic识别

![image-20200713100637589](E:\Typora\Image\image-20200713100637589.png)

### JAVA反序列化漏洞绕过

#### WAF

WAF时最常见的防御手段，但是WAF对于反序列化攻击的防御还是有点心有余力不足.WAF主要还是依靠黑名单规则，常见的绕过手段有：寻找新的gadget、payload加密的基本直接过，比如Shiro反序列化漏洞、还有直接拦/wls-wsat/CoordinatorPortType11的，结合weblogic特性加上;/../x即可绕过......

#### Look-Ahead Check

通过重写ObjectInputStream的resolveClass增加黑名单或者白名单类