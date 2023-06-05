## Docker 容器数据卷简介

-   卷就是**目录或文件**，存在于一个或多个容器中，由 **Docker 挂载到容器**，但是不属于联合文件系统（UnionFS），因此能够绕过 UnionFS 提供一些用于持续存储或共享数据的特性。
-   卷的设计目的就是**数据的持久化**，完全独立于容器的生命周期，因此 Docker **不会**在容器删除的时候**删除其挂载的容器数据卷**。

```shell
 docker run -it --privileged=true -v /宿主机绝对路径目录:/容器内目录 镜像名
```

>**将 Docker 容器内的数据保存进宿主机的磁盘中**（主机的数据卷挂载到容器上）

一般情况下，当容器实例删除之后，容器内的数据自然就丢失了，因此可以使用 Docker 容器数据卷的方式完成对重要数据的备份，也就是数据的持久化。

## Docker 容器数据卷特点

-  数据卷可以在**容器之间共享**或重用数据
-  数据卷中的更改可以**实时生效**
-  数据卷中的更改不会包含在镜像的更新中
-  数据卷的生命周期一直持续到**没有容器使用它**为止

## 宿主机和容器之间映射添加容器数据卷

### 添加容器数据卷

若没有对应路径的目录，docker 会自建这个路径的目录，取名为u1

```shell
docker run -it \
	--privileged=true \
	-v /tmp/host_data:/tmp/docker_data \
	--name=u1 ubuntu
```

### 测试

-  在容器下新建 dockerin.txt

```shell
cd /tmp/docker_data
ls
touch dockerin.txt
ls
```

-  在宿主机下查看 dockerin.txt

```shell
cd /tmp/host_data
ls
```

>同理，在宿主机下的数据卷进行修改也会同步更新到容器内

-  另一种方式：在 Mount 下查看

```shell
docker inspect u1
```

```shell
"Source": "/tmp/host_data",
"Destination": "/tmp/docker_data",
```

## 读写规则映射添加说明

-  默认情况下，容器内目录是读写（_rw_）

```shell
 docker run -it --privileged=true -v /宿主机绝对路径目录:/容器内目录:rw 镜像名
```

-  如果想设置**容器内目录只能读取，不能写入**，可以设为只读（_ro_），对应的命令为（此时如果宿主机写入内容，可以同步给容器内，容器可以读取数据）

```shell
 docker run -it --privileged=true -v /宿主机绝对路径目录:/容器内目录:ro 镜像名
```

## 容器数据卷的继承和共享

-  容器 1 完成和宿主机的映射
```shell
docker run -it  --privileged=true -v /var/tmp:/tmp --name u1 ubuntu
```

-  容器 2 继承容器 1 的卷规则
```shell
docker run -it  --privileged=true --volumes-from u1  --name u2 ubuntu
```