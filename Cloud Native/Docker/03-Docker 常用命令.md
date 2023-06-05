## 帮助启动类命令

-  `systemctl start docker` 启动 Docker
-  `systemctl stop docker` 停止 Docker
-  `systemctl restart docker` 重启 Docker
-  `systemctl status docker` 查看 Docker 状态
-  `systemctl enable docker` 开机自动启动 Docker
-  `docker info` 查看 Docker 概要信息
-  `docker --help` 查看 Docker 总体帮助文档
-  `docker 具体命令 --help` 查看 Docker 命令帮助文档

## 镜像命令

-  `docker images` 列出本地主机上的镜像
	-  `-q`：只列出镜像 ID 
	-  `-a`：列出所有镜像，包括历史镜像
-  `docker search 镜像名称` 搜索镜像，例：`docker search --limit 5 redis`
-  `docker pull 镜像名称[:TAG]` 拉取镜像（没有 TAG 就表示 latest ）例：`docker pull mysql:5.7`
-  `docker system df` 查看镜像、容器、数据卷所占的空间
-  `docker rmi 镜像名称|镜像ID` 删除镜像
	-  `docker rmi -f mysql:5.7` 强制删除`mysql:5.7`
	-  `docker rmi -f $(docker images -qa)` 强制删除所有镜像（命令组合）

## 虚悬镜像

仓库名、标签都是None的镜像，俗称虚悬镜像（_angling image_）


## 容器镜像

-  `docker pull ubuntu` 拉取ubuntu
-  `docker run [OPTIONS] 镜像名称|镜像ID [COMMAND] [ARG...]` 新建 + 启动容器
	-  `--name="容器名称"`，为容器指定一个名称（若不取名字，系统随机分配）
	-   `-d`：**后台守护式启动容器**并返回容器 ID（_detach_） ，即启动守护式容器（后台运行）
	-   `-i`：**前台交互式启动容器**（_iteravtive_），通常和 -t 同时使用
	-   `-t`：为容器重新分配一个**伪输入终端**（_tty_），通常和 -i 同时使用
	-  例：`docker run -it --name=myu1 ubuntu /bin/bash` 其中`/bin/bash`作为shell交互命令的接口
	
	-  `-P`：**随机**端口映射
	-  `-p`：**指定**端口映射 `hostPort:containerPort`
		- 例：`-p 6379:6379` 访问Docker的6379端口以及Docker内部访问redis的6379端口（hostPort:containerPort）

-  `docker commit -m="提交的描述信息" -a="作者" 容器名|容器ID 要创建的目标镜像名:TAG` 提交容器副本使之成为一个新的镜像，以在Ubuntu中安装vim为例：
	-  `docker pull ubuntu`
	-  `docker run -it --name="ubuntu-default" ubuntu /bin/bash` 
	-  `vim a.txt` 此时发现默认安装的 Ubuntu 没有 vim 命令
	-  `apt-get -y update` 更新包管理器
	-  `apt-get -y install vim` 安装 vim 命令
	-  `docker commit -m="ubuntu with vim" -a="zx" ubuntu-default zx/ubuntu` 新镜像的仓库名为zx/ubuntu，注意这里的容器名要和上面的创建的容器名一致
	-  `docker run -it --name="zx-ubuntu" zx/ubuntu /bin/bash` 启动新的Ubuntu
	-  `vim a.txt` 此时发现新的 Ubuntu 有 vim 命令

-  `docker ps` 列出当前所有正在运行的容器
	-  `-a`：累出当前所有正在运行的容器 + 历史上运行过的容器
	-  `-l`：显示最近创建的容器
	-  `-n`：显示最近n个创建的容器
	-  `-q`：静默模式，只显示容器编号

-  `exit` run 进容器，exit 退出，容器停止
-  `ctrl + p + q` run 进容器，ctrl + p + q 退出，容器不停止
-  `docker start 容器名称或容器ID` 启动已停止的容器
-  `docker restart 容器名称或容器ID` 重启容器
-  `docker stop 容器名称或容器ID` 停止容器
-  `docker kill 容器名称或容器ID` 强制停止容器
-  `docker rm 容器名称或容器ID` 删除已停止的容器
-  `docker logs [-f] 容器名称|容器ID` 查看容器日志
-  `docker top 容器名称|容器ID` 查看容器内运行的进程
-  `docker inspect 容器名称|容器ID` 查看容器内部细节
-  `docker exec -it 容器名称|容器ID [COMMAND]` 在容器中打开新的终端，可以启动新的进程
	-  例：`docker exec -it redis /bin/bash`
	-  使用 exec 时，**当 exit 退出容器时，不会导致容器终止**

-  `docker attach 容器名称|容器ID` 直接进入容器启动命令的终端，不会启动新的进程
	-  例：`docker attach redis`
	-  使用 attach 时，**当 exit 退出容器时，会导致容器终止**


## 后台守护式容器

在大部分场景下，我们希望 Docker 的服务是在后台运行的，我们可以通过 -d 指定容器的后台运行模式。

-  `docker run -d ubuntu` 以后台守护式方式启动ubuntu

如果用 docker ps 查看，会发现容器已经退出了，这是因为 **Docker 容器后台运行的时候，必须有一个前台的进程**，这个是 Docker 的机制问题：如果容器中运行的命令不是**一直挂起的命令**（比如：top、tail 等），当容器以**守护式方式启动就会退出容器**。

因此 ubuntu 要以前台进程的形式运行，常见就是命令行模式，表示我还有交互操作。而 redis 有后台服务比如：server 因此可以以后台守护式启动。两种启动方式对比：

-  `docker run -it redis:6.0.8` 前台交互式启动
-  `docker run -d redis:6.0.8` 后台守护式启动

## 容器和主机文件的互相拷贝

-  `docker cp 容器id:容器内路径 目的主机路径` 拷贝容器中的文件到主机
-  `docker cp 目的主机路径 容器id:容器内路径` 拷贝主机中的文件到容器

## 导入和导出容器

-  `docker export 容器ID|容器名称 > 文件名.tar` 导出容器，将容器的内容导出为一个 tar 包
-  `docker import 文件名.tar 用户名/镜像名称:TAG` 导入容器，从 tar 包中的内容创建一个新的文件系统再导入为镜像

## Docker 命令总结

![](../../Image/Cloud%20Native/Docker/Docker%20命令总结.png)