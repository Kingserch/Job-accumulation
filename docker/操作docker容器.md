+ ### 操作docker容器
    + [创建容器create,start,run,wait,logs](#创建容器)
	+ [启动容器start](#启动容器)
	+ [查看容器输出logs](#查看容器输出)
	+ [停止容器pause/unpause,stop,prune](#停止容器)
	+ [进入容器attach,exec](#进入容器)
	+ [删除容器rm](#删除容器)
	+ [导入import和导出export容器](#导入和导出容器)
	+ [镜像save,load容器import,export区别](#镜像容器区别)
	+ [查看容器inspect,top,stats](#查看容器)
	+ [其他容器命令cp,diff,port,update](#其他容器命令)
+ ### 创建容器
`docker [container] create 命令新建一个容器`    
```
[root@42-m /]# docker create -it centos:latest	#创建一个新容器，容器处于停止状态
Unable to find image 'centos:latest' locally	#本地找不到这个容器，不是报错
latest: Pulling from library/centos
729ec3a6ada3: Pull complete 
Digest: sha256:f94c1d992c193b3dc09e297ffd54d8a4f1dc946c37cbeceb26d35ce1647f88d9
Status: Downloaded newer image for centos:latest
ed4fcd15c8f95ae997d6ced834b9805deb8e856d6d032e238ecb49def3ff6a4b
[root@42-m /]# docker ps -a		#ed4fcd15c8f9这个id就是创建的容器
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                    PORTS               NAMES
ed4fcd15c8f9        centos:latest       "/bin/bash"              38 seconds ago      Created                                       gallant_wilbur
a8beb3a9e1fe        centos:7            "/bin/bash"              13 hours ago        Exited (0) 13 hours ago                       quizzical_meitner
436d39f3ffa3        centos:7            "echo 'I am running!'"   14 hours ago        Exited (0) 14 hours ago                       nice_dirac
7b2a90fe3ad8        centos:7            "/bin/bash"              15 hours ago        Exited (0) 15 hours ago                       reverent_albattani
[root@42-m /]#
```
create命令与容器运行模式相关的选项，以下留个记录吧，以后参数用到翻阅起来也方便。
![](https://github.com/Kingserch/Job-accumulation/blob/Docker/images/create1.jpg)  
create命令与容器环境和配置相关的选项，create命令是一个非常重要的命令。
![](https://github.com/Kingserch/Job-accumulation/blob/Docker/images/create2.jpg)  
create命令与容器资源限制和安全保护相关的选项，以后会把重要的参数罗列出来的。
![](https://github.com/Kingserch/Job-accumulation/blob/Docker/images/create3.jpg)  
+ ### 启动容器
`docker [container] start 命令启动容器`  
`docker run centos:7 /bin/echo 'hello world'   centos:7本地没有就从官方下载，直接运行容器`
+ ### 查看容器输出
https://docs.docker.com/engine/reference/commandline/logs/	#logs命令官方解释
```
[root@42-m /]# docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                     PORTS               NAMES
1d48d20107ce        centos:7            "/bin/bash"              5 minutes ago       Exited (0) 4 minutes ago                       bold_wing
ed4fcd15c8f9        centos:latest       "/bin/bash"              2 hours ago         Created                                        gallant_wilbur
a8beb3a9e1fe        centos:7            "/bin/bash"              15 hours ago        Exited (0) 15 hours ago                        quizzical_meitner
436d39f3ffa3        centos:7            "echo 'I am running!'"   15 hours ago        Exited (0) 15 hours ago                        nice_dirac
7b2a90fe3ad8        centos:7            "/bin/bash"              16 hours ago        Exited (0) 16 hours ago                        reverent_albattani
[root@42-m /]# docker logs 436d39f3ffa3		#logs命令查看容器输出
I am running!
[root@42-m /]#
```
+ ### 停止容器
https://docs.docker.com/engine/reference/commandline/pause/ #pause命令
https://docs.docker.com/engine/reference/commandline/stop/	#stop命令
+ ### 进入容器
```
[root@42-m /]# docker run -it centos:7 /bin/bash	#需要先启动一个容器
[root@f3595a1a712d /]#
[root@42-m ~]# docker attach f3595a1a712d		#根据容器ID进入容器
[root@42-m /]# docker exec -it f3595a1a712d /bin/bash	#exec命令字运行容器内直接执行任意命令
[root@f3595a1a712d /]# pwd
/
[root@f3595a1a712d /]# ps -ef
UID         PID   PPID  C STIME TTY          TIME CMD
root          1      0  0 08:01 pts/0    00:00:00 /bin/bash
root         14      0  0 08:03 pts/1    00:00:00 /bin/bash
root         27     14  0 08:03 pts/1    00:00:00 ps -ef
[root@f3595a1a712d /]#
```
+ ### 删除容器
`用docker [container] rm 命令来删除处于终止或退出状态的容器。-f,是否强行删除，-l删除容器连接，保留容器，-v删除容器挂载的数据卷`
```
[root@42-m ~]# docker rm a8beb3a9e1fe 		#跟容器ID删除，rmi i是指image 
a8beb3a9e1fe
[root@42-m ~]# docker ps -a		#查看容器
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                    PORTS               NAMES
f3595a1a712d        centos:7            "/bin/bash"              3 hours ago         Up 27 minutes                                 heuristic_einstein
ed4fcd15c8f9        centos:latest       "/bin/bash"              7 hours ago         Created                                       gallant_wilbur
436d39f3ffa3        centos:7            "echo 'I am running!'"   21 hours ago        Exited (0) 21 hours ago                       nice_dirac
[root@42-m ~]#
```
+ ### 导入和导出容器
`1.导出容器是指，导出一个已经创建的容器到一个文件，不管此时这个容器是否运行`
```
[root@42-m ~]# docker export -o centos_for_exec.tar f3	#export导出容器-o指定名字
[root@42-m ~]# ls
anaconda-ks.cfg  centos_7.tar  centos_for_exec.tar
[root@42-m ~]# docker export ed >centos_for_stop.tar
[root@42-m ~]# ls
anaconda-ks.cfg  centos_7.tar  centos_for_exec.tar  centos_for_stop.tar
```
`2.导出的文件可以用docker [container] import 导入变成镜像`
```
[root@42-m ~]# docker  import centos_7.tar centos:v1	
sha256:850b87045c9866f807a2404563e87b9d494a46cece275781afc84f8ca364bbe3
[root@42-m ~]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
centos              v1                  850b87045c98        6 seconds ago       211MB
test                1                   027bb2937462        20 hours ago        203MB
centos              7                   5e35e350aded        6 weeks ago         203MB
centos              latest              0f3e07c0138f        2 months ago        220MB
[root@42-m ~]#
```
+ ### 镜像容器区别
```
一.两个方法的区别：
1)容器（export 导出、import导入) 是将当前容器 变成一个新的镜像，导入时会丢失镜像所有的历史，所以无法进行回滚操作(docker tag <LAYER ID> <IMAGE NAME>)；
2)镜像（save保存、load加载） 是复制的过程，没有丢失镜像的历史，可以回滚到之前的层(layer)。(查看方式：docker images --tree)
save 和 export区别
1)save 保存镜像所有的信息-包含历史
2)export 只导出当前的信息
镜像导出的 tar 文件比容器导出文件大。
二.使用场景的区别
容器内项目有修改
不需要历史记录的、想要体积小、又快速的，选择容器导出；反之选择镜像导出，镜像导出需先将容器commit成新的镜像，才能使用镜像导出。
容器内项目无修改
选择镜像导出。
若是只想备份images，使用save、load即可
若是在启动容器后，容器内容有变化，需要备份，则使用export、import。（或者将容器commit成新的镜像，在使用镜像导出）
```
+ ### 查看容器
```
# inspect命令
[root@42-m ~]# docker inspect centos:7	#json格式返回容器id，创建时间路径，状态，镜像，配置
[
    {
        "Id": "sha256:5e35e350aded98340bc8fcb0ba392d809c807bc3eb5c618d4a0674d98d88bccd",
        "RepoTags": [
            "centos:7"
        ],
        "RepoDigests": [
            "centos@sha256:4a701376d03f6b39b8c2a8f4a8e499441b0d567f9ab9d58e4991de4472fb813c"
        ],
		......
    }
]
# top命令
[root@42-m ~]# docker ps -a 		#查看当前的容器
CONTAINER ID        IMAGE               COMMAND                 CREATED              STATUS                     PORTS               NAMES
384733417a81        centos:latest       "/bin/bash"             About a minute ago   Created                                        exciting_mcnulty
2221fc238dee        centos:7            "echo 'I am running'"   3 minutes ago        Exited (0) 3 minutes ago                       awesome_buck
[root@42-m ~]# docker top 384733417a81	#容器没运行，
Error response from daemon: Container 384733417a812b08e9a3d6359e120a6a34c538c0d073e1f46a54674f178564c5 is not running
[root@42-m ~]# docker start 384733417a81	#启动这个容器
384733417a81
[root@42-m ~]# docker top 384733417a81		#查看这个容器的进程
UID                 PID                 PPID                C                   STIME               TTY                 TIME                CMD
root                3006                2989                0                   09:52               pts/0               00:00:00            /bin/bash
```
+ ### 其他容器命令
```
#cp命令支持在容器和主机之间复制文件-a,-archive:打包模式，复制文件会带有原始的UID/GID,-L,-follow-link，复制软连接的目标内容。
[root@42-m ~]# cd /home/tools/
[root@42-m tools]# ls
cmake-3.8.1
[root@42-m tools]# docker cp /home/tools/ 384733417a81:/tmp/	#把本地的/home/tools/下复制到容器的/tmp目录下
[root@42-m ~]# docker attach 384733417a81		#查看容器
[root@384733417a81 /]# cd /tmp/
[root@384733417a81 tmp]# ls
ks-script-0n44nrd1  ks-script-w6m6m_20	tools
[root@384733417a81 tmp]# cd tools/
[root@384733417a81 tools]# ls
cmake-3.8.1		#可以看到复制成功
#diff查看容器内文件系统的变更
[root@42-m tools]# dockker diff 384733417a81	#可以看到上传的cmake工具
A /tmp/tools/cmake-3.8.1/Utilities/cmvssetup/Setup.Configuration.h
....
#port可以查看端口映射，update更新配置
[root@42-m tools]# docker port 384733417a81
[root@42-m tools]# docker update --cpu-quota 100000 384733417a81
384733417a81
```
`工作中为了提高容器的高可用和安全性，我一般用HAProxy等辅助工具来处理负载均衡，启动切换故障的应用容器`


