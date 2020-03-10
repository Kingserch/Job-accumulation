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
Time taken for tests:   32.943 seconds
Complete requests:      2000
Failed requests:        0
Write errors:           0
Total transferred:      7576000 bytes
HTML transferred:       6706000 bytes
Requests per second:    60.71 [#/sec] (mean)
Time per request:       329.432 [ms] (mean)
Time per request:       16.472 [ms] (mean, across all concurrent requests)
Transfer rate:          224.58 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    0   2.8      0      85
Processing:    29  328 174.0    286    1840
Waiting:       29  327 174.0    285    1839
Total:         30  329 174.0    287    1840

Percentage of the requests served within a certain time (ms)
  50%    287
  66%    354
  75%    405
  80%    442
  90%    557
  95%    661
  98%    804
  99%    917
 100%   1840 (longest request)
[root@m3 scripts]#
```