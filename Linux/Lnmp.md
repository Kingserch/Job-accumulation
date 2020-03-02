+ ### Lnmp环境 
    + [更换yum源](#yum源)
    + [安装nginx](#安装nginx)
    + [批量管理镜像](#批量管理镜像)
    + [使用通知系统](#使用通知系统)
+ ### yum源
#### 切换yum源
```
cp /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
yum clean all
yum makecache
yum update
```
#### 安装nginx

##### 1.YUM源中没有Nginx，我们需要增加一个nginx的源nginx.repo
```
vi /etc/yum.repos.d/nginx.repo
[nginx]
name=nginx repo
baseurl=http://nginx.org/packages/centos/$releasever/$basearch/
gpgcheck=0
enabled=1
```
