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
	
- Name/Namespace
- Label/Label选择器
- Service/Ingress 