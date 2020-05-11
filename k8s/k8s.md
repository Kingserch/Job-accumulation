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

```