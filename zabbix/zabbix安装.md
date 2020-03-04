+ ### Zabbix安装
    + [Zabbix-Server服务端安装](#Zabbix-Server服务端安装)
    + [查看镜像images,tag,inspect,history](#查看镜像)
    + [搜寻镜像search](#搜寻镜像)
    + [创建镜像commit,import,build](#创建镜像)
    + [存出和载入镜像save,load](#存出和载入镜像)
    + [上传镜像push](#上传镜像)	
### Zabbix-Server服务端安装

#### 1)配置时间同步
```
yum install ntp -y 
systemctl start ntpd			#启动
systemctl enable ntpd		#加入开机启动
systemctl list-dependencies|grep ntpd	#检测是否加入开机启动
```
#### 2)服务端安装zabbix
```
rpm -Uvh https://repo.zabbix.com/zabbix/4.0/rhel/7/x86_64/zabbix-release-4.0-1.el7.noarch.rpm
yum -y install zabbix-server-mysql zabbix-web-mysql zabbix-agent zabbix-get zabbix-web zabbix-sender

```
#### 3)数据库操作
```
create database zabbix character set utf8 collate utf8_bin;  #创建zabbix数据库
grant all privileges on zabbix.* to zabbix@localhost identified by 'zabbix'; #授权zabbix用户从localhost访问，对zabbix数据库有完全控制权限
授权的时候可能会提示,密码过短。
mysql> grant all privileges on zabbix.* to zabbix@localhost identified by 'zabbix';
ERROR 1819 (HY000): Your password does not satisfy the current policy requirements

set global validate_password_policy=0;
set global validate_password_length=1;
select @@validate_password_length;
set global validate_password_mixed_case_count=2;
select @@validate_password_mixed_case_count;
SHOW VARIABLES LIKE 'validate_password%';
flush privileges;
zcat /usr/share/doc/zabbix-server-mysql*/create.sql.gz | mysql -uzabbix -p zabbix #初始化数据库结构并导入数据
```
```
###插曲：如果是lnmp环境请这样配置
cp -a /usr/share/zabbix	/usr/share/nginx/html/	#这个是我nginx的默认目录
需要授权zabbix用户对配置web路径的访问权限
chown -R zabbix:zabbix /etc/zabbix/
chown -R zabbix:zabbix /usr/share/nginx/
chown -R zabbix:zabbix /usr/lib/zabbix/
chmod -R 755 /etc/zabbix/web/
chmod -R 777 /var/lib/php/session/
```
#### 4)配置zabbix_server.conf
```
[root@m3 ]# egrep -v "^#|^$" /etc/zabbix/zabbix_server.conf
LogFile=/var/log/zabbix/zabbix_server.log
LogFileSize=0
PidFile=/var/run/zabbix/zabbix_server.pid
SocketDir=/var/run/zabbix
DBHost=119.110.1.3				#需要修改,默认localhost
DBName=zabbix				#默认
DBUser=zabbix				#默认
DBPassword=zabbix				#连接数据库的密码
SNMPTrapperFile=/var/log/snmptrap/snmptrap.log
Timeout=4
AlertScriptsPath=/usr/lib/zabbix/alertscripts
ExternalScripts=/usr/lib/zabbix/externalscripts
LogSlowQueries=3000
AllowRoot=1	#默认为0 改1为了其他用户可以连接数据库，不是必要配置
```
```
启动zabbix
systemctl start zabbix-server		#启动
systemctl enable zabbix-server		#开机自启动
systemctl list-dependencies|grep zabbix-server	#检测
systemctl restart mysqld nginx zabbix-server.service  zabbix-agent.service php-fpm
systemctl enable mysqld nginx zabbix-server.service  zabbix-agent.service php-fpm
```
#### 5)授权zabbix用户对配置web路径的访问权限
```
cp -r /usr/share/zabbix/ /usr/share/nginx/html/zabbix/
chown -R zabbix:zabbix /etc/zabbix
chown -R zabbix:zabbix /usr/lib/zabbix
chown -R zabbix:zabbix /usr/share/zabbix
chown -R zabbix:zabbix /usr/share/nginx/html/zabbix/
chmod -R 777 /var/lib/php/session
chown nginx:nginx /etc/zabbix/web/ -R	#授权nginx用户访问webzabbix
```
#### 6)创建告警和扩展脚本目录(../install)，这里会单独列出一个文件来描述
mkdir -p /etc/zabbix/alertsscripts  /etc/zabbix/externalscripts
zabbix_mysql 分表 备份脚本：https://github.com/Kingserch/dir/tree/master/Zabbix/scripts


6)如果关闭防火墙不需要配置下面，开启则需要(我维护的生产环境就没开selinux)
6.1)防火墙设置
#CentOS 7操作系统防火墙规则设置
firewall-cmd --permanent --add-port=80/tcp
firewall-cmd --permanent --add-port=10050/tcp
firewall-cmd --permanent --add-port=10051/tcp
firewall-cmd –reload
上述规则中，10050是Agent的端口，Agent采用被动方式，Server主动连接Agent的10050端口；10051是Server的端口，Agent采用主动或Trapper方式，会连接Server的10051端口。对于具有防火墙的网络环境，采取相同的防火墙规则策略即可。
6.2)SELinux的设置
如果操作系统已开启SELinux，则需要设置语句开启允许SELinux相关策略。
setsebool -P httpd_can_connect_zabbix on
setsebool -P httpd_can_network_connect_db on
关闭selinux
setenforce 0 	#0代表permissive，1代表enforcing；也可直接用permissive和enforcing，不用重启服务器即生效
sed -i 's/SELINUX=enforcing/SELINUX=disabled/' /etc/selinux/config   #需要重启系统，永久关闭
getenforce 	#获取当前SELinux的运行状态

7)zabbix相关配置
7.1)Zabbix-Web连接数据库和Zabbix-Server端口的相关配置信息如下：
[root@s-30 zabbix]# cat /etc/zabbix/web/zabbix.conf.php 
<?php
// Zabbix GUI configuration file.
global $DB;

$DB['TYPE']     = 'MYSQL';
$DB['SERVER']   = 'localhost';
$DB['PORT']     = '0';
$DB['DATABASE'] = 'zabbix';
$DB['USER']     = 'zabbix';
$DB['PASSWORD'] = 'zabbix';

// Schema name. Used for IBM DB2 and PostgreSQL.
$DB['SCHEMA'] = '';

$ZBX_SERVER      = 'localhost';
$ZBX_SERVER_PORT = '10051';
$ZBX_SERVER_NAME = 'zabbix';

$IMAGE_FORMAT_DEFAULT = IMAGE_FORMAT_PNG;

$ZBX_SERVER      = '127.0.0.1';
$ZBX_SERVER_PORT = '10051';
$ZBX_SERVER_NAME = 'zbx4';

$IMAGE_FORMAT_DEFAULT = IMAGE_FORMAT_PNG;
7.2) /etc/zabbix/zabbix_server.conf 中的参数
[root@s-30 zabbix]# egrep -v "^#|^$" /etc/zabbix/zabbix_server.conf
LogFile=/var/log/zabbix/zabbix_server.log
LogFileSize=0
PidFile=/var/run/zabbix/zabbix_server.pid
SocketDir=/var/run/zabbix
DBHost=localhost				#数据库的ip如果不在本机，要写真实的iP
DBName=zabbix				#数据库的名称
DBUser=zabbix				#数据库的用户
DBPassword=zabbix				#数据库的密码
SNMPTrapperFile=/var/log/snmptrap/snmptrap.log
Timeout=4
AlertScriptsPath=/usr/lib/zabbix/alertscripts
ExternalScripts=/usr/lib/zabbix/externalscripts
LogSlowQueries=3000

