# dlut_eda_network_auto_login_openwrt

> 大连理工大学开发区校区dlut-eda网络openwrt自动登陆、开机自启配置

## 第一步 下载login.sh文件

首先需要感谢bboymega大佬的 [dlut-eda-shell-login](https://github.com/bboymega/dlut-eda-shell-login) dlut_eda_login.sh文件

由于OpenWrt中curl可能不能进行compressed的工作（windows下也有可能出现相同的错误），我们需要将curl命令后面带有的 --compressed全部去掉。去掉compressed功能不会影响正常的网络连接。

这里我也给不想自己更改文件的小伙伴们一份更改好的文件 [dlut_eda_login.sh](https://github.com/MoLiYue/dlut_eda_network_auto_login_openwrt/blob/main/dlut_eda_login.sh) 自行下载即可。

## 第二步 将文件上传至OpenWrt路由器

接下来使用scp（linux）或者WinSCP（windows）将文件上传至OpenWrt路由器。

linux：
`scp /你的文件目录/dlut_eda_login.sh root@192.168.1.1:/etc`

WinSCP有图形化界面自己将文件上传至/etc下即可。

文件上传位置可自定义，但是不要放入`/tmp/tmp`等会自动删除的位置。

## 第三步 运行login.sh文件

使用ssh（linux）或者PuTTY（windows）将连接至OpenWrt路由器。

在路由器命令行下执行`/etc/dlut_eda_login.sh -u "你的用户名" -p "你的密码"`

显示Internet connected字样表示连接成功。

## 第四步 开机自动运行此脚本文件

如果有需要开机自动运行此脚本文件的同学请继续学习。

### 方法1 使用crontab计划任务

在OpenWrt下找到计划任务，在编辑文件处添加`@reboot /etc/dlut_eda_login.sh -u "你的用户名" -p "你的密码"`，之后保存。

![](https://github.com/MoLiYue/dlut_eda_network_auto_login_openwrt/blob/main/pictures/2021-09-10_09-51.jpg)

### 方法2 使用本地启动脚本

在OpenWrt下找到启动项，在最下面找到本地启动脚本，在里面添加`/etc/dlut_eda_login.sh -u "你的用户名" -p "你的密码"`，之后保存。

![](https://github.com/MoLiYue/dlut_eda_network_auto_login_openwrt/blob/main/pictures/2021-09-10_09-54.jpg)

### 方法3 直接设置启动项

首先需要下载 [脚本文件](https://github.com/MoLiYue/dlut_eda_network_auto_login_openwrt/blob/main/dlut_eda_login) 更改里面的用户名和密码，再通过**第二步**的方法(`scp /你的文件目录/dlut_eda_login root@192.168.1.1:/etc/init.d`)上传至`/etc/init.d`。

给脚本添加可执行权限`chmod 777 dlut_eda_login`

创建一个软连接`/etc/init.d/dlut_eda_login enable`，实质上是在`/etc/rc.d`创建了一个软连接。

设置完成。

# 基于单线多拨的login.sh

基于多拨的脚本文件为 [dlut_eda_login.sh](https://github.com/MoLiYue/dlut_eda_network_auto_login_openwrt/blob/main/%E5%9F%BA%E4%BA%8E%E5%A4%9A%E6%8B%A8%E7%9A%84login/dlut_eda_login.sh) 在这个文件中需要使用的同学自助更改`curl --interface`后的vwan接口名称。

本脚本仅在OpenWrt上尝试成功使用过。所以接下来我要介绍在OpenWrt上的使用方法。

首先需要确认我们使用的OpenWrt上是否支持**多线多拨**、**负载均衡**功能，如果支持则可以继续使用，否则请重新编译OpenWrt固件。

首先启动多线多拨功能，设置如下图：

![](https://github.com/MoLiYue/dlut_eda_network_auto_login_openwrt/blob/main/pictures/2021-09-19_16-09.jpg)

之后设置接口，将接口改成DHCP客户端。再在**高级设置**中设置**使用网关跃点**

*注意：网关跃点在各个vwan下要设置不同的数值*建议20、21、22这三个数。

![](https://github.com/MoLiYue/dlut_eda_network_auto_login_openwrt/blob/main/pictures/2021-09-19_16-20.jpg)

之后再回到**负载均衡**，查看**接口**、**成员**，将与vwan有关的接口和成员留下，将与wan有关的接口去掉。

在**策略**下的***balanced**策略中点击修改将wan去掉，保留与vwan相关的内容。

最终设置如下：

![](https://github.com/MoLiYue/dlut_eda_network_auto_login_openwrt/blob/main/pictures/2021-09-19_16-25.jpg)

![](https://github.com/MoLiYue/dlut_eda_network_auto_login_openwrt/blob/main/pictures/2021-09-19_16-26.jpg)

![](https://github.com/MoLiYue/dlut_eda_network_auto_login_openwrt/blob/main/pictures/2021-09-19_16-27.jpg)

