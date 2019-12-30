+ ### Dockerfile创建镜像
    + [Dockerfile基本结构和命令](#dockerfile基本结构和命令)
    + [Dockerfile创建镜像](#dockerfile创建镜像)
+ ### dockerfile基本结构和命令
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

        RUN apt-get update && apt-get install -y nginx	#run指令对镜像执行跟随的命令，每运行一条镜像就添加新的一层，并提交
        RUN echo "\ndaemon off; " >> /etc/nginx/nginx.conf

        # Commands when creating a new container
        CMD /usr/sbin/nginx	#CMD指令，来指定运行容器时的操作命令
```
借Docker Hub上的nginx的dockerfile做例子解释一下dockerfile指令:
```
        FROM debian:jessie	#指定所创建镜像的基础镜像，FROM <image> [AS <name>]或FROM <image>:tag [AS <name>]

        LABEL maintainer docker_user<docker_user@email.com>	#LABEL为生成的镜像添加元数据标签信息

        ENV NGINX_VERSION 1.10.1-1~jessie	#ENV指定变量将在镜像中保留，ARG指定变量，当镜像编译成功后将不在存在

        RUN apt-key adv --keyserver hkp://pgp.mit.edu:80--recv-keys 573BFD6B3D8FBC64107
            9A6ABABF5BD827BD9BF62 \
                && echo  "deb  http://nginx.org/packages/debian/  jessie  nginx"  >>  /etc/
                      apt/sources.list \
                && apt-get update \
                && apt-get install --no-install-recommends --no-install-suggests -y \
                ca-certificates \
                nginx=${NGINX_VERSION} \
                nginx-module-xslt \
                nginx-module-geoip \
                nginx-module-image-filter \
                nginx-module-perl \
                nginx-module-njs \
                gettext-base \
                && rm -rf /var/lib/apt/lists/＊
				# forward request and error logs to docker log collector
        RUN ln -sf /dev/stdout /var/log/nginx/access.log \
            && ln -sf /dev/stderr /var/log/nginx/error.log

        EXPOSE 80443	#EXPOSE声明镜像内服务监听的端口，不能完成端口映射哦。

        CMD ["nginx", "-g", "daemon off; "]
```
| 指令      | 说明                                              | 格式                                                                                                                                                                                                                    |
| ----------- | --------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ARG         | 创建镜像过程中使用的变量                | ARG<name>[=<default value>]                                                                                                                                                                                               |
| FROM        | 创建镜像的基础镜像                         | FROM<image>:<tag>[AS <name>]                                                                                                                                                                                              |
| LABEL       | 为生成的镜像添加元数据标签信息       | LABEL<key>=<value>                                                                                                                                                                                                        |
| EXPOSE      | 指定镜像内服务监听的端口                | EXPOSE<port>                                                                                                                                                                                                              |
| ENV         | 指定环境变量                                  | ENV<key>=<value>                                                                                                                                                                                                          |
| ENTRYPOINT  | 指定镜像的默认入口命令                   | ENTRYPOINT command param1 param2：shell中执行                                                                                                                                                                         |
| VOLUME      | 创建一个数据卷挂载点                      | VOLUME["/data"]                                                                                                                                                                                                           |
| USER        | 指定运行用户                                  | USER daemon                                                                                                                                                                                                               |
| WORKDIR     | 为后续的RUN，CMD，ENTRYPOINT指令配置工作目录 | WORKDIR 绝对路径                                                                                                                                                                                                      |
| HEALTHCHECK | 配置所启动容器如何进行健康检查       | HEALTHCHECK[OPTIONS]CMD command 返回值是否为0判断[OPTIONS]的参数-interval=DURATION(default:30s)过多久检查一次，-timeout=DEFAULT(default:30s)每次检查等待结果的超时-retries=N(default:3)如果失败了，重试几次才最终确定失败 |
| run         | 运行指定命令                                  | RUN<command>每条RUN指令在当前镜像基础上执行指定命令，并提交为新的镜像                                                                                                                          |
| CMD         | 每个dockerfile只有有一条CMD命令，且只会执行最后一条 | 略                                                                                                                                                                                                                       ||  | 指令      | 说明                                              |                                                                                                                                                                                                                           |
+ ### dockerfile创建镜像
写完dockerfile文件后可以用docker [image] build命令来创建镜像，读取指定路径下的dockerfile，并将该路径的下所有数据作为上下文(context)发送给客户端，docker服务端会校验dockerfile格式通过后，卓条执行其中定义的命令，
dockerfile[image]build 命令参数  
![](https://github.com/Kingserch/Job-accumulation/blob/Docker/images/build.jpg)  
相关资源
`https://github.com/jessfraz/dockerfiles	#dockerfile文件资源`