8)zabbix_server程序中的参数
通过zabbix_server --help 可以查看配置的参数，可以通过在线热加改变某个配置参数
8.1)手动执行清理器Housekeeper，可以删除过期数据，如下：
[root@s-30 conf]# zabbix_server -R housekeeper_execute
zabbix_server [33612]: command sent successfully
[root@s-30 conf]# tail -f /var/log/zabbix/zabbix_server.log
  1649:20191101:051138.056 forced execution of the housekeeper
  1649:20191101:051138.056 executing housekeeper
  1649:20191101:051138.473 housekeeper [deleted 1073 hist/trends, 0 items/triggers, 0 events, 0 problems, 0 sessions, 0 alarms, 0 audit items in 0.376787 sec, idle for 1 hour(s)]
  1648:20191101:051238.693 forced reloading of the configuration cache
  1651:20191101:090445.247 slow query: 10340.401633 sec, "select h.hostid,h.host,h.name,t.httptestid,t.name,t.agent,t.authentication,t.http_user,t.http_password,t.http_proxy,t.retries,t.ssl_cert_file,t.ssl_key_file,t.ssl_key_password,t.verify_peer,t.verify_host,t.delay from httptest t,hosts h where t.hostid=h.hostid and t.nextcheck<=1572559944 and mod(t.httptestid,1)=0 and t.status=0 and h.proxy_hostid is null and h.status=0 and (h.maintenance_status=0 or h.maintenance_type=0)"
  1649:20191101:090602.697 executing housekeeper
  1649:20191101:090603.126 housekeeper [deleted 3649 hist/trends, 0 items/triggers, 0 events, 0 problems, 0 sessions, 0 alarms, 0 audit items in 0.427603 sec, idle for 1 hour(s)]
  1649:20191101:093731.806 forced execution of the housekeeper
  1649:20191101:093731.806 executing housekeeper
  1649:20191101:093732.103 housekeeper [deleted 2122 hist/trends, 0 items/triggers, 0 events, 0 problems, 0 sessions, 0 alarms, 0 audit items in 0.283118 sec, idle for 1 hour(s)]
