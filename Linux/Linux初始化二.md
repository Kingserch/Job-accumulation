+ #### linux web环境的搭建
***
cmake工具的安装
```
tar xvf cmake-3.8.1.tar.gz
cd cmake-3.8.1/
./configure 
make & make install
```
#### 1.更新yum源
```
[root@like /]# curl -s -o/etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
[root@like /]# curl -s -o /etc/yum.repos.d/epel.repo http://mirrors.aliyun.com/repo/epel-7.repo
```
#### 2.安装工作中需要的工具


