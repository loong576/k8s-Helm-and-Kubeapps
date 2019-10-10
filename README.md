# k8s实践(九)：Helm and Kubeapps UI

<br>

**环境说明：**

| 主机名 | 操作系统版本 | ip | docker version | kubelet version |helm version | 配置 | 备注 |
| :------: | :------:  | :------: | :------: | :------: | :------: |:------: |:------: |
| master | Centos 7.6.1810 | 172.27.9.131 |Docker 18.09.6 | V1.14.2 | v2.14.3 | 2C2G | master主机 |
| node01 | Centos 7.6.1810 | 172.27.9.135 |Docker 18.09.6 | V1.14.2 | v2.14.3 | 2C2G | node节点 |
| node02 | Centos 7.6.1810 | 172.27.9.136 |Docker 18.09.6 | V1.14.2 | v2.14.3 | 2C2G | node节点 |
| centos7 | Centos 7.3.1611 | 172.27.9.181 | × | × | × | 1C1G | nfs服务器 |



<br>

## 1. Helm简介
Helm是一个Kubemetes包管理器（类似于OS包管理器，比如Linux中的yum、apt,或者MacOS中的 homebrew)。

<br>

Helm包括两个部分：helm客户端和tiller服务端。

<br>

**helm客户端**
<br>
helm客户端是一个命令行工具，负责管理charts、repository和release。它通过gPRC API（使用kubectl port-forward将tiller的端口映射到本地，然后再通过映射后的端口跟tiller通信）向tiller发送请求，并由tiller来管理对应的Kubernetes资源。helm作用：

- 本地Charts开发；
- 管理Charts仓库；
- 与Tiller服务器交互：发送Charts以安装、查询Release的相关信息及升级或卸载已有的Release

<br>

**tiller 服务端**
<br>
tiller 接收来自helm客户端的请求，并把相关资源的操作发送到Kubernetes，负责管理（安装、查询、升级或删除等）和跟踪 Kubernetes 资源。为了方便管理，tiller把release的相关信息保存在kubernetes的ConfigMap中。tiller作用：

- 监听来自helm客户端的请求；
- 合并Charts和配置以构建一个Release；
- 向Kubernetes集群安装Charts并对相应的Release进行跟踪；
- 升级和卸载Charts；

## 2. 为什么需要Helm
&emsp;&emsp;Kubernetes虽然提供了多种容器编排对象，例如Deployment、StatefulSet、DeamonSet、Job等，还有多种基础资源封装例如ConfigMap、Secret、Serivce等，但是一个应用往往有多个服务，有的可能还要依赖持久化存储，当这些服务之间直接互相依赖，需要有一定的组合的情况下，使用YAML文件的方式配置应用往往十分繁琐还容易出错，这时候就需要服务编排工具。
&emsp;&emsp;服务编排管理工具就是构建在kubernetes的基础object之上，统筹各个服务之间的关系和依赖的。目前常用到的工具是Helm。


## 3. Helm核心术语

> * **Chart：**一个Helm包，其中包含了运行一个应用所需要的工具和资源定义，还可能包含Kubernetes集群中的服务定义，类似于Homebrew中的formula、APT中的dpkg或者Yum中的RPM文件： 
> * **Release：**在Kubernetes集群上运行的一个Chart实例。在同一个集群上，一个Chart可以安装多次。例如有一个MySQLChart，如果想在服务器上运行两个MySQL数据库，就可以基于这个Chart安装两次，每次安装都会生成新的Release，会有独立的Release名称；
> * **Repository：**用于存放和共享Chart的仓库。


Helm将charts安装到Kubernetes中，每个安装创建一个新release。要找到新的chart，可以搜索Helm charts存储库repositories。

<br>
<br>

**文章目录：**
# 一、概述
## 1. Helm简介
## 2. 为什么需要Helm
## 3. Helm核心术语
# 二、安装Helm
## 1. 安装Helm Client
### 1.1 脚本安装方式
### 1.2 二进制方式安装
### 1.3 命令补全
## 2. 安装Tiller server
### 2.1 设置环境变量
### 2.2 创建带有cluster-admin角色权限的服务账户
### 2.3 下载tiller镜像
### 2.4 安装Tiller
## 3. 卸载Helm
### 3.1 卸载tiller server
### 3.2 卸载helm客户端
# 三、Helm UI
## 1. 新增仓库bitnami
## 2. 安装Kubeapps
## 3. 创建API token
## 4. 创建service
## 5. 登录kubeapps
## 6.通过kubeapps创建mysql
### 6.1 授权
### 6.2 创建pv
### 6.3 创建mysql
### 6.4 进入mysql
# 四、Helm的使用
## 1. helm search
## 2. helm inspect
## 3. helm status
## 4. helm delete
## 5. helm install
## 6. helm upgrade
## 7. helm  rollback
## 8. helm fetch
# 五、Chart目录结构
# 六、搭建本地仓库
## 1. 创建自定义chart并打包
## 2. 挂载NFS共享目录
## 3. 新建容器web-repo
## 4. 生成仓库的index文件
## 5. 将新仓库添加到Helm
## 6. 验证新仓库
## 7. 更新本地仓库


<br>
<br>

**详细搭建过程及测试：**

https://blog.51cto.com/3241766/2438955
