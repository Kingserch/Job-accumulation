+ ### Lnmp环境 
    + [更换yum源](#yum源)
    + [配置registry](#配置文件管理私有仓库)
    + [批量管理镜像](#批量管理镜像)
    + [使用通知系统](#使用通知系统)
+ ### yum源
##### 切换yum源
```
cp /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
yum clean all
yum makecache
yum update
```