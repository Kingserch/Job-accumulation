+ ### Job accumulation 工作积累
    + [Zabbix工作环境](https://github.com/Kingserch/Job-accumulation/blob/Linux/Linux/Lnmp.md)
	+ [Zabbix安装]()







### 什么是zabbix

	Zabbix 是由Alexei Vladishev创建，目前由Zabbix SIA在持续开发和支持，Zabbix 是一个企业级的分布式开源监控方案。  
Zabbix是一款能够监控各种网络参数以及服务器健康性和完整性的软件。Zabbix使用灵活的通知机制，允许用户为几乎任何事件配置基于邮件的告警。这样可以快速反馈服务器的问题。基于已存储的数据，Zabbix提供了出色的报告和数据可视化功能。这些功能使得Zabbix成为容量规划的理想方案，Zabbix支持主动轮询和被动捕获。Zabbix所有的报告、统计信息和配置参数都可以通过基于Web的前端页面进行访问。基于Web的前端页面可以确保从任何方面评估网络状态和服务器的健康性。适当的配置后，Zabbix可以在IT基础架构监控方面扮演重要的角色。对于只有少量服务器的小型组织和拥有大量服务器的大型公司也同样如此。

 

### zabbix功能和特性

Zabbix是一个高度集成的网络监控解决方案，一个简单的安装包中提供多样性的功能。

数据收集

可用性和性能检查

支持SNMP（包括主动轮训和被动获取），IPMI，JMX，VMware监控

自定义检查

按照自定义的间隔收集需要的数据

通过server/proxy+agents来执行

灵活的阀值定义

可以非常灵活的定义问题阈值,称之为触发器,触发器从后端数据库获取参考值

高度可配置化的告警

可根据递增机制，接收方和媒介类型自定义发送告警通知

使用宏变量可以使告警通知更加高效有用

自动相应动作可包含远程命令

实时图表绘制

使用内置图表绘制功能可以将监控项的内容实时绘制成图表

Web监控功能

Zabbix可以追踪模拟鼠标在Web网站上的点击操作，来检查Web的功能和响应时间

丰富的可视化选项

支持创建自定义的图表，一个试图集中展现多个监控项

网络拓扑图

以仪表盘的样式自定义大屏展现和幻灯片轮询播放

报表

监控内容的高级（业务）视图

历史数据存储

数据库数据

可配置历史数据

内置数据管理机制（housekeeping）

配置简单

将被监控对象添加为主机

在数据库中获取主机进行监视

应用模板来监控设备

使用模板

在模板中分组检查

模板可以关联其他模板

网络发现

自动发现网络设备

监控代理自动注册

发现文件系统，网络接口和SNMP OID值

快捷的Web界面

PHP Web前端

可从任何地方访问

可以定制自己的操作方式

审核日志

Zabbix API

Zabbix API为Zabbix 提供了对外的可编程接口，用于批量操作，第三方软件集成和其他目的

权限管理系统

安全用户认证

特定用户可以限制访问特定的视图

功能强大，易于扩展的agent

部署在被监控对象上

支持Linux和Windows

二进制代码

为了性能和更少内存的占用，用C语言编写

便于移植

为复杂环境准备

使用Zabbix proxy代理服务器，使得远程监控更简单

 

3、zabbix概述

结构

Zabbix由几个主要的软件组件构成，这些组件的功能如下。

服务器

Zabbix服务器是代理程序报告系统可用性，系统完整性和统计数据的核心组件，是所有配置信息，统计信息和操作数据的核心存储器。

数据库存储

所有配置信息和的zabbix收集到的数据都被存储在数据库中。

网络界面

为了从任何地方和任何平台都可以轻松的访问Zabbix，我们提供基于Web的Zabbix界面。该界面是Zabbix Server的一部分，通常（但不一定）跟Zabbix Server运行在同一台物理机器上。

代理代理服务器

Zabbix proxy可以替Zabbix Server收集性能和可用性数据.Proxy代理服务器是Zabbix软件可选择部署的一部分;当然，Proxy代理服务器可以帮助单台Zabbix Server分担负载压力。

代理监控代理

Zabbix代理监控代理部署在监控目标上，能够主动监控本地资源和应用程序，并将收集到的数据报告给Zabbix服务器。

数据流

此外，了解的zabbix内部的数据流同样很重要。监控方面，为了创建一个监控项（项）用于采集数据，必须先创建一个主机（主机）。告警方面，在监控项里创建触发器（扳机） ，因此，如果你想收到Server X CPU负载过高的告警，你必须：1.为Server X创建一个主机并关联一个用于对CPU进行监控的监控项（Item）。2.创建一个Trigger，设置成当CPU负载过高时会触发3.触发被触发，发送告警邮件虽然看起来有很多步骤，但是使用模板的话操作起来其实很简单， ZABBIX这样的设计使得配置机制非常灵活易用。

 

4、zabbix架构



Zabbix Server：负责接收Agent发送的报告信息，组织所有配置、数据和操作。

Database Storage：存储配置信息以及收集到的数据。

Web Interface：Zabbix的GUI 接口，通常与Server运行在同一台机器上。

Proxy：可选组件，常用于分布式监控环境中。

Agent：部署在被监控主机上，负责收集数据发送给Server。

 

5、zabbix的工作流程

Agent获取被监控端数据，发送给Server。

Server记录所接收到的数据，存储在Database中并按照策略进行相应操作。

如果是分布式，Server会将数据传送一份到上级Server中。

Web Interface将收集到的数据和操作信息显示给用户。

 

6、zabbix的进程

默认情况下zabbix包含5个程序：zabbix_agentd、zabbix_get、zabbix_proxy、zabbix_sender、zabbix_server，另外一个zabbix_java_gateway是可选，这个需要另外安装。下面来分别介绍下他们各自的作用。

zabbix_agentd客户端守护进程，此进程收集客户端数据，例如cpu负载、内存、硬盘使用情况等。

zabbix_getzabbix工具，单独使用的命令，通常在server或者proxy端执行获取远程客户端信息的命令。通常用户排错。例如在server端获取不到客户端的内存数据，我们可以使用zabbix_get获取客户端的内容的方式来做故障排查。

zabbix_senderzabbix工具，用于发送数据给server或者proxy，通常用于耗时比较长的检查。很多检查非常耗时间，导致zabbix超时。于是我们在脚本执行完毕之后，使用sender主动提交数据。

zabbix_serverzabbix服务端守护进程。zabbix_agentd、zabbix_get、zabbix_sender、zabbix_proxy、zabbix_java_gateway的数据最终都是提交到server

备注：当然不是数据都是主动提交给zabbix_server,也有的是server主动去取数据。

zabbix_proxyzabbix代理守护进程。功能类似server，唯一不同的是它只是一个中转站，它需要把收集到的数据提交/被提交到server里。为什么要用代理？代理是做什么的？卖个关子，请继续关注运维生存时间zabbix教程系列。

zabbix_java_gatewayzabbix2.0之后引入的一个功能。顾名思义：Java网关，类似agentd，但是只用于Java方面。需要特别注意的是，它只能主动去获取数据，而不能被动获取数据。它的数据最终会给到server或者proxy。

 

7、zabbix的逻辑关系图



 

8、zabbix监控环境中相关术语

主机（host）：要监控的网络设备，可由IP或DNS名称指定；

主机组（host group）：主机的逻辑容器，可以包含主机和模板，但同一个组织内的主机和模板不能互相链接；主机组通常在给用户或用户组指派监控权限时使用；

监控项（item）：一个特定监控指标的相关的数据；这些数据来自于被监控对象；item是zabbix进行数据收集的核心，相对某个监控对象，每个item都由"key"标识；

触发器（trigger）：一个表达式，用于评估某监控对象的特定item内接收到的数据是否在合理范围内，也就是阈值；接收的数据量大于阈值时，触发器状态将从"OK"转变为"Problem"，当数据再次恢复到合理范围，又转变为"OK"；

事件（event）：触发一个值得关注的事情，比如触发器状态转变，新的agent或重新上线的agent的自动注册等；

动作（action）：指对于特定事件事先定义的处理方法，如发送通知，何时执行操作；

报警升级（escalation）：发送警报或者执行远程命令的自定义方案，如每隔5分钟发送一次警报，共发送5次等；

媒介（media）：发送通知的手段或者通道，如Email、Jabber或者SMS等；

通知（notification）：通过选定的媒介向用户发送的有关某事件的信息

远程命令（remote command）：预定义的命令，可在被监控主机处于某特定条件下时自动执行

模板（template）：用于快速定义被监控主机的预设条目集合，通常包含了item、trigger、graph、screen、application以及low-level discovery rule；模板可以直接链接至某个主机

应用（application）：一组item的集合

web场景（web scennario）：用于检测web站点可用性的一个活多个HTTP请求
	
zabbix
