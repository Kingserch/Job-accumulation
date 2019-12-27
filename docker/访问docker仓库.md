+ ### 访问docker仓库(Repository)
    + [仓库(Repository)简介](#简介)
	+ [搭建本地私有仓库registry](#搭建私有仓库)
	+ [docker数据管理](#docker数据管理)
	+ [停止容器pause/unpause,stop,prune](#停止容器)
	+ [进入容器attach,exec](#进入容器)
	+ [删除容器rm](#删除容器)
	+ [导入import和导出export容器](#导入和导出容器)
	+ [镜像save,load&容器import,export区别](#镜像save,load&容器import,export区别)
	+ [查看容器inspect,top,stats](#查看容器)
	+ [其他容器命令cp,diff,port,update(#其他容器命令)
+ ### 简介
仓库是集中存放镜像的地址，分为公有仓库和私有仓库。注册服务器是存放仓库的具体服务器，一个注册服务器有多个仓库，而每个仓库下面有多个镜像。
<a href="https://hub.docker.com/" target="_blank">https://hub.docker.com/</a>可以通过这个网站来下载镜像。
```
[root@42-m ~]# docker login -u838915764 -p*******		#登录docker仓库
WARNING! Using --password via the CLI is insecure. Use --password-stdin.
WARNING! Your password will be stored unencrypted in /root/.docker/config.json.#你的密码将以未加密的方式存在config.json中
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded
[root@42-m ~]# cd /root/.docker		#查看密码
[root@42-m .docker]# cat config.json 
{
	"auths": {
		"https://index.docker.io/v1/": {
			"auth": "ODM4OTE1NzY0OnFheDEyMzEyMw=="
		}
	},
	"HttpHeaders": {
		"User-Agent": "Docker-Client/19.03.5 (linux)"
	}
}[root@42-m .docker]# docker logout		#退出登录
Removing login credentials for https://index.docker.io/v1/
[root@42-m .docker]#
```
<a href="https://hub.tenxcloud.com" target="_blank">https://hub.tenxcloud.com</a>时速云镜像市场
+ ### 搭建私有仓库
```
1.先下载一个容器绑定端口5000

[root@42-m ~]# docker run -d -p 888:888 registry:2	#下载启动registry容器，仓库会被创建在容器的/var/lib/registry下
Unable to find image 'registry:2' locally
2: Pulling from library/registry
c87736221ed0: Pull complete 
1cc8e0bb44df: Pull complete 
54d33bcb37f5: Pull complete 
e8afc091c171: Pull complete 
b4541f6d3db6: Pull complete 
Digest: sha256:8004747f1e8cd820a148fb7499d71a76d45ff66bac6a29129bfdbfdc0154d146
Status: Downloaded newer image for registry:2
c4bbbd6d32eda420a59ff9ad0a67a8813db1db0cb2e3bedc87aea143de53bf48
[root@42-m ~]# docker run -d -p 5000:5000 -v /home/docker_registry/:/var/lib/registry registry:2	#-v参数指定仓库为宿主机本地目录
Unable to find image 'registry:2' locally
2: Pulling from library/registry
c87736221ed0: Pull complete 
1cc8e0bb44df: Pull complete 
54d33bcb37f5: Pull complete 
e8afc091c171: Pull complete 
b4541f6d3db6: Pull complete 
Digest: sha256:8004747f1e8cd820a148fb7499d71a76d45ff66bac6a29129bfdbfdc0154d146
Status: Downloaded newer image for registry:2
e49440199476b307ef1d9d17d85d35923e6cc1ae884f127f1b378e9d52279183
[root@42-m ~]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
centos              7                   5e35e350aded        6 weeks ago         203MB
centos              latest              0f3e07c0138f        2 months ago        220MB
registry            2                   e49440199476        9 months ago        25.8MB
[root@42-m ~]# docker tag registry:2 192.168.31.142:5000/test registry:2 
2.管理私有仓库

[root@42-m ~]# docker ps -a 	#查看刚才绑定5000端口容器
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                           PORTS                    NAMES
e49440199476        registry:2          "/entrypoint.sh /etc…"   33 minutes ago      Up 12 minutes                    0.0.0.0:5000->5000/tcp   romantic_rhodes
384733417a81        centos:latest       "/bin/bash"              8 hours ago         Exited (127) About an hour ago                            exciting_mcnulty
2221fc238dee        centos:7            "echo 'I am running'"    8 hours ago         Exited (0) 8 hours ago                                    awesome_buck
3.排错

[root@42-m ~]# docker push 192.168.31.142:5000/test		#push报错说客户端不支持https
The push refers to repository [192.168.31.142:5000/test]
Get https://192.168.31.142:5000/v2/: http: server gave HTTP response to HTTPS client
[root@42-m docker]# vim daemon.json 	#在/etc/docker/daemon.json文件中添加"insecure-registries":["192.168.31.142:5000"]，注意json的格式
{
        "registry-mirrors": ["https://njrds9qc.mirror.aliyuncs.com"],
        "insecure-registries":["192.168.31.142:5000"]
}
[root@42-m ~]# docker push 192.168.31.142:5000/test		#说tcp连接不通，解决办法看下面
The push refers to repository [192.168.31.142:5000/test]
Get http://192.168.31.142:5000/v2/: dial tcp 192.168.31.142:5000: connect: connection refused
[root@42-m ~]# docker login -u838915764 -p*****	#需要登录的tcp就通了
WARNING! Using --password via the CLI is insecure. Use --password-stdin.
WARNING! Your password will be stored unencrypted in /root/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store
Login Succeeded
[root@42-m ~]# docker tag registry:2 192.168.31.142:5000/test
[root@42-m ~]# docker push 192.168.31.142:5000/test
The push refers to repository [192.168.31.142:5000/test]
73d61bf022fd: Pushed 
5bbc5831d696: Pushed 
d5974ddb5a45: Pushed 
f641ef7a37ad: Pushed 
d9ff549177a9: Pushed 
latest: digest: sha256:b1165286043f2745f45ea637873d61939bff6d9a59f76539d6228abf79f87774 size: 1363
[root@42-m ~]# curl 192.168.31.142:5000/v2/search	#提示404，哎心塞
404 page not found
[root@42-m ~]# cd /home/docker_registry/	#我们来本地仓库看看是否有镜像吧
[root@42-m docker_registry]# ls
docker
[root@42-m docker_registry]# cd docker/registry/v2
[root@42-m v2]# ls
blobs  repositories
[root@42-m v2]# cd repositories/
[root@42-m repositories]# ls
test							#可以看到test镜像的
[root@42-m repositories]#
```
+ ### docker数据管理
数据卷是一个可供容器使用的特殊目录，将主机操作系统目录直接映射到容器，类似linux的mount行为。
1.创建数据卷volume
```
[root@42-m volumes]# docker volume create -d local test
test
[root@42-m volumes]# cd /var/lib/docker/volumes/
[root@42-m volumes]# ls
bf537e065d3654aaa0f8920109ff7f9e1b66d41afd71e32786c686dae18d37ba  metadata.db
e4d530ac649caad516e6de840dc0f209aadbb5bdf36932ffbf59b8c7d2a6b16a  test
[root@42-m volumes]#
```