+ ### docker进阶
    + [基本架构](#基本架构)
    + [命名空间](#命名空间)
    + [Jenkins和GitLab](#Jenkins和GitLab)
	+ [数据库](#数据库)
+ ### 基本架构
1.服务端主要包括四个组件
* dockerd:为客户端提供PESTful API，响应来自客服端的请求，采用模块化的架构，通过专门的Engine模块来分发管理各个来自客户端的任务
* docker-proxy:是dockerd的子进程，docker-proxy是实现容器端口映射的
* containerd:是dockerd的子进程，提供gRPC接口响应来自dockerd的请求，对下管理runC镜像和容器环境
* containerd-shim:是contaiinerd的子进程，为runC容器提供支持，同时作为容器的根进程
```
# dockerd默认监听本地的unix:///var/run/docker.sock套接字，只允许本地的root用户或docker用户组成员来访问，可以用-H来修改监听的方式
[root@42-m run]# systemctl stop docker
[root@42-m run]# dockerd
INFO[2020-01-03T09:39:28.225681930+08:00] Starting up                                  
INFO[2020-01-03T09:39:28.247200234+08:00] parsed scheme: "unix"                         module=grpc
INFO[2020-01-03T09:39:28.247263463+08:00] scheme "unix" not registered, fallback to default scheme  module=grpc
...
```
2.客户端  
`客户端默认通过本地的unix:///var/run/docker套接字向服务端发送命令，如果服务没监听默认的地址，则需要用docker -H tcp://127.0.0.1:1234 info`  
3.镜像仓库
镜像是使用容器的基础，docker使用镜像仓库在大规模场景下存出和分发docker镜像  
![](https://github.com/docker/distribution)  
+ ### 命名空间
```
[root@42-m ~]# docker network ls	#查看当前系统中的网桥，docker采用虚拟网络设备(VND)方式，将不同的空间网络设备连接到一起
NETWORK ID          NAME                DRIVER              SCOPE
3f9aab951a12        bridge              bridge              local
b700de0add7c        host                host                local
47646d8b88cf        none                null                local
[root@42-m ~]#
```
```
[root@42-m ~]# yum install bridge-utils -y #安装brctl工具包，
#每个容器默认分配一个网桥上的虚拟网口，并将docker0的ip地址设置为默认的网关，容器发起的网络流量通过宿主机的iptables规则进行转发。
[root@42-m ~]# brctl show	#可以看到连接到网桥上的虚拟网口的信息
bridge name	bridge id		STP enabled	interfaces
docker0		8000.0242bd1affc3	no		
[root@42-m ~]#
```