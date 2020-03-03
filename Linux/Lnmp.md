+ ### Lnmp环境 
    + [更换yum源](#yum源)
    + [安装nginx](#安装nginx)
    + [安装mysql](#安装mysql)
    + [安装php7](#安装php7)
+ ### yum源
#### 切换yum源
```
cp /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
yum clean all
yum makecache
yum update
```
+ ### 安装nginx

#### 1.YUM源中没有Nginx，我们需要增加一个nginx的源nginx.repo
```
vi /etc/yum.repos.d/nginx.repo
[nginx]
name=nginx repo
baseurl=http://nginx.org/packages/centos/$releasever/$basearch/
gpgcheck=0
enabled=1
```
#### 2.安装nginx
```
yum -y install nginx
nginx #启动nginx
curl 127.0.0.1
```
#### 3.开机启动设置
```
systemctl enable nginx
systemctl daemon-reload
```
+ ### 安装mysql
```
rpm -Uvh https://repo.mysql.com//mysql80-community-release-el7-2.noarch.rpm	#要是安装最新版本直接安装就可以
yum repolist all | grep mysql		#查看yum 源中的mysql版本
yum-config-manager --disable mysql80-community		#禁用mysql8.0
yum-config-manager --enable mysql57-community		#启动mysql5.7
yum repolist enabled | grep mysql		#查看配置是否生效
yum install mysql-community-server -y		#开始安装
systemctl start mysqld			#启动服务
systemctl enable mysqld			#加入开机启动
grep 'temporary password' /var/log/mysqld.log

mysql> set global validate_password_policy=0;
Query OK, 0 rows affected (0.00 sec)
mysql> set global validate_password_length=1;
Query OK, 0 rows affected (0.00 sec)
mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY 'root';
Query OK, 0 rows affected (0.00 sec)
mysql> 
grant all on *.* to root@'%' identified by 'root';	#授权可视化工具可以连接数据库
到此mysql 安装完成，
因为我们配置的yum  repository（仓库），所以以后yum 操作都会自动更新，所以我们可以移除mysql 的yum仓库
yum -y remove mysql80-community-release-el7-2.noarch
skip-grant-tables  #跳过数据库权限验证	mysql忘记密码可以在/etc/my.conf中添加这个字段，来登录修改密码
```
+ ### 安装php7
#### 1.配置yum源
```
yum -y install epel-release
rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
rpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm
```
#### 2.安装php7
```
yum install php70w.x86_64 php70w-cli.x86_64 php70w-common.x86_64 php70w-gd.x86_64 php70w-ldap.x86_64 php70w-mbstring.x86_64 php70w-mcrypt.x86_64 php70w-mysql.x86_64 php70w-pdo.x86_64
```
#### 3.安装php-fpm
```
yum install php70w-fpm php70w-opcache
```
#### 4.php配置

##### 4.1配置php.ini
```
vi /etc/php.ini
#理论上配置一下时区就够了，
date.timezone = Asia/Shanghai	#877行
#但是需要配置php连接数据库
pdo_mysql.default_socket=/var/lib/mysql/mysql.sock	#957行
mysqli.default_socket =/var/lib/mysql/mysql.sock		#1097行
post_max_size=16M					#656行
max_execution_time=300				#368行，0表示没有限制
max_input_time=300					#378行
```
##### 4.2配置php-fpm
```
vi /etc/php-fpm.d/www.conf
;默认情况下是apache
user= apache	#8行
group=apache	#10行
; 修改为配置php所属用户为nginx
user = nginx
group = nginx
```
#### 5.启动php
```
systemctl start php-fpm	#启动php-fpm
systemctl enable php-fpm	#开机启动设置
systemctl daemon-reload
```
#### 6.测试
```
#在/nginx配置的站点目录编辑vim index.php 
<?php
    phpinfo();
?>

#测试连接数据库
vim mysql_test.php
<?php
$link = mysqli_connect('localhost', 'root', 'root');
if (!$link) {
die('Could not connect: ' . mysqli_error());
}
echo 'mysql数据库连接成功';
mysqli_close($link);
?>
```
