---
layout: post
date:   2014-02-17 13:31:01 +0800
categories: network
tags: develop, network
title:  "Linux iptables samples"
---

* content
{:toc}


# sample1: block all ports except for 1962, 999, 12020?

At first you should always flush to be sure whats already defined… nothing

    iptables -F

Then set the default policy of the INPUT chain to DROP if the end is reached and no rule matched:

    iptables -P INPUT DROP

To ensure the loopback is not affacted you should add

    iptables -A INPUT -i lo -p all -j ACCEPT
    iptables -A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT

to allow all traffic on the lo-if and every incomming traffic for connections you etablished. After that add every rule you need for your services (don't forget to open ssh if you need it! else you're out):

    iptables -A INPUT -p tcp -m tcp --dport 1962 -j ACCEPT 
    iptables -A INPUT -p tcp -m tcp --dport 999 -j ACCEPT 
    iptables -A INPUT -p tcp -m tcp --dport 12020 -j ACCEPT 

A little trick I do to keep myself and others from accidentally drilling holes into the security I finally add:

    iptables -A INPUT -j DROP

This line matches everything for the INPUT chain and the policy should not get anything. advantage of this is even if you add an ACCEPT-rule sometime after initializing your ruleset it will never become checked because everything is droped before. so it ensures you have to keep everything in one place.

For your question the whole thing looks like this in summary:

    iptables -F
    iptables -P INPUT DROP
    iptables -A INPUT -i lo -p all -j ACCEPT
    iptables -A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT
    iptables -A INPUT -p tcp -m tcp --dport 1962 -j ACCEPT 
    iptables -A INPUT -p tcp -m tcp --dport 999 -j ACCEPT 
    iptables -A INPUT -p tcp -m tcp --dport 12020 -j ACCEPT 
    iptables -A INPUT -j DROP

# iptables示例

## filter表

### filter表INPUT链

怎么处理发往本机的包。

    iptables {-A|-D|-I} INPUT rule-specification
    iptables -A INPUT -s 10.1.2.11 -p tcp --dport 80 -j DROP
    iptables -A INPUT -s 10.1.2.11 -p tcp --dport 80 -j REJECT --reject-with tcp-reset
    iptables -A INPUT -s 10.1.2.11 -p tcp --dport 80 -j ACCEPT

以上表示将从源地址10.1.2.11访问本机80端口的包丢弃（以tcp-reset方式拒绝和接受）。

  -s 表示源地址（--src,--source），其后面可以是一个IP地址（10.1.2.11）、一个IP网段（10.0.0.0/8）、几个IP或网段（192.168.1.11/32,10.0.0.0/8，添加完成之后其实是两条规则）。  
  -d 表示目的地址（--dst,--destination），其后面可以是一个IP地址（10.1.2.11）、一个IP网段（10.0.0.0/8）、几个IP或网段（10.1.2.11/32,10.1.3.0/24，添加完成之后其实是两条规则）。  
  -p 表示协议类型（--protocol），后面可以是tcp, udp, udplite, icmp, esp, ah, sctp, all，其中all表示所有的协议。  
  --sport 表示源端口（--source-port），后面可以是一个端口（80）、一系列端口（80:90，从80到90之间的所有端口），一般在OUTPUT链使用。  
  --dport 表示目的端口（--destination-port），后面可以是一个端口（80）、一系列端口（80:90，从80到90之间的所有端口）。  
  -j 表示iptables规则的目标（--jump），即一个符合目标的数据包来了之后怎么去处理它。常用的有ACCEPT, DROP, REJECT, REDIRECT, LOG, DNAT, SNAT。


    iptables -A INPUT -p tcp --dport 80 -j DROP
    iptables -A INPUT -p tcp --dport 80:90 -j DROP
    iptables -A INPUT -m multiport -p tcp --dports 80,8080 -j DROP

以上表示将所有访问本机80端口（80和90之间的所有端口，80和8080端口）的包丢弃。

-m 匹配更多规则（--match），可以指定更多的iptables匹配扩展。可以是tcp, udp, multiport, cpu, time, ttl等，即你可以指定一个或多个端口，或者本机的一个CPU核心，或者某个时间段内的包。

