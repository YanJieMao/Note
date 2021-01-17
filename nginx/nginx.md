# 1.Nginx简介

## 1.1Nginx概述

​	Nginx ("engine x") 是一个高性能的 HTTP 和反向代理服务器,特点是占有内存少，并发能力强，事实上nginx的并发能力确实在同类型的网页服务器中表现较好，中国大陆使用nginx网站用户有：百度、京东、新浪、网易、腾讯、淘宝等

## 1.2Nginx作为web服务器

​	Nginx 可以作为静态页面的 web 服务器，同时还支持 CGI 协议的动态语言，比如 perl、php等。但是不支持 java。Java 程序只能通过与 tomcat 配合完成。Nginx 专为性能优化而开发，性能是其最重要的考量,实现上非常注重效率 ，能经受高负载的考验,有报告表明能支持高达 50,000 个并发连接数。
https://lnmp.org/nginx.html

## 1.3正向代理

​	Nginx 不仅可以做反向代理，实现负载均衡。还能用作正向代理来进行上网等功能。
正向代理：如果把局域网外的 Internet 想象成一个巨大的资源库，则局域网中的客户端要访问 Internet，则需要通过代理服务器来访问，这种代理服务就称为正向代理。

## 1.4反向代理

​	反向代理，其实客户端对代理是无感知的，因为客户端不需要任何配置就可以访问，我们只需要将请求发送到反向代理服务器，由反向代理服务器去选择目标服务器获取数据后，在返回给客户端，此时反向代理服务器和目标服务器对外就是一个服务器，暴露的是代理服务器地址，隐藏了真实服务器 IP 地址。

## 1.5负载均衡

​	客户端发送多个请求到服务器，服务器处理请求，有一些可能要与数据库进行交互，服务器处理完毕后，再将结果返回给客户端。
​	这种架构模式对于早期的系统相对单一，并发请求相对较少的情况下是比较适合的，成本也低。但是随着信息数量的不断增长，访问量和数据量的飞速增长，以及系统业务的复杂度增加，这种架构会造成服务器相应客户端的请求日益缓慢，并发量特别大的时候，还容易造成服务器直接崩溃。很明显这是由于服务器性能的瓶颈造成的问题，那么如何解决这种情况呢？
​	我们首先想到的可能是升级服务器的配置，比如提高 CPU 执行频率，加大内存等提高机器的物理性能来解决此问题，但是我们知道摩尔定律的日益失效，硬件的性能提升已经不能满足日益提升的需求了。最明显的一个例子，天猫双十一当天，某个热销商品的瞬时访问量是极其庞大的，那么类似上面的系统架构，将机器都增加到现有的顶级物理配置，都是不能够满足需求的。那么怎么办呢？
​	上面的分析我们去掉了增加服务器物理配置来解决问题的办法，也就是说纵向解决问题的办法行不通了，那么横向增加服务器的数量呢？这时候集群的概念产生了，单个服务器解决不了，我们增加服务器的数量，然后将请求分发到各个服务器上，将原先请求集中到单个服务器上的情况改为将请求分发到多个服务器上，将负载分发到不同的服务器，也就是我们所说的**负载均衡**

## 1.6动静分离

为了加快网站的解析速度，可以把动态页面和静态页面由不同的服务器来解析，加快解析速度。降低原来单个服务器的压力。

# 2.Nginx安装

Nginx主要用于服务器，所以就在linux下安nginx

在Ubuntu下只需要一条命令就可以安装了`apt install nginx`

# 3.Nginx常用的命令和配置文件

## 3.1nginx常用的命令

- 启动命令

  在/usr/sbin 目录下执行 ./nginx

 - 关闭命令

   在/usr/sbin 目录下执行 ./nginx -s stop

- 重新加载命令

  在/usr/sbin 目录下执行 ./nginx -s reload

  **PS** :ubuntu通过apt安装的可以全局使用nginx 命令，只有启动时需要在相应目录下执行

## 3.2nginx.conf配置文件

​	ubuntu通过apt安装nginx，其默认的配置文件都放在/etc/nginx目录下，而主配置文件nginx.conf 也在其中，后续对 nginx 的使用基本上都是对此配置文件进行相应的修改

<img src="/img/image-20201106144728253.png" alt="image-20201106144728253" style="zoom: 67%;" />

配置文件内容如下

