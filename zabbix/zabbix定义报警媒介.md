+ ### zabbix定义报警媒介
    + [邮件报警](#邮件报警)
    + [微信报警](#微信报警)
### 邮件报警

#### 定义发件人
![](https://github.com/Kingserch/Job-accumulation/blob/zabbix/images/zabbix-qq.png)
![](https://github.com/Kingserch/Job-accumulation/blob/zabbix/images/zabbix-q.png)
#### 定义收件人
![](https://github.com/Kingserch/Job-accumulation/blob/zabbix/images/zabbix-s.png)
#### 添加报警触发器
![](https://github.com/Kingserch/Job-accumulation/blob/zabbix/images/zabbix-b.png)
```
动作是条件，操作跟恢复操作更新操作都需要配置
发送警告标题：故障{TRIGGER.STATUS},服务器:{HOSTNAME1}发生: {TRIGGER.NAME}故障!
发送警告消息内容：
告警主机:{HOSTNAME1}
告警时间:{EVENT.DATE} {EVENT.TIME}
告警等级:{TRIGGER.SEVERITY}
告警信息: {TRIGGER.NAME}
告警项目:{TRIGGER.KEY1}
问题详情:{ITEM.NAME}:{ITEM.VALUE}
当前状态:{TRIGGER.STATUS}:{ITEM.VALUE1}
事件ID:{EVENT.ID}　

恢复标题：恢复{TRIGGER.STATUS}, 服务器:{HOSTNAME1}: {TRIGGER.NAME}已恢复!
恢复信息：
告警主机:{HOSTNAME1}
告警时间:{EVENT.DATE} {EVENT.TIME}
告警等级:{TRIGGER.SEVERITY}
告警信息: {TRIGGER.NAME}
告警项目:{TRIGGER.KEY1}
问题详情:{ITEM.NAME}:{ITEM.VALUE}
当前状态:{TRIGGER.STATUS}:{ITEM.VALUE1}
事件ID:{EVENT.ID}
```