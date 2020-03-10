+ ### Zabbix安装
    + [Zabbix-Server服务端安装](#Zabbix-Server服务端安装)
    + [Zabbix-Agent客户端安装](#Zabbix-Agent客户端安装)
	+ [zabbix_get使用](#zabbix_get使用)
	+ [zabbix数据库表分区](#zabbix数据库表分区)
	+ [Zabbix数据库备份](#Zabbix数据库备份)
	+ [查询mysql的zabbix数据库中历史表据量的大小](#查询mysql的zabbix数据库中历史表据量的大小)	
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

IP+zabbix访问，web页面如下：
![zabbix页面](https://github.com/Kingserch/Job-accumulation/blob/zabbix/images/zabbix.png)
下面看到中文乱码了，
![zabbix-font](https://github.com/Kingserch/Job-accumulation/blob/zabbix/images/zabbix-font.png)
解决中文乱码问题
```
yum install wqy-microhei-fonts -y
cp /usr/share/fonts/wqy-microhei/wqy-microhei.ttc /usr/share/zabbix/assets/fonts/graphfont.ttf
```
#### 6)创建告警和扩展脚本目录(../install)，这里会单独列出一个文件来描述
`mkdir -p /etc/zabbix/alertsscripts  /etc/zabbix/externalscripts`  

#### 7)如果关闭防火墙不需要配置下面，开启则需要(我维护的生产环境就没开selinux)

##### 7.1)防火墙设置
```
#CentOS 7操作系统防火墙规则设置
firewall-cmd --permanent --add-port=80/tcp
firewall-cmd --permanent --add-port=10050/tcp
firewall-cmd --permanent --add-port=10051/tcp
firewall-cmd –reload
上述规则中，10050是Agent的端口，Agent采用被动方式，Server主动连接Agent的10050端口；10051是Server的端口，Agent采用主动或Trapper方式，会连接Server的10051端口。对于具有防火墙的网络环境，采取相同的防火墙规则策略即可。
```
##### 7.2)SELinux的设置
```
如果操作系统已开启SELinux，则需要设置语句开启允许SELinux相关策略。
setsebool -P httpd_can_connect_zabbix on
setsebool -P httpd_can_network_connect_db on
关闭selinux
setenforce 0 	#0代表permissive，1代表enforcing；也可直接用permissive和enforcing，不用重启服务器即生效
sed -i 's/SELINUX=enforcing/SELINUX=disabled/' /etc/selinux/config   #需要重启系统，永久关闭
getenforce 	#获取当前SELinux的运行状态
```
#### 8)zabbix相关配置

##### 8.1)Zabbix-Web连接数据库和Zabbix-Server端口的相关配置信息如下：
```
[root@m3 /]# cat /etc/zabbix/web/zabbix.conf.php
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

$ZBX_SERVER      = '119.110.1.3';
$ZBX_SERVER_PORT = '10051';
$ZBX_SERVER_NAME = '';

$IMAGE_FORMAT_DEFAULT = IMAGE_FORMAT_PNG;
[root@m3 /]# 
```
##### 8.2) /etc/zabbix/zabbix_server.conf 中的参数
```
[root@m3 /]#  egrep -v "^#|^$" /etc/zabbix/zabbix_server.conf
LogFile=/var/log/zabbix/zabbix_server.log
LogFileSize=0
PidFile=/var/run/zabbix/zabbix_server.pid
SocketDir=/var/run/zabbix
DBHost=localhost
DBName=zabbix
DBUser=zabbix
DBPassword=zabbix
SNMPTrapperFile=/var/log/snmptrap/snmptrap.log
Timeout=4
AlertScriptsPath=/usr/lib/zabbix/alertscripts	#/etc/zabbix/alertsscripts
ExternalScripts=/usr/lib/zabbix/externalscripts	#/etc/zabbix/externalscripts
LogSlowQueries=3000
AllowRoot=1
[root@m3 /]#
```
### Zabbix-Agent客户端安装
#### 1)客服端的采集方式为Agent，snmp等
```
rpm -ivh http://repo.zabbix.com/zabbix/4.0/rhel/7/x86_64/zabbix-release-4.0-1.el7.noarch.rpm #安装Zabbix官方的yum源
yum install -y  zabbix-agent  
# 由于Zabbix-Server服务器本身也需要监控，所以在Zabbix-Server服务器中也同样需要安装Zabbix-Agent
```
#### 2)防火墙配置
```
#CentOS 7
firewall-cmd --permanent --add-port=10050/tcp
firewall-cmd --reload
```
#### 3)配置zabbix_agentd.conf
```
[root@m3 /]# egrep -v "^#|^$" /etc/zabbix/zabbix_agentd.conf 
PidFile=/var/run/zabbix/zabbix_agentd.pid	#pid文件路径
LogFile=/var/log/zabbix/zabbix_agentd.log	#日志文件路径
LogFileSize=0				#日志切割大小，0表示不切割		
Server=127.0.0.1		#被动模式，zabbix-server的IP地址，允许zabbix_server服务器连接客户端，多个ip用逗号分隔		
ServerActive=127.0.0.1	#主动模式，zabbix-server的ip地址，Hostname值与zabbix-web页面中的主机名一致
Hostname=Zabbix server	#本机的Hostname，使用主动模式必须配置
Include=/etc/zabbix/zabbix_agentd.d/*.conf	#包含的子配置文件
UnsafeUserParameters=1              	#启用特殊字符，用于自定义监控
```
#### 4)启动
```
systemctl enable zabbix-agent	#加入开机启动
systemctl start zabbix-agent	#启动服务
到此agent，的监控方式安装完成
```
### zabbix_get使用
```
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
```
### zabbix数据库表分区
```
#web页面关闭Housekeeper
#Administrattion（管理）——>General(一般)——>Housekeeper(管家)，去掉History和Trends选项的勾选状态，可关闭History，Trends，Housekeeper功能
因为是生产环境，history trends表中数据较大，所以需要先清空在执行脚本
清空语句如下：
mysql> use zabbix; 
mysql> truncate table history; 
mysql> optimize table history; 
mysql> truncate table history_str; 
mysql> optimize table history_str; 
mysql> truncate table history_uint; 
mysql> optimize table history_uint; 
mysql> truncate table history_log; 
mysql> optimize table history_log; 
mysql> truncate table history_text; 
mysql> optimize table history_text; 
mysql> truncate table trends; 
mysql> optimize table trends; 
mysql> truncate table trends_uint; 
mysql> optimize table trends_uint; 
#运行表分区脚本，为防止网络中断后引起脚本运行中断导致数据库故障，我们用screen后台执行的方式
yum install screen -y 
screen  -R  zabbix
#授权脚本执行权限，执行脚本完成后，提示mysql: [Warning] Using a password on the command line interface can be insecure 暂时忽略
crontab -e 	#加入开机启动
1 0 * * *	 sh /scripts/partitiontables_zabbix.sh  #分时日月周 跟脚本路径
#验证表分区是否成功，可以查看history表结构
mysql>  show create table history\G
*************************** 1. row ***************************
       Table: history
Create Table: CREATE TABLE `history` (
  `itemid` bigint(20) unsigned NOT NULL,
  `clock` int(11) NOT NULL DEFAULT '0',
  `value` double(16,4) NOT NULL DEFAULT '0.0000',
  `ns` int(11) NOT NULL DEFAULT '0',
  KEY `history_1` (`itemid`,`clock`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin
/*!50100 PARTITION BY RANGE ( clock)
(PARTITION p20200310 VALUES LESS THAN (1583855999) ENGINE = InnoDB,
 PARTITION p20200311 VALUES LESS THAN (1583942399) ENGINE = InnoDB,
 PARTITION p20200312 VALUES LESS THAN (1584028799) ENGINE = InnoDB,
 PARTITION p20200313 VALUES LESS THAN (1584115199) ENGINE = InnoDB,
 PARTITION p20200314 VALUES LESS THAN (1584201599) ENGINE = InnoDB,
 PARTITION p20200315 VALUES LESS THAN (1584287999) ENGINE = InnoDB,
 PARTITION p20200316 VALUES LESS THAN (1584374399) ENGINE = InnoDB,
 PARTITION p20200317 VALUES LESS THAN (1584460799) ENGINE = InnoDB) */
1 row in set (0.00 sec)
mysql>
date -d @1583855999 "+%Y-%m-%d"		#1583855999 更改时间戳
date -d "2020-03-10" +%s
```
[mysql表分区脚本](https://github.com/Kingserch/Job-accumulation/blob/zabbix/sh/partitiontables_zabbix.sh)
### Zabbix数据库备份
备份采用单表备份，对监控的历史展示数据不做备份（history* trends* Acknowledges ALerts Auditlog Events service_alarms）
```
chmod 700 /scripts//scripts/zabbix-mysql/mysql_back.sh	
sh /scripts//scripts/zabbix-mysql/mysql_back.sh mysqldump	#备份数据
crontab -e 
0 3 * * *  /scripts//scripts/zabbix-mysql/mysql_back.sh mysqldump	#加入定时任务
sh /scripts//scripts/zabbix-mysql/mysql_back.sh mysqlimport		#恢复数据
```
[mysql备份脚本](https://github.com/Kingserch/Job-accumulation/blob/zabbix/sh/mysql_back.sh)
### 查询mysql的zabbix数据库中历史表据量的大小
```
select table_name, (data_length+index_length)/1024/1024 as total_mb, table_rows  from  information_schema.tables  where  table_schema='zabbix';
```