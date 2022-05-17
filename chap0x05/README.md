# 实验环境
- Windows10
- Ubuntu20.04
- Nginx
- VeryNginx
- Wordpress4.7

# 实验要求
## 基本要求
- 在一台主机（虚拟机）上同时配置Nginx和VeryNginx
  - VeryNginx作为本次实验的Web App的反向代理服务器和WAF
  - PHP-FPM进程的反向代理配置在nginx服务器上，VeryNginx服务器不直接配置Web站点服务
- 使用Wordpress搭建的站点对外提供访问的地址为： http://wp.sec.cuc.edu.cn
- 使用Damn Vulnerable Web Application (DVWA)搭建的站点对外提供访问的地址为： http://dvwa.sec.cuc.edu.cn

## 安全加固要求
- 使用IP地址方式均无法访问上述任意站点，并向访客展示自定义的友好错误提示信息页面-1
- Damn Vulnerable Web Application (DVWA)只允许白名单上的访客来源IP，其他来源的IP访问均向访客展示自定义的友好错误提示信息页面-2
- 在不升级Wordpress版本的情况下，通过定制VeryNginx的访问控制策略规则，热修复WordPress < 4.7.1 - Username Enumeration
- 通过配置VeryNginx的Filter规则实现对Damn Vulnerable Web Application (DVWA)的SQL注入实验在低安全等级条件下进行防护

## VeryNginx配置要求
- VeryNginx的Web管理页面仅允许白名单上的访客来源IP，其他来源的IP访问均向访客展示自定义的友好错误提示信息页面-3
- 通过定制VeryNginx的访问控制策略规则实现：
  - 限制DVWA站点的单IP访问速率为每秒请求数 < 50
  - 限制Wordpress站点的单IP访问速率为每秒请求数 < 20
  - 超过访问频率限制的请求直接返回自定义错误提示信息页面-4
  - 禁止curl访问

# 实验内容
## 准备：搭建环境
* Windows和虚拟机中hosts文件中加入以下信息：
```
192.168.56.101 vn.sec.cuc.edu.cn
192.168.56.101 dvwa.sec.cuc.edu.cn
192.168.56.101 wp.sec.cuc.edu.cn
```

* VeryNginx环境
  - 对所需要的库安装
  ```
  # zlib
  sudo apt-get install zlib1g-dev
  # pcre
  sudo apt-get update 
  sudo apt-get install libpcre3 libpcre3-dev
  # gcc 
  sudo apt install gcc
  # make
  sudo apt install make
  # penssl library
  sudo apt install libssl-dev
  ```

  - 仓库克隆和python3安装
  ```
  # 克隆VeryNginx仓库
  git clone https://github.com/alexazhou/VeryNginx.git
  cd VeryNginx
  # python3
  sudo python3 install.py install
  ``` 

  - 配置相关环境
  ```
  # 修改配置文件
  sudo vim /opt/verynginx/openresty/nginx/conf/nginx.conf

  # 修改用户名
  user  www-data;

  # 添加
  include /opt/verynginx/verynginx/nginx_conf/in_external.conf;
  include /opt/verynginx/verynginx/nginx_conf/in_http_block.conf;
  include /opt/verynginx/verynginx/nginx_conf/in_server_block.conf;

  # 修改监听端口
  # 注：为了不和8080端口冲突，此处设置为8081
  server {
        listen 192.168.56.101:8081;
        
        #this line shoud be include in every server block
        include /opt/verynginx/verynginx/nginx_conf/in_server_block.conf;

        location = / {
            root   html;
            index  index.html index.htm;
        }
    }
  ```
  - 权限赋予
  ```
  chmod -R 777 /opt/verynginx/verynginx/configs
  ```
  - verynginx运行命令
  ```
  # 启动服务
  sudo /opt/verynginx/openresty/nginx/sbin/nginx
  # 停止服务
  sudo /opt/verynginx/openresty/nginx/sbin/nginx -s stop
  # 重启服务
  sudo /opt/verynginx/openresty/nginx/sbin/nginx -s reload
  ```
  - 环境设置成功：
   ![verynginx登录](img\verynginx-login.png)
   ![verynginx成功](img\verynginx-success.png)


