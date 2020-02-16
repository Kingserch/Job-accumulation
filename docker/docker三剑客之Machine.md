+ ### docker三剑客之Machine
    + [安装和使用Machine](#安装和使用Machine)
    + [高级网络功能](#高级网络功能)
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