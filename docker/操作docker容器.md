+ ### 操作docker容器
    + [创建容器create,start,run,wait,logs](#创建容器)
	+ [坑位](#坑位)
	+ [坑位](#坑位)
	+ [坑位](#坑位)
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
create命令与容器运行模式相关的选项
![](https://github.com/Kingserch/Job-accumulation/blob/Docker/images/create1.jpg)
![](https://github.com/Kingserch/Job-accumulation/blob/Docker/images/create2.jpg)
![](https://github.com/Kingserch/Job-accumulation/blob/Docker/images/create3.jpg)