* nginx环境
```
 # 安装
 sudo apt install nginx
 # nginx目录
 sudo vim /etc/nginx/sites-enabled/default
 # 配置
 root /var/www/html/wp.sec.cuc.edu.cn;

  # Add index.php to the list if you are using PHP
  index readme.html index.php;

 location ~ \.php$ {
	#	include snippets/fastcgi-php.conf;
	#
	#	# With php-fpm (or other unix sockets):
		fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
		fastcgi_index index.php;
		fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
		include fastcgi_params;
	#	# With php-cgi (or other tcp sockets):
 #		fastcgi_pass 127.0.0.1:9000;
	}
```
  ![nginx语法检查](img\nginx-grammar.png)
  ![nginx欢迎界面](img\nginx-welcome.png)

* wordpress环境
 - 安装
 ```
 # 下载安装包
 sudo wget https://wordpress.org/wordpress-4.7.zip

 # 解压
 sudo apt install p7zip-full
 7z x wordpress-4.7.zip

 # 将解压后的wordpress移至指定路径
 sudo mkdir /var/www/html/wp.sec.cuc.edu.cn
 sudo cp wordpress /var/www/html/wp.sec.cuc.edu.cn
 # 修改wp-config-sample中的内容，并更名为wp-config
 sudo vim wp-config-sample
 mv wp-config-sample wp-config
 ```
 - 添加数据库
 ```
 # 下载mysql
 sudo apt-get install mysql-server;
 sudo mysql
 # 建库
 CREATE DATABASE wordpress DEFAULT CHARACTER SET utf8 COLLATE utf8_unicode_ci;
 # 建新用户
 create user 'yjm'@'localhost' identified by 'yangjiamin11';
 # 授权
 grant all on wordpress.* to 'yjm'@'localhost';
 ```
   ![database建立成功](img\databa.png)
  
 - 修改配置
 ```
 sudo vim /var/www/html/wordpress/wp-config.php
 // ** MySQL settings - You can get this info from your web host ** //
 /** The name of the database for WordPress */
 define('DB_NAME', 'wordpress');

 /** MySQL database username */
 define('DB_USER', 'yjm');

 /** MySQL database password */
 define('DB_PASSWORD', 'yangjiamin11');

 /** MySQL hostname */
 define('DB_HOST','localhost');
 /** Database Charset to use in creating database tables. */
 dedine("DB_CHARSET','utf8');
 /** The Database Collate type. Don't change this if in double. */
 define('DB_COLLATE','');
 ```
 #### 安装成功：
   ![wordpress初始化](img\wordpress-welcome.png)
  
   ![wordpress登录](img\wordpress-login.png)
   ![wordpress成功](img\wordpress-success.png)


