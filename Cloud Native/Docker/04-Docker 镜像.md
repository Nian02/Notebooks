## Docker 镜像是什么

-  镜像是一种轻量级、可执行的独立软件包，它包含运行某个软件所需要的所有内容，我们将应用程序和配置打包好形成一个可交付的运行环境（包括代码、运行时所需要的库、环境变量和配置文件等），这个打包好的运行环境就是 image 镜像文件。

-  只有通过镜像文件才能生成 Docker 容器实例。

## UnionFS

Union 文件系统（_UnionFS_）是一种分层、轻量级并且高性能的文件系统，它支持**对文件系统的修改作为一次提交来一层层的叠加**，同时可以将**不同目录**挂载**到同一个虚拟文件系统**下(_unite several directories into a single virtual filesystem_) 

Union 文件系统是 Docker 镜像的基础（**分层镜像**）。镜像可以通过分层来进行**继承**，基于基础镜像（没有父镜像），可以制作各种具体的应用镜像。

![](../../Image/Cloud%20Native/Docker/Docker%20UnionFS.png)

特性：一次同时加载多个文件系统，但从外面看起来，只能看到一个文件系统，联合加载会把各层文件系统叠加起来，这样最终的文件系统会包含所有底层的文件和目录。

## Docker 镜像的加载原理

Docker 的镜像实际上由一层层的文件系统组成，这种层级的文件系统就是 UnionFS 。

-  bootfs （_boot file system_）主要包含 bootloader 和 kernel。这一层和典型的 Linux/Unix 系统是一样的，包含 bootloader 和 kernel。
	-  bootloader 主要是引导加载 kernel ，Linux 刚启动的时候会加载 bootfs 文件系统，在 Docker 镜像的最底层是引导文件 bootfs，当 **bootloader 加载完成之后整个内核就在内存之中了**，此时内存的使用权已经由 bootfs 转交给内核，系统也会卸载 bootfs 。
	-  kernel 就是 linux 的内核。

-  rootfs（_root file system_），在 bootfs 之上，包含的就是典型 Linux 系统中的 /dev、/proc、/etc 等标准目录和文件。rootfs 就是各种不同操作系统发行版，如：ubuntu、centos 等。

![](../../Image/Cloud%20Native/Docker/Docker%20镜像加载原理.png)

Docker 中的 centos7 很小，因为rootfs 可以很小，只需要包括最基本的命令、工具和程序库就可以了，**底层直接用的宿主机的 kernel**。

## Docker 镜像分层

### Docker 镜像分层结构的好处

镜像分层最大的一个好处就是**共享资源，方便复制迁移**。

比如：多个镜像都是从相同的 base 镜像构建而来，那么 Docker 只需要在磁盘中保存一份 base 镜像，同时内存中也只是加载一份 base 镜像，就可以为所有容器服务了，而且镜像的每一层都可以被共享。

### Docker 镜像分层的理解

-  写时复制，用时分配：Docker 镜像层都是只读的，容器层是可写的 。
-  当**容器启动的时候，一个新的可写层被加载到镜像的顶部，这一层通常被称为容器层**，容器层之下的都叫做镜像层。
-  所有对容器的改动，无论添加、删除还是修改文件都只会发生在容器层中，**只有容器层是可写的，容器层下面的所有镜像层都是只读的**。

![](../../Image/Cloud%20Native/Docker/Docker%20镜像分层的理解.png)

