+ ### nginx优化
    + [压力测试工具实战](#压力测试工具实战)
    + [系统性能优化](#系统性能优化)
    + [CPU亲和与Worker进程](#CPU亲和与Worker进程)
+ ###  压力测试工具实战
```
yum install httpd-tools -y
[root@m3 scripts]# ab -n2000 -c20 http://119.110.1.3/zabbix/
....
Server Software:        nginx
Server Hostname:        119.110.1.3
Server Port:            80

Document Path:          /zabbix/
Document Length:        3353 bytes

Concurrency Level:      20
Time taken for tests:   32.943 seconds	#总花费总时长
Complete requests:      2000		 #总请求数
Failed requests:        0		#请求失败数
Write errors:           0
Total transferred:      7576000 bytes		#总传输大小
HTML transferred:       6706000 bytes		#页面传输大小
Requests per second:    60.71 [#/sec] (mean)	[#/sec] (mean)    #每秒多少请求/s(总请求数/总共完成的时间)
Time per request:       329.432 [ms] (mean)	 #客户端访问服务端, 单个请求所需花费的时间
Time per request:       16.472 [ms] (mean, across all concurrent requests)	#服务端处理请求的时间
Transfer rate:          224.58 [Kbytes/sec] received	#判断网络传输速率, 观察网络是否存在瓶颈
...
```
了解影响性能指标，网络的流量，网络是否丢包，系统有没有磁盘损坏磁盘速率系统负载、内存、系统稳定性，程序接口的性能，处理速度，程序执行效率等。
+ ###  系统性能优化
```
#系统全局性修改
​#用户局部性修改
#进程局部性修改
[root@m3 scripts]# vim /etc/security/limits.conf
#针对root用户，soft提醒，hard限制，nofile打开最大文件数
root soft nofile 65535
root hard nofile 65535
# *代表所有用户
* soft nofile 25535
* hard nofile 25535
##对于Nginx进程
worker_rlimit_nofile 65535;
```
+ ### CPU亲和与Worker进程
​CPU亲和(affinity)减少进程之间不断频繁切换，减少性能损耗，其实现原理是将CPU核心和Nginx工作进程绑定方式，把每个worker进程固定对应的cpu上执行，减少切换cpu的cache miss，获得更好的性能。
```
[root@m3 nginx]# lscpu |grep "CPU(s)"	#查看当前cpu物理状态
CPU(s):                2
On-line CPU(s) list:   0,1
NUMA node0 CPU(s):     0,1
[root@m3 nginx]# vim nginx.conf
worker_cpu_affinity auto;	#将Nginx worker进程绑至不同的核心上，官方建议与cpu的核心保持一致
[root@m3 nginx]# egrep '^[a-z]' nginx.conf 
user nginx;
worker_processes auto;
worker_cpu_affinity auto;
error_log /var/log/nginx/error.log info;	#全局错误日志定义类型，[ debug | info | notice | warn | error | crit ]
pid /run/nginx.pid;
include /usr/share/nginx/modules/*.conf;
events {
...
```
