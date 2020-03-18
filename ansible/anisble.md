+ ### ansible环境 
    + [ansible任务执行](#ansible任务执行)
    + [安装](#安装)
    + [安装mysql](#安装mysql)
    + [安装php7](#安装php7)


+ ###  ansible任务执行
```
Ansible 系统由控制主机对被管节点的操作方式可分为两类，即adhoc和playbook：
使用单个模块，支持批量执行单条命令。ad-hoc 命令是一种可以快速输入的命令，而且不需要保存起来的命令。就相当于bash中的一句话shell。
playbook模式(剧本模式)是Ansible主要管理方式，也是Ansible功能强大的关键所在。playbook通过多个task集合完成一类功能，如Web服务的安
装部署、数据库服务器的批量备份等。可以简单地把playbook理解为通过组合多条ad-hoc操作的配置文件。
```
![ansible执行流程](https://github.com/Kingserch/Job-accumulation/blob/Linux/images/ansible.png)
+ ###  ansible安装
```
yum install epel-release -y
yum install ansible –y
#ansible 的配置文件为/etc/ansible/ansible.cfg，ansible 有许多参数，下面我们列出一些常见的参数：
inventory = /etc/ansible/hosts      #这个参数表示资源清单inventory文件的位置
library = /usr/share/ansible        #指向存放Ansible模块的目录，支持多个目录方式，只要用冒号（：）隔开就可以
forks = 5       #并发连接数，默认为5
sudo_user = root        #设置默认执行命令的用户
remote_port = 22        #指定连接被管节点的管理端口，默认为22端口，建议修改，能够更加安全
host_key_checking = False       #设置是否检查SSH主机的密钥，值为True/False。关闭后第一次连接不会提示配置实例
timeout = 60        #设置SSH连接的超时时间，单位为秒
log_path = /var/log/ansible.log     #指定一个存储ansible日志的文件（默认不记录日志）
```