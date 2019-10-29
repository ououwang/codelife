

1、替换YUM源
yum -y install epel-release 
rpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm

2、php7.2安装
yum -y install php72w

3、安装扩展
yum -y install php72w-cli php72w-fpm php72w-common 
安装成功可通过 “php -v ”查看是否安装成功

4、安装pecl
yum -y install php72w-devel
yum -y install php72w-pear

5、安装swoole（确认gcc编译器已安装，如果没有运行 yum install gcc*）
pecl install swoole

6、加入PHP扩展
vim /etc/php.ini
最后面添加 extension=swoole.so
通过 php -m 可查看是否安装成功

7、安装中出现的问题，解决方案
https://wiki.swoole.com/wiki/page/1013.html

openssl安装：
yum install openssl
yum install openssl-devel


————————————————
版权声明：本文为CSDN博主「快乐木头人」的原创文章，遵循 CC 4.0 BY-SA 版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/weixin_35242653/article/details/90691960