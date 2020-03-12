+ ### elk
    + [环境](#环境)
    + [安装](#安装)
    + [elasticsearch配置](#elasticsearch配置)
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
[elasticsearch安装官方文档](https://www.elastic.co/guide/en/elasticsearch/reference/7.6/rpm.html#install-rpm)

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
#安装elasticsearch需要jdk环境，请自行安装
yum install -y elasticsearch
```
### elasticsearch配置
```
vim /etc/elasticsearch/elasticsearch.yml
#找到配置文件中的cluster.name，打开该配置并设置集群名称
cluster.name: cluster
#找到配置文件中的node.name，打开该配置并设置节点名称
node.name: node-1
#修改data存放的路径
path.data: /tmp/es-data
#修改logs日志的路径
path.logs: /var/log/elasticsearch/
#配置内存使用用交换分区
bootstrap.memory_lock: true
#监听的网络地址,[119.110.1.30,119.110.1.39]
network.host: 0.0.0.0
#开启监听的端口
http.port: 9200
#增加新的参数，这样head插件可以访问es
http.cors.enabled: true
http.cors.allow-origin: "*"
#启动elasticsearch服务
systemc	start elasticsearch
systemc	enable elasticsearch
```
注意每台主机的配置文件集群名字一样，但是节点不一样
vim /etc/security/limits.conf




