+ ### Kubernetes使用
    + [Kubernetes安装](https://github.com/Kingserch/Job-accumulation/blob/Kubernetes/k8s/k8s.md)

128-130 etcd集群
129 DNS
131根证书和harbor	
+ ### Kubernetes简介

1)Kubernetes优势:
- 自动装箱，水平扩展，自我修复  
- 服务发现和负载均衡，  
- 自动发布(默认滚动发布模式)和回滚，  
- 集中化配置管理和秘钥管理，  
- 存储编排，任务批处理运行
  
2)四组基本概念
- Pod/Pod控制器  
	- Pod是k8s里能够被运行的最小逻辑单元(原子单元)
	- 一个Pod里面可以运行多个容器，他们共享UTS+NET+IPC名称空间
	- 可以把Pod理解成豌豆菜，而同一个Pod内的每个容器是一颗颗豌豆
	- 一个Pod里运行多个容器，又叫：边车(SideCar)模式
	- Pod控制器是Pod启动的一种模板，用来保证k8s里启动的Pod应始终按照人们的预期运行(副本数，生命周期，健康状态检查)
	- k8s内提供了众多的Pod控制器，常用的有
		- Deployment
		- DaemonSet
		- ReplicaSet
		- StatefulSet(管理有状态的pod控制器)
		- Job(管理任务)
		- Cronjob(管理定时任务)
- Name/Namespace
	- Name 由于k8s内部，使用资源来定义每一种逻辑概念(功能)，故每种资源都应该有自己的名称
	- 资源有api版本，类别(kind)，元数据(metadata)，定义清单(spec),状态(status)等配置信息
	- 名称通常定义在资源的元数据信息里
	- Namespace项目增多，人员增加，集群规模的扩大，需要一种能够隔离k8s的各种资源的方法，这就是名称空间
	- 名称空间可以理解为k8s内部的虚拟机群组
	- 不同的名称空间内的资源，名称可以相同，相同名称空间的同种资源，名称不能相同
	- k8s里默认存在的名称空间有：default,kube-system ,kube-public
- Label/Label选择器
	- Label标签是k8s特色的管理方式，便于分类管理资源对象，
	- 一个标签可以对应多个资源，一个资源也可以有多个标签，他们是多对多的关系
	- Label选择器给资源打上标签后，可以使用标签选择器过滤指定的标签
	- 标签选择器目前有俩个：基于等值关系，(等于，不等于)和基于集合关系(属于，不属于，存在)
	- 许多资源支持内嵌标签选择器字段,比如:matchLabels matchExpressions
- Service/Ingress 
	- 在k8s的世界里，虽然每个Pod都会被分配一个单独的IP地址，但这个ip地址会随着Pod的销毁而销毁
	- 一个Service可以看作一组提供相同服务的Pod对外访问接口
	- Service作用于那些Pod是通过标签选择器来定义的
	- Ingress是k8s集群里工作在OSI网络参考模型下，第7层的应用，对外暴露接口
	- Service只能进行L4流量调度，表现形式是IP+port
	- Ingress则可以调度不同业务域，不同URL访问路径的业务流量
3)核心组件
	- 配置存储中心-etcd服务
	- 主控(master)节点
		- kube-apiserver服务
		- kube-controller-manager服务
		- kube-secheduler服务
	- 运算(node)节点
		- kube-kubelet服务
		- kube-proxy服务
	GLI客户端
		- kubectl
	- 核心附件
		- CNI网络插件 flannel/calico
		- 服务发现用插件 coredns
		- 服务保留用插件 traefik
		- GUI管理插件 Dashboard