```
user www-data;
worker_processes auto;
pid /run/nginx.pid;
include /etc/nginx/modules-enabled/*.conf;

events {
	worker_connections 768;
	# multi_accept on;
}

http {
	sendfile on;
	tcp_nopush on;
	tcp_nodelay on;
	keepalive_timeout 65;
	types_hash_max_size 2048;

	include /etc/nginx/mime.types;
	default_type application/octet-stream;
	
	server {
	listen 80 default_server;
	listen [::]:80 default_server;
	root /var/www/html;

	server_name _;
	location / {
		root html;
		index index.html index.htm;
	}

	}


	ssl_protocols TLSv1 TLSv1.1 TLSv1.2 TLSv1.3; # Dropping SSLv3, ref: POODLE
	ssl_prefer_server_ciphers on;

	access_log /var/log/nginx/access.log;
	error_log /var/log/nginx/error.log;

	gzip on;

	include /etc/nginx/conf.d/*.conf;
	include /etc/nginx/sites-enabled/*;
}

```

根据上述文件，我们可以很明显的将 nginx.conf  配置文件分为三部分：

### 第一部分：全局块

​		从配置文件开始到 events 块之间的内容，主要会设置一些影响 nginx 服务器整体运行的配置指令，主要包括配置运行 Nginx 服务器的用户（组）、允许生成的 worker process 数，进程 PID 存放路径、日志存放路径和类型以
及配置文件的引入等。
​    比如上面第二行配置的：

```
worker_processes auto;
```

​	这是 Nginx 服务器并发处理服务的关键配置，worker_processes 值可以定义，并且值越大，可以支持的并发处理量也越多，但是会受到硬件、软件等设备的制约

### 第二部分：events块

```
events {
	worker_connections 768;
	# multi_accept on;
}
```

​	events 块涉及的指令主要影响 Nginx 服务器与用户的网络连接，常用的设置包括是否开启对多 work process下的网络连接进行序列化，是否允许同时接收多个网络连接，选取哪种事件驱动模型来处理连接请求，每个 wordprocess 可以同时支持的最大连接数等。
上述例子就表示每个 work process 支持的最大连接数为 768.
这部分的配置对 Nginx 的性能影响较大，在实际中应该灵活配置。

### 第三部分：http块

```
http {


	sendfile on;
	tcp_nopush on;
	tcp_nodelay on;
	keepalive_timeout 65;
	types_hash_max_size 2048;

	include /etc/nginx/mime.types;
	default_type application/octet-stream;
	
	server {
	listen 80 default_server;
	listen [::]:80 default_server;


	root /var/www/html;

	server_name _;

	location / {
		root html;
		index index.html index.htm;
	}

	}


	ssl_protocols TLSv1 TLSv1.1 TLSv1.2 TLSv1.3; # Dropping SSLv3, ref: POODLE
	ssl_prefer_server_ciphers on;

	access_log /var/log/nginx/access.log;
	error_log /var/log/nginx/error.log;

	gzip on;


	include /etc/nginx/conf.d/*.conf;
	include /etc/nginx/sites-enabled/*;
}
```

​	这算是 Nginx 服务器配置中最频繁的部分，代理、缓存和日志定义等绝大多数能和第三方模块的配置都在这里。
​	需要注意的是：http 块也可以包括 http 全局块、server 块。

**1.http全局块**

​	http 全局块配置的指令包括文件引入、MIME-TYPE 定义、日志自定义、连接超时时间、单链接请求数上限等。

**2.server块**

​	这块和虚拟主机有密切关系，虚拟主机从用户角度看，和一台独立的硬件主机是完全一样的，该技术的产生是为了节省互联网服务器硬件成本。
​	每个 http 块可以包括多个 server 块，而每个 server 块就相当于一个虚拟主机。
​	而每个 server 块也分为全局 server 块，以及可以同时包含多个 locaton 块。

①全局 server 块
	最常见的配置是本虚拟机主机的监听配置和本虚拟主机的名称或 IP 配置。
②location 块
	一个 server 块可以配置多个 location 块。
	这块的主要作用是基于 Nginx 服务器接收到的请求字符串（例如 server_name/uri-string），对虚拟主机名称（也可以是 IP 别名）之外的字符串（例如 前面的 /uri-string）进行匹配，对特定的请求进行处理。地址定向、数据缓
存和应答控制等功能，还有许多第三方模块的配置也在这里进行。

# 4.Nginx配置-反向代理

## 4.1反向代理一

1.先启动一个tomcat服务器

2.修改本地host文件，将虚拟机的ip地址映射到你所映射的域名

例如：192.168.50.229  www.hao.com（任意设置的只对本地pc有效）

