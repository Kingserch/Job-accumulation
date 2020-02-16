+ ### docker三剑客
    + [Machine](#安装和使用Machine)
    + [Compose](#安装和使用Compose)
    + [配置容器DNS和主机名](#配置容器DNS和主机名)
    + [容器访问控制](#容器访问控制)
	+ [配置容器网桥](#配置容器网桥)
	+ [libnetwork插件化网络功能](#libnetwork插件化网络功能)
+ ### 安装和使用Machine
##### Linux平台安装
```
base=https://github.com/docker/machine/releases/download/v0.16.0 &&
curl -L $base/docker-machine-$(uname -s)-$(uname -m) >/tmp/docker-machine &&
sudo mv /tmp/docker-machine /usr/local/bin/docker-machine &&
chmod +x /usr/local/bin/docker-machine
#更多请参考官方文档https://docs.docker.com/machine/install-machine/
```
##### 使用Machine
Mac的Docker桌面 -您可以docker-machine create与virtualbox驱动程序一起使用来创建其他本地计算机。  
Windows的Docker桌面 -您可以docker-machine create与hyperv驱动程序一起使用来创建其他本地计算机。  
##### Machine命令
docker-machine <COMMAND> -h  可以查看Machine命令帮助  
+ ### 安装和使用Machine  
任务(task):一个容器被称为一个任务，任务拥有独一无二的ID，在同一个服务中的多个任务序号依次递增  
服务(service):某个相同应用镜像的容器副本集合，一个服务可以横向扩展为多个容器实例  
服务栈(stack):由多个服务器组成，相互配合完成特定业务，如web应用服务，数据库服务共同构成web服务栈，一般由一个docker-compose.yml文件定义
##### Linux平台安装
```
#1.二进制安装
sudo curl -L https://github.com/docker/compose/releases/download/1.17.1/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
#卸载
sudo rm /usr/local/bin/docker-compose
#2.pip安装
curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py   # 下载安装脚本
sudo python get-pip.py    # 运行安装脚本
#如果是centos直接用yum安装pip
yum -y install epel-release
yum -y install python-pip
pip install --upgrade pip
pip install docker-compose
```
##### Compose常用命令
```
docker-compose up -d  # 后台启动
docker-compose down    # 销毁
docker-compose restart # 重启
```
