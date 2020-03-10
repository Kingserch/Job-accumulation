+ ### nginx优化
    + [ 压力测试工具实战](# 压力测试工具实战)
    + [安装nginx](#安装nginx)
    + [安装mysql](#安装mysql)
    + [安装php7](#安装php7)
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