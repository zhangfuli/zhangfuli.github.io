---
title: Nginx篇-2021春招准备
date: 2021-02-26 11:59:19
tags:
---

#### 1、Nginx

##### 1、Nginx模块化设计

![title](/images/2021年春招准备-Nginx篇/1.png)

- **核心模块**

提供错误日志记录、配置文件解析、事件驱动机制、进程管理等核心功能。

- **标准 HTTP 模块**

 HTTP 协议解析相关的功能，如：端口配置、网页编码设置、HTTP 响应头设置等。

- **可选 HTTP 模块**

主要用于扩展标准的 HTTP 功能，让 Nginx 能处理一些特殊的服务，如：Flash 多媒体传输、解析 GeoIP 请求、SSL 支持等。

- **邮件服务模块**

用于支持 Nginx 的邮件服务，包括对 POP3 协议、IMAP 协议和 SMTP 协议的支持。

- **第三方模块**

扩展 Nginx 服务器应用，完成开发者自定义功能，如：Json 支持、Lua 支持等。

##### 2、Nginx架构

![title](/images/2021年春招准备-Nginx篇/2.png)

- Nginx默认采用多进程工作方式，Nginx启动后，会运行一个master进程和多个worker进程
- 一般来说，当一个连接进来后，所有worker都会收到通知，但是只有一个进程可以接受这个连接请求，其它的都失败，这是所谓的惊群现象。
- nginx提供了一个accept_mutex（互斥锁），有了这把锁之后，同一时刻，就只会有一个进程在accpet连接，这样就不会有惊群问题了。
- 每个worker进程都有一个独立的连接池



##### 3、Listen

- 当nginx接到请求后，会匹配其配置中的service模块，

- 匹配方法就是靠请求携带的host和port正好对应其配置中的server_name 和listen。

- 如果做过ip和域名绑定，ip和域名二者是对等的
- fastcgi介绍：CGI全称是“公共网关接口”(Common Gateway Interface)，HTTP服务器与你的或其它机器上的程序进行“交谈”的一种工具，其程序一般运行在网络服务器上。 CGI可以用任何一种语言编写，只要这种语言具有标准输入、输出和环境变量。如php,perl,tcl等。

```nginx
server {
        listen 8080;
        server_name www.abc.com;
        access_log  /opt/htdocs/logs/abc.log combined;
        index index.html index.htm index.php;
        root /opt/htdocs/abc;
        if ( $query_string ~* ".*[\;'\<\>].*" ){
                return 404;
        }
        location / {
                try_files $uri $uri/ /index.php;
        }
        location ~ .*\.(php|php5)?$  {
                #fastcgi_pass unix:/dev/shm/php-cgi.sock;
                fastcgi_pass 127.0.0.1:9000;     # 网管代理
                fastcgi_index index.php;
                include fastcgi.conf;
                }
 
        location ~ .*\.(gif|jpg|jpeg|png|bmp|swf|flv|ico)$ {
                expires 30d;
                }
 
        location ~ .*\.(js|css)?$ {
                expires 7d;
                }
}

```



##### 4、反向代理

```nginx
http {
    upstream cluster {
        server srv1;
        server srv2;
        server srv3;
    }
 
    server {
        listen 80;
 				server_name localhost www.xxx.com; #域名
        location / {    # 匹配 / 转发到反向代理
            proxy_pass http://cluster;  # 反向代理
        }
    }
}
```

默认的HTTP负载均衡算法 - 加权轮询

##### 5、Nginx调度算法

- **轮询:** 按时间顺序逐一分配到不同的后端服务器

```nginx
upstream lb_demo {
        server 172.16.255.194:9001;
        server 172.16.255.195:9001;
}
```

- **加权轮询:** 可在配置的server后面加个weight=number，number值越高，分配的概率越大

```nginx
upstream lb_demo {
        server 172.16.255.194:9001 weight=10;
        server 172.16.255.195:9001 weight=20;
    }
```

- **ip_hash:** 每个请求按访问IP的hash分配，这样来自同一IP固定访问一个后台服务器，实现会话粘滞，避免处理session共享问题

  - 如果没有`ip_hash`策略，那么如何实现回话沾滞？
    `答：`可以维护一张映射表，存储客户端IP或者sessionid与具体目标服务器的映射关系，但是存在缺点

  - **缺点：**
    - 在客户端很多的情况下，映射表非常大，浪费内存空间
    - 客户端上下线，目标服务器上下线，都会导致重新维护映射表，映射表维护成本很大
  - **解决方案：** 如果使用哈希算法，事情就简单很多，我们可以对ip地址或者sessionId进行计算哈希值，哈希值与服务器数量进行取模运算，

```nginx
upstream lb_demo {
        ip_hash;
        server 172.16.255.194:9001;
        server 172.16.255.195:9001;
    }
```

