+ ### Job accumulation 工作积累
[Elk日志分析系统](https://github.com/Kingserch/Job-accumulation/blob/master/elk/elk.md)
    + [Linux](https://github.com/Kingserch/Job-accumulation/tree/Linux)
    + [Docker](https://github.com/Kingserch/Job-accumulation/tree/Docker)
    + [Devops](https://github.com/Kingserch/Job-accumulation/tree/Devops)
	+ [Jenkins](https://github.com/Kingserch/Job-accumulation/tree/Jenkins)
	+ [Zabbix](https://github.com/Kingserch/Job-accumulation/tree/zabbix)
	
	
[elk集成镜像](https://hub.docker.com/r/sebp/elk/tags)	
##### docker快速安装elk
```
docker pull sebp/elk:760
echo "vm.max_map_count=262144" > /etc/sysctl.conf
sysctl -p
docker run -dit --name elk \
    -p 5601:5601 \
    -p 9200:9200 \
    -p 5044:5044 \
    -v /home/elk-data:/var/lib/elasticsearch \
    -v /etc/localtime:/etc/localtime \
	 sebp/elk:760
```
master
