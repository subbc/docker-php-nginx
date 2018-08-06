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

4、拉取镜像与启动
#拉取镜像
docker pull eelly-php7
docker pull eelly-nginx

# linux 需要绝对路径
docker run -p 9000:9000 -d --name=php \
	-v /data/docker/file/web:/data/web \
	-v /data/docker/file/logs:/data/logs \
	-v /data/docker/file/php7/php:/usr/local/etc/php \
	-v /data/docker/file/php7/php-fpm.d:/usr/local/etc/php-fpm.d \
	subbc/eelly-php7

docker run -p 80:80 -p 443:443 -d --name=nginx \
	--link php:php \
	-v /data/docker/file/web:/data/web \
	-v /data/docker/file/logs:/data/logs \
	-v /data/docker/file/nginx/conf.d:/etc/nginx/conf.d \
	subbc/eelly-nginx

# windows 需要绝对路径
docker run -p 9000:9000 -d \
    --name=php \
    -v D:/docker/file/web:/data/web \
    -v D:/docker/file/logs:/data/logs \
    -v D:/docker/file/php7/php:/usr/local/etc/php \
	-v D:/docker/file/php7/php-fpm.d:/usr/local/etc/php-fpm.d \
    subbc/eelly-php7
	
docker run -p 80:80 -p 443:443 -d \
    --name=nginx \
	--link php:php \
    -v D:/docker/file/web:/data/web \
    -v D:/docker/file/logs:/data/logs \
    -v D:/docker/file/nginx/conf.d:/etc/nginx/conf.d \
    subbc/eelly-nginx

# windows mariadb server
docker run -p 3306:3306 \
    --name=eelly-mariadb \
    -e MYSQL_ROOT_PASSWORD=123456 \
    -d mariadb

运行composer

# linux
docker exec eelly-php7 composer install -d api.eelly.com -vvv
# windows
winpty docker exec eelly-php7 composer install -d api.eelly.com -vvv