### filter表OUTPUT链

怎么处理本机向外发的包。

    iptables -A OUTPUT -p tcp --sport 80 -j DROP

以上这条规则意思是不允许访问本机80端口的包出去。即你可以向本机80端口发送请求包，但是本机回应给你的包会被该条规则丢弃。

INPUT链与OUTPUT链用法一样，但是表示的意思不同。

### filter表的FORWARD链

For packets being routed through the box（不知道怎么解释）。

其用法与INPUT链和OUTPUT链类似。

## nat表

nat表有三条链，分别是PREROUTING, OUTPUT, POSTROUTING。

### nat表PREROUTING链

修改发往本机的包。

    iptables -t nat -A PREROUTING -p tcp -d 202.102.152.23 --dport 80 -j DNAT --to-destination 10.67.15.23:8080
    iptables -t nat -A PREROUTING -p tcp -d 202.102.152.23 -j DNAT --to-destination 10.67.15.23

以上这两条规则的意思是将发往IP地址202.102.152.23和端口80的包的目的地址修改为10.67.15.23，目的端口修改为8080。将发往202.102.152.23的其他非80端口的包目的地址修改为10.67.15.23。第二条规则中的-p tcp是可选的，也可以指定其他协议。

其实类似这样的规则一般在路由器上做，路由器上有个公网IP（202.102.152.23），其中有个用户的内网IP（10.67.15.23）想提供外网的web服务，而路由器又不想将公网IP地址绑定到用户机器上，因此就出来了以上的蛋疼规则。

### nat表POSTROUTING链

修改本机向外发的包。

    iptables -t nat -A POSTROUTING -p tcp -s 10.67.15.23 --sport 8080 -j SNAT --to-source 202.102.152.23:80
    iptables -t nat -A POSTROUTING -p tcp -s 10.67.15.23 -j SNAT --to-source 202.102.152.23

以上两条规则的意思是将从IP地址10.67.15.23和端口8080发出的包的源地址修改为202.102.152.23，源端口修改为80。将从10.67.15.23发出的非80端口的包的源地址修改为202.102.152.23。

这两条正好与以上两条PREROUTING共同完成了内网用户想提供外网服务的功能。

其中的--to-destination和--to-source都可以缩写成--to，在DNAT和SNAT中会分别被理解成--to-destination和--to-source。

注： 之所以将内网服务的端口和外网服务的端口写的不一致是因为二者其实真的可以不一致。另外，是否将PREROUTNG中的-d改为域名就可以使用一个公网IP为不同用户提供服务了呢？这个需要哥哥我稍后验证。

### nat表做HA的实例

有两台服务器和三个IP地址，分别是10.1.2.21, 10.1.2.22, 10.1.5.11。假设他们提供的是相同的WEB服务，现在想让他们做HA，而10.1.5.11是他们的VIP。

* 10.1.2.21这台的NAT规则如下：

    iptables -t nat -A PREROUTING -p tcp -d 10.1.2.11 --dport 80 -j DNAT --to-destination 10.1.2.21:80
    iptables -t nat -A POSTROUTING -p tcp -s 10.1.2.21 --sport 80 -j SNAT --to-source 10.1.2.11:80

* 10.1.2.22这台的NAT规则如下：

    iptables -t nat -A PREROUTING -p tcp -d 10.1.2.11 --dport 80 -j DNAT --to-destination 10.1.2.22:80
    iptables -t nat -A POSTROUTING -p tcp -s 10.1.2.22 --sport 80 -j SNAT --to-source 10.1.2.11:80

默认可以认为VIP在10.1.2.21上挂着，那么当这台机器发生故障不能提供服务时，我们可以及时将VIP挂到10.1.2.22上，这样就可以保证服务不中断了。当然我们可以写一个简单的SHELL脚本来完成VIP的检测及挂载，方法非常简单。

注： LVS的实现中貌似有这么一项，还没有深入去研究LVS。

### nat表为虚拟机做内外网联通

宿主机内网IP是10.67.15.183(eth1)，外网IP是202.102.152.183(eth0)，内网网关是10.67.15.1，其上面的虚拟机IP是10.67.15.250(eth1)。

