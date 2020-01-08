##  docker-ce安装   
####   一,查看服务器上的docker版本
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
yum install -y yum-utils device-mapper-persistent-data lvm2
```
####  四,添加Docker稳定版本的yum软件源
```
yum-config-manager  --add-repo https://download.docker.com/linux/centos/docker-ce.repo
``` 
####  五,更新yum软件源缓存,并安装docker
```
yum makecache fast
yum install docker-ce -y
```  
####  六,为docker添加一个json
[json文件地址](https://github.com/Kingserch/Job-accumulation/blob/Docker/json/daemon.json)   
```
curl -sSL https://get.daocloud.io/daotools/set_mirror.sh | sh -s http://f1361db2.m.daocloud.io	#该脚本可以将 --registry-mirror 加入到你的 Docker 配置文件 /etc/docker/daemon.json 中。
[root@42-m /]# systemctl start docker
[root@42-m /]# systemctl enable docker
Created symlink from /etc/systemd/system/multi-user.target.wants/docker.service to /usr/lib/systemd/system/docker.service.
[root@42-m /]# cd /etc/docker/
[root@42-m docker]# ls
key.json
[root@42-m docker]# rz -E
rz waiting to receive.
[root@42-m docker]# ls
daemon.json  key.json
[root@42-m docker]#
```

`[root@42-m docker]# systemctl restart docker`



