+ ### Docker命令
    + [获取镜像](#获取镜像)
    + [查看镜像](#查看镜像)
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

	
	
	
	
	
	
	






	
+ ### 坑位
    + [坑位](https://github.com/Kiaccumulation/blob/Docker/docker%E5%AE%89%E8%A3%85.md)