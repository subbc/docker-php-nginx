# docker-php-nginx

1、如果在windows下，编译过程下载文件太慢，建议在C:\Users\Administrator\.docker下，打开daemon.json，更新里面内容如下：
{"registry-mirrors":[http://registry.docker-cn.com],"insecure-registries":[https://registry.docker-cn.com], "debug":true, "experimental": false}

2、建议docker使用配置如下：
CPU : 2或以上
MEM : 3G或以上
DISK : 50G或以上