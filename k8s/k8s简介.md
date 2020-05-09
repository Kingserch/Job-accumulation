+ ### Kubernetes
1)Kubernetes优势:
- 自动装箱，水平扩展，自我修复  
- 服务发现和负载均衡，  
- 自动发布(默认滚动发布模式)和回滚，  
- 集中化配置管理和秘钥管理，  
- 存储编排，任务批处理运行
  
2）四组基本概念
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
- Service/Ingress 