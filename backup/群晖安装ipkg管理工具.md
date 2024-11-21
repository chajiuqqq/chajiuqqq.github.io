> https://www.landiannews.com/archives/92174.html

安装ipkg
```bash
ssh 用户名@IP地址 #按回车输入密码 密码不显示 输入后按回车即可登陆
sudo -i #再次输入密码切换root权限
wget http://ipkg.nslu2-linux.org/feeds/optware/syno-i686/cross/unstable/syno-i686-bootstrap_1.2-7_i686.xsh
chmod +x syno-i686-bootstrap_1.2-7_i686.xsh
sh syno-i686-bootstrap_1.2-7_i686.xsh
#执行上述命令后即可安装ipkg包管理器
#安装完成后执行以下命令进行测试
ipkg update #如果没有报错就说明成功安装
```
ipkg使用
```
#首先更新软件源
ipkg update
#安装命令如下
ipkg install 软件包名称
#卸载命令如下
ipkg remove 软件包名称
#例如安装wakelan
ipkg install wakelan
```