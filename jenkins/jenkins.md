+ ### Jenkins
    + [jenkins安装](#jenkins安装)
	+ [gitlab](#gitlab安装)
	+ [坑位](#坑位)
	+ [坑位](#坑位)
+ ### Jenkins安装
```
sudo wget -O /etc/yum.repos.d/jenkins.repo http://pkg.jenkins-ci.org/redhat-stable/jenkins.repo
sudo rpm --import https://jenkins-ci.org/redhat/jenkins-ci.org.key
上传jenkins的rpm包安装就可以了

mkdir /home/jenkins
chown jenkins:jenkins  /home/jenkins
ln -s  /usr/java/jdk/bin/java     /usr/bin/java   

vim   /home/jenkins/hudson.model.UpdateCenter.xml
<?xml version='1.1' encoding='UTF-8'?>
<sites>
  <site>
    <id>default</id>
    <url>http://mirror.xmission.com/jenkins/updates/update-center.json</url>
  </site>

[root@jenkins jenkins]# egrep "^[a-Z]"  /etc/sysconfig/jenkins
JENKINS_HOME="/home/jenkins"	#jenkins工作目录
JENKINS_JAVA_CMD=""
JENKINS_USER="jenkins"
JENKINS_JAVA_OPTIONS="-Djava.awt.headless=true"
JENKINS_PORT="8080"		#端口可修改其他的
JENKINS_LISTEN_ADDRESS=""
JENKINS_HTTPS_PORT=""
JENKINS_HTTPS_KEYSTORE=""
JENKINS_HTTPS_KEYSTORE_PASSWORD=""
JENKINS_HTTPS_LISTEN_ADDRESS=""
JENKINS_HTTP2_PORT=""
JENKINS_HTTP2_LISTEN_ADDRESS=""
JENKINS_DEBUG_LEVEL="5"
JENKINS_ENABLE_ACCESS_LOG="no"
JENKINS_HANDLER_MAX="100"
JENKINS_HANDLER_IDLE="20"
JENKINS_EXTRA_LIB_FOLDER=""
JENKINS_ARGS=""
```
+ ### gitlab安装
https://about.gitlab.com/install/#centos-7
```
#安装并配置必要的依赖项
sudo yum install -y curl policycoreutils-python openssh-server
sudo systemctl enable sshd
sudo systemctl start sshd
sudo firewall-cmd --permanent --add-service=http
sudo firewall-cmd --permanent --add-service=https
sudo systemctl reload firewalld
#安装Postfix以发送通知电子邮件
sudo yum install postfix
sudo systemctl enable postfix
sudo systemctl start postfix
#添加GitLab软件包存储库并安装软件包
curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ee/script.rpm.sh | sudo bash
sudo EXTERNAL_URL="https://gitlab.example.com" yum install -y gitlab-ee
#修改配置文件，设置服务器IP和端口。
vim  /etc/gitlab/gitlab.rb                  //编辑配置文件
external_url 'https://gitlab.example.com'   //配置服务器ip和端口
#重置并启动GitLab
gitlab-ctl reconfigure                      //重置GitLab
gitlab-ctl restart                          //重启GitLab





```