8.2)在线执行重载配置缓存
[root@s-30 conf]# zabbix_server -R config_cache_reload
[root@s-30 conf]# zabbix_server -R config_cache_reload
8.3)在线调整日志运行级别，执行一次，降低一个级别
[root@s-30 conf]# zabbix_server -R log_level_decrease
zabbix_server [33878]: command sent successfully
[root@s-30 conf]# tail -1 /var/log/zabbix/zabbix_server.log
  1669:20191101:094205.982 log level has been decreased to 2 (error)
8.3)在线调整日志级别，执行一次，增加一个级别
[root@s-30 conf]# zabbix_server -R log_level_increase
zabbix_server [33973]: command sent successfully
[root@s-30 conf]# tail -1 /var/log/zabbix/zabbix_server.log
  1675:20191101:094338.416 log level has been increased to 3 (warning)
8.4)调整某个进程（pid）的日志运行级别
[root@s-30 conf]# ps -ef |grep zabbix|wc -l
zabbix_server -R log_level_increase=pid		#可以跟进程的pid
zabbix_server -R log_level_increase=poller		#可以跟进程的名字
zabbix_server -R log_level_increase=poller	,3	#可以根据进程名字设置日志级别

二)Zabbix-Agent客户端安装
1）客服端的采集方式为Agent，snmp等
rpm -ivh http://repo.zabbix.com/zabbix/4.0/rhel/7/x86_64/zabbix-release-4.0-1.el7.noarch.rpm #安装Zabbix官方的yum源
yum install -y  zabbix-agent  
# 由于Zabbix-Server服务器本身也需要监控，所以在Zabbix-Server服务器中也同样需要安装Zabbix-Agent
防火墙配置
#CentOS 7
firewall-cmd --permanent --add-port=10050/tcp
firewall-cmd --reload
2)配置zabbix_agentd.conf
[root@s-30 /]# egrep -v "^#|^$" /etc/zabbix/zabbix_agentd.conf 
PidFile=/var/run/zabbix/zabbix_agentd.pid	#pid文件路径
LogFile=/var/log/zabbix/zabbix_agentd.log	#日志文件路径
LogFileSize=0				#日志切割大小，0表示不切割		
Server=127.0.0.1		#被动模式，zabbix-server的IP地址，允许zabbix_server服务器连接客户端，多个ip用逗号分隔		
ServerActive=127.0.0.1	#主动模式，zabbix-server的ip地址，Hostname值与zabbix-web页面中的主机名一致
Hostname=Zabbix server	#本机的Hostname，使用主动模式必须配置
Include=/etc/zabbix/zabbix_agentd.d/*.conf	#包含的子配置文件
UnsafeUserParameters=1              	#启用特殊字符，用于自定义监控

[root@s-30 /]# systemctl enable zabbix-agent	#加入开机启动
[root@s-30 /]# systemctl start zabbix-agent	#启动服务
到此agent，的监控方式安装完成

zabbix_get检测验证客户端的配置是否正确,命令格式如下：
zabbix_get [-hV] -s <host name or IP> [-p <port>] [-I <ip address>] -k <key>
-s	远程zabbix-agent的ip地址或者主机名字
-p	远程zabbix-agent的端口
-I	本机的出口ip地址，用于一台机器中有多块网卡的情况
-k	获取远程zabbix-agent数据所使用的key
如果没有这个命令需要安装，在zabbix-server端安装
配置yum源
rpm -ivh http://repo.zabbix.com/zabbix/3.2/rhel/7/x86_64/zabbix-release-3.2-1.el7.noarch.rpm7.noarch.rpm
yum install zabbix-get.x86_64 -y

查询mysql的zabbix数据库中历史表据量的大小
select table_name, (data_length+index_length)/1024/1024 as total_mb, table_rows  from  information_schema.tables  where  table_schema='zabbix';