---
title: 搭建LNMP环境（Linux+Nginx+PHP）
categories: 学习资料
tags: Linux Nginx PHP
---

## 环境说明

* Linux，在VitrualBox上搭建的Ubuntu16.04LTS的虚拟机环境
* 其他说明，软件安装前最好的了解安装方式的途径是访问软件的官网

## Nginx安装

[Nginx](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/)，在其官网上面有安装的文档。我们可以很容易的找到对应的Ubuntu下面的安装方式
```
sudo -s
nginx=stable # use nginx=development for latest development version
add-apt-repository ppa:nginx/$nginx
apt-get update
apt-get install nginx
```

OK，Nginx安装完毕。通过`nginx -h`可以查看相关的命令帮助，`nginx`启动Nginx，`nginx -s stop`停止Nginx

## PHP安装
直接从[PHP官网](http://php.net/downloads.php)上下载PHP安装文件，并解压
```
wget http://au1.php.net/get/php-5.6.20.tar.bz2/from/this/mirror
mv mirror php-5.6.20.tar.bz2 
tar -xvf php-5.6.20.tar.bz2 
cd php-5.6.20.tar.bz2 
```
* 编译安装

```
./configure --prefix=/usr/local/php --with-config-file-path=/usr/local/php/etc --enable-inline-optimization --disable-debug --enable-bcmath --enable-clendar --enable-ctype --enable-ftp --enable-gd-native-ttf --enable-magic-quotes --enable-shmop --disable-sigchild --enable-sysvsem --enable-sysvshm --enable-wddx --with-openssl  --with-xsl=/usr/local/php --enable-fpm --with-curl=/usr/local/php

make
make test
sudo make install
```
* 创建配置文件，并将其复制到正确的位置

```
cp php.ini-development /usr/local/php/etc/php.ini 
cp /usr/local/php/etc/php-fpm.conf.default /usr/local/php/etc/php-fpm.conf
编辑php-fpm.conf将用户和组修改成www
```

* 启动php-fpm服务 `/usr/local/php/sbin/php-fpm`

master进程可以理解下面几个信号
INT,TERM立刻终止，QUIT平滑终止，USR1重新打开日志文件，USR2平滑重载所有worker进程并重新载入配置和二进制模块

php-fpm关闭`kill -INT 'cat /usr/local/php/var/run/php-fpm.pid'`

php-fpm重启`kill -USR2 'cat /usr/local/php/var/run/php-fpm.pid'`

* 配置nginx使其支持PHP应用

根据上文的Nginx安装配置文件处于/usr/etc/nginx/nginx.conf，编辑配置文件使其对于.php文件的请求被传送到后端的PHP-FPM模块

```
location ~* \.php$ {
    fastcgi_index   index.php;
    fastcgi_pass    127.0.0.1:9000;
    include         fastcgi_params;
    fastcgi_param   SCRIPT_FILENAME    $document_root$fastcgi_script_name;
    fastcgi_param   SCRIPT_NAME        $fastcgi_script_name;
}
```

本人的完整配置文件是 **注意：我的配置文件使用server_name，因此需要将server_name对应的域名添加到host文件里面使其指向你的Nginx服务IP地址**

```
user www;
worker_processes auto; 
pid /run/nginx.pid;

events {
	worker_connections 768;
	# multi_accept on;
}

http {

	##
	# Basic Settings
	##

	sendfile on;
	tcp_nopush on;
	tcp_nodelay on;
	keepalive_timeout 65;
	types_hash_max_size 2048;
	# server_tokens off;

	# server_names_hash_bucket_size 64;
	# server_name_in_redirect off;

	include /etc/nginx/mime.types;
	default_type application/octet-stream;

	##
	# SSL Settings
	##

	ssl_protocols TLSv1 TLSv1.1 TLSv1.2; # Dropping SSLv3, ref: POODLE
	ssl_prefer_server_ciphers on;

	##
	# Logging Settings
	##

	access_log /var/log/nginx/access.log;
	error_log /var/log/nginx/error.log;

	##
	# Gzip Settings
	##

	gzip on;
	gzip_disable "msie6";

	# gzip_vary on;
	# gzip_proxied any;
	# gzip_comp_level 6;
	# gzip_buffers 16 8k;
	# gzip_http_version 1.1;
	# gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

	##
	# Virtual Host Configs
	##

	include /etc/nginx/conf.d/*.conf;
	include /etc/nginx/sites-enabled/*;

	server {
		listen		    80;
		server_name     blog.work.com;
		location / {
			proxy_pass	http://127.0.0.1:4000;
		}
	}
	
	server {
		listen		80;
		server_name	www.work.com;
		root		/www/www;
		index		index.html index.htm index.php;
		location ~ \.php$ {
			try_files $uri = 404;
			fastcgi_pass 127.0.0.1:9000;
			fastcgi_index index.php;
			fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
			fastcgi_param PATH_INFO $fastcgi_script_name;
			include /etc/nginx/fastcgi_params;		
		}	
	}
}

```

* 重新启动Nginx

```
sudo /etc/nginx/nginx -s -stop
sudo /etc/nginx/nginx
```



根据自己的需要进行配置安装，为了让PHP支持php-fpm，需要添加`--enable-fpm`，相关信息可以参考官方文档[Unix系统下的Nginx](http://php.net/manual/zh/install.unix.nginx.php)

### PHP安装可能遇到的问题，基本上所有的问题我们都可以在网络上找到答案

根据自己的需求我们在PHP的安装过程中会添加一些模块依赖等，如果有些模块或者软件没有在你的系统安装可能会出现编译错误。编者自己使用的环境以及上文提到的配置出现了一下问题：

* `configure: error: xml2-config not found. Please check your libxml2 installat`

根据提示我们安装libxml2`sudo agt-get install libxml2`，重新编译发现问题依然存在，google一下发现我们还需要安装libxml2-dev `sudo agt-get install libxml2-dev`，在CentOS系统里`yum install libxml2-devel`

* `configure: error: Cannot find OpenSSL's <evp.h>`
根据提示我们安装openssl libcurl4-openssl libssl-dev即可

* `configure: error: Cannot find OpenSSL's libraries`
该问题是找不到对应的库文件`ln -s /usr/lib/x86_64-linux-gnu/libssl.so  /usr/lib`

### Nginx和XAMMP共存问题

因为Nginx和Apache都是使用的80端口，所以如果要两者同时使用那么我们就需要将XAMPP的Apahce端口修改掉（这里将修改成8888）。我们找到对应的httpd.conf修改port为8888，使用`/opt/lampp restart`发现Apahce仍然启动不了报错`xampp another web server is already running`

这个地方很明显要么是我们的8888端口已经被占用或者是Apache的启动脚本有问题，很快的就排除了端口占用这个原因。那么我们找寻启动脚本`lampp`,发现还真是有检查80端口的脚本，在function startApahce我们需要将检查的端口号修改成8888即可
```
function startApache() {
	....
	....
	
	if testport 8888
	then
		$GETTEXT -s "fail."
		echo "XAMPP: " $($GETTEXT 'Another web server is already running.')
		return 1
	fi

```