* DVWA
 - 安装
 ```
 # 下载
 git clone https://github.com/digininja/DVWA.git
 # 建目录
 sudo mkdir /var/www/html/dvwa.sec.cuc.edu.cn
 # 移文件
 sudo mv DVWA/* /var/www/html/dvwa.sec.cuc.edu.cn
 ```
 - 建立数据库
 ```
 sudo mysql
 CREATE DATABASE dvwa DEFAULT CHARACTER SET utf8 COLLATE utf8_unicode_ci;
 CREATE USER 'dvwa'@'localhost' IDENTIFIED BY 'p@ssw0rd';
 GRANT ALL ON dvwa.* TO 'dvwa'@'localhost';
 ```
 - `php`配置
 ```
 # php安装
 sudo apt install php-fpm php-mysql php-curl php-gd php-intl php-mbstring php-soap php-xml php-xmlrpc php-zip

 sudo mv config.inc.php.dist config.inc.php

 # 默认配置：
 $_DVWA[ 'db_database' ] = 'dvwa';
 $_DVWA[ 'db_user' ] = 'dvwa';
 $_DVWA[ 'db_password' ] = 'p@ssw0rd';

 # 修改php-fpm文件
 sudo vim /etc/php/7.4/fpm/php.ini 

 display_errors: Off
 safe_mode: Off
 allow_url_include: On
 allow_url_fopen: On

 #重启php
 systemctl restart php7.4-fpm.service

 #授权
 sudo chown -R www-data.www-data /var/www/html/dvwa.sec.cuc.edu.cn
 ```
 - 服务器配置
 ```
 sudo vim /etc/nginx/sites-available/dvwa.sec.cuc.edu.cn

 # 配置文件
 server {
    listen 8080 default_server;
    listen [::]:8080 default_server;

    root /var/www/html/dvwa.sec.cuc.edu.cn;
    index index.php index.html index.htm index.nginx-debian.html;
    server_name dvwa.sec.cuc.edu.cn;

    location / {
        #try_files $uri $uri/ =404;
        try_files $uri $uri/ /index.php$is_args$args;  
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
    }

    location ~ /\.ht {
        deny all;
    }
 }

 # 创建软链接
 sudo ln -s /etc/nginx/sites-available/dvwa.sec.cuc.edu.cn /etc/nginx/sites-enabled/

 # 检查,重启,登录查看
 sudo nginx -t
 systemctl restart nginx.service
 ```
   ![dvwa登录界面](img\dvwa-login.png)
   ![dvwa-setup](img\dvwa-setup.png)
   ![dvwa-php](img\dvwa-php.png)
## 实验：具体内容
* ### verynginx反向代理word press、dvwa
  - matcher
    ![matcher](img\matcher.png)
  - stream
    ![stream](img\stream.png)
  - proxy pass
    ![proxy pass](img\proxy-pass.png)
* ### 安全加固要求
 - 使用IP地址方式均无法访问上述任意站点，并向访客展示自定义的友好错误提示信息页面-1
   - matcher
    ![ipmatcher](img\ipmatching.png)
   - response
    ![ipresponse](img\ip-response.png)
   - filter
    ![ipfilter](img\ip-filter.png)
    - 结果
    ![页面-1的结果](img\-1_result.png)
  
 - Damn Vulnerable Web Application (DVWA)只允许白名单上的访客来源IP，其他来源的IP访问均向访客展示自定义的友好错误提示信息页面-2
    - matcher
     ![ip-white-list](img\ip-white-list.png)
    - response
     ![white-list](img\white-list.jpg)
    - 结果
     ![页面-2的结果](img\-2_result.png)
- 在不升级Wordpress版本的情况下，通过定制VeryNginx的访问控制策略规则，热修复WordPress < 4.7.1 - Username Enumeration
  - 漏洞载入
  ```
  # 拷贝漏洞文本并修改
  $url = "http://wp.sec.cuc.edu.cn"
  # 安装包
  sudo apt install php7.2-cli
  # 执行
  php err.php
  ```
  - 对应的安全策略：
    - 在Basic中添加匹配规则
    - 在Custom Action中添加过滤条件
  - filter
   !添加filte](img\filter.png)
  - 失败后404
   ![404](img\404.jpg)
- 通过配置VeryNginx的Filter规则实现对Damn Vulnerable Web Application (DVWA)的SQL注入实验在低安全等级条件下进行防护
  - security level->low
   ![安全等级设置为low](img\security-low.jpg)
  - dvwa sql injection
   ![injection](img\injection.png)
  - matcher
   ![matcher](img\dvwa-injection.png)
  - response
   ![response](img\dvwa-response.png)
  - filter
   ![filter](img\dvwa-filter.png)
