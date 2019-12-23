###  docker-ce安装
####  移除安装的docker
一，yum remove docker 
cat /etc/redhat-release 
yum install -y yum-utils device-mapper-persistent-data lvm2
yum-config-manager  --add-repo https://download.docker.com/linux/centos/docker-ce.repo
yum makecache fast
yum update -y
yum install docker-ce
