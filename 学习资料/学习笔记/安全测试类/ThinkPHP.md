## ThinkPHP

Think PHP是一个快速、兼容而且简单的轻量级国产PHP开发框架，诞生于2006年初，原名FCS，2007年更名为ThinkPHP。

四种路由形式

rewrite路由形式：http://网址/分组名/控制器名/方法/参数名1/参数值1/参数名2/参数值2

兼容路由形式：

- http://网址/入口文件?s=/分组名/控制器名/方法名/参数名1/参数值1
- http://localhost/think/index.php?s=Home/User/test/id/1000

普通形式路由（get形式路由）：

- http://网址/入库文件?m=分组&c=控制器&a=方法名&参数名=参数值
- http://localhost/think/index.php?m=Home&c=User&a=test&id=1

pathinfo路由形式（默认）：

- http://网址//入口文件/分组名/控制器名/方法/参数名1/参数值1/参数名2/参数值2/
- http://localhost/think/index.php/Home/User/test/id/100

xray插件编写

长亭的一款被动代理扫描器

poc编写方便，检测较为准确

poc语法：yaml配置文件格式