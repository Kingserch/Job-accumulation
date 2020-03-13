+ ### docker快速安装elk
    + [docker镜像](#docker镜像)
    + [Dockerfile创建镜像](#dockerfile创建镜像)
+ ### docker镜像
[elk各个镜像](https://www.docker.elastic.co/#)  
[mq消息队列镜像](https://store.docker.com/images/rabbitmq) 
```
#需要的镜像
docker images
REPOSITORY                                      TAG                 IMAGE ID            CREATED             SIZE
rabbitmq                                        latest              4f856f1c03fc        2 days ago          151MB
docker.elastic.co/kibana/kibana                 7.6.1               f9ca33465ce3        13 days ago         1.01GB
docker.elastic.co/elasticsearch/elasticsearch   7.6.1               41072cdeebc5        13 days ago         790MB
#安装docker-compose命令
curl -L https://github.com/docker/compose/releases/download/1.19.0/run.sh >/usr/local/bin/docker-compose
ll /usr/local/bin/docker-compose 
chmod +x /usr/local/bin/docker-compose
```