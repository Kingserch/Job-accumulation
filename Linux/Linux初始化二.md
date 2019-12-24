+ #### linux web环境的搭建
***
#### 1.cmake工具的安装
```
tar xvf cmake-3.8.1.tar.gz
cd cmake-3.8.1/
./configure 
make & make install
```
#### 2.安装JDK
```
mkdir /usr/java			#创建存放jdk目录
cp /home/tools/jdk-8u231-linux-x64.tar.gz /usr/java/
cd /usr/java 
tar xvf jdk-8u231-linux-x64.tar.gz		#解压jdk
mv jdk1.8.0_231/ jdk		#改名字
vim /etc/profile		#编辑环境变量文件，添加下面的内容
export JAVA_HOME=/usr/java/jdk
export PATH=$PATH:$JAVA_HOME/bin
export CLASSPATH=.:$JAVA_HOME/lib
export JAVA_HOME PATH CLASSPATH
source /etc/profile		#使环境变量配置文件生效
java -version		#检查java安装是否成功
```
#### 3.安装TOMCAT
```
[root@42-m tools]# mkdir /usr/tomcat   
[root@42-m tools]# cp /home/tools/apache-tomcat-8.5.47.tar.gz /usr/tomcat/
[root@42-m tools]# cd /usr/tomcat/
[root@42-m tools]# tar xvf apache-tomcat-8.5.47.tar.gz 
[root@42-m tomcat]# mv apache-tomcat-8.5.47/ apache
[root@42-m tools]# vim /etc/profile
export JAVA_HOME=/usr/java/jdk
export CATALINA_HOME=/usr/tomcat/apache
export PATH=$PATH:$JAVA_HOME/bin:$CATALINA_HOME/bin		#每增加一个变量就:跟用户配置的路径就可以了
export CLASSPATH=.:$JAVA_HOME/lib:$CATALINA_HOME/lib
export JAVA_HOME PATH CLASSPATH CATALINA_HOME
[root@42-m tomcat]# cd /usr/tomcat/apache/bin/
[root@42-m bin]# ./startup.sh
[root@42-m bin]# ./shutdown.sh 
```
在浏览器上访问ip+8080端口，可以看到以下页面，证明tomcat安装成功
![](https://github.com/Kingserch/Job-accumulation/blob/Linux/images/tomcat.png)
***
#### 4.安装MYSQL
```
[root@42-m bin]# rpm -Uvh https://repo.mysql.com//mysql80-community-release-el7-2.noarch.rpm	#要是安装最新版本 直接安装就可以了
Retrieving https://repo.mysql.com//mysql80-community-release-el7-2.noarch.rpm
warning: /var/tmp/rpm-tmp.6pUPIg: Header V3 DSA/SHA1 Signature, key ID 5072e1f5: NOKEY
Preparing...                          ################################# [100%]
Updating / installing...
   1:mysql80-community-release-el7-2  ################################# [100%]
[root@42-m bin]# yum repolist all | grep mysql		#查看yum 源中的mysql版本
mysql-cluster-7.5-community/x86_64 MySQL Cluster 7.5 Community   disabled
mysql-cluster-7.5-community-source MySQL Cluster 7.5 Community - disabled
mysql-cluster-7.6-community/x86_64 MySQL Cluster 7.6 Community   disabled
mysql-cluster-7.6-community-source MySQL Cluster 7.6 Community - disabled
mysql-connectors-community/x86_64  MySQL Connectors Community    enabled:    131
mysql-connectors-community-source  MySQL Connectors Community -  disabled
mysql-tools-community/x86_64       MySQL Tools Community         enabled:    100
mysql-tools-community-source       MySQL Tools Community - Sourc disabled
mysql-tools-preview/x86_64         MySQL Tools Preview           disabled
mysql-tools-preview-source         MySQL Tools Preview - Source  disabled
mysql55-community/x86_64           MySQL 5.5 Community Server    disabled
mysql55-community-source           MySQL 5.5 Community Server -  disabled
mysql56-community/x86_64           MySQL 5.6 Community Server    disabled
mysql56-community-source           MySQL 5.6 Community Server -  disabled
mysql57-community/x86_64           MySQL 5.7 Community Server    disabled
mysql57-community-source           MySQL 5.7 Community Server -  disabled
mysql80-community/x86_64           MySQL 8.0 Community Server    enabled:    145
mysql80-community-source           MySQL 8.0 Community Server -  disabled
[root@42-m bin]# yum-config-manager --disable mysql80-community		#禁用mysql8.0
[root@42-m bin]# yum-config-manager --enable mysql57-community		#启动mysql5.7
[root@42-m bin]# yum repolist enabled | grep mysql		#查看配置是否生效
mysql-connectors-community/x86_64 MySQL Connectors Community                 131
mysql-tools-community/x86_64      MySQL Tools Community                      100
mysql57-community/x86_64          MySQL 5.7 Community Server                 384
[root@42-m bin]# yum install mysql-community-server -y		#开始安装mysql
[root@42-m bin]# systemctl start mysqld		#启动mysql
[root@42-m bin]# systemctl enable mysqld	#加入开机启动		
[root@42-m bin]# grep 'temporary password' /var/log/mysqld.log		#查看mysql的密码****
2019-12-24T08:49:30.350256Z 1 [Note] A temporary password is generated for root@localhost: ************
[root@42-m bin]# mysql -uroot -p
Enter password: ************
mysql> set global validate_password_policy=0;
Query OK, 0 rows affected (0.00 sec)

mysql> set global validate_password_length=1;
Query OK, 0 rows affected (0.00 sec)

mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY 'root';
Query OK, 0 rows affected (0.00 sec)

mysql> grant all on *.* to root@'%' identified by 'root';	#授权可视化工具连接，%代表任意ip都可以连接，这里我用%代替了生产环境ip
Query OK, 0 rows affected, 1 warning (0.01 sec)

mysql> exit
```
#### 到此mysql 安装完成，
`因为我们配置的yum  repository（仓库），所以以后yum 操作都会自动更新，所以我们可以移除mysql 的yum仓库`  
`[root@42-m bin]# yum -y remove mysql80-community-release-el7-2.noarch`  
`skip-grant-tables  #跳过数据库权限验证	mysql忘记密码可以在/etc/my.conf中添加这个字段，来登录修改密码`  