目前虚拟机只能连接内网，其路由信息如下：

    # ip r s
    10.67.15.0/24 dev eth1  proto kernel  scope link  src 10.67.15.250
    169.254.0.0/16 dev eth1  scope link  metric 1003 
    192.168.0.0/16 via 10.67.15.1 dev eth1 
    172.16.0.0/12 via 10.67.15.1 dev eth1 
    10.0.0.0/8 via 10.67.15.1 dev eth1 
    default via 10.67.15.1 dev eth1

若要以NAT方式实现该虚拟机即能连接公网又能连接内网，则该虚拟机路由需要改成以下：

    # ip r s
    10.67.15.0/24 dev eth1  proto kernel  scope link  src 10.67.15.250
    169.254.0.0/16 dev eth1  scope link  metric 1003 
    192.168.0.0/16 via 10.67.15.1 dev eth1 
    172.16.0.0/12 via 10.67.15.1 dev eth1 
    10.0.0.0/8 via 10.67.15.1 dev eth1 
    default via 10.67.15.183 dev eth1

虚拟机连接内网的网关地址也可以写成宿主机内网IP地址。

宿主机上面添加如下NAT规则：

    iptables -t nat -A POSTROUTING -s 10.67.15.250/32 -d 10.0.0.0/8 -j SNAT --to-source 10.67.15.250
    iptables -t nat -A POSTROUTING -s 10.67.15.250/32 -d 172.16.0.0/12 -j SNAT --to-source 10.67.15.250
    iptables -t nat -A POSTROUTING -s 10.67.15.250/32 -d 192.168.0.0/16 -j SNAT --to-source 10.67.15.250
    iptables -t nat -A POSTROUTING -s 10.67.15.250/32 -j SNAT --to-source 202.102.152.183

以上四条规则的意思是将从源地址10.67.15.250发往内网机器上的数据包的源地址改为10.67.15.250。将从源地址10.67.15.250发往公网机器上的数据包的源地址修改为202.102.152.183。

## iptables管理命令

### 查看iptables规则

    iptables -nL
    iptables -n -L
    iptables --numeric --list
    iptables -S
    iptables --list-rules
    iptables -t nat -nL
    iptables-save

-n代表--numeric，意思是IP和端口都以数字形式打印出来。否则会将127.0.0.1:80输出成localhost:http。端口与服务的对应关系可以在/etc/services中查看。  
-L代表--list，列出iptables规则，默认列出filter链中的规则，可以用-t来指定列出哪个表中的规则。  
-t代表--tables，指定一个表。  
-S代表--list-rules，以原命令格式列出规则。  
iptables-save命令是以原命令格式列出所有规则，可以-t指定某个表。

### 清除iptables规则

    iptables -F
    iptables --flush
    iptables -F OUTPUT
    iptables -t nat -F
    iptables -t nat -F PREROUTING

-F代表--flush，清除规则，其后面可以跟着链名，默认是将指定表里所有的链规则都清除。

### 保存iptables规则

    /etc/init.d/iptables save

该命令会将iptables规则保存到/etc/sysconfig/iptables文件里面，如果iptable有开机启动的话，开机时会自动将这些规则添加到机器上。

## 其他内容

iptables命令中的很多选项前面都可以加"!"，意思是“非”。如"! -s 10.0.0.0/8"表示除这个网段以外的源地址，"! --dport 80"表示除80以外的其他端口。

还有其他的内容，慢慢补充。


# iptables使用

## iptables使用的语法

iptables [-t table] 操作 chain RULE

  - 如果想查看特别的表时使用-t指定,如果查看单独的链需要在操作后面指定.
  - 如果是查看规则定义,使用-S. -S比-L查看规则时更加清晰.
  - 如果查看匹配状况使用-nvL.配合watch使用.
  - --line-number用于查看规则号.

## iptables-save output

