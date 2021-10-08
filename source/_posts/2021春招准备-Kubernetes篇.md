---
title: Kubernetes篇-2021春招准备
date: 2021-02-09 11:03:59
tags:
---

#### 1、Kubernetes架构

Kubernetes是一个轻便的和可扩展的开源平台，用于管理容器化应用和服务。通过Kubernetes能够进行应用的自动化部署和扩缩容

![title](/images/2021年春招准备-Kubernetes篇/1.png)

- Master Node：作为控制节点，对集群进行调度管理
  - API Server主要用来处理REST的操作，并执行相关业务逻辑，以及更新etcd中的相关对象。相关结果状态将被保存在etcd中。
    - REST语义，监控，持久化和一致性保证，API 版本控制，放弃和生效
    - 内置准入控制语义，同步准入控制钩子，以及异步资源初始化
    - API注册和发现
  - Cluster state store:  Kubernetes默认使用etcd作为集群整体存储，etcd是一个简单的、分布式的、一致的key-value存储，主要被用来共享配置和服务发现。
  - Controller-Manager Server：执行大部分的集群层次的功能，如命名空间创建和生命周期
  
    - Replication Controller
  
    - Node Controller
  
    - Namespace Controller
  
    - Service Controller
  
    - Endpoints Controller: Endpoints Controller监听Service的Pod对应的副本变化add,update,delete变化，并处理
  
    - Persistent Controller
  
    - DaemonSet Controller
  - Scheduler:  负责根据调度策略自动将Pod部署到合适Node中
    - 预选Node：遍历集群中所有的Node，按照具体的预选策略筛选出符合要求的Node列表。如没有Node符合预选策略规则，该Pod就会被挂起。
    - 优选Node：预选Node列表的基础上，按照优选策略为待选的Node进行打分和排序，从中获取最优Node。
