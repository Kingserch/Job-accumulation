+ ### Dockerfile创建镜像
    + [Dockerfile](#dockerfile创建镜像)
    + [坑位](#坑位)
    + [坑位](#坑位)
    + [坑位](#坑位)
+ ### dockerfile创建镜像
Dockerfile有四部分:基础镜像信息，维护者信息，镜像操作指令和容器启动时执行命令
```
# escape=\ (backslash)	# 执行解析器命令。
        # This dockerfile uses the ubuntu:xeniel image	#借用那个作者镜像的信息
        # VERSION 2- EDITION 1	#版本
        # Author: docker_user	#维护者信息
        # Command format: Instruction [arguments / command] ..	#命令格式：指令[参数/命令]

        # Base image to use, this must be set as the first line	#要使用基本图像，必须将其设置为第一行
        FROM ubuntu:xeniel

        # Maintainer: docker_user <docker_user at email.com> (@docker_user)
        LABEL maintainer docker_user<docker_user@email.com>

        # Commands to update the image	#更新图像的命令

RUN echo "deb http://archive.ubuntu.com/ubuntu/ xeniel main universe" >> /etc/
            apt/sources.list

        RUN apt-get update && apt-get install -y nginx
        RUN echo "\ndaemon off; " >> /etc/nginx/nginx.conf

        # Commands when creating a new container
        CMD /usr/sbin/nginx
```

	
	
	
	






	
+ ### 坑位
    + [坑位](https://github.com/Kiaccumulation/blob/Docker/docker%E5%AE%89%E8%A3%85.md)