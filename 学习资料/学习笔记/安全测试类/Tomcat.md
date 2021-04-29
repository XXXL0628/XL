# Tomcat

Tomcat是Apache软件基金会的核心项目，Tomcat服务器是一个免费的开放源代码的Web应用服务器，属于轻量级应用服务器。

> Tomcat是中间件，其他中间件:Weblogic、IIS、WebSphere等

默认端口8080

应用场景：

- 利用tomcat自动部署
- 利用控制台进行部署
- 增加自定义的Web部署文件(%Tomcat_Home%\conf\Catalina\localhost\AppName.xml)
- 手动修改%Tomcat Home%\conf\server.xml文件来部署web应用

攻击面分析：

中间件的安全问题主要来源于两部分：

- 一个是中间件本身由于涉及缺陷而导致的安全问题
- 另一个就是默认配置或错误配置导致的安全风险



#### Tomcat测试--配置不当

开启不必要的服务：8000、8009、10001、10002

设置弱口令：8080

Tomcat测试-8000（debug模式的默认端口）

- tomcat在进行远程调试时需要开启debug模式，在调试器和JVM之间使用JDWP进行通信
- tomcat的debug默认是不开启的，需要手动配置，默认端口为8000

Tomcat测试-8009

Tomcat有两个连接器:

- 一个连接器监听8080端口，负责建立HTTP连接。在通过浏览器访问Tomcat服务器的Web应用时，使用的就是这个连接器。
- 第二个连接器监听8009端口，负责和其他的HTTP服务器建立连接，在把Tomcat与其他HTTP服务器集成时，就需要用到这个连接器。

![image-20200713153819849](E:\Typora\Image\image-20200713153819849.png)

Tomcat测试-10001

Java Management Extension (JMX)服务用来远程监视和管理的Tomcat服务器，如果对外开放并且是空口令或者弱口令的话会产生很多安全问题，通过JavaRemote Method Invocation (RMI)进行交互。

#### 设计缺陷

CVE-2016-8735（反序列化漏洞）

CVE-2017-12615（HTTP PUT协议缺陷）

CVE-2020-1938（Tomcat AJP协议缺陷）

##### Tomcat加固措施

- Tomcat以普通系统权限去运行
- 根据业务需求去详细分配Tomcat涉及的安装目录和应用目录文件夹的读、写及执行的权限
- 保证Tomcat系统用户的密码口令符合一定的复杂度要求甚至是禁止远程登录
- 关注新漏洞并及时更新补丁

