+ ### Kubernetes
    + [环境准备](#环境准备)
    + [安装bind服务](#安装bind服务)
+ ### 环境准备
`yum install epel-release` 
`yum install wget net-tools telnet tree nmap sysstat lrzsz dos2unix bind-utils -y`  
+ ### 安装bind服务
##### 1)在hdss7-129主机上安装bind服务(DNS服务)
```
[root@hdss7-129 ~]# yum install bind -y
[root@hdss7-129 ~]# rpm -qa bind
bind-9.11.4-16.P2.el7_8.2.x86_64
[root@hdss7-129 ~]# netstat   -rn   #查看网关
Kernel IP routing table
Destination     Gateway         Genmask         Flags   MSS Window  irtt Iface
0.0.0.0         192.168.56.2    0.0.0.0         UG        0 0          0 ens33
172.7.68.0      0.0.0.0         255.255.255.0   U         0 0          0 docker0
192.168.56.0    0.0.0.0         255.255.255.0   U         0 0          0 ens33
[root@hdss7-129 ~]# vi /etc/named.conf
options {
        listen-on port 53 { 192.168.56.129; };	#默认是127.0.0.1本机需要改成本机的内网ip
        directory       "/var/named";
        dump-file       "/var/named/data/cache_dump.db";
        statistics-file "/var/named/data/named_stats.txt";
        memstatistics-file "/var/named/data/named_mem_stats.txt";
        recursing-file  "/var/named/data/named.recursing";
        secroots-file   "/var/named/data/named.secroots";
        allow-query     { any; };
        forwarders      { 0.0.0.0; };	#本机的网关
        recursion yes;	#一定要是yes 表示用递归的算法查询DNS，另一种是迭代
        dnssec-enable no;
        dnssec-validation no;
[root@hdss7-129 /]# named-checkconf	#检查
```
##### 2)在hdss7-129主机配置区域配置文件
```
[root@hdss7-129 /]# vim /etc/named.rfc1912.zones
...#在最后添加下面字段
zone "host.com" IN {
        type  master;
        file  "host.com.zone";
        allow-update { 192.168.56.129; };
};

zone "od.com" IN {
        type  master;
        file  "od.com.zone";
        allow-update { 192.168.56.129; };
};
```
##### 3)在hdss7-129主机编辑区域数据文件
```
[root@hdss7-129 /]# vim /var/named/host.com.zone
$ORIGIN host.com.
$TTL 600	; 10 minutes
@       IN SOA	dns.host.com. dnsadmin.host.com. (
				2020051101 ; serial
				10800      ; refresh (3 hours)
				900        ; retry (15 minutes)
				604800     ; expire (1 week)
				86400      ; minimum (1 day)
				)
			NS   dns.host.com.
$TTL 60	; 1 minute
dns                A    192.168.56.129
HDSS7-11           A    192.168.56.129
HDSS7-12           A    192.168.56.128
HDSS7-21           A    192.168.56.130
HDSS7-22           A    192.168.56.131
[root@hdss7-129 /]# vim /var/named/od.com.zone	业务域
$ORIGIN od.com.
$TTL 600	; 10 minutes
@   		IN SOA	dns.od.com. dnsadmin.od.com. (
				2020051101 ; serial
				10800      ; refresh (3 hours)
				900        ; retry (15 minutes)
				604800     ; expire (1 week)
				86400      ; minimum (1 day)
				)
				NS   dns.od.com.
$TTL 60	; 1 minute
dns                A    192.168.56.129

```
