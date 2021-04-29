## PHPcms

Phpcms是国内领先的网站内容管理系统，同时也是一个开源的PHP开发框架。Phpcms采用模块化开发，支持自定义内容模型和会员模型，并且可以自定义字段。

常见cms

phpcms：内容管理平台，一般用于企业的网站运营或者政府发布类型的用途

帝国cms：可用作音乐系统、商城系统、产品库等用途，常用的建站工具

discuz：最常见的论坛系统

dedecms（织梦）：常用的建站工具，各种管理系统

authkey简介

phpcms_auth函数是phpcms里面为了增强程序的安全性的一个加密函数，在play.php、down.php 、 download.php等等文件用它来对用户提交的加密字符串进行解密，如果我们可以控制了phpcms_auth函数的解密，我们就可以通过Authkey注射我们的恶意代码，进行攻击。

代码结构

phpsso_server

- 类似于uc_center，实现管理网站会员的相关功能，可独立于phpcms存在

phpcms

- 主要的功能实现模块，各模块代码房子啊modules目录下