- **least_hash:** 最少链接数，哪个机器连接数少就发分发给哪个机器

```nginx
upstream lb_demo {
        least_conn;
        server 172.16.255.194:9001;
        server 172.16.255.195:9001;
    }
```

- **url_hash**: 按访问的url的hash结果分配请求，是每个url定向到同一后端服务器上

```nginx
upstream lb_demo {
        url_hash;
        server 172.16.255.194:9001;
        server 172.16.255.195:9001;
    }
```

##### 6、nginx配置证书

> 使用openssl生成证书

```nginx
server {
	listen 443 ssl;
	server_name www.yourdomain.com;
	ssl_certificate /usr/local/ssl/nginx.crt;
	ssl_certificate_key /usr/local/ssl/nginx.key;
	ssl_session_cache    shared:SSL:1m;
	ssl_session_timeout  5m;
	#禁止在header中出现服务器版本，防止黑客利用版本漏洞攻击
	server_tokens off;
	#如果是全站 HTTPS 并且不考虑 HTTP 的话，可以加入 HSTS 告诉你的浏览器本网站全站加密，并且强制用 HTTPS 访问
	fastcgi_param   HTTPS               on;
	fastcgi_param   HTTP_SCHEME         https;
	access_log /usr/local/nginx/logs/httpsaccess.log;
}
```

##### 7、nginx到upstream这个网络出现问题出现502(Bad Gateway)比较多，如何解决？

- php-fpm的进程是否启动 ,没启动肯定报这个错误
-  tcp连接数超过了fpm的进程数`netstat -altupn|grep EST|grep php|wc -l`

- FastCGI执行时间过长,根据实际情况调高以下参数值

  - fastcgi_connect_timeout 300;
  - fastcgi_send_timeout 300;
  - fastcgi_read_timeout 300;

- FastCGI Buffer不够，nginx和apache一样，有前端缓冲限制，可以调整缓冲参数

  - fastcgi_buffer_size 32k;
  - fastcgi_buffers 8 32k;

- Proxy Buffer不够，如果你用了Proxying，调整

  - proxy_buffer_size 16k;

  - proxy_buffers 4 16k;

    

##### 8、nginx 504错误排查

504 Gateway Time-out的含义是所请求的网关没有请求到，简单来说就是没有请求到可以执行的PHP-CGI。

一般来说Nginx 502 Bad Gateway和php-fpm.conf的设置有关，而Nginx 504 Gateway Time-out则是与nginx.conf的设置有关

##### 9、nginx 400 错误排查

> 由于request header过大

- 不要在cookie里记录过多数据
- 调整在nginx.conf中的client_header_buffer_size(默认1k)
- 若cookie太大，可能还需要调整large_client_header_buffers(默认4k)，该参数说明如下：
  - 请求行如果超过buffer，就会报HTTP 414错误(URI Too Long)
  - nginx接受最长的HTTP头部大小必须比其中一个buffer大，否则就会报400的HTTP错误(Bad Request)

##### 10、**事件驱动模型**

- select模型
- poll模型
- epoll模型。

##### 11、nginx与apache的区别

- nginx
  - 轻量级，采用C进行编写，同样的web服务，会占用更少的内存及资源
  - 抗并发
  - 设计高度模块化，编写模块相对简单
  - 配置简洁
  - 一般用于处理静态文件，静态处理性能比apache高三倍以上
  - 作为负载均衡服务器，支持7层负载均衡
  - 反向代理服务器，而且可以作为非常优秀的邮件代理服务器
  - nginx启动特别容易, 并且几乎可以做到 7*24 不间断运行
- apache
  - apache更为成熟，少bug ，nginx的bug相对较多
  - 模块超多，基本想到的都可以找到
  - 超稳定，一个进程死掉时，会影响到多个用户的使用，稳定性差
  - 在处理动态请求有优势，nginx在这方面是鸡肋，一般动态请求要apache去做，nginx适合静态和反向

##### 12、nginx惊群问题

**问题描述：**在建立连接的时候，Nginx处于充分发挥多核CPU架构性能的考虑，使用了多个worker子进程监听相同端口的设计，这样多个子进程在accept建立新连接时会有争抢，这会带来著名的“惊群”问题，子进程数量越多越明显，这会造成系统性能的下降

一般情况下，有多少CPU核心就有配置多少个worker子进程。假设现在没有用户连入服务器，某一时刻恰好所有的子进程都休眠且等待新连接的系统调用（如epoll_wait），这时有一个用户向服务器发起了连接，内核在收到TCP的SYN包时，会激活所有的休眠worker子进程。最终只有最先开始执行accept的子进程可以成功建立新连接，而其他worker子进程都将accept失败。这些accept失败的子进程被内核唤醒是不必要的，他们被唤醒会的执行很可能是多余的，那么这一时刻他们占用了本不需要占用的资源，引发了不必要的进程切换，增加了系统开销。

