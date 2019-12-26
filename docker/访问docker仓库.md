+ ### 访问docker仓库(Repository)
    + [仓库(Repository)简介](#简介)
	+ [启动容器start](#启动容器)
	+ [查看容器输出logs](#查看容器输出)
	+ [停止容器pause/unpause,stop,prune](#停止容器)
	+ [进入容器attach,exec](#进入容器)
	+ [删除容器rm](#删除容器)
	+ [导入import和导出export容器](#导入和导出容器)
	+ [镜像save,load&容器import,export区别](#镜像save,load&容器import,export区别)
	+ [查看容器inspect,top,stats](#查看容器)
	+ [其他容器命令cp,diff,port,update(#其他容器命令)
+ ### 简介
仓库是集中存放镜像的地址，分为公有仓库和私有仓库。注册服务器是存放仓库的具体服务器，一个注册服务器有多个仓库，而每个仓库下面有多个镜像.
docker hub 公共镜像市场
<a href="https://hub.docker.com/" target="_blank">https://hub.docker.com/</a>
