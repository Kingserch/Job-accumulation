+ ### Docker命令
    + [获取镜像pull](#获取镜像)
    + [查看镜像images,tag,inspect,history](#查看镜像)
    + [搜寻镜像](#搜寻镜像)
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
`docker search [option] keyword`   
<ul>
  <li>-f,--filter filter:过滤输出内容</li>
  <li>--format string:格式化输出内容</li>
  <li>--limit int:限制输出结果的个数，默认为25个</li>
  <li>--no-trunc:不截断输出结果</li>
</ul>
	
	
	
	
	
	
	






	
+ ### 坑位
    + [坑位](https://github.com/Kiaccumulation/blob/Docker/docker%E5%AE%89%E8%A3%85.md)