**解决：**Nginx规定了同一时刻只有唯一一个worker子进程监听web端口，这一就不会发生惊群了，此时新连接事件只能唤醒唯一的正在监听端口的worker子进程。基于这个原理，nginx就使用了一个**共享锁**来控制当前进程是否有权限将需要监听的端口添加到当前进程的epoll句柄中，也就是说，只有获取锁的进程才会监听目标端口。通过这种方式，就保证了每次事件发生时，只有一个worker进程会被触发

![title](/images/2021年春招准备-Nginx篇/5.png)



##### 13、nginx增加最大链接数量

一般来说nginx 配置文件中对优化比较有作用的为以下几项：

- worker_processes 8: nginx 进程数，建议按照cpu 数目来指定，一般为它的倍数 (如,2个四核的cpu计为8)
- use epoll：使用epoll 的I/O 模型
- worker_connections 65535：每个进程允许的最多连接数， 理论上每台nginx 服务器的最大连接数为worker_processes*worker_connections



##### 14、nginx热更新

> nginx -s reload

-s 代表的是向主进程发送信号。其中信号有 4 个，stop, quit, reopen, reload



#### 2、服务注册与服务发现机制

##### 为什么需要服务注册与发现机制

将一个大的单应用拆解成多个独立自治的小服务，如果在没有服务发现的机制下

##### 服务发现

服务的消费者就需要有一个强大的服务发现机制，服务消费者使用这个机制获取服务提供者的网络信息。即使服务提供者的信息发生变化，服务消费者也无须修改配置文件。

在各个服务在启动时，**将自己的网络地址等信息注册到服务发现组件**中，服务发现组件会存储这些信息。
服务消费者可从服务发现组件中查询提供者到网络地址，并使用该地址调用服务提供者的接口。

各个服务与服务发现组件使用一定机制（例如**心跳机制**）通信。服务发现组件如果长时间无法与某微服务实例通信，就会注销该实例。
微服务网络地址发生变更时，会重新注册到微服务发现组件。

#### 3、Eureka

> 保证AP，Zookeeper是CP

**组成**

- Eureka Server提供服务注册服务，各个节点启动后，会在Eureka Server中进行注册，这样EurekaServer中的服务**注册表**中将会存储所有可用服务节点的信息，服务节点的信息可以在界面中直观的看到。

- Eureka Client是一个java客户端，用于简化与Eureka Server的交互，客户端同时也就是一个内置的、使用轮询(round-robin)负载算法的负载均衡器。

**原理**

- Eureka Server 提供服务发现的能力，各个微服务启动时，会向 Eureka Server 注册自己的信息，Eureka Server 会存储这些信息
- Eureka Client 是一个 Java 客户端，用于简化与 Eureka Server 的交互
- 微服务启动后会周期性（默认30秒）的向 Eureka Server 发送心跳以续约自己的“租期“
- 如果 Eureka Server 在一定时间内没有接收到某个服务实例到心跳，那么会注销该实例
- 默认情况下，Eureka Server 同时也是 Eureka Client，多个 Eureka Server 实例之间是通过复制的方式来实现服务注册表中数据的同步
- Eureka Client 会缓存注册表中的信息。无须微服务每次请求都查询 Eureka Server

![title](/images/2021春招准备-服务器运维技巧篇/3.png)



#### 4、ServiceMesh

- Connect
- secure
- control
- observe services

> 微服务化、多语言和容器化发展的趋势，企业迫切需要一种轻量级的服务发现机制
>
> Nginx(重)、Eureka(只适合单语言)

- 业务代码进程(相当于主驾驶)共享一个代理(相当于边车, configMap挂载)
- 代理负责服务发现和负载均衡，还负责动态路由、容错限流、监控度量和安全日志等功能，这些功能是具体业务无关的，属于跨横切面关注点(Cross-Cutting Concerns)范畴
- ServiceMesh比较正式的术语也叫数据面板(DataPlane)，
- 数据面板对应的还有一个独立部署的控制面板(ControlPlane)，用来集中配置和管理数据面板，也可以对接各种服务发现机制(如K8S服务发现)

![title](/images/2021年春招准备-Nginx篇/3.png)

**Istio**

![title](/images/2021年春招准备-Nginx篇/4.png)

- Istio-Manager：负责服务发现，路由分流，熔断限流等配置数据的管理和下发

- Mixer：负责收集代理上采集的度量数据，进行集中监控

- Istio-Auth：负责安全控制数据的管理和下发

- Envoy是目前Istio主力支持的数据面板代理，其它主流代理如nginx/kong等也正在陆续加入这个阵营

