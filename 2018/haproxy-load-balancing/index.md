# HAProxy实现负载均衡

当随着访问量的增加时，原来的单台服务器难以承受大量的访问时，就需要使用负载均衡来分担服务器的压力。本文介绍使用`HAProxy`实现一个免费、高效、可靠的高可用负载均衡解决方案。
<!--more-->
### HAProxy简介
HAProxy是免费、高效、可靠的高可用负载均衡解决方案。适合处理高负载站点的七层数据请求，对外可屏蔽内部的真实Web服务器，防止内部服务器遭受外部攻击。

### HAProxy解决方案
* 客户端IP：将客户端IP进行Hash计算并保存，当相同的IP访问代理服务器时可以转发至相同的后端服务器；
* Cookie：依靠真实服务器发送给客户端的Cookie信息进行会话保持；
* Session：保存真实服务器的Session及服务标识，实现会话保持功能；

### 实验拓扑图

![blog-haproxy2](https://pengshp.coding.net/p/images/d/images/git/raw/master/blog-haproxy2.png "Haproxy")

### 实验配置
#### HAProxy配置
1、安装HAProxy

    $ yum install -y haproxy

2、修改配置文件
```sh
$ [root@CentOS7] ~$ vim /etc/haproxy/haproxy.cfg
global
    log         127.0.0.1 local2

    chroot      /var/lib/haproxy
    pidfile     /var/run/haproxy.pid
    maxconn     4000
    user        haproxy
    group       haproxy
    daemon

    # turn on stats unix socket
    stats socket /var/lib/haproxy/stats

frontend haproxy_inbound
    bind *:80
    default_backend haproxy_httpd

backend haproxy_httpd
    balance     roundrobin
    option httpchk GET /index.html  # 健康检查
    server web1 192.168.10.129:80 check
    server web2 192.168.10.130:80 check
```

3、内核优化
```sh
$ vim /etc/security/limits.conf
*               soft    nofile        65535
*               hard    nofile        65535
```

4、开启日志服务
```sh
# 修改日志配置文件
$ vim /etc/rsyslog.conf
$ModLoad imudp
$UDPServerRun 514
local3.*                           /var/log/haproxy.log

# 重启日志服务
$ systemctl restart syslog
```

5、开启HAProxy

    $ haproxy -f /etc/haproxy/haproxy.cfg
#### 后端Web服务器配置
后端服务器配置基本相同，为了验证试验效果，后端网页不同
1、配置网卡
```sh
$ vim /etc/sysconfig/network-scripts/ifcfg-ens33554984
NAME=ens33554984
BOOTPROTO='static'
ONBOOT="yes"
IPADDR=192.168.10.129
GATEWAY=192.168.10.128  #网关指向HAProxy的内网IP
TYPE="Ethernet"

$ systemctl restart network
```

2、Apache配置
网页文件做修改，其他配置相同
```sh
[root@CentOS7] ~$ yum install -y httpd

# Web-1
[root@CentOS7] ~$ echo "This is Web-1" > /var/www/html/index.html

# Web-2
[root@CentOS7] ~$ echo "This is Web-2" > /var/www/html/index.html

[root@CentOS7] ~$ systemctl start httpd
[root@CentOS7] ~$ systemctl enable httpd
```

#### 测试
重启HAProxy

    $ systemctl restart haproxy.service   

浏览器打开 <http://192.168.10.128>,刷新网页，便可以看到在`Web-1`和`Web-2`之间轮询。实现一个负载均衡。



