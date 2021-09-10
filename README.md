# dlut_eda_network_auto_login_openwrt

> 大连理工大学开发区校区dlut-eda网络openwrt自动登陆、开机自启配置

## 第一步 下载login.sh文件

首先需要感谢bboymega大佬的 [dlut-eda-shell-login](https://github.com/bboymega/dlut-eda-shell-login) dlut_eda_login.sh文件

由于OpenWrt中curl可能不能进行compressed的工作（windows下也有可能出现相同的错误），我们需要将curl命令后面带有的 --compressed全部去掉。去掉compressed功能不会影响正常的网络连接。

这里我也给不想自己更改文件的小伙伴们一份更改好的文件 [tmp](tmp) 自行下载即可。

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

### 方法2 使用本地启动脚本

在OpenWrt下找到启动项，在最下面找到本地启动脚本，在里面添加`/etc/dlut_eda_login.sh -u "你的用户名" -p "你的密码"`，之后保存。

### 方法3 直接设置启动项

首先需要将写好的 [脚本文件](tmp) 通过**第二步**的方法(`scp /你的文件目录/dlut_eda_login root@192.168.1.1:/etc/init.d`)上传至`/etc/init.d`。

给脚本添加可执行权限`chmod 777 dlut_eda_login`

创建一个软连接`/etc/init.d/dlut_eda_login enable`，实质上是在`/etc/rc.d`创建了一个软连接。

设置完成。