* ### VeryNginx配置要求
- VeryNginx的Web管理页面仅允许白名单上的访客来源IP，其他来源的IP访问均向访客展示自定义的友好错误提示信息页面-3
  - Basic添加规则和信息
   ![匹配规则](img\basic1.png)
   ![响应信息](img\basic2.png)
  - Custom Action添加过滤条件
   ![过滤条件](img\custom.png)
  - 结果
   ![页面-3的结果](img\-3_result.jpg)
- 通过定制VeryNginx的访问控制策略规则实现：
  - 限制DVWA站点的单IP访问速率为每秒请求数 < 50
  - 限制Wordpress站点的单IP访问速率为每秒请求数 < 20
  - 超过访问频率限制的请求直接返回自定义错误提示信息页面-4
     - response
       ![自定义response](img\fast-limit.png)
     - Frequency Limit
       ![频率限制](img\frequency-limit.png)
     - 结果
       ![测试结果](img\result.jpg)
  - 禁止curl访问
     - matcher
       ![curlmatcher](img\curl-matcher.png)
     - response
       ![curlrespons](img\curl-response.png)
     - filter
       ![curlfilt](img\curl-filter.png)

# 问题及解决
* zlib1g-dev安装失败
默认安装源里没有zlib1g-dev，需要在packages.ubuntu.com里查找
* 找不到nginx配置文件
在安装python3时可能是网络的问题没有安装好，隔天再来重新做发现安装好了之后会有`enjoy it!`的提示，然后再来找配置文件就有了
* nginx 启动问题
  - root权限下chmod报错`chmod: changing permissions of '/opt/verynginx/verynginx/configs': Operation not permitted`
   使用命令`sudo chmod -R 777 /opt/verynginx/verynginx/configs`
  - 无法打开VeryNginx
   考虑浏览器不兼容问题，使用另外的浏览器打开
  - nginx报错`nginx: [emerg] "upstream" directive is not allowed here in /opt/verynginx/verynginx/nginx_conf/in_http_block.conf:1`
   配置文件中upstream位置错误，应该在http模块内server模块外
  - nginx重启报错`nginx: failed to start A high performance web server and a reverse proxy server`
   之前卸载nginx时没卸载干净，导致此错误，清除干净后安装即可

* DVWA 问题
  - 下载DVWA时报错`Error in the pull function.`
    github加载太慢连接失败把`https`换成`git`
  - 移动文件时报错`mv: cannot stat 'config.inc.php.dist': No such file or directory`
   没有更新，`sudo apt-get update`即可


# 参考文献
- [zlib1g-dev安装](https://www.kmw.com/news/3132474/2.html)
- [verynginx故障排除](https://github.com/alexazhou/VeryNginx/wiki/Trouble-Shooting)
- [nginx无法启动问题](https://www.jianshu.com/p/c66fbf53d498)
- [wordpress在安装使用中出现404、403、500及502问题的分析与解决方法](https://m.jb51.net/cms/570973.html)
- [ubuntu安装nginx报错](https://blog.csdn.net/bean_business/article/details/113447591)
- [nginx常见502故障的一些解决方案与汇总](https://blog.51cto.com/u_15127568/2667005?b=totalstatistic)
- [wordpress建立连接时数据库出错](https://www.bandwagonhost.net/7941.html)
- [kal1x师哥or师姐作业](https://github.com/CUCCS/2021-linux-public-kal1x/blob/chap0x05/chap0x05/%E7%AC%AC%E4%BA%94%E6%AC%A1%E5%AE%9E%E9%AA%8C.md)


#### 一些说明
可能是由于地域的原因之前一直不能访问GitHub，偶尔换个亲戚家的网络可以访问一两分钟。后来按照黄大给的3个IP添加到hosts文件中，第一次正常访问再后来也不行了。这就导致了一个问题就是之前偶尔访问到看到自己分支很乱就进行了一些操作导致前三章作业可能合并到了一个分支里面并且关闭了pr，所以如果批改作业的师姐看到我的作业实在一个分支里的请谅解。后来的第四章作业是请同学提交的pr，这次作业不知道我换个地方看能不能提交，如果不行的话还是同学帮忙😭。