这个命令主要是把内存态的规则保存到文件,然后下次启动的时候用iptables-restore来载入规则. 但是这个命令常常用来查看防火墙规则.比iptables用得都多, 主要是输出结果的格式比较紧凑直观.而且能方便的能看到所有表的规则.

    ┌─[ubuntu@fishcried] - [~] - [2015-07-17 05:47:22]
    └─[0] sudo iptables-save
    # Generated by iptables-save v1.4.21 on Fri Jul 17 17:46:45 2015
    *mangle
    :PREROUTING ACCEPT [170491:25205613]
    :INPUT ACCEPT [170491:25205613]
    :FORWARD ACCEPT [0:0]
    :OUTPUT ACCEPT [186436:72064133]
    :POSTROUTING ACCEPT [186436:72064133]
    COMMIT
    # Completed on Fri Jul 17 17:46:45 2015
    # Generated by iptables-save v1.4.21 on Fri Jul 17 17:46:45 2015
    *nat
    :PREROUTING ACCEPT [726722:43601168]
    :INPUT ACCEPT [726722:43601168]
    :OUTPUT ACCEPT [17016:1033385]
    :POSTROUTING ACCEPT [17016:1033385]
    :DOCKER - [0:0]
    -A PREROUTING -m addrtype --dst-type LOCAL -j DOCKER
    -A OUTPUT ! -d 127.0.0.0/8 -m addrtype --dst-type LOCAL -j DOCKER
    -A POSTROUTING -s 172.17.0.0/16 ! -d 172.17.0.0/16 -j MASQUERADE
    COMMIT
    # Completed on Fri Jul 17 17:46:45 2015
    # Generated by iptables-save v1.4.21 on Fri Jul 17 17:46:45 2015
    *filter
    :INPUT ACCEPT [5130295:773100722]
    :FORWARD ACCEPT [0:0]
    :OUTPUT ACCEPT [5052679:681817970]
    -A FORWARD -o docker0 -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT
    -A FORWARD -i docker0 ! -o docker0 -j ACCEPT
    -A FORWARD -i docker0 -o docker0 -j ACCEPT
    COMMIT
    # Completed on Fri Jul 17 17:46:45 2015

输出是分段的,每个段落一个表,*开头后面跟着表名
:开头的行是对链匹配次数的总结,后面跟着统计信息,[数据包:字节数].
后面跟着的是具体规则
最后跟着COMMIT表示一个表的结束

### iptables-save有两个选项:

-t 后面可以指定表明
-c 输出会在每条规则前加上匹配信息

### 如何快速查看iptables的流量匹配?

iptables [-t 表] -L [链] -nv
iptables-save -c
如果想快速的查看规则匹配情况,可以使用上面两条命令.第一条命令较为灵活,可以控制表和链,而且统计信息比较人性化.第二条命令简单. 配合watch能够动态观察.

## iptables自定义连

如果想自行定义规则链,可以通过-N参数,然后通过-j/--jump跳转过来就可以了.

    iptables -N 链名
    iptables ... -j 链名

## iptables规则调优策略

优化的核心原则就是减少规则匹配.

### 简化规则

用multiport,iprange模块,简化规则数量,也减少了匹配次数.

### 调整匹配顺序

  - 书写规则顺序的原则,通用匹配放在前面. 配合iptable -t tname -Lnv的观察数据
  - 提前规则的在表的位置.例如将filter表中针对dnat后匹配的规则放到raw中匹配,需要使用没有做dnat的ip.这样减少了很多次匹配.
  - 分层优化,减少匹配顺序.通过自定义的链,来跳转规则.这样就能减少匹配次数了

## NEW,ESTABLISHED,RELATED,INVALID状态

  - NEW: conntrack模块看到的某个连接第一个包，它即将被匹配了。比如，我们看到一个SYN包，是我们所留意的连接的第一个包，就要匹配它。第一个包也可能不是SYN包，但它仍会被认为是NEW状态。
  - ESTABLISHED: 已经注意到两个方向上的数据传输，而且会继续匹配这个连接的包。处于ESTABLISHED状态的连接是非常容易理解的。只要发送并接到应答，连接就是ESTABLISHED的了。一个连接要从NEW变为ESTABLISHED，只需要接到应答包即可，不管这个包是发往防火墙的，还是要由防火墙转发的。ICMP的错误和重定向等信息包也被看作是ESTABLISHED，只要它们是我们所发出的信息的应答。
  - RELATED 当一个连接和某个已处于ESTABLISHED状态的连接有关系时，就被认为是RELATED的了。换句话说，一个连接要想是RELATED的，首先要有一个ESTABLISHED的连接。这个ESTABLISHED连接再产生一个主连接之外的连接，这个新的连接就是RELATED的了，比如ftp的父子链接.
  - 非以上状态的包.

