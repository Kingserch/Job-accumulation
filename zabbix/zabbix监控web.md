+ ### zabbix监控web
    + [TCP监控](#TCP监控)
    + [nginx监控](#nginx监控)
### TCP监控
```
#被监控端配置
vim /etc/zabbix/zabbix_agentd.d/tcp_status.conf
UserParameter=tcp_state[*],netstat -ant|grep -c $1
systemctl restart zabbix-agent
#服务端测试
[root@m3 /]# zabbix_get -s 119.110.1.39 -k tcp_state[LISTEN]
9
[root@m3 /]# zabbix_get -s 119.110.1.39 -k tcp_state[ESTABLISHED]
3
[root@m3 /]# zabbix_get -s 119.110.1.39 -k tcp_state[TIME_WAIT]
26
```
[TCP监控模板](https://github.com/Kingserch/Job-accumulation/blob/zabbix/Template/TCP%E8%BF%9E%E6%8E%A5%E7%8A%B6%E6%80%81_templates.xml)
### nginx监控
```
[root@m39]# vim /etc/nginx/conf.d/default.conf
...
    location /status{			#在server中配置一个location
        stub_status on;
    }
...
#测试
[root@m39]# curl -I  127.0.0.1/status
HTTP/1.1 200 OK		#返回状态码200
Server: nginx/1.16.1
Date: Wed, 11 Mar 2020 06:42:09 GMT
Content-Type: text/plain
Connection: keep-alive
[root@m39]# vim /etc/zabbix/scripts/ngx_status.sh	#编辑监控nginx脚本，
```
[监控nginx脚本](https://github.com/Kingserch/Job-accumulation/blob/zabbix/sh/ngx_status.sh)
```
#配置zabbix参数
[root@m39 ]# vim /etc/zabbix/zabbix_agentd.d/ngx_statux.conf
UserParameter=nginx_status[*],/bin/bash /etc/zabbix/scripts/ngx_status.sh $1
#服务端测试
[root@m3]# zabbix_get -s 119.110.1.39  -k nginx_status[accepts]
8
```