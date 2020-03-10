+ ### nginx优化
    + [压力测试工具实战](#压力测试工具实战)
    + [系统性能优化](#系统性能优化)
    + [CPU亲和与Worker进程](#CPU亲和与Worker进程)
	+ [php优化](#php优化)
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
+ ### php优化
```
[root@m3 nginx]# egrep  "^[a-z]" /etc/php.ini 
log_errors=On	# 开启日志
engine = On
short_open_tag = Off
precision = 14
output_buffering = 4096
zlib.output_compression = Off
implicit_flush = Off
unserialize_callback_func =
serialize_precision = 17
disable_functions =
disable_classes =
zend.enable_gc = On
expose_php = Off		#关闭php版本信息
max_execution_time = 300
max_input_time = 300
memory_limit = 128M	   #每个脚本执行最大内存
error_reporting = E_ALL & ~E_DEPRECATED & ~E_STRICT
display_errors = Off
display_startup_errors = Off	#屏幕不显示错误日志
log_errors = On	
log_errors_max_len = 1024
ignore_repeated_errors = Off
ignore_repeated_source = Off
report_memleaks = On
track_errors = Off
html_errors = On
variables_order = "GPCS"
request_order = "GP"
register_argc_argv = Off
auto_globals_jit = On
post_max_size = 16M		#允许客户端单个POST请求发送的最大数据
auto_prepend_file =
auto_append_file =
default_mimetype = "text/html"
default_charset = "UTF-8"
doc_root =
user_dir =
enable_dl = Off
file_uploads = On	# 开启文件上传功能，默认启动
upload_max_filesize = 300M	#允许上传文件的最大大小
max_file_uploads = 20	#允许同时上传的文件的最大数量
allow_url_fopen = On
allow_url_include = Off
default_socket_timeout = 60
cli_server.color = On
date.timezone =Asia/Shanghai		#时区调整,默认PRC, 建议调整为Asia/Shanghai
pdo_mysql.cache_size = 2000
pdo_mysql.default_socket=/var/lib/mysql/mysql.sock
sendmail_path = /usr/sbin/sendmail -t -i
mail.add_x_header = On
sql.safe_mode = Off
odbc.allow_persistent = On
odbc.check_persistent = On
odbc.max_persistent = -1
odbc.max_links = -1
odbc.defaultlrl = 4096
odbc.defaultbinmode = 1
ibase.allow_persistent = 1
ibase.max_persistent = -1
ibase.max_links = -1
ibase.timestampformat = "%Y-%m-%d %H:%M:%S"
ibase.dateformat = "%Y-%m-%d"
ibase.timeformat = "%H:%M:%S"
mysqli.max_persistent = -1
mysqli.allow_persistent = On
mysqli.max_links = -1
mysqli.cache_size = 2000
mysqli.default_port = 3306
mysqli.default_socket =/var/lib/mysql/mysql.sock
mysqli.default_host =
mysqli.default_user =
mysqli.default_pw =
mysqli.reconnect = Off
mysqlnd.collect_statistics = On
mysqlnd.collect_memory_statistics = Off
pgsql.allow_persistent = On
pgsql.auto_reset_persistent = Off
pgsql.max_persistent = -1
pgsql.max_links = -1
pgsql.ignore_notice = 0
pgsql.log_notice = 0
bcmath.scale = 0
session.save_handler = files
session.use_strict_mode = 0
session.use_cookies = 1
session.use_only_cookies = 1
session.name = PHPSESSID
session.auto_start = 0
session.cookie_lifetime = 0
session.cookie_path = /
session.cookie_domain =
session.cookie_httponly =
session.serialize_handler = php
session.gc_probability = 1
session.gc_divisor = 1000
session.gc_maxlifetime = 1440
session.referer_check =
session.cache_limiter = nocache
session.cache_expire = 180
session.use_trans_sid = 0
session.hash_function = 0
session.hash_bits_per_character = 5
url_rewriter.tags = "a=href,area=href,frame=src,input=src,form=fakeentry"
zend.assertions = -1
error_reporting=E_WARNING & E_ERROR		#记录php错误日志至后台
tidy.clean_output = Off
soap.wsdl_cache_enabled=1
soap.wsdl_cache_dir="/tmp"
soap.wsdl_cache_ttl=86400
soap.wsdl_cache_limit = 5
ldap.max_links = -1
[root@m3 nginx]# 
```