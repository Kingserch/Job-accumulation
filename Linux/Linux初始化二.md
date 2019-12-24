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
![](images/tomcat.png)
