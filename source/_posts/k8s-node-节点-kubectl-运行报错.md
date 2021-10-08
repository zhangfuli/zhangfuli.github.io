---
title: k8s node 节点 kubectl 运行报错
date: 2019-11-18 15:41:12
tags:
---



###背景描述：



使用kubeadm搭建的集群环境，node节点 使用 kubectl查询资源报错

```
The connection to the server localhost:8080 was refused - did you specify the right host or port?
```



### 解决方案

1、 出现这个问题的原因是kubectl命令需要使用kubernetes-admin来运行，解决方法如下，将master节点中的**/etc/kubernetes/admin.conf**文件拷贝到从节点相同目录下，然后配置环境变量：

```
echo "export KUBECONFIG=/etc/kubernetes/admin.conf" >> ~/.bash_profile
```

2、使其生效

```
source ~/.bash_profile
```

