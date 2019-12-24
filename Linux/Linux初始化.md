+ #### Linux最小化安装
***
```
[root@like /]# sed -i 's#ONBOOT=no#ONBOOT=yes#g' /etc/sysconfig/network-scripts/ifcfg-ens33 	#查看网卡是否开启
[root@like /]# grep ONBOOT /etc/sysconfig/network-scripts/ifcfg-ens33							#查看修改是否成功
ONBOOT="yes"
```