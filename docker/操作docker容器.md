+ ### 操作docker容器
    + [创建容器create,start,run,wait,logs](#创建容器)
	+ [启动容器start](#启动容器)
	+ [查看容器输出logs](#查看容器输出)
	+ [停止容器pause/unpause,stop,prune](#停止容器)
	+ [坑位](#坑位)
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
