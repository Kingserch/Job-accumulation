+ ### 访问docker仓库(Repository)
    + [仓库(Repository)简介](#简介)
	+ [搭建本地私有仓库registry](#搭建私有仓库)
	+ [docker数据管理，数据卷](#docker数据管理)
	+ [数据卷操作](#数据卷操作)
	+ [端口映射与容器互联](#端口映射与容器互联)
	+ [删除容器rm](#删除容器)
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
[root@42-m volumes]# docker volume create -d local test #快速在本地创建一个数据卷
test
[root@42-m volumes]# cd /var/lib/docker/volumes/	#数据卷的路径
[root@42-m volumes]# ls
bf537e065d3654aaa0f8920109ff7f9e1b66d41afd71e32786c686dae18d37ba  metadata.db
e4d530ac649caad516e6de840dc0f209aadbb5bdf36932ffbf59b8c7d2a6b16a  test
[root@42-m volumes]#
```
2.绑定数据卷
```
[root@42-m volumes]# docker run -d -P --name web -v /home/webapp:/opt/webapp training/webapp python app.py	#把本地/home/webapp与容器/opt/webapp绑定
Unable to find image 'training/webapp:latest' locally
latest: Pulling from training/webapp
Image docker.io/training/webapp:latest uses outdated schema1 manifest format. Please upgrade to a schema2 image for better future compatibility. More information at https://docs.docker.com/registry/spec/deprecated-schema-v1/
e190868d63f8: Pull complete 
909cd34c6fd7: Pull complete 
0b9bfabab7c1: Pull complete 
a3ed95caeb02: Pull complete 
10bbbc0fc0ff: Pull complete 
fca59b508e9f: Pull complete 
e7ae2541b15b: Pull complete 
9dd97ef58ce9: Pull complete 
a4c1b0cb7af7: Pull complete 
Digest: sha256:06e9c1983bd6d5db5fba376ccd63bfa529e8d02f23d5079b8f74a616308fb11d
Status: Downloaded newer image for training/webapp:latest
0e94778c22169dc3fd24d3179c6a4efc194770ed0d0172fac76a72244f603d9b
[root@42-m volumes]#
```
3.共享数据卷容器
```
[root@42-m volumes]# docker run -it -v /dbdata --name dbdate centos	#在centos镜像中创建一个dbdate的数据卷容器，并且挂载到/dbdata
[root@45a288af5224 /]# ls
bin	dev  home  lib64       media  opt   root  sbin	sys  usr
dbdata	etc  lib   lost+found  mnt    proc  run   srv	tmp  var
[root@45a288af5224 /]# docker run -it --volumes-from dbdata --name db1 centos	#创建一个db1容器挂载到/dadata
bash: docker: command not found
[root@45a288af5224 /]# ls
bin	dev  home  lib64       media  opt   root  sbin	sys  usr
dbdata	etc  lib   lost+found  mnt    proc  run   srv	tmp  var
[root@45a288af5224 /]# cd dbdata/	#在dbdate容器中
[root@45a288af5224 dbdata]# ls
[root@45a288af5224 dbdata]# touch test
[root@45a288af5224 dbdata]# ls
test
[root@cf3aa9b8752a /]# cd dbdata/	#在db1容器中
[root@cf3aa9b8752a dbdata]# ls
test
```
注意：如果删除了dbdate和db1挂载的容器，数据卷dbdate不会被自动删除，如果要删除一个数据卷，必须删除最后一个还挂载着它的容器时显示使用docker rm -v 命令
+ ### 数据卷操作
1.备份
```
[root@42-m /]# docker run --volumes-from dbdata -v $(pwd):/backup --name dbdata-back centos tar cvf /backup/backup.tar /dbdata	#centos镜像中必须有dbdata这个容器，备份dbdata数据卷在centos镜像的dbdata-back容器中
/dbdata/
tar: Removing leading `/' from member names
[root@42-m /]# docker ps -a 	#
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                      PORTS               NAMES
fe17c0acb1f5        centos              "tar cvf /backup/bac…"   18 seconds ago      Exited (0) 17 seconds ago                       dbdata-back
27782adac215        centos              "/bin/bash"              38 seconds ago      Up 37 seconds                                   dbdata
cf3aa9b8752a        centos              "/bin/bash"              28 minutes ago      Exited (0) 10 minutes ago                       db1
0e94778c2216        training/webapp     "python app.py"          37 minutes ago      Exited (2) 37 minutes ago                       web
9ad83714533c        registry:2          "/entrypoint.sh /etc…"   2 hours ago         Exited (2) 48 minutes ago                       jovial_chebyshev
384733417a81        centos:latest       "/bin/bash"              25 hours ago        Exited (127) 19 hours ago                       exciting_mcnulty
2221fc238dee        centos:7            "echo 'I am running'"    26 hours ago        Exited (0) 26 hours ago                         awesome_buck
[root@42-m /]# ls
backup.tar
```
`数据卷备份命令解析：利用centos镜像创建了一个dbdata-backup容器，使用--volumes-from dbdata参数让dbdata-backup容器挂载dbdata数据卷;使用-v $(pwd):/backup参数来挂载到本地，用tar命令打包，最终是在宿主机的当前目录下backup.tar`  
2.恢复
```
docker run -v /dbdata --name dbdata2 centos 	#创建一个带有数据卷的新容器
docker run --volumes-from dbdata2 -v $(pwd):/backup untar xvf /backup/backup.tar	#在创建一个新容器，挂载dbdata2容器，并用untar解压就可以了
```
+ ### 端口映射与容器互联
1.从外部访问容器应用
```
[root@42-m /]# docker run -d -P training/webapp python app.py	#-P(大写的)docker随机映射端口49000~49900到内部容器开放的网络端口
8f24a605151f0e7d81b4420d2aa38c52344609b98ba8b831d131ff61424b7d77
[root@42-m /]# docker ps -l	#可以看到映射到5000
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS                     NAMES
8f24a605151f        training/webapp     "python app.py"     14 seconds ago      Up 13 seconds       0.0.0.0:32770->5000/tcp   recursing_spence
[root@42-m /]# docker logs 8f	#查看容器应用的信息
 * Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)
192.168.31.1 - - [27/Dec/2019 08:19:27] "GET / HTTP/1.1" 200 -
192.168.31.1 - - [27/Dec/2019 08:19:28] "GET /favicon.ico HTTP/1.1" 404 -
[root@42-m /]#
```
也可以在浏览器上用宿主机的ip加容器映射的端口来访问web。-p(小写的)可以指定要映射的端口。
![](https://github.com/Kingserch/Job-accumulation/blob/Docker/images/mapping.png)
```
docker run -d -p 5000:5000 trainning/webapp python app.py	#本地的5000端口映射容器的5000端口
docker run -d -p 5000:5000 -p 3000:80 trainning/webapp python app.py	#多次使用-p标记可以绑定多个端口
docker run -d -p 127.0.0.1::5000  trainning/webapp python app.py	#绑定localhost的任意端口到容器的5000端口
```
2.互联网机制实现便捷访问
```
[root@42-m /]# docker run -d -P --name web training/webapp python app.py	#自定义容器的名字，方便使用
587d1cd60e7c4dbd5f8fdb0c218c91d8f3b0f820cecb09ef36d6fd6acc990106
[root@42-m /]# docker inspect -f "{{.Name}}" 58	#inspect查看容器的名字
/web
[root@42-m /]#
```
`注意：容器的名称是唯一的，如果已经有了web容器，在此使用web容器，需要把之前的删除了。在执行docker run的时候添加--rm标记，则容器会在终止后立即删除，--rm跟-d参数不能同时使用`
3.容器互联--link name:alias ,name是链接容器的名称，alias是别名
```
[root@42-m /]# docker run -d --name db training/postgres	#运行名字为db的容器
Unable to find image 'training/postgres:latest' locally
latest: Pulling from training/postgres
Image docker.io/training/postgres:latest uses outdated schema1 manifest format. Please upgrade to a schema2 image for better future compatibility. More information at https://docs.docker.com/registry/spec/deprecated-schema-v1/
a3ed95caeb02: Pull complete 
6e71c809542e: Pull complete 
2978d9af87ba: Pull complete 
e1bca35b062f: Pull complete 
500b6decf741: Pull complete 
74b14ef2151f: Pull complete 
7afd5ed3826e: Pull complete 
3c69bb244f5e: Pull complete 
d86f9ec5aedf: Pull complete 
010fabf20157: Pull complete 
Digest: sha256:a945dc6dcfbc8d009c3d972931608344b76c2870ce796da00a827bd50791907e
Status: Downloaded newer image for training/postgres:latest
d9913a3317189484e63697d8a150862c7a53a2c113ee6c4439f370736e774889
[root@42-m /]# docker ps 
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                     NAMES
d9913a331718        training/postgres   "su postgres -c '/us…"   8 seconds ago       Up 6 seconds        5432/tcp                  db
587d1cd60e7c        training/webapp     "python app.py"          10 minutes ago      Up 10 minutes       0.0.0.0:32771->5000/tcp   web
[root@42-m /]# docker run -d -P --name web --link db:db training/webapp python app.py
docker: Error response from daemon: Conflict. The container name "/web" is already in use by container "587d1cd60e7c4dbd5f8fdb0c218c91d8f3b0f820cecb09ef36d6fd6acc990106". You have to remove (or rename) that container to be able to reuse that name.
See 'docker run --help'.
[root@42-m /]# docker rm -f web
web
[root@42-m /]# docker run -d -P --name web --link db:db training/webapp python app.py
c629a1c3d54a7bde6351a5ca5677df780be31270df7ae90f6d00456ed4c1da96
[root@42-m /]# dokcer ps 
-bash: dokcer: command not found
[root@42-m /]# docker ps 
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                     NAMES
8f39e118f910        training/webapp     "python app.py"          6 seconds ago       Up 5 seconds        0.0.0.0:32774->5000/tcp   web
f5c455fd9cf6        training/postgres   "su postgres -c '/us…"   31 seconds ago      Up 30 seconds       5432/tcp                  db
[root@42-m /]# docker run --rm --name web2 --link db:db training/webapp env
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
HOSTNAME=cb5aaa586e2f
DB_PORT=tcp://172.17.0.2:5432
DB_PORT_5432_TCP=tcp://172.17.0.2:5432
DB_PORT_5432_TCP_ADDR=172.17.0.2
DB_PORT_5432_TCP_PORT=5432
DB_PORT_5432_TCP_PROTO=tcp
DB_NAME=/web2/db
DB_ENV_PG_VERSION=9.3
HOME=/root
[root@42-m /]# docker run -t -i --rm --link db:db training/webapp /bin/bash
root@ce5fd828c072:/opt/webapp# cat /etc/hosts 
127.0.0.1	localhost	#web绑定在宿主机
::1	localhost ip6-localhost ip6-loopback
fe00::0	ip6-localnet
ff00::0	ip6-mcastprefix
ff02::1	ip6-allnodes
ff02::2	ip6-allrouters
172.17.0.2	db f5c455fd9cf6
172.17.0.4	ce5fd828c072
root@ce5fd828c072:/opt/webapp# ping db
PING db (172.17.0.2) 56(84) bytes of data.
64 bytes from db (172.17.0.2): icmp_seq=1 ttl=64 time=0.332 ms
64 bytes from db (172.17.0.2): icmp_seq=2 ttl=64 time=0.109 ms
64 bytes from db (172.17.0.2): icmp_seq=3 ttl=64 time=0.114 ms
^C
--- db ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2000ms
rtt min/avg/max/mdev = 0.109/0.185/0.332/0.103 ms
root@ce5fd828c072:/opt/webapp#
```