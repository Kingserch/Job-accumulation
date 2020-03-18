+ ### ansible环境 
    + [ansible任务执行](#ansible任务执行)
    + [安装nginx](#安装nginx)
    + [安装mysql](#安装mysql)
    + [安装php7](#安装php7)


+ ###  ansible任务执行
```
Ansible 系统由控制主机对被管节点的操作方式可分为两类，即adhoc和playbook：
使用单个模块，支持批量执行单条命令。ad-hoc 命令是一种可以快速输入的命令，而且不需要保存起来的命令。就相当于bash中的一句话shell。
playbook模式(剧本模式)是Ansible主要管理方式，也是Ansible功能强大的关键所在。playbook通过多个task集合完成一类功能，如Web服务的安装部署、数据库服务器的批量备份等。
可以简单地把playbook理解为通过组合多条ad-hoc操作的配置文件。
```
![ansible执行流程](https://github.com/Kingserch/Job-accumulation/blob/Linux/images/ansible.png)