3.修改nginx的配置

由于是nginx1.18.0版本，所以在/etc/nginx/sites-enabled/ 目录下新建配置文件（命名任意)

并修改如下

```
server {
		listen 80;
			server_name www.hao.com;
		
		location / {
			proxy_pass http://127.0.0.1:8080;
			index index.html index.htm index.jsp;
		}
	
	}
```

如上配置，我们监听 80 端口，访问域名为 www.hao.com，不加端口号时默认为 80 端口，故
访问该域名时会跳转到 127.0.0.1:8080 路径上。在浏览器端输入 www.123.com 结果如下：

<img src="/img/image-20201106190405339.png" alt="image-20201106190405339" style="zoom:80%;" />

## 4.2反向代理二

实现效果：使用 nginx 反向代理，根据访问的路径跳转到不同端口的服务中

1.准备好两个tomcat 一个8080端口，一个8081端口，并准备好测试的相应页面

2.修改/etc/nginx/sites-enabled/ 目录下的配置文件

修改如下

```
server {
		listen 80;
			server_name www.hao.com;
		
		location ~/edu/ {
			proxy_pass http://127.0.0.1:8080;
			index index.html index.htm index.jsp;
		}
		location /blog/ {
			proxy_pass http://127.0.0.1:8081;
			index index.html index.htm index.jsp;
		}
	
	}
```

浏览器访问www.hao.com/edu/

<img src="/img/image-20201106192907044.png" alt="image-20201106192907044" style="zoom:67%;" />

浏览器访问www.hao.com/blog/

<img src="/img/image-20201106192930975.png" alt="image-20201106192930975" style="zoom:67%;" />

> location  指令说明

该指令用于匹配 URL。
语法如下：

```
location [= |~ | ~*] uri{

}
```

1、= ：用于不含正则表达式的 uri 前，要求请求字符串与 uri 严格匹配，如果匹配成功，就停止继续向下搜索并立即处理该请求。
2、~：用于表示 uri 包含正则表达式，并且区分大小写。
3、~：用于表示 uri 包含正则表达式，并且不区分大小写。
4、^~：用于不含正则表达式的 uri 前，要求 Nginx 服务器找到标识 uri 和请求字符串匹配度最高的 location 后，立即使用此 location 处理请求，而不再使用 location块中的正则 uri 和请求字符串做匹配。
注意：如果 uri 包含正则表达式，则必须要有 ~ 或者 ~ 标识。

# 5.Nginx配置-负载均衡

1.同时启动两个tomcat

2.修改nginx.conf

修改如下（在http区域中添加）

```
upstream www.hao.com{
		ip_hash;
		server 127.0.0.1:8080 weight=1;
		server 127.0.0.1:8081 weight=1;
	}
```

3.修改/etc/nginx/sites-enabled/ 目录下的配置文件

修改如下

```
server {
		listen 80;
			server_name www.hao.com;
		
		location / {
			proxy_pass http://www.hao.com;
			proxy_connect_timeout 10;
		}
	
	}
```

3.用浏览器访问

<img src="/img/image-20201106200320675.png" alt="image-20201106200320675" style="zoom:80%;" />

ps:因为负载均衡的分配策略，是根据ip的hash结果分配，每个访客固定一个后端服务器，我就只放一张截图了

> Nginx提供的几种分配方式

1.轮询（默认）

每个请求按时间顺序逐一分配到不同的后端服务器，如果后端服务器 down 掉，能自动剔除。

2.weight

weight 代表权,重默认为 1,权重越高被分配的客户端越多
指定轮询几率，weight 和访问比率成正比，用于后端服务器性能不均的情况。 例如：

```
upstream www.hao.com {
server 127.0.0.1:8080 weight=1;
server 127.0.0.1:8081 weight=1;
}
```

3.ip_hash

每个请求按访问 ip 的 hash 结果分配，这样每个访客固定访问一个后端服务器，可以解决 session 的问题。 例如：

```
upstream www.hao.com{
		ip_hash;
		server 127.0.0.1:8080 weight=1;
		server 127.0.0.1:8081 weight=1;
	}
```

4 、fair （第三方）

按后端服务器的响应时间来分配请求，响应时间短的优先分配。

```
upstream www.hao.com{
		server 127.0.0.1:8080 weight=1;
		server 127.0.0.1:8081 weight=1;
		fair;
	}
```

# 6.Nginx动静分离

