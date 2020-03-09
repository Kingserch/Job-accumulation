+ ### Zabbix监控mysql
    + [Zabbix监控mysql](#zabbix监控mysql)
    + [坑](#坑)
### zabbix监控mysql

#### 1)配置被监控端agent
```
[root@m39 scripts]# egrep -v "^#|^$" /etc/zabbix/zabbix_agentd.conf 
PidFile=/var/run/zabbix/zabbix_agentd.pid
LogFile=/var/log/zabbix/zabbix_agentd.log
LogFileSize=0
Server=119.110.1.3	#服务端IP
ListenIP=0.0.0.0
ServerActive=119.110.1.3	##服务端IP
Hostname=mysql	#配置的主机
UserParameter=mysql_status[*],/scripts/mysql_status.sh "$1"		#自定义需添加键值及脚本路径
Timeout=10
Include=/etc/zabbix/zabbix_agentd.d/*.conf
UnsafeUserParameters=1 	#1开启自定义监控，默认0不开启
[root@m39 scripts]# systemctl restart zabbix-agent
```
![mysql_status.sh](https://github.com/Kingserch/Job-accumulation/blob/zabbix/sh/mysql_status.sh)
#### 2)编写脚本

#### 3)配置被监控端my.cnf
```
[root@m39 scripts]# egrep -v "^#|^$" /etc/my.cnf
[mysqld]
datadir=/var/lib/mysql
socket=/var/lib/mysql/mysql.sock
symbolic-links=0
log-error=/var/log/mysqld.log
pid-file=/var/run/mysqld/mysqld.pid
[client]	#需要添加的字段
host=localhost
user=status
password='zabbix'
```