## 一些实例

### tcp通用规则

    # 这条规则能够过滤掉很多类型的端口扫描
    iptables -t filter -A INPUT -p tcp  -m state --state INVALID -j DROP

    # 避免配置很多内部端口通行规则
    iptables -t filter -A INPUT -p tcp -m state --state ESTABLISHED,RELATED -j ACCEPT
    端口扫描

state与recent模块配合使用.30分钟内发起22,21,80以外端口超过10次,认为是portscan.

    iptables -A INPUT -p all -m state --state ESTABLISHED,RELATED -j ACCEPT
    iptables -A INPUT -p all -m state --state NEW -m recent --name port_scan --update --seconds 1800 --hitcount 10 -j DROP
    iptables -A INPUT -p tcp --syn -m state --state NEW -m multiport --dports 22,21,80 -j ACCEPT
    iptables -A INPUT -p all -m recent --name port_scan --set

### 暴力猜解

暴力猜解会发起很多次尝试,主要是在这里下手.

    #pop3猜解
    iptables -A OUTPUT -p tcp --sport 110 -m string --algo bm --string "-ERR Authentication failed." \
                              -m recent --name pop3 \
                              --update --seconds 600 --hicount 6 -j REJECT
    iptables -A OUTPUT -p tcp --sport 110 -m string --algo bm --string "-ERR Authentication failed." \
                              -m recent --name pop3 --set


    #ssh猜解
    iptables -A INPUT -p tcp --syn --dport 22 -m recent --name ssh --update --seconds 600 --hitcount 4 -j DROP
    iptables -A INPUT -p tcp --syn --dport 22 -m recent --name ssh --set

### Syn flood

    modprobe ipt_recent ip_list_tot=16384
    iptables -N SYN_FLOOD
    iptables -A FORWARD -p tcp --syn --dport 80 -m limit --limit 1/m --limit-burst 300 -j ACCEPT
    iptables -A FORWARD -p tcp --syn --dport 80 -j SYN_FLOOD
    iptables -A SYN_FLOOD -p tcp --syn --dport 80 -m recent --name syn_flood --update --second 123 --hitcount 1 -j ACCEPT
    iptables -A SYN_FLOOD -p tcp --syn --dport 80 -m recent --name syn_flood --set
    iptables -A SYN_FLOOD -p tcp --syn --dport 80 -j DROP

也可以通过调整网络子系统的参数来抵御flood攻击:

    net.ipv4.tcp_synack_retries
    net.ipv4.tcp_max_syn_backlog
    net.ipv4.tcp_syncookies

### ip欺骗经典配置

  - 任何进入网络的数据包不能把网络内部的地址作为源地址。
  - 任何进入网络的数据包必须把网络内部的地址作为目的地址。
  - 任何离开网络的数据包必须把网络内部的地址作为源地址。
  - 任何离开网络的数据包不能把网络内部的地址作为目的地址。
  - 任何进入或离开网络的数据包不能把一个私有地址(Private Address)或在RFC1918中列出的属于保留空间(包括10.x.x.x/8、172.16.x.x/12 或192.168.x.x/16 和网络回送地址127.0.0.0/8.)的地址作为源或目的地址。
  - 阻塞任意源路由包或任何设置了IP选项的包。


  [1]: https://www.netfilter.org/documentation/HOWTO/netfilter-hacking-HOWTO.txt
  [2]: http://www.docum.org/docum.org/kptd/
  [3]: http://fishcried.com/2016-02-19/iptables/
  [4]: https://gigenchang.wordpress.com/2014/04/19/10%E5%88%86%E9%90%98%E5%AD%B8%E6%9C%83iptables/
  [5]: https://my.oschina.net/HankCN/blog/117796

