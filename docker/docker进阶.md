+ ### docker进阶
    + [基本架构](#基本架构)
    + [命名空间](#命名空间)
    + [Linux网络虚拟化](#Linux网络虚拟化)
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
镜像是使用容器的基础，docker使用镜像仓库在大规模场景下存出和分发docker镜像https://github.com/docker/distribution
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
+ ### Linux网络虚拟化
Docker的本地网路实现是利用Linux上的网络命名空间和虚拟网络设备(特别是veth pair)  
##### 1.基本原理  
实现网络通信，机器至少需要一个网络接口(物理或者虚拟)与外界通信，并可以收发数据包，此外，如果不同子网之间通信，还需要额外的路由机制。docker中的网络接口默认都是虚拟网络接口，(转发效率高)。docker容器网络利用了linxu虚拟网络技术，在本地主机和容器容器内分别创建了一个虚拟接口veth，并连同。  
##### 2.网络创建过程  
* 创建一对虚拟接口，分别放到本地主机和新容器的命名空间中
* 本地主机一端的虚拟接口连接到默认的docker0或指定的网桥上，
* 容器一端的虚拟接口将放到新创建的容器中，并修改名字eth0，这个接口只在容器的命名空间可见
* 从网桥可用地址段中获取一个空闲地址分配给容器的eth0，并配置默认路由网关为docker0网卡内部接口docker0的ip地址。
  
使用docker run命令启动容器的时候，可用通过--net参数来指定容器的网络配置   
* --net=bridge:默认值，在docker网桥docker0上为容器创建新的网络栈
* --net=none:让docker将新容器放到隔离的网络栈中，但不进行网络配置，随后，用户自定义配置
* --net=container:NAME_OR_ID:让docker将新容器进程放到已存在的网络栈中，新容器有自己的文件系统，进程列表和资源限制，但会和已存在的容器共享ip地址和端口等网络资源，俩着进程可以通过lo环回接口通信
* --net=host:告诉docker不将容器网络放在隔离的命名空间中，即不要容器化容器内的网络
* --net=user_defind_network:用户自行用network命令创建一个网络，之后将容器连接到指定的已创建的网络上去  
##### 3.手动配置网络  
```
[root@42-m ~]# docker run -it --rm --net=none centos:7 /bin/bash	#使用--net=nono后，Docker将不对容器网络进行配置
[root@42-m ~]# docker inspect -f '{{.State.Pid}}' 4b3a73b38c42	#在本地主机查找容器进程的id，并为他创建网络命名空间
2630
[root@42-m ~]# pid=2603	#为变量赋值2630
[root@42-m ~]# mkdir -p /var/run/netns	#创建一个存储容器id的目录
[root@42-m ~]# ln -s /proc/$pid/ns/net /var/run/netns/$pid	#
[root@42-m /]# ip add show docker0 |grep inet	#检查桥接网卡的ip和子网掩码信息
    inet 172.17.0.1/16 brd 172.17.255.255 scope global docker0
    inet6 fe80::42:c4ff:feaa:4bd2/64 scope link 
[root@42-m /]# ip link add A type veth peer name B	#创建一对‘veth pair’接口A和B，
[root@42-m /]# brctl addif docker0 A	#绑定A接口到docker0，
[root@42-m /]# ip link set A up	#启动创建的且绑定docker0的A接口
pid=2630
ip link set B netns $pid \
ip netns exec $pid ip link set dev B name eth0 \
ip netns exec $pid ip link set eth0 up \
ip netns exec $pid ip addr add 172.17.0.3/16 dev eth0 \ 
ip netns exec $pid ip route add default via 172.17.0.1 \
```
##### 说明
当容器终止后，docker会清空容器，容器内的网络接口会随网络命名空间一起被清除，A接口也被自动从docker0卸载并清除，此外，在删除/var/run/netns/下的内容之前，可以用ip netns exec命令在指定网络命名空间中配置，从而更新新容器内的网络配置