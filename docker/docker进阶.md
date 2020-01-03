+ ### docker进阶
    + [基本架构](#基本架构)
    + [web服务apache,nginx,tomcat,lamp](#web服务)
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