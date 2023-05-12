## 集群类型
Kubernetes 集群大体上分为两类：**一主多从**和**多主多从**。
-  一主多从：一台Master节点和多台Node节点，搭建简单，有单机故障风险适合测试。
-  多主多从：多台Master节点和多台Node节点，搭建麻烦，适合生产环境。

![](../../Image/Cloud%20Native/Kubernetes/Kubernetes集群环境搭建.png)

## 安装方式
目前生产部署Kubernetes 集群主要有三种方式：

-  **Minikube**：一个用于快速搭建单节点Kubernetes的工具。
-  **Kubeadm**：Kubeadm 是一个K8s 部署工具，提供kubeadm init 和kubeadm join，用于快速部署Kubernetes 集群。
-  **二进制包**：从github 下载发行版的二进制包，手动部署每个组件，组成Kubernetes 集群。

Kubeadm 降低部署门槛，但屏蔽了很多细节，遇到问题很难排查。如果想更容易可控，推荐使用二进制包部署Kubernetes 集群，虽然手动部署麻烦点，期间可以学习很多工作原理，也利于后期维护。

## 准备环境

| 角色     | IP地址        | 组件                             | 操作系统      | 配置               |
|--------|-------------|--------------------------------|-----------|------------------|
| Master | 192.168.109.100 | docker，kubectl，kubeadm，kubelet | Centos7.5 基础设施服务器 | 2颗CPU 2G内存 50G硬盘 |
| Node1  | 192.168.109.101 | docker，kubectl，kubeadm，kubelet | Centos7.5 基础设施服务器 | 2颗CPU 2G内存 50G硬盘 |
| Node2  | 192.168.109.102 | docker，kubectl，kubeadm，kubelet | Centos7.5 基础设施服务器 | 2颗CPU 2G内存 50G硬盘 |

>docker（18.06.3）、kubectl（1.17.4）、kubeadm（1.17.4）、kubelet（1.17.4）

