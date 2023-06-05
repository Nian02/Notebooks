## 图形化界面

### 安装

```
yum -y groupinstall 'X Window System'
yum -y groupinstall 'GNOME Desktop'
```

### 卸载

```
yum -y groupremove  'X Window System'
yum -y groupremove 'GNOME Desktop'
```

## VM tools

```
tar -xzvf filename.tar.gz
cd vmware-tools-distrib
./vmware-install.pl
```

## 常用命令

## yum

-  `yum clean all` 删除所有已下载的软件包以及任何缓存的元数据、头文件和索引文件。
-  `yum makecache` 创建 Yum 软件包管理器的缓存
-  `yum -y update` 更新系统中已安装软件包

## cp

-  `cp file1 dir1/` 将文件 `file1` 复制到目录 `dir1` 中（此命令可将多个文件复制到`dir1`中）
-  `cp file1 file2` 将文件 `file1` 复制并重命名为 `file2`
-  `cp -r dir1 dir2/` 将目录 `dir1` 复制到目录 `dir2` 中：

-  `cp -a` 使用归档模式进行复制，可以复制所有属性（权限、时间戳、所有者、组、硬链接、符号链接等）

## mv

-  `mv /etc/epel-testing.repo /etc/epel-testing.repo.backup` mv用于移动文件或重命名文件，此处用于创建备份文件
-  
## wget

-  `wget -O file.txt https://example.com/file.txt` wget命令下载文件，-O指定下载文件的保存位置和名称

-  `apt-get` 适用于deb包管理式的操作系统（如Ubuntu），搜索、安装、升级、卸载软件
	-  `-y` 在需要确认的场景中回应 yes

-  `cat` 创建单个或多个文件，查看文件的内容
	-  `cat /etc/redhat-release` 查看当前系统的版本
	-  `cat abcd.tar` 读取abcd.tar文件，并将文件内容打印到标准输出

-  `ls` 列出目录内容
	-  `-l`：查看当前所在目录下内容，可以简写为`ll`
	-  `-a`：查看隐藏文件

-  `ps` 查看服务器的进程信息
	-  `-e`：等价于 ‘-A’ ，表示列出全部的进程  
	-  `-f`：显示全部的列（显示全字段）

-  `pwd` 显示工作目录（_print work directory_）的绝对路径
-  `rm` 删除目录及文件
-  `-r`：（_recursive_）递归删除目录及其内容
-  `top` 展示linux进程信息
-  `touch` 修改文件或者目录的时间属性，若文件不存在，系统会建立一个新的文件。
## df

-  `df -h`
## tar

-  `tar -xzvf filename.tar.gz` 解压filename文件，-x解压，-z指`tar.gz`文件

## cat

-  `cat /etc/redhat-release` 查看当前系统的版本

## mkdir

-  `mkdir -p /etc/docker` 上级目录不存在,会按目录层级自动创建目录
## Shell

-  Ctrl + C 中断程序执行
-  Ctrl + Z 暂停程序执行
-  `ctrl + u` 清空当前行
-  `clear` 清空终端

-  `uname -sr` 查看当前系统内核