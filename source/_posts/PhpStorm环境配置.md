---
title: mac PhpStorm环境配置
date: 2016-09-30 10:12:03
tags: mac PhpStorm apahce
---

### 环境介绍
* apache、php 使用 mac 系统自带
* apache 相关配置文件位于 /etc/apache,
	* 默认已加载 php 模块,加载模块文件位于 /etc/apache2/other/php5.conf
	* 页面根路径为 /Library/WebServer/Documents
* php 配置文件为 ／etc/php.ini

### 下载 xdebug 

	https://xdebug.org/wizard.php
	将phpinfo()的源代码复制到文本框中，xdebug会提示如何配置和下载哪个版本的xdebug。

### 配置 php.ini

```
$ sudo cp xdebug.so /usr/lib/php/extensions/no-debug-non-zts-20121212

$ sudo vi /etc/php.ini
[xdebug]
zend_extension=/usr/lib/php/extensions/no-debug-non-zts-20121212/xdebug.so
xdebug.remote_autostart=on
xdebug.remote_enable=on
xdebug.remote_mode="req"
xdebug.remote_port=9000
xdebug.remote_handler="dbgp"
xdebug.idekey="PhpStorm"
```
** 重启 apache，查看 phpinfo 能够看到 xdebug 模块的信息，表明配置成功 **

### 配置 PhpStorm
* PhpStorm>Preferences>Languages & Frameworks>PHP
> 配置Php

* PhpStorm>Preferences>Languages & Frameworks>PHP>Debug
> XDebug port填9000，其他默认

* PhpStorm>Preferences>Languages & Frameworks>PHP>Debug>DBGp Proxy
> IDE key = PhpStorm，host=localhost，port=80

* PhpStorm>Preferences>Languages & Frameworks>PHP>Servers
> 填写服务器端的相关信息，name、host，port，debugger选XDebug

### Chrome 安装 debug 插件
>https://chrome.google.com/webstore/detail/jetbrains-ide-support/hmhgeddbohgjknpmjagkdomcpobmllji

### Debug
* 点击 PhpStorm 中右上角的「小电话」按钮:「红叉」变「绿」
* 点击 Crome 右上的 「JB」，连接到 PhpStorm
* 此时在 Chrome 中打开php页面则可以开始调试了

### apache 虚拟目录配置
```
$ sudo vi /etc/apache2/extra/httpd-vhosts.conf
<VirtualHost *:80>
    ServerAdmin gaosheng08@gmail.com
    ServerName test.php.com
    ErrorLog "/private/var/log/apache2/php.test.com-error_log"
    CustomLog "/private/var/log/apache2/php.test.com-access_log" combined

    Alias /test "/Users/gaosheng/project/test"
    <Directory "/Users/gaosheng/project/test">
        Options Indexes FollowSymLinks
        AllowOverride None
        Require all granted
        DirectoryIndex index.php
    </Directory>
</VirtualHost>

<VirtualHost *:80>
    ServerAdmin gaosheng08@gmail.com
    ServerName api.yifeng.com
    ErrorLog "/private/var/log/apache2/yifengtech.com-error_log"
    CustomLog "/private/var/log/apache2/yifengtech.com-access_log" combined

    DocumentRoot "/Users/gaosheng/project/3kjapidev"
    <Directory "/Users/gaosheng/project/3kjapidev">
        Options Indexes FollowSymLinks
        AllowOverride None
        Require all granted
        DirectoryIndex index.php
    </Directory>
    
    #URL 重写
    <IfModule mod_rewrite.c>
        RewriteEngine On
		RewriteCond %{REQUEST_FILENAME} !-f
		RewriteCond %{REQUEST_FILENAME} !-d
    	RewriteRule ^/$  /client/ [QSA,L]
	
		RewriteCond %{REQUEST_FILENAME} !-f
		RewriteCond %{REQUEST_FILENAME} !-d
		RewriteRule ^(.*)$ /server/index\.php/$1 [QSA,L]
    </IfModule>
</VirtualHost>
```


