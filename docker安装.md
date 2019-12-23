##  docker-ce安装   
####  一,查看服务器上的docker版本
```
yum search docker 
```   
####  二,移除操作
```
yum remove docker
```  
####  三,为了方便添加软件源，支持devicemapper存储类型,安装如下软件包
```
yum update -y
yum makecache fast
yum install -y yum-utils device-mapper-persistent-data lvm2
```   


cat /etc/redhat-release 
yum install -y yum-utils device-mapper-persistent-data lvm2
yum-config-manager  --add-repo https://download.docker.com/linux/centos/docker-ce.repo
yum makecache fast
yum update -y
yum install docker-ce
