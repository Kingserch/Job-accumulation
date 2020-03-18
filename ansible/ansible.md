+ ### ansible使用
    + [ansible基本使用](#ansible基本使用)
    + [ssh ](#ssh)
![ansible参考 ](https://www.cnblogs.com/keerya/p/7987886.html#_label0_0)
+ ### ansible 基本使用

#### ansible配置文件
```
#ansible 的配置文件为/etc/ansible/ansible.cfg
inventory = /etc/ansible/hosts      #这个参数表示资源清单inventory文件的位置
library = /usr/share/ansible        #指向存放Ansible模块的目录，支持多个目录方式，只要用冒号（：）隔开就可以
forks = 5       #并发连接数，默认为5
sudo_user = root        #设置默认执行命令的用户
remote_port = 22        #指定连接被管节点的管理端口，默认为22端口，建议修改，能够更加安全
host_key_checking = False       #设置是否检查SSH主机的密钥，值为True/False。关闭后第一次连接不会提示配置实例
timeout = 60        #设置SSH连接的超时时间，单位为秒
log_path = /var/log/ansible.log     #指定一个存储ansible日志的文件（默认不记录日志）
```
#### ansible常用命令
```
/usr/bin/ansible　　Ansibe AD-Hoc 临时命令执行工具，常用于临时命令的执行
/usr/bin/ansible-doc 　　Ansible 模块功能查看工具
/usr/bin/ansible-galaxy　　下载/上传优秀代码或Roles模块 的官网平台，基于网络的
/usr/bin/ansible-playbook　　Ansible 定制自动化的任务集编排工具
/usr/bin/ansible-pull　　Ansible远程执行命令的工具，拉取配置而非推送配置（使用较少，海量机器时使用，对运维的架构能力要求较高）
/usr/bin/ansible-vault　　Ansible 文件加密工具
/usr/bin/ansible-console　　Ansible基于Linux Consoble界面可与用户交互的命令执行工具
```
+ ### ssh
```
[root@m129 ansible]# ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/root/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /root/.ssh/id_rsa.
Your public key has been saved in /root/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:THQgYwrQ2CnDajREccTPLRe/4E5uo6WpDOzZtnpkL4A root@m129
The key's randomart image is:
+---[RSA 2048]----+
|=O=+  + o..      |
|++=o o = .       |
|oo. + . +        |
|..   + * .       |
|o     + S .      |
|E. o   o .       |
| o+ . +.         |
|. =+ .+=         |
| ++=++o .        |
+----[SHA256]-----+
```