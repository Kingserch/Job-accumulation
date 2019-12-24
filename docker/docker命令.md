+ ### Docker命令
    + [获取镜像pull](#获取镜像)
    + [查看镜像images,tag,inspect,history](#查看镜像)
    + [搜寻镜像search](#搜寻镜像)
    + [创建镜像commit,import,build](#创建镜像)
    + [坑位](#坑位)
    + [坑位](#坑位)
    + [坑位](#坑位)
    + [坑位](#坑位)
    + [坑位](#坑位)	
+ ### 获取镜像
`docker [image] pull NAME[:TAG]`   
`NAME是镜像仓库名称，用来区分镜像，TAG是镜像的标签，用来表示版本信息，docker拉取一个官方镜像，例如:`   
```
[root@42-m ~]# docker pull centos:7
7: Pulling from library/centos
ab5ef0e58194: Pull complete 
Digest: sha256:4a701376d03f6b39b8c2a8f4a8e499441b0d567f9ab9d58e4991de4472fb813c
Status: Downloaded newer image for centos:7
docker.io/library/centos:7
[root@42-m ~]# docker run -it centos:7 /bin/bash
[root@7b2a90fe3ad8 /]# echo "docker"
docker
[root@7b2a90fe3ad8 /]# exit
exit
[root@42-m ~]# 
```
+ ### 查看镜像
```
[root@42-m ~]# docker images		#images命令查看镜像
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
centos              7                   5e35e350aded        6 weeks ago         203MB
[root@42-m ~]# docker tag centos:7 mycentos:7		#使用tag命令给镜像做个类似于软连接
[root@42-m ~]# docker images		#查看镜像可以看到centos跟mycentos IMAGE ID是一致的
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
centos              7                   5e35e350aded        6 weeks ago         203MB
mycentos            7                   5e35e350aded        6 weeks ago         203MB
[root@42-m ~]# docker inspect mycentos:7		#inspect命令查看镜像的详细信息
[
    {
        "Id": "sha256:5e35e350aded98340bc8fcb0ba392d809c807bc3eb5c618d4a0674d98d88bccd",
        "RepoTags": [
            "centos:7",
            "mycentos:7"
        ],
        "RepoDigests": [
            "centos@sha256:4a701376d03f6b39b8c2a8f4a8e499441b0d567f9ab9d58e4991de4472fb813c"
        ],
      .....省略若干
]
[root@42-m ~]# docker history mycentos:7		#history命令查看镜像历史
IMAGE               CREATED             CREATED BY                                      SIZE                COMMENT
5e35e350aded        6 weeks ago         /bin/sh -c #(nop)  CMD ["/bin/bash"]            0B                  
<missing>           6 weeks ago         /bin/sh -c #(nop)  LABEL org.label-schema.sc…   0B                  
<missing>           6 weeks ago         /bin/sh -c #(nop) ADD file:45a381049c52b5664…   203MB               
[root@42-m ~]# 
```
+ ### 搜寻镜像
`语法:docker search [option] keyword`   
<ul>
  <li>-f,--filter filter:过滤输出内容</li>
  <li>--format string:格式化输出内容</li>
  <li>--limit int:限制输出结果的个数，默认为25个</li>
  <li>--no-trunc:不截断输出结果</li>
</ul>
`例如:搜索官方提供带nginx关键字的镜像`  

```
[root@42-m ~]# docker search --filter=is-official=true nginx
NAME                DESCRIPTION                STARS               OFFICIAL            AUTOMATED
nginx               Official build of Nginx.   12375               [OK]                
[root@42-m ~]# 
```
+ ### 删除和清理镜像
`使用docker rmi 或docker image rm 命令可以删除镜像,-f,force:强制删除;-no-prune:不要清理未带标签的父镜像`
```
[root@42-m ~]# docker rmi mycentos:7	#删除一个镜像，但是这个镜像有多个名字的，只要有标签指向这个镜像就没彻底删除，
Untagged: mycentos:7
[root@42-m ~]# docker image ls			#也可以用rmi 加ID删除，这样删除会先删除指向这个ID的所有标签，在删除ID
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
centos              7                   5e35e350aded        6 weeks ago         203MB
[root@42-m ~]# docker run centos:7 echo 'I am running!'		#用centos:7输入一段话
I am running!
[root@42-m ~]# docker ps -a 	#可以看到后台存在一个退出状态的容器，试图删除该容易，docker会提示容器正在运行，无法删除。-f可以强制删除
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                      PORTS               NAMES
436d39f3ffa3        centos:7            "echo 'I am running!'"   10 seconds ago      Exited (0) 9 seconds ago                        nice_dirac
7b2a90fe3ad8        centos:7            "/bin/bash"              41 minutes ago      Exited (0) 40 minutes ago                       reverent_albattani
[root@42-m ~]# docker rmi 436d39f3ffa3 #正确的做法是先删除正在运行的容器，然后在根据ID来删除镜像
```
`清理镜像docker image prune命令进行清理`
<ul>
  <li>-f,--filter filter:只清理符合给定过滤器的镜像</li>
  <li>-a,all:删除所有无用镜像，不光是临时镜像</li>
  <li>-f,-force:强制删除镜像。</li>
</ul>
`docker image prune -f #这个命令会自动清理临时遗留镜像文件层，最后会提示释放的空间大小`
+ ### 创建镜像

	




	
+ ### 坑位
    + [坑位](https://github.com/Kiaccumulation/blob/Docker/docker%E5%AE%89%E8%A3%85.md)
	
	
	