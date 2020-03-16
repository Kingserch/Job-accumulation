+ ### zabbix定义报警媒介
    + [邮件报警](#邮件报警)
	+ [钉钉报警](#钉钉报警)
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
服务器:{HOST.NAME}发生: {TRIGGER.NAME}故障!

{
告警主机:{HOST.NAME}
告警地址:{HOST.IP}
监控项目:{ITEM.NAME}
监控取值:{ITEM.LASTVALUE}
告警等级:{TRIGGER.SEVERITY}
当前状态:{TRIGGER.STATUS}
告警信息:{TRIGGER.NAME}
告警时间:{EVENT.DATE} {EVENT.TIME}
事件ID:{EVENT.ID}
}

服务器:{HOST.NAME}: {TRIGGER.NAME}已恢复!

{
告警主机:{HOST.NAME}
告警地址:{HOST.IP}
监控项目:{ITEM.NAME}
监控取值:{ITEM.LASTVALUE}
告警等级:{TRIGGER.SEVERITY}
当前状态:{TRIGGER.STATUS}
告警信息:{TRIGGER.NAME}
告警时间:{EVENT.DATE} {EVENT.TIME}
恢复时间:{EVENT.RECOVERY.DATE} {EVENT.RECOVERY.TIME}
持续时间:{EVENT.AGE}
事件ID:{EVENT.ID}
}

服务器:{HOST.NAME}: 报警确认

{
确认人:{USER.FULLNAME} 
时间:{ACK.DATE} {ACK.TIME} 
确认信息如下:
"{ACK.MESSAGE}"
问题服务器IP:{HOSTNAME1}
问题ID:{EVENT.ID}
当前的问题是: {TRIGGER.NAME}
}
```
### 钉钉报警
```
#!/usr/bin/python
import requests
import json
import sys
import os
 
headers = {'Content-Type': 'application/json;charset=utf-8'}
api_url = "https://oapi.dingtalk.com/robot/send?access_token=d9e42ab476193699dd987f6"
 
def msg(text):
    json_text= {
     "msgtype": "text",
        "at": {
            "atMobiles": [
                "15638926930"
            ],
            "isAtAll": False
        },
        "text": {
            "content": text
        }
    }
    print requests.post(api_url,json.dumps(json_text),headers=headers).content
     
if __name__ == '__main__':
    text = sys.argv[1]
    msg(text)

```