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