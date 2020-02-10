+ ### Etcd高可用的键值数据库
    + [安装和使用Etcd](#安装和使用Etcd)
    + [高级网络功能](#高级网络功能)
    + [配置容器DNS和主机名](#配置容器DNS和主机名)
    + [容器访问控制](#容器访问控制)
	+ [配置容器网桥](#配置容器网桥)
	+ [libnetwork插件化网络功能](#libnetwork插件化网络功能)
+ ### 安装和使用Etcd
##### 1.二进制安装
```
[root@m129 ~]# curl  -L  https://github.com/coreos/etcd/releases/download/v3.3.1/etcd-v3.3.1-linux-amd64.tar.gz | tar xzvf
[root@m129 ~]# cd etcd-v3.3.1-linux-amd64
[root@m129 ~]# ls        
Documentation   etcd   etcdctl   README-etcdctl.md   README.md   READMEv2-etcdctl.md
# 其中etcd是服务主文件，etcdctl是提供给用户的命令客户端，其他都是文档文件。
# 通过下面的命令将所需要的二进制文件都放到系统可执行路径/usr/local/bin/下：
[root@m129 ~]# sudo cp etcd＊ /usr/local/bin/
Etcd安装到此完成。
```
##### 2.Docker镜像方式下载
