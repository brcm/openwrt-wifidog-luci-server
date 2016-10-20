# OpenWRT-Wifidog之利用Luci认证

OpenWRT下实现Portal认证(WEB认证) 我介绍了OpenWRT环境下实现Wifidog认证的办法，文末我曾经写道“有的人可能还会问，能不能把这些脚本集成到路由器当中，我的回答是能，只要你的脚本的功能不多，问题应该不大，但是这么做的风险比较大，路由的负载比较高，导致路由的运行会很不稳定，甚至经常死机，这也是笔者亲身实践的结果，所以笔者不建议这么做”。

折腾间突然冒出一个想法，既然OpenWRT有uhttpd和Luci作为Web服务提供图形配置界面，那么可否把Wifidog的认证页面集成到Luci当中呢？之前笔者曾经测试把Wifidog的Web认证页面集成到OpenWRT当中，其实现方法是用传统的LMNP方案，即在OpenWRT上安装Nginx、PHP和Mysql，部署Wifidog的认证服务。但是效果十分不理想，OpenWRT将长期处于高负载状态，甚至影响到了用户的正常上网，稳定性也得不到保证，经常死机，故这种方案不可取，也是我不建议这么做的原因。

OpenWRT中集成了uhttp和Luci作为Web服务提供图形配置界面。uhttpd是一个轻量级的Web服务器，而Luci是用Lua这种轻量级的脚本语言编写的，其性能十分优秀，这也是OpenWRT选择他们的原因，

详细研究Lua和Luci的文档以及相关资料后，终于实现了这个想法，并把程序编译成ipk(下载见文末)，需要说明的是Wifidog需自行安装。

安装完成后，Wifidog的默认登录地址是http://192.168.1.1/cgi-bin/luci/login/(路由的lan IP不是192.168.1.1的需自行修改)，默认的用户名是root，密码是admin，注意须在Wifidog开启的情况下登录才能成功。如果想新增用户和修改密码，修改/etc/wifidog.auth文件，其格式为username:password，中间用英文半角冒号分隔，每个用户分别占一行。

![image](http://talk.withme.me/wp-content/uploads/2014/07/login.png)

关于安装完成后Wifidog的配置，我在ipk里加入了wifidog.conf.example，安装完成后位于/etc/目录下，但仍需要根据你的环境作相应的配置，需要配置的参数有ExternalInterface、GatewayInterface、GatewayAddress，其中ExternalInterface指的是你的路由的WAN的接口，如果是VLAN接口就填写VLAN接口；GatewayInterface指的是你的路由的LAN的接口，一般是br-lan；GatewayAddress指的是你的路由的LAN的IP地址。关键配置AuthServer已经提供，如果你想了解AuthServer的配置细节，你可以参见本博客的相关文章。

如果你有HTML+CSS甚至是DIV+CSS编程基础和经验，你可以自定义登录认证页面，其htm页面位于/usr/lib/lua/luci/view/wifidog/文件夹下，CSS等资源文件位于/www/wifidog/文件夹下。

经过测试，其稳定性果然提高不少。不过笔者仍然建议，如果你的WIFI接入用户数大于10，独立的认证服务器仍然是很有必要的。另外，特别提醒，如果你的WIFI是开放的，Luci配置登录界面也会暴露，你的ROOT密码如果过于简单，就会存在安全风险。

### 附件下载
[luci-app-wifidog-authmini_1.0-1_all.ipk](http://talk.withme.me/download/luci-app-wifidog-authmini_1.0-1_all.ipk) (全平台通用)
