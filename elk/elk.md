+ ### elk
    + [环境](#环境)
    + [安装](#安装)
    + [Devops](https://github.com/Kingserch/Job-accumulation/tree/Devops)
	+ [Jenkins](https://github.com/Kingserch/Job-accumulation/tree/Jenkins)
	+ [Zabbix](https://github.com/Kingserch/Job-accumulation/tree/zabbix)	
	
### 环境
```
[root@m39 ]# cat /etc/redhat-release 
CentOS Linux release 7.7.1908 (Core)
IP: 119.110.1.39	安装： elasticsearch、logstash、Kibana、Nginx、Http、Redis
	119.110.1.30	安装:  logstash
```
### 安装
```
#安装elasticsearch的yum源的密钥（这个需要在所有服务器上都配置）
rpm --import https://artifacts.elastic.co/GPG-KEY-elasticsearch
#配置elasticsearch的yum源
vim /etc/yum.repos.d/elasticsearch.repo
[elasticsearch-7.x]
name=Elasticsearch repository for 7.x packages
baseurl=https://artifacts.elastic.co/packages/5.x/yum
gpgcheck=1
gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch
enabled=1
autorefresh=1
type=rpm-md
#把yum传送到30服务器
[root@m39 yum.repos.d]# scp /etc/yum.repos.d/elasticsearch.repo root@119.110.1.30:/etc/yum.repos.d/elasticsearch.repo
```





