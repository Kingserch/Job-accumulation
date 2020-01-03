+ ### 容器提供服务，数据(都比较简略)
    + [服务端介绍](#服务端介绍)
    + [web服务apache,nginx,tomcat,lamp](#web服务)
    + [Jenkins和GitLab](#Jenkins和GitLab)
	+ [数据库](#数据库)
+ ### 服务端介绍
服务端主要包括四个组件
* dockerd:为客户端提供PESTful API，响应来自客服端的请求，采用模块化的架构，通过专门的Engine模块来分发管理各个来自客户端的任务
* docker-proxy:是dockerd的子进程，docker-proxy是实现容器端口映射的
* containerd:是dockerd的子进程，提供gRPC接口响应来自dockerd的请求，对下管理runC镜像和容器环境
* containerd-shim:是contaiinerd的子进程，为runC容器提供支持，同时作为容器的根进程