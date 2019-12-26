+ ### 访问docker仓库(Repository)
    + [仓库(Repository)简介](#简介)
	+ [搭建本地私有仓库registry](#搭建私有仓库)
	+ [查看容器输出logs](#查看容器输出)
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
#1.先下载一个容器绑定端口886
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
[root@42-m ~]# docker run -d -p 886:886 -v /home/docker_registry/:/var/lib/registry registry:2	#-v参数指定仓库为宿主机本地目录
5ed3fbb7124db504f709c9b06359f14f8a5714144e1aaaf792d00ca75a4a90d9
#2.管理私有仓库
[root@42-m ~]# docker ps -a 	#查看刚才创建的俩个registry容器
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                        PORTS               NAMES
5ed3fbb7124d        registry:2          "/entrypoint.sh /etc…"   17 minutes ago      Exited (2) 16 minutes ago                         elated_yalow
c4bbbd6d32ed        registry:2          "/entrypoint.sh /etc…"   20 minutes ago      Exited (2) 6 seconds ago                          inspiring_lehmann
384733417a81        centos:latest       "/bin/bash"              7 hours ago         Exited (127) 28 minutes ago                       exciting_mcnulty
2221fc238dee        centos:7            "echo 'I am running'"    7 hours ago         Exited (0) 7 hours ago                            awesome_buck
[root@42-m ~]# docker start 5e	#启动
5e
[root@42-m ~]# docker port 5e	#查看容器绑定的端口是886
886/tcp -> 0.0.0.0:886
[root@42-m ~]#
```
