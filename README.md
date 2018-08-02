# docker-php-nginx

1、如果在windows下，编译过程下载文件太慢，建议在C:\Users\Administrator\.docker下，打开daemon.json，更新里面内容如下：
{"registry-mirrors":[],"insecure-registries":[https://registry.docker-cn.com], "debug":true, "experimental": false}
如果在启动docker的时候报错，建议就不要加加速地址了。

2、建议docker使用配置如下：
CPU : 2或以上
MEM : 3G或以上
DISK : 50G或以上

3、软件下载地址：
composer下载地址： https://getcomposer.org/download/
Git下载地址：https://git-scm.com/downloads
docker下载地址： https://download.docker.com/win/static/stable/x86_64/