- Worker Node：作为真正的工作节点，运行业务应用的容器；Worker Node包含kubelet、kube proxy和Container Runtime；
  - Kubelet：使用cAdvisor进行资源监控，Kubelet才是Pod是否能够运行在特定Node上的最终裁决者，而不是scheduler或者DaemonSet
    - 负责node节点上pod的创建、修改、监控、删除等生命周期的管理
    - 定时上报本node的状态信息给kube-apiserver
    - 接收kube-apiserver下发的指令
    - 通过kube-apiserver简介与etcd集群交互，读取配置信息
    - **CRI**–一个能让kubelet无需编译就可以支持多种容器运行时的插件接口
  -  Container Runtime：每一个Node都会运行一个Container Runtime，其负责下载镜像和运行容器。
  - kube proxy：
    - 负责为Pod创建代理服务；引到访问至服务；并实现服务到Pod的路由和转发，以及通过应用的负载均衡
    - `kube-proxy`负责为`Services`以外的类型实现一种虚拟IP形式[`ExternalName`](https://kubernetes.io/docs/concepts/services-networking/service/#externalname)
- Add-on：是对Kubernetes核心功能的扩展，例如增加网络和网络策略等能力

  - kube-dns负责为整个集群提供DNS服务
  - Ingress Controller为服务提供外网入口
  - Heapster提供资源监控
  - Dashboard提供GUI
  - Federation提供跨可用区的集群
  - Fluentd-elasticsearch提供集群日志采集、存储与查询

#### 2、Kubernetes核心资源

- Pod: 所有业务类型的基础，它是一个或多个容器的组合。

- Replication Controller(副本控制器，RC): kubernetes集群中最早的保证Pod高可用的API对象，通过监控运行中的Pod来保证集群中运行指定数目的Pod副本。

- ReplicaSets: 下一代Replication Controller（selector）

- Deployment(无状态应用)：Pod和ReplicaSet提供了一个声明式(declarative)方法

- StatefulSet(有状态应用)：对于RC和RS中的Pod，一般不挂载存储或者挂载共享存储。对于StatefulSet中的Pod，每个Pod挂载自己独立的存储，如果一个Pod出现故障，从其他节点启动一个同样名字的Pod，要挂载上原来Pod的存储继续以它的状态提供服务。适合于StatefulSet的业务包括数据库服务MySQL和PostgreSQL

- Service: 定义了一组Pod的策略抽象，这些被标记的Pod都是通过label Selector决定的。Service提供了外部服务访问Pod的入口

  - **Endpoint**是可被访问的服务端点，即一个状态为running的pod，它是service访问的落点，只有service关联的pod才可能成为endpoint

  ![title](/images/2021年春招准备-Kubernetes篇/9.png)

  - **Endpoints**表示一个Service对应的所有Pod副本的访问地址。Node上的Kube-proxy进程获取每个Service的Endpoints，实现Service的负载均衡功能

  ![title](/images/2021年春招准备-Kubernetes篇/10.png)

- namespace

- node

#### 3、访问Pod的方式

- `hostNetwork: true`: Pod的字段

- `hostPort`是直接将容器的端口与所调度的节点上的端口路由，这样用户就可以通过宿主机的IP加上来访问Pod

  ```yaml
  apiVersion: v1
  kind: Pod
  metadata:
    name: influxdb
  spec:
    containers:
      - name: influxdb
        image: influxdb
        ports:
          - containerPort: 8086
            hostPort: 8086
  ```

- Service NodePort：接口转发映射范围30000-32767，在node节点上

  ```yaml
  apiVersion: v1
  kind: Pod
  metadata:
    name: nginx
    labels:
      name: nginx
  spec:
    containers:
      - name: nginx
        image: nginx
        ports:
          - containerPort: 80
  ---
  kind: Service
  apiVersion: v1
  metadata:
    name: nginx
  spec:
    type: NodePort
    ports:
      - name: nginx
        port: 80
        nodePort: 30000
    selector:
      name: nginx
  ```

- `LoadBalancer`: Service上定义 公有云上

- `Ingress`:  Ingress 简单的理解就是你原来需要改 Nginx 配置，然后配置各种域名对应哪个 Service，现在把这个动作抽象出来，变成一个 Ingress 对象，你可以用 yaml 创建，每次不要去改 Nginx 了，直接改 yaml 然后创建/更新就行了

  - ①下载Ingress-controller相关的YAML文件，并给Ingress-controller创建独立的名称空间；
  
- ②部署后端的服务，如myapp，并通过service进行暴露；
  
  - ③部署Ingress-controller的service，以实现接入集群外部流量；
  
- ④部署Ingress，进行定义规则，使Ingress-controller和后端服务的Pod组进行关联。
  
  ![title](/images/2021年春招准备-Kubernetes篇/2.png)
  
  ![title](/images/2021年春招准备-Kubernetes篇/3.png)
  
  ![title](/images/2021年春招准备-Kubernetes篇/4.png)
  
  

#### 4、Pod与Docker的区别

##### **为什么要设计Pod**

在操作系统中，进程是以进程组的方式，"有原则的"组织到一起运行。 k8s 需要做的工作就是将 "进程组" 的概念映射到容器技术中。

以 rsyslogd 为例子。已知 rsyslogd 有三个进程组成，一个 imklog 模块，一个 imuxsock 模块， 一个 rsyslogd 自己的 main 函数主进程。这三个进程一定要运行在同一台机器上，否则，他们之间基于 Socket 的通信和文件交换，都会出现问题。

如果要将 rsyslogd 服务容器化，受限于容器的"单进程模式"，那么就需要制作三个不同的容器，最明显的问题，在调度的时候，这三个容器可能被调度到不同的 node 上。

> 容器单进程模式：并不是指容器里只能运行"一个"进程而是指容器没有管理多进程的能力。这是因为容器里 PID=1 的进程就是应用本身，其他的进程都是 PID=1 进程的子进程。

如果存在 Pod，想 imklog、imudock 和 main 函数主进程这样的三个容器，这是一个典型的由三个容器组成的 Pod。像这样容器间的紧密协作，我们可以称为 "超亲密关系"。 这些具有 "超亲密关系" 容器的典型特征包括但不限于：

- 互相之间发生直接的文件交换
- 使用 localhost 或者 Socket 文件进行本地通信
- 会发生非常频繁的远程调用
- 需要共性某些 Linux Namespace

关于一个 Pod 最重要的事实是： **它只是一个逻辑概念**。

也就是说，k8s 真正处理，还是宿主机操作系统上 Linux
 容器的 Namespace 和 Cgroups，而并不存在一个所谓的 Pod 的边界或者隔离环境。



##### 区别

- Kubernetes并不是只支持Docker这一个容器运行时，Kubernetes通过CRI这个抽象层，支持除Docker之外的其他容器运行
- 假设Kubernetes没有pod的概念，而是直接管理容器，那么一组容器作为一个单元，假设其中一个容器死亡了，此时这个单元的状态应该如何定义呢？应该理解成整体死亡，还是个别死亡。pod里都有一个Kubernetes系统自带的Infra容器(pause镜像)，代表pod的存亡；同时解除同一Pod内容器A与容器B的拓扑关系。
- 每个pod都有唯一的IP地址，pod里所有的业务容器共享pause容器的IP地址，以及pause容器mount的Volume，通过这种设计，业务容器之间可以直接通信，文件也能够直接彼此共享



#### 5、Kubernetes常见的开源项目

- Helm：Helm是Kubernetes的包管理器
- Istio: Istio是现在最热门的Kubernetes容器网络工具。它基于“服务网格”模型。
- Prometheus
- OpenTracing



#### 6、Flannel与Calico的区别

- Flannel之所以可以搭建Kubernetes依赖的底层网络，是因为它能实现以下两点。

  Flannel首先创建了一个名为flannel0的网桥，而且这个网桥的一端连接docker0网桥，另一端连接一个叫作flanneld的服务进程。flanneld进程上连etcd，利用etcd来管理可分配的IP地址段资源，同时监控etcd中每个Pod的实际地址，并在内存中建立了一个Pod节点路由表；flanneld进程下连docker0和物理网络，使用内存中的Pod节点路由表，将docker0发给它的数据包包装起来，利用物理网络的连接将数据包投递到目标flanneld上，从而完成Pod到Pod之间的直接地址通信。

  - 它能协助Kubernetes，给每一个Node上的Docker容器都分配互相不冲突的IP地址。

  - 它能在这些IP地址之间建立一个覆盖网络（Overlay Network），通过这个覆盖网络，将数据包原封不动地传递到目标容器内

  - flannel在进行路由转发的基础上进行了封包解包的操作

![title](/images/2021年春招准备-Kubernetes篇/5.png)

- Calico是一个基于BGP的纯三层的网络方案，根据iptables规则进行路由转发。源容器-宿主机-目标容器



#### 7、Swarm与Kubernetes的区别

- Docker Swarm 是一款用来管理多主机上的Docker容器的工具，可以负责帮你启动容器，监控容器状态，如果容器的状态不正常它会帮你重新帮你启动一个新的容器，来提供服务，同时也提供服务之间的负载均衡
- Kubernetes它本身的角色定位是和Docker Swarm 是一样的，也就是说他们负责的工作在容器领域来说是相同的部分，都是一个跨主机的容器管理平台，当然也有自己一些不一样的特点，k8s是谷歌公司根据自身的多年的运维经验研发的一款容器管理平台。Docker Swarm则是由Docker 公司研发的



#### 8、挖矿

kubelet 10250溢出漏洞导致任意代码远程执行



#### 9、Kubenetes弃用Docker

**docker不支持CRI（docker公司不用google的标准）**

Kubelet 之前使用的是一个名为 docker-shim 的模块，用以实现对 Docker 的 CRI 支持。但 Kubernetes 社区发现了与之相关的维护问题

选择什么？选择社区推荐的containerd、CRI-O 

> Docker 并不支持 CRI（容器运行时接口）这一 Kubernetes 运行时 API，而 Kubernetes 用户一直以来所使用的其实是名为“dockershim”的桥接服务。
>
> Dockershim 能够转换 Docker API 与 CRI，但在后续版本当中，Kubernetes 将不再提供这项桥接服务。

Kubernetes 实际上需要保持在红框之内。Docker 网络与存储卷都被排除在外。而这些用不到的功能本身就可能带来安全隐患

![title](/images/2021年春招准备-Kubernetes篇/6.jpg)

原先的运行方式

![title](/images/2021年春招准备-Kubernetes篇/7.png)



#### 10、容器探针

> 探针是由 kubelet对容器执行的定期诊断

##### Pod生命周期

- Pending：表示集群系统正在创建Pod，但是Pod中的container还没有全部被创建，这其中也包含集群为container创建网络，或者下载镜像的时间；
- Running：表示pod已经运行在一个节点商量，并且所有的container都已经被创建。但是并不代表所有的container都运行，它仅仅代表至少有一个container是处于运行的状态或者进程出于启动中或者重启中；
- Succeeded：所有Pod中的container都已经终止成功，并且没有处于重启的container；
- Failed：所有的Pod中的container都已经终止了，但是至少还有一个container没有被正常的终止(其终止时的退出码不为0)

##### 容器探针

- livenessProbe ：指示**容器是否正在运行**。如果存活探测失败，则 kubelet 会杀死容器，并且容器将受到其重启策略 的影响。如果容器不提供存活探针，则默认状态为 Success。
  - ExecAction: 在容器内执行指定命令。如果命令退出时返回码为 0 则认为诊断成功
  - TCPSocketAction: 对指定端口上的容器的 IP 地址进行 TCP 检查。如果端口打开，则诊断被认为是成功的
  - HTTPGetAction: 对指定的端口和路径上的容器的 IP 地址执行 HTTP Get 请求。如果响应的状态码大于等于 200 且小于 400 ，则诊断被认为是成功的
- readinessProbe ：指示**容器是否准备好服务请求**。表示container是否以及处于可接受service请求的状态了。如果就绪探测失败，Endpoints controller将从与 Pod 匹配的所有 Service 的端点中删除该 Pod 的 IP 地址。默认Readiness的初始值是Failure，如果一个container没有提供 Readiness则被认为是Success。

**liveness探针影响的是单个容器，但readiness探针影响的是整个pod，即如果pod中有多个容器，只要有一个容器的readiness探针诊断失败，那么整个pod都会处于unready状态。**

**容器探针参数**

> 对于Liveness Probe和Readiness Probe用法都一样，拥有相同的参数和相同的监测方式。

- initialDelaySeconds：用来表示初始化延迟的时间，也就是告诉监测从多久之后开始运行，单位是秒
- timeoutSeconds: 用来表示监测的超时时间，如果超过这个时长后，则认为监测失败
  当前对每一个Container都可以设置不同的restartpolicy，有三种值可以设置：
  - Always: 只要container退出就重新启动
  - OnFailure: 当container非正常退出后重新启动
  - Never: 从不进行重新启动

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: goproxy
  labels:
    app: goproxy
spec:
  containers:
  - name: goproxy
    image: k8s.gcr.io/goproxy:0.1
    ports:
    - containerPort: 8080
    readinessProbe:
      tcpSocket:
        port: 8080
      initialDelaySeconds: 5
      periodSeconds: 10
    livenessProbe:
      tcpSocket:
        port: 8080
      initialDelaySeconds: 15
      periodSeconds: 20
```



#### 11、失效 Pod 的垃圾收集

对于已失败的 Pod 而言，对应的 API 对象仍然会保留在集群的 API 服务器上，直到用户或者控制器进程显式地将其删除。

控制面组件会在 Pod 个数超出所配置的阈值 （根据 kube-controller-manager 的 terminated-pod-gc-threshold 设置）时删除已终止的 Pod（阶段值为 Succeeded 或 Failed）。 这一行为会避免随着时间演进不断创建和终止 Pod 而引起的资源泄露问题



#### 12、三种IP

- Node IP：Node节点的IP地址，即物理网卡的IP地址
- Pod IP：Pod的IP地址，即docker容器的IP地址，Docker Engine根据docker网桥的IP地址段进行分配的，此为虚拟IP地址，虚拟的二层网络
  - 同Service下的pod可以直接根据PodIP相互通信
  - 不同Service下的pod在集群间pod通信要借助于 cluster ip
  - pod和集群外通信，要借助于node ip
- Cluster IP：Service的IP地址，此为虚拟IP地址
  - Cluster IP仅仅作用于Kubernetes Service这个对象，并由Kubernetes管理和分配IP地址
  - Cluster IP无法被ping，没有一个“实体网络对象”来响应
  - Cluster IP只能结合Service Port组成一个具体的通信端口，单独的Cluster IP不具备通信的基础，并且他们属于Kubernetes集群这样一个封闭的空间。
  - 在不同Service下的pod节点在集群间相互访问可以通过Cluster IP
    

#### 13、Kube-Proxy代理模式

##### 用户空间代理模式(User space proxy mode):

![title](/images/2021年春招准备-Kubernetes篇/11.png)

![title](/images/2021年春招准备-Kubernetes篇/12.png)

- 为每个service在node上打开一个随机端口（代理端口）
- 建立iptables规则，将clusterip的请求重定向到代理端口
- 到达代理端口（用户空间）的请求再由kubeproxy转发到后端pod
- 若与第一个Pod连接失败，Kube-Proxy将检测到与第一个Pod的连接已失败，并会自动尝试使用其他Pod进行重试

> 这里为什么需要建iptables规则，因为kube-proxy 监听的端口在用户空间，所以需要一层 iptables 把访问服务的连接重定向给 kube-proxy 服务，这里就存在内核态到用户态的切换，代价很大，因此就有了iptables
>
> iptables处理流量具有较低的系统开销，因为流量由**Linux netfilter**处理，而无需在用户空间和内核空间之间切换。这种方法也可能更可靠

##### `iptables` 代理模式

![title](/images/2021年春招准备-Kubernetes篇/13.png)

- 每个Service，都安装iptables规则，该规则捕获到服务`clusterIP`和的`port`流量，并将该流量重定向到Service

- 每个Endpoint对象，它将安装iptables规则，该规则选择一个Pod
- 若所选的第一个Pod没有响应，则连接失败，使用Pod探针来验证后端Pod正常运行
- **讨论:** kube-proxy不再负责转发，数据包的走向完全由iptables规则决定，效率明显会高很多。但是随着service的增加，iptables规则会不断增加，导致内核十分繁忙（等于在读一张很大的没建索引的表）


##### `IPVS`代理模式

>  Kubernetes v1.11 [stable]

![title](/images/2021年春招准备-Kubernetes篇/14.png)

- kube-proxy监视Kubernetes的Service和Endpoint，调用`netlink`接口以相应地创建IPVS规则，
- 定期将IPVS规则与Kubernetes服务和端点同步，该控制循环可确保IPVS状态与所需状态匹配。访问服务时，IPVS将流量定向到Pod之一。

- IPVS代理模式基于类似于iptables模式的netfilter挂钩函数，但是使用**哈希表**作为基础数据结构，并且在内核空间中工作。与iptables模式下的kube-proxy相比，IPVS模式下的kube-proxy可以**以更低的延迟来重定向流量**，从而在同步代理规则时具有更好的性能。与其他代理模式相比，IPVS模式还**支持更高的网络流量吞吐量**
- IPVS提供了更多选项来平衡后端Pod的流量
  - `rr`：轮循
  - `lc`：连接最少（打开的连接最少）
  - `dh`：目标哈希
  - `sh`：源哈希
  - `sed`：最短的预期延迟
  - `nq`：永不排队



#### 14、Kubernetes调度机制

亲和性和反亲和性是运行时调度策略，主要包括`nodeAffinity（主机亲和性）`、`podAffinity（pod亲和性）`、`podAntiAffinity（pod反亲和性）`三类。

- nodeAffinity：用于规定pod可以部署在哪个node或者不能部署在哪个节点上。解决pod和主机的问题。
- podAffinity：用于规定pod可以和哪些pod部署在同一拓扑结构下。
- podAntiAffinity：
  - 用于规定pod不可以和哪些pod部署在同一拓扑结构下，与podAffinity一起解决pod和pod之间的关系。
  - 当应用的采用多副本部署时，有必要采用反亲和性让各个应用实例打散分布在各个node上，以提高HA(高可用)

三种Affinity使用时都有三种规则可以设定：

- `requiredDuringSchedulingIgnoredDuringExecution`:

  - hard，严格执行，满足规则调度，否则不调度，在预选阶段执行，所以违反hard约定一定不会调度到

  - 在首次调度时一定要满足相应的Affinity规则，如果没有满足条件的node将不会进行调度。在pod运行过程中如果不再满足相应的Affinity规则，会进行重新调度。

- `preferredDuringSchedulingIgnoredDuringExecution`: 

  - soft，尽力执行，优先满足规则调度，在优选阶段执行，
  - 在首次调度时需要满足相应的Affinity规则，如果没有满足条件的node将不会进行调度。在后续pod的运行过程中不在检查这些规则是否满足。

- `requiredDuringSchedulingRequiredDuringExecution`: 

  - 未实现，与之前类似，只是后缀不同，代表如果labels发生改变，kubelet将驱逐不满足规则的pod
  - 在首次调度时尽量满足相应的Affinity规则，如果没有满足要求的node也会进行调度。后续pod的运行过程中不再检查是否满足

  

**对称性**

> 对称性，pod1设定了的同时pod2也设定

考虑一个场景，两个应用S1和S2。现在**严格**要求S1 pod不能与S2 pod运行在一个node，如果仅设置S1的hard反亲和性是不够的，必须同时给S2设置对应的hard反亲和性。即调度S1 pod时，考虑node没有S2 pod，同时需要在调度S2 pod时，考虑node上没有S1 pod。

考虑下面两种情况：

1. 先调度S2，后调度S1，可以满足反亲和性，
2. 先调度S1，后调度S2，违反S1的反亲和性规则，因为S2没有反亲和性规则，所以在**schedule-time**可以与S1调度在一个拓扑下。

这就是对称性，即S1设置了与S2相关的hard反亲和性规则，就必须**对称地**给S2设置与S1相关的hard反亲和性规则，以达到调度预期。

**Note：**

- 反亲和性（soft/hard）具备对称性，上面已经举过例子了
- hard亲和性不具备对称性，例如期望S1、S2亲和，那么调度S2的时候没有必要node上一定要有S1，但是有一个隐含规则，node上有S1更好
- soft亲和性具备对称性，不是很理解，遗留



#### 15、Kubernetes 主节点高可用

![title](/images/2021年春招准备-Kubernetes篇/8.png)

- 所有管理节点都部署kube-apiserver，kube-controller-manager，kube-scheduler，以及etcd等组件
- kube-apiserver均与本地的etcd进行通信，etcd在三个节点间同步数据；
- kube-controller-manager和kube-scheduler也只与本地的kube-apiserver进行通信(或者通过LB访问)，基于[Leader Election Mechanism](https://github.com/kubernetes/client-go/tree/master/examples/leader-election)实现了高可用

- kube-apiserver前面顶一个LoadBalancer(LB)；work节点kubelet以及kube-proxy组件对接LB访问apiserver

- work node工作节点上部署应用，应用按照**[反亲和](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#more-practical-use-cases)**部署多个副本，使副本调度在不同的work node上，这样如果其中一个副本所在的母机宕机了，理论上通过Kubernetes service可以把请求切换到另外副本上，使服务依旧可用

**节点宕机对网络层面的影响**

> 集群中还部署了依赖nginx服务的应用A，如果某个时刻work node1宕机了，此时应用A访问nginx service会有问题吗？

有问题

因为service不会马上剔除掉宕机上对应的nginx pod，同时由于service常用的[iptables代理模式](https://kubernetes.io/docs/concepts/services-networking/service/#proxy-mode-iptables)没有实现[retry with another backend](https://kubernetes.io/docs/concepts/services-networking/service/#proxy-mode-iptables)特性，所以在一段时间内访问会出现间歇性问题(如果请求轮询到挂掉的nginx pod上)，也就是说会**存在一个访问失败间隔期**

这个间隔期取决于service对应的endpoint什么时候踢掉宕机的pod。之后，kube-proxy会watch到endpoint变化，然后更新对应的iptables规则，使service访问后端列表恢复正常(也就是踢掉该pod)

每个node在kube-node-lease namespace下会对应一个Lease object，kubelet每隔node-status-update-frequency时间(默认10s)会更新对应node的Lease object

node-controller会每隔node-monitor-period时间(默认5s)检查Lease object是否更新，如果超过node-monitor-grace-period时间(默认40s)没有发生过更新，则认为这个node不健康，会更新NodeStatus(ConditionUnknown)。如果这样的状态在这之后持续了一段时间(默认5mins)，则会驱逐(evict)该node上的pod

对于母机宕机的场景，node controller在node-monitor-grace-period时间段没有观察到心跳的情况下，会更新NodeStatus(ConditionUnknown)，之后endpoint controller会从endpoint backend中踢掉该母机上的所有pod，最终服务访问正常，请求不会落在挂掉的pod上

也就是说在母机宕机后，在node-monitor-grace-period间隔内访问service会有间歇性问题，我们可以通过调整相关参数来缩小这个窗口期



#### 16、Pod创建过程

##### 静态Pod

- 静态pod 是**由 kubelet 管理的只在特定node上存在的pod**

- 静态pod 不能通过 api-server来管理，无法和 RC，RS，Deployment或者 DaemonSet进行关联
-  kubelet无法对静态pod 进行健康检查。

**静态创建过程**
静态pod是在特定文件目录定义好yaml，通过kubelet在启动进程时候增加启动参数，之后kubelet会隔一段时间去扫描特定目录下时候有pod配置文件生成，其中--config自定特定目录 ，--file-check-frequency指定扫描间隔，默认为20S





##### 动态pod创建过程

kubelet通过apiserver，利用etcd的watch加list机制，通过消息订阅的方式，etcd提供的接口可以参考专栏etcd的描述，etcd有pod的操作，将会被监听到，之后完成pod的创建

**pod的调度过程**

![title](/images/2021年春招准备-Kubernetes篇/15.png)

- 执行kubectl命令创建pod，根据kubeconfig的配置向kube-apiserver发送创建请求

- kube-apiserver存储pod数据到etcd

  - API Server 在接收到创建pod的请求之后，会根据用户提交的参数值来创建一个运行时的pod对象
  - 根据 API Server 请求的上下文的元数据来验证两者的 namespace 是否匹配，如果不匹配则创建失败
  - Namespace 匹配成功之后，会向 pod 对象注入一些系统数据，如果 pod 未提供 pod 的名字，则 API Server 会将 pod 的 uid 作为 pod 的名字
  - API Server 接下来会**检查 pod 对象的必需字段是否为空，如果为空，创建失败**
  - **在 etcd 中持久化这个对象**，将异步调用返回结果封装成 restful.response
  - pod 处于 pending 状态

- kube-scheduler通过kube-apiserver查看未绑定的pod，尝试为pod分配主机。**API Server 完成任务之后，将信息写入到 etcd 中，此时 scheduler 通过 watch 机制监听到写入到 etcd 的信息然后再进行工作**，kube-scheduler的调度分为两步

  - **节点预选**：基于一系列预选规则（如 PodFitsResource 和 MatchNode-Selector 等）对每个节点进行检查，将不符合的节点过滤掉从而完成节点预选
  - **节点优选**：为符合要求的主机计算权重。权重的计算设计到一些整体优化策略，比如replicas分配到不同主机，使用负载较低的主机等。对预选出的节点进行优先级排序，以便选出最适合运行 pod 对象的节点
  - 从优先级结果中挑选出优先级最高的节点来运行 pod 对象，当此类节点多个时则随机选择一个。
  - 进行binding操作，结果存储到etcd中。kube-scheduler会调用kube-apiserver在etcd创建boundpod对象，描述在一个工作节点上绑定运行的所有pod信息
  - pod的调度过程除了自身的调度算法外，还支持其他的调度策略，包括**node seletctor，nodeaffnity(亲和)，podaffnity，pod驱逐**
  
- Kubelet组件启动pod，**Kubelet 通过 watch 机制监听到写入到 etcd 的信息然后再进行工作**
  - kubelet监听etcd，kubelet服务定时和etcd同步boundpod信息，获取到pod被分配到哪个特定的node

  - 为该 pod 创建一个数据目录，从 API Server 读取该 pod 清单，为该 pod 挂载外部卷，下载 pod 所需的 Secret

  - 检查已经运行在节点中 pod，如果该 pod 没有容器或者 Pause 容器没有启动，则先停止pod里所有的容器进程

  - 使用 pause 镜像为每个pod创建一个容器，该容器用于接管 Pod 中所有其他容器的网络

     

##### **详述pod声明周期中的重要行为**

![title](/images/2021年春招准备-Kubernetes篇/17.png)

- **初始化容器**：即 pod 内主容器启动之前要运行的容器，主要是做一些前置工作，初始化容器具有以下特征：
  - 初始化容器必须首先执行，若初始化容器运行失败，集群会一直重启初始化容器直至完成，注意，如果 pod 的重启策略为 Never，那初始化容器启动失败后就不会重启
  - 初始化容器必须按照定义的顺序执行，初始化容器可以通过 pod 的 spec.initContainers 进行定义

- **声明周期钩子函数**，Kubernetes 为容器提供了两种生命周期钩子：
    - Poststart:容器创建完成之后立即运行的钩子程序。
    - preStop:容器终止之前立即运行的程序，是以同步方式的进行，因此其完成之前会阻塞 删除容器的调用

  - 备注：钩子程序的执行方式有“Exec”和“HTTP”两种。

- **容器探测**，容器探测分为存活性探测和就绪性探测容器探测是kubelet对容器健康状态进行诊断，容器探测的方式主要以下三种：
    - ExecAction：在容器中执行命令，根据返回的状态码判断容器健康状态，返回0即表示成功，否则为失败
    - TCPSocketAction: 通过与容器的某TCP端口尝试建立连接进行诊断，端口能打开即为表示成功，否则失败
    - HTTPGetAction：向容器指定 URL 发起 HTTP GET 请求，响应码为2xx或者是3xx为成功，否则失败

 

 ##### **Pod终止过程**

- 用户发出删除 pod 命令
- Pod 对象随着时间的推移更新，在宽限期（默认情况下30秒），pod 被视为“dead”状态
- 将 pod 标记为“Terminating”状态
- 第三步同时运行，监控到 pod 对象为“Terminating”状态的同时启动 pod 关闭过程
- 第三步同时进行，endpoints 控制器监控到 pod 对象关闭，将pod与service匹配的 endpoints 列表中删除
- 如果 pod 中定义了 preStop 钩子处理程序，则 pod 被标记为“Terminating”状态时以同步的方式启动执行；若宽限期结束后，preStop 仍未执行结束，第二步会重新执行并额外获得一个2秒的小宽限期
- Pod 内对象的容器收到 TERM 信号
- 宽限期结束之后，若存在任何一个运行的进程，pod 会收到 SIGKILL 信号
- Kubelet 请求 API Server 将此 Pod 资源宽限期设置为0从而完成删除操作

  

##### 创建service

- 通过Kubectl提交一个新的映射到该Pod的Service的创建请求
- ControllerManager会通过Label标签查询到相关联的Pod实例，然后生成Service的Endpoints信息，并通过APIServer写入到etcd中
- 所有Node上运行的Proxy进程通过APIServer查询并监听Service对象与其对应的Endpoints信息，建立一个软件方式的负载均衡器来实现Service访问到后端Pod的流量转发功能



#### 17、Pod状态

![title](/images/2021年春招准备-Kubernetes篇/16.png)



#### 18、声明式API

##### k8s API

Kubernetes 项目中，一个 API 对象在 Etcd 里的完整资源路径，是由：Group（API 组）、Version（API 版本）和 Resource（API 资源类型）三个部分组成的。

![title](/images/2021年春招准备-Kubernetes篇/18.png)

```yaml
如果现在要声明一个CronJob对象，那么YAML的开始部分会这么写

apiVersion: batch/v2alpha1
kind: CronJob
...
CronJob就是这个API对象的资源类型，Batch就是它们的组，v2alpha1就是它的版本
```

##### 命令式

**命令行式**

```shell
$ docker service create --name nginx --replicas 2  nginx
$ docker service update --image nginx:1.7.9 nginx
```

##### 命令式配置文件

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 2
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
        ports:
        - containerPort: 80
```

```shell
$ kubectl create -f nginx.yaml
```

创建出来一个Pod之后，更新容器镜像版本：修改yaml文件的Pod template部分

```yaml
...
    spec:
      containers:
      - name: nginx
        image: nginx:1.7.9
```

```shell
$ kubectl replace -f nginx.yaml
```



##### 声明式API

对于上述yaml文件，进行kubectl apply操作

```shell
$ kubectl apply -f nginx.yaml
```

同样的，修改过镜像版本后，执行kubectl apply，更新pod，触发滚动更新。

```shell
$ kubectl apply -f nginx.yaml
```

> kubelet apply和kubelet replace区别
>
> - replace 是使用新的yaml文件中的API对象，替换原有API对象
> - apply 是对原有API对象PATCH操作
>    此外，kubetcl set image, kubectl edit也是对原有API对象的修改
>    这就意味着，kube-apiserver在响应式命令请求的时候，一次只能处理一个，否则多个替换操作的结果就可能导致冲突，而**对于对于声明式请求来说，一次可以进行多个写操作，具备前面提到过的Merge能力**

> 所以什么是“声明式API”？
>  其实就是，通过给一个配置文件（期望的最终状态），然后**通过使用支持patch的命令模式，去叠加的演进对象的最终状态的这样一种做事情的方式**



- 声明式API，就是说我们来提供一个好的API对象进行声明，我们期望的状态是啥样子的

- 声明式API允许多个API对象写端，并在Kubernetes中支持以Patch的方式，对API对象进行修改以达到最终期望的实际状态，而无需关心最初的YAML文件的内容

- 基于以上两个能力特性（提供API明确的API对象来声明最终的期望状态+支持多个API对象声明并以PATCH的能力使控制器模型拿到的是最终的期望状态），Kubernetes能在无需外部干预的情况下，只要你给我明确的若干个API对象，我就能够给你无感调谐到你最终期望的状态



##### 应用：Istio

![title](/images/2021年春招准备-Kubernetes篇/19.png)

Istio项目的最根本组件，是运行在每个应用Pod中的**Envoy容器**。它以sidecar容器的方式，运行在每一个治理的应用Pod中。他的作用是通过配置Pod中的iptables规则，把整个Pod的进出流量接管下来。这样，Istio控制层的Pilot组件，就能够通过调用每个Envoy容器的API，对这个Envoy进行配置，实现微服务治理

**在整个微服务治理的过程中，无论是对Envoy的部署，还是对Envoy代理的配置，这些对于用户和应用都是完全“无感”的。**

 实现这种Envoy容器无感修改的技术，是Kubernetes中一个叫做Dynamic Admission Control的功能，这个功能也叫做Initializer。

> 后面就可以看到，Dynamic Admission Control，也就是Initilizer，其实也是一个容器起来的Pod。他的功能就是**将存在Etcd中的ConfigMap类型的关于Envoy容器的配置，和用户的Pod API对象通过Kubernetes的PATCH API做merge**

> 实际上，在Kubernetes项目中，每当一个Pod或者任何一个API对象通过命令等方式提交给APIServer之后，总要有一些“初始化”性质的工作需要他们被Kubernetes项目正式处理之前进行。这个操作，是一个叫做Admission的功能。这是Kubernetes中一段Admission Controller的代码，可以被选择性地变异进入API Server，在API对象创建后会被立刻调用。但是，如果Envoy的配置修改想用这个功能的话，那么每次都要动态的修改代码重新build并重启APIServer，显然这是不可接受的。所以就有了“热更新”方式的Admission机制，就是上面提到的动态Admission Control技术

具体到例子来分析，看看Istio的效果
 受限，用户有一个应用Pod的配置如下

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
    app: myapp
spec:
  containers:
  - name: myapp-container
    image: busybox
    command: ['sh', '-c', 'echo Hello Kubernetes! && sleep 3600']
```

Istio要做的事情，就是上面这个Pod被提交给Kubernetes之后，在对应的API对象中加上Envoy容器的配置（**这里就是无感了**），使API对象对应的yaml文件如下变成下面这样



```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
    app: myapp
spec:
  containers:
  - name: myapp-container
    image: busybox
    command: ['sh', '-c', 'echo Hello Kubernetes! && sleep 3600']
  - name: envoy
    image: lyft/envoy:845747b88f102c0fd262ab234308e9e22f693a1
    command: ["/usr/local/bin/envoy"]
    ...
```

**那么，对这个API对象无感的修改，是怎么通过Initializer来实现的呢？**

- Istio将这个Envoy容器本身的定义，以ConfigMap的方式，存在Etcd当中，这个ConfigMap（叫做envoy-initializer）的定义举例如下：

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: envoy-initializer
data:
  config: |
    containers:
      - name: envoy
        image: lyft/envoy:845747db88f102c0fd262ab234308e9e22f693a1
        command: ["/usr/local/bin/envoy"]
        args:
          - "--concurrency 4"
          - "--config-path /etc/envoy/envoy.json"
          - "--mode serve"
        ports:
          - containerPort: 80
            protocol: TCP
        resources:
          limits:
            cpu: "1000m"
            memory: "512Mi"
          requests:
            cpu: "100m"
            memory: "64Mi"
        volumeMounts:
          - name: envoy-conf
            mountPath: /etc/envoy
    volumes:
      - name: envoy-conf
        configMap:
          name: envoy
```

data的部分就是envoy容器的对应配置字段和volumes的配置。而Initializer的工作，就是把这个部分配置，**自动添加到用户的POD API对象中**。这就用到了Kubernetes的PATCH API，而这种patch操作，正是**声明式API**的主要能力。

- Istio把一个编写好的Initializer作为一个Pod部署在Kubernetes集群中。如下：

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    app: envoy-initializer
  name: envoy-initializer
spec:
  containers:
    - name: envoy-initializer
      image: envoy-initializer:0.0.1
      imagePullPolicy: Always
```

这里envoy-initializer的使用的镜像envoy-initializer:0.0.1就是一个自定义控制器（Custom Controller），是可以自己事先编写好的。
 而这个控制器同样也是遵循控制器模型的，他的实际状态是获取到的用户新建的Pod的配置情况，期望状态就是这个Pod里被加入了Envoy容器的定义。伪代码如下：

```go
for {
  // 获取新创建的 Pod
  pod := client.GetLatestPod()
  // Diff 一下，检查是否已经初始化过
  if !isInitialized(pod) {
    // 没有？那就来初始化一下
    doSomething(pod)
  }
}
```

如果获取到的Pod的API对象中，没有Envoy的容器的定义，那么就开始进行doSomething的初始化，具体做的事情就是前面提到过的，把Etcd中存储的那个关于Envoy容器配置的ConfigMap对象加到一个空的Pod里面去，然后调用Kubernetes的API库中TwoWayMerge
 Patch方法，把用户Pod对象和新Pod对象 merge。

- 至此，Istio对通过声明式API的Patch特性实现Dynamic Adminssion Control功能，进而支持Envoy容器在用户和应用侧无感的能力就实现了。

**Dynamic Admission Control的配置**

Dynamic Admission Control，即Initializer的相关配置（注意，这里不是指的Initializer本身，而是Initializer的配置文件）。
 我们可以通过配置这个配置对象，来指明对什么类型的资源进行Initialize的操作。比如下面这段代码，就是对全部的pods都做初始化操作。



```yaml
apiVersion: admissionregistration.k8s.io/v1alpha1
kind: InitializerConfiguration
metadata:
  name: envoy-config
initializers:
  // 这个名字必须至少包括两个 "."
  - name: envoy.initializer.kubernetes.io
    rules:
      - apiGroups:
          - "" // 前面说过， "" 就是 core API Group 的意思
        apiVersions:
          - v1
        resources:
          - pods
```

同时，只要这个对象一杯kubectl apply/create出来之后，Kubernetes就会把这个Initializer的名字（envoy.initializer）打在所有新创建Pod的metada上，如下：

```yaml
apiVersion: v1
kind: Pod
metadata:
  initializers:
    pending:
      - name: envoy.initializer.kubernetes.io
  name: myapp-pod
  labels:
    app: myapp
...
```

**这个字段，就是在Initializer的控制器模型中，根据什么区判断有没有被初始化过的依据，换言之，每当我们自定义的Initialzer在做完Initialize的操作之后，要把metadata.initializers.pending标识删除掉！！！**

除了Kubernetes在我们创建出来kind是InitializerConfiguration的对象后，会自动给所有新建的Pod都打这个标签之外，如果我们没有新建这个InitializerConfiguration对象，但是又想让我的某个Pod去使用某个Initializer的话，这么干：**给对应Pod打metadata.annotations字段**实例如下：

```yaml
apiVersion: v1
kind: Pod
metadata
  annotations:
    "initializer.kubernetes.io/envoy": "true"
    ...
```

而Istio项目的核心，就是用无数个运行在应用Pod中的Envoy容器组成的服务代理网格。这也是Service Mesh的含义。

> 总地来说，从上到下，灰度升级的切流场景->(Service Mesh)->Envoy们组成的服务代理网格->Envoy的实现->Kubernetes通过声明式API支持的Patch修改Pod的能力->声明式API



#### 19、CRD

> CRD允许用户在Kubernetes中添加一个跟Pod、Node类似的、新的API资源类型，即：自定义API资源

在 Kubernetes 中一切都可视为资源，Kubernetes 1.7 之后增加了对 CRD 自定义资源二次开发能力来扩展 Kubernetes API，通过 CRD 我们可以向 Kubernetes API 中增加新资源类型，而不需要修改 Kubernetes 源码来创建自定义的 API server，该功能大大提高了 Kubernetes 的扩展能力。

当你创建一个新的CustomResourceDefinition (CRD)时，Kubernetes API服务器将为你指定的每个版本创建一个新的RESTful资源路径，我们可以根据该api路径来创建一些我们自己定义的类型资源。CRD可以是命名空间的，也可以是集群范围的，由CRD的作用域(scpoe)字段中所指定的，与现有的内置对象一样，删除名称空间将删除该名称空间中的所有自定义对象。customresourcedefinition本身没有名称空间，所有名称空间都可以使用

在 Kubernetes 中我们使用的 Deployment， DamenSet，StatefulSet, Service，Ingress, ConfigMap, Secret 这些都是资源，而对这些资源的创建、更新、删除的动作都会被称为为事件(Event)，Kubernetes 的 Controller Manager 负责事件监听，并触发相应的动作来满足期望（Spec），这种方式也就是声明式，即用户只需要关心应用程序的最终状态。当我们在使用中发现现有的这些资源不能满足我们的需求的时候，Kubernetes 提供了自定义资源（Custom Resource）和 opertor 为应用程序提供基于 kuberntes 扩展。

**operator** 是一种 kubernetes 的扩展形式，利用自定义资源对象（Custom Resource）来管理应用和组件，允许用户以 Kubernetes 的声明式 API 风格来管理应用及服务。operator 定义了一组在 Kubernetes 集群中打包和部署复杂业务应用的方法，operator主要是为解决特定应用或服务关于如何运行、部署及出现问题时如何处理提供的一种特定的自定义方式

**operator-CRD**



#### 20、ConfigMap

在生产环境中经常会遇到需要修改配置文件的情况，传统的修改方式不仅会影响到服务的正常运行，而且操作步骤也很繁琐。为了解决这个问题，kubernetes项目从1.2版本引入了ConfigMap功能，**用于将应用的配置信息与程序的分离**。这种方式不仅可以实现应用程序被的复用，而且还可以通过不同的配置实现更灵活的功能。在创建容器时，**用户可以将应用程序打包为容器镜像后，通过环境变量或者外接挂载文件的方式进行配置注入**。
ConfigMap是以`key:value`的形式保存配置项，既可以用于表示一个变量的值（例如config=info），也可以用于表示一个完整配置文件的内容（例如server.xml=<?xml…>…）



##### configMap配置Deployment自动更新

- **ENV 是在容器启动的时候注入的，启动之后 kubernetes 就不会再改变环境变量的值**，且同一个 namespace 中的 pod 的环境变量是不断累加的。

- Volume不同，**kubelet的源码里KubeletManager中是有Volume Manager**的，这就说明Kubelet会监控管理每个Pod中的Volume资源，当发现配置的Volume更新后，就会重建Pod，以更新所用的Volume，但是会有一定延迟，大概10秒以内。

- Config Map一定触发Deployment滚动更新呢？可以通过修改 pod annotations 的方式强制触发滚动更新。这样一定会触发更新，亲测有效。

  ```javascript
   kubectl patch deployment test-deploy --patch '{"spec": {"template": {"metadata": {"annotations": {"update": "2" }}}}}'
  ```

- [configmap-reload](https://link.segmentfault.com/?url=https%3A%2F%2Fgithub.com%2Fiyacontrol%2Fconfigmap-reload) 采用rust语言实现，作为主业务容器的sidercar，主要用于k8s当中监听configmap的变化，待变化后通过http接口的方式通知主业务。在资源消耗上，更小



#### 21、部署Kubernetes(k8s)时，为什么要关闭swap、selinux、防火墙

关于防火墙的原因（nftables后端兼容性问题，产生重复的防火墙规则）

> The`iptables`tooling can act as a compatibility layer, behaving like iptables but actually configuring nftables. This nftables backend is not compatible with the current kubeadm packages: it causes duplicated firewall rules and breaks`kube-proxy`.

关于selinux的原因（关闭selinux以允许容器访问宿主机的文件系统）

> Setting SELinux in permissive mode by running`setenforce 0`and`sed ...`effectively disables it. This is required to allow containers to access the host filesystem, which is needed by pod networks for example. You have to do this until SELinux support is improved in the kubelet.

关闭swap(一个node性能不足时应该立即迁移到性能足够的node上，不能让swap拖慢进度)

所以关闭swap主要是为了性能考虑。

当然为了一些节省资源的场景，比如运行容器数量较多，可添加kubelet参数 `--fail-swap-on=false`来解决。