​	Nginx 动静分离简单来说就是把动态跟静态请求分开，不能理解成只是单纯的把动态页面和静态页面物理分离。严格意义上说应该是动态请求跟静态请求分开，可以理解成使用 Nginx处理静态页面，Tomcat 处理动态页面。动静分离从目前实现角度来讲大致分为两种：

​	一种是纯粹把静态文件独立成单独的域名，放在独立的服务器上，也是目前主流推崇的方案；

​	另外一种方法就是动态跟静态文件混合在一起发布，通过 nginx 来分开。通过 location 指定不同的后缀名实现不同的请求转发。通过 expires 参数设置，可以使浏览器缓存过期时间，减少与服务器之前的请求和流量。

​	具体 Expires 定义：是给一个资源设定一个过期时间，也就是说无需去服务端验证，直接通过浏览器自身确认是否过期即可，所以不会产生额外的流量。此种方法非常适合不经常变动的资源。（如果经常更新的文件，
不建议使用 Expires 来缓存），我这里设置 3d，表示在这 3 天之内访问这个 URL，发送一个请求，比对服务器该文件最后更新时间没有变化，则不会从服务器抓取，返回状态码304，如果有修改，则直接从服务器重新下载，返回状态码 200。

1.先在linux系统中，/home/www中新建 data目录，放入相应的资源在相应的目录下

2.配置nginx,修改/etc/nginx/sites-enabled/ 目录下的配置文件

```
server {
		listen 80;
			server_name www.hao.com;
		
		location / {
			proxy_pass http://www.hao.com;
			proxy_connect_timeout 10;
		}
		
		location /www/ {
		
			root /home/www/data/ ;
			index index.html index.html;
		
		}
		
		location /img/ {
			
			root /home/www/data/ ;
			autoindex on;
		}
	
	}
```

3.访问

<img src="/img/image-20201106205335566.png" alt="image-20201106205335566" style="zoom:67%;" />

<img src="/img/image-20201106205348050.png" alt="image-20201106205348050" style="zoom:67%;" />

# 7.Nginx原理与优化参数配置

<img src="/img/image-20201106214613826.png" alt="image-20201106214613826" style="zoom:80%;" />

<img src="/img/image-20201106214653572.png" alt="image-20201106214653572" style="zoom:80%;" />

**master- -s workers  的机制的好处**
	首先，对于每个 worker 进程来说，独立的进程，不需要加锁，所以省掉了锁带来的开销，
同时在编程以及问题查找时，也会方便很多。其次，采用独立的进程，可以让互相之间不会
影响，一个进程退出后，其它进程还在工作，服务不会中断，master 进程则很快启动新的
worker 进程。当然，worker 进程的异常退出，肯定是程序有 bug 了，异常退出，会导致当
前 worker 上的所有请求失败，不过不会影响到所有请求，所以降低了风险。

**需要设置多少个 worker**
Nginx 同 redis 类似都采用了 io 多路复用机制，每个 worker 都是一个独立的进程，但每个进
程里只有一个主线程，通过异步非阻塞的方式来处理请求， 即使是千上万个请求也不在话
下。每个 worker 的线程可以把一个 cpu 的性能发挥到极致。所以 worker 数和服务器的 cpu
数相等是最为适宜的。设少了会浪费 cpu，设多了会造成 cpu 频繁切换上下文带来的损耗。



## 设置 worker  数量

worker_processes 4
#work 绑定 cpu(4 work 绑定 4cpu)。
worker_cpu_affinity 0001 0010 0100 1000
#work 绑定 cpu (4 work 绑定 8cpu 中的 4 个) 。
worker_cpu_affinity 0000001 00000010 00000100 00001000

## **连接数 worker_connection**

​	这个值是表示每个 worker 进程所能建立连接的最大值，所以，一个 nginx 能建立的最大连接数，应该是 worker_connections * worker_processes。当然，这里说的是最大连接数，对于HTTP 请 求 本 地 资 源 来 说 ， 能 够 支 持 的 最 大 并 发 数 量 是 worker_connections *worker_processes，如果是支持 http1.1 的浏览器每次访问要占两个连接，所以普通的静态访问最大并发数是： worker_connections * worker_processes /2，而如果是 HTTP 作 为反向代理来说，最大并发数量应该是 worker_connections *worker_processes/4。因为作为反向代理服务器，每个并发会建立与客户端的连接和与后端服务的连接，会占用两个连接。

## nginx的配置结构

<img src="/img/image-20201106214959842.png" alt="image-20201106214959842" style="zoom:80%;" />

