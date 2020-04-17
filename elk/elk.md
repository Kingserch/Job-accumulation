+ ### elk
    + [环境](#环境)
    + [安装](#安装)
	+ [安装elasticsearch-head插件](#安装elasticsearch-head插件)
	+ [安装kibana](#安装kibana)	
	+ [kibana的使用](#kibana的使用)	
### 环境
```
公司要求搭建日志系统，分析tomcat日志和业务日志，感觉主流还是选择elk来进行日志分析，
#服务器
1.ALY ECS 2C8G
2.JDK版本1.8
3.elk版本
	elasticsearch 7.5.0
	kibana 7.5.0
	logstash 7.5.0
4.官网安装包比较慢，从AWS找了个rpm包
wget https://rgc-solution-server-validation.s3.cn-north-1.amazonaws.com.cn/andrewxu-test/elasticsearch-7.5.0-x86_64.rpm
wget https://rgc-solution-server-validation.s3.cn-north-1.amazonaws.com.cn/andrewxu-test/kibana-7.5.0-x86_64.rpm
wget https://rgc-solution-server-validation.s3.cn-north-1.amazonaws.com.cn/andrewxu-test/logstash-7.5.0.rpm
```
### 安装
[elasticsearch安装官方文档](https://www.elastic.co/guide/en/elasticsearch/reference/7.6/rpm.html#install-rpm)
```
#1.安装elasticsearch
rpm -ivh elasticsearch-7.5.0-x86_64.rpm

#2.修改elasticsearch配置文件
mkdir -p /data/es-data
chown -R elasticsearch:elasticsearch /data/es-data
chown -R elasticsearch:elasticsearch /var/log/elasticsearch/

[root@elk-server ~]cat /etc/elasticsearch/elasticsearch.yml  |grep -v "^#"
#找到配置文件中的cluster.name，打开该配置并设置集群名称
cluster.name:  my-es
#找到配置文件中的node.name，打开该配置并设置节点名称
node.name: node-1
#修改data存放的路径,数据和日志目录所属用户、组都要是elasticsearch
path.data: /data/es-data
#修改logs日志的路径  chown -R elasticsearch:elasticsearch /var/log/elasticsearch/
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
#单机
[root@elk-server ~] egrep "^[a-Z]" /etc/elasticsearch/elasticsearch.yml 
node.name: node-1
path.data: /data/es-data
path.logs: /var/log/elasticsearch
network.host: 0.0.0.0
http.port: 9200
cluster.initial_master_nodes: ["node-1"]
cluster.routing.allocation.disk.threshold_enabled: false
http.cors.enabled: true                 #配置可以使用elasticsearch-head插件
http.cors.allow-origin: "*"
#3.检测是否启动
[root@elk-server ~]netstat -pantl |grep  9200
[root@elk-server ~]curl 10.0.20.74:9200
{
  "name" : "node-1",
  "cluster_name" : "my-es",
  "cluster_uuid" : "FhHOQO2MQbWRX0MiTRFF6g",
  "version" : {
    "number" : "7.5.0",
    "build_flavor" : "default",
    "build_type" : "rpm",
    "build_hash" : "e9ccaed468e2fac2275a3761849cbee64b39519f",
    "build_date" : "2020-3-03T01:06:52.518245Z",
    "build_snapshot" : false,
    "lucene_version" : "8.3.0",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "You Know, for Search"
}
```
### 安装elasticsearch-head插件
```
yum install -y npm
git clone git://github.com/mobz/elasticsearch-head.git
cd elasticsearch-head
npm install
npm run start
```
### 安装kibana
```
#1.安装 kibana
rpm -ivh kibana-7.5.0-x86_64.rpm
#2.修改配置文件[root@elk-server ~]cat /etc/kibana/kibana.yml |grep  -v "^#"
server.port: 5601 
server.host: "0.0.0.0" #改成本机IP
server.name: "node-1"
elasticsearch.hosts: "http://10.0.20.74:9200"  #改成elasticsearch的IP
logging.dest: /var/log/kibana/kibana.log  
kibana.index: ".kibana"
3.启动
systemctl start kibana
systemctl enable kibana　
```
web页面查看，还没有索引
![](https://github.com/Kingserch/Job-accumulation/blob/master/images/kibana.png)
### LogStash的使用
[LogStashg官方安装手册](https://www.elastic.co/guide/en/logstash/current/installing-logstash.html)  
[LogStashg官方包下载地址](https://www.elastic.co/cn/downloads/logstash)
```
rpm --import https://artifacts.elastic.co/GPG-KEY-elasticsearch
yum install -y logstash
#查看下logstash的安装目录
rpm -ql logstash
#创建一个软连接（默认安装在/usr/share下）
ln -s /usr/share/logstash/bin/logstash /bin/
```
[LogStashg官方使用配置文件](https://www.elastic.co/guide/en/logstash/current/configuration.html)  
[Input使用文档](https://www.elastic.co/guide/en/logstash/current/input-plugins.html)  
[Output使用文档](https://www.elastic.co/guide/en/logstash/current/output-plugins.html)
```
[root@elk-server ~] vim /etc/logstash/conf.d/elk.conf
input {
    file {
        path => "/var/log/messages"
        type => "system-log"
        start_position => "beginning"
    }

    file {
        path => "/var/log/secure"
        type => "secure"
        start_position => "beginning"
    }
}

output {

    if [type] == "system" {

        elasticsearch {
            hosts => ["10.0.20.74:9200"]
            index => "system-log-%{+YYYY.MM.dd}"
        }
    }

    if [type] == "secure" {

        elasticsearch {
            hosts => ["10.0.20.74:9200"]
            index => "system-log-%{+YYYY.MM.dd}"
        }
    }
}
#启动放置在后台
/usr/share/logstash/bin/logstash -f /etc/logstash/conf.d/file.conf &
```
把日志添加至kiban展示
![](https://github.com/Kingserch/Job-accumulation/blob/master/images/web-k1.png)
![](https://github.com/Kingserch/Job-accumulation/blob/master/images/web-k2.png)
![](https://github.com/Kingserch/Job-accumulation/blob/master/images/web-k3.png)


