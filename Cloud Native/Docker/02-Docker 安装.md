CentOS 7 安装 Docker：
-   Docker 并非是一个通用的容器工具，它依赖于已经存在并运行的 Linux 内核环境。
-   Docker 实际上是在已经运行的 Linux 下制造一个隔离的文件环境，因此它执行的效率几乎等同于所部署的 Linux 主机。
-   因此，Docker 必须部署在 Linux 内核的系统上。如果其他系统想要部署 Docker 就必须要安装一个虚拟的 Linux 环境。

![](../../Image/Cloud%20Native/Docker/Docker%20环境.png)

## 环境设置

-  操作系统环境：CPU（2C）内存（2G）硬盘（50G）
-  语言：中文简体
-  软件选择：基础设施服务器
-  分区选择：自动分区
-  网络配置：
	-  网络地址：192.168.88.100（根据虚拟网络编辑器的NAT进行设置）
	-  子网掩码：255.255.255.0
	-  默认网关：192.168.88.2
	-  DNS服务器：223.5.5.5（阿里云）

## 卸载Docker旧版本
```shell
yum remove docker \
          docker-client \
          docker-client-latest \
          docker-common \
          docker-latest \
          docker-latest-logrotate \
          docker-logrotate \
          docker-engine
```


## 安装gcc相关

```shell
yum -y install gcc
yum -y install gcc-c++
```

## 安装所需要的软件包

```shell
yum install -y yum-utils
```

## 设置 stable 镜像仓库

```shell
# 官方源
yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
```

```shell
# aliyun 源
yum-config-manager \
	--add-repo \
	https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

## 更新 yum 软件包索引

```shell
yum makecache fast
```

## 安装最新版的DOCKER CE

```shell
yum -y install docker-ce docker-ce-cli containerd.io
```

## 启动并测试Docker

```shell
systemctl start docker
ps -ef|grep docker

# Client、Server:23.0.6
docker version

docker run hello-world
```

## 卸载Docker

```shell
systemctl stop docker \
	&& systemctl disable docker \
	&& yum -y remove docker-ce docker-ce-cli containerd.io \
	&& rm -rf /var/lib/docker \
	&& rm -rf /var/lib/containerd
```

