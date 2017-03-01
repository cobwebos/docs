---
title: "Azure 中的 Kubernetes 群集快速入门 | Microsoft 文档"
description: "在 Azure 容器服务中部署 Kubernetes 群集并开始使用"
services: container-service
documentationcenter: 
author: anhowe
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 8da267e8-2aeb-4c24-9a7a-65bdca3a82d6
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2016
ms.author: anhowe
translationtype: Human Translation
ms.sourcegitcommit: 7ed8fb75f057d5a7cfde5436e72e8fec52d07156
ms.openlocfilehash: f3b2fc301bf7083f192c0ec872c4e032472eef97


---

# <a name="microsoft-azure-container-service-engine---kubernetes-walkthrough"></a>Microsoft Azure 容器服务引擎 - Kubernetes 演练

## <a name="prerequisites"></a>先决条件
本演练假定用户已安装[“azure-cli”命令行工具](https://github.com/azure/azure-cli#installation)并已在 `~/.ssh/id_rsa.pub` 创建 [SSH 公钥](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md)。

## <a name="overview"></a>概述

以下说明将创建一个 Kubernetes 群集，使用一个主节点和两个辅助节点。
主节点提供 Kubernetes REST API。  辅助节点分组在 Azure 可用性集中，运行用户的容器。 所有 VM 在相同的专用 VNET 中并完全可以相互访问。

> [!NOTE]
> Azure 容器服务中的 Kubernetes 支持当前为预览版。
>

下图显示了具有一个主机、两个代理的容器服务群集的体系结构：

![Azure 上的 Kubernetes 群集映像](media/container-service-kubernetes-walkthrough/kubernetes.png)

## <a name="creating-your-kubernetes-cluster"></a>创建 Kubernetes 群集

### <a name="create-a-resource-group"></a>创建资源组
若要创建群集，首先需在特定位置创建一个资源组，所用方法是：
```console
RESOURCE_GROUP=my-resource-group
LOCATION=westus
az group create --name=$RESOURCE_GROUP --location=$LOCATION
```

### <a name="create-a-cluster"></a>创建群集
有了资源组之后，即可在该组中使用以下方式创建群集：
```console
DNS_PREFIX=some-unique-value
SERVICE_NAME=any-acs-service-name
az acs create --orchestrator-type=kubernetes --resource-group $RESOURCE_GROUP --name=$SERVICE_NAME --dns-prefix=$DNS_PREFIX
```

> [!NOTE]
> azure-cli 会将 `~/.ssh/id_rsa.pub` 上载到 Linux VM。
>

该命令执行完以后，你应该有一个工作的 Kubernetes 群集。

### <a name="configure-kubectl"></a>配置 kubectl
`kubectl` 是 Kubernetes 命令行客户端。  如果尚未安装，可通过以下方式安装：

```console
az acs kubernetes install-cli
```

安装 `kubectl` 以后，运行以下命令就会将主 kubernetes 群集配置下载到 ~/.kube/config 文件
```console
az acs kubernetes get-credentials --resource-group=$RESOURCE_GROUP --name=$SERVICE_NAME
```

此时应可从计算机访问群集，可尝试运行：
```console
kubectl get nodes
```

另外，请验证是否可以在群集中看到计算机。

## <a name="create-your-first-kubernetes-service"></a>创建第一个 Kubernetes 服务

完成本演练后，用户将知道如何：
 * 部署 Docker 应用程序并向世界公开，
 * 使用 `kubectl exec` 在容器中运行命令， 
 * 并访问 Kubernetes 仪表板。

### <a name="start-a-simple-container"></a>启动一个简单的容器
可以通过运行以下语句来运行简单的容器（本示例中为 `nginx` Web 服务器）：

```console
kubectl run nginx --image nginx
```

此命令将启动其中一个节点上 pod 中的 nginx Docker 容器。

你可以运行
```console
kubectl get pods
```

来查看正在运行的容器。

### <a name="expose-the-service-to-the-world"></a>向世界公开该服务
若要向世界公开该服务，  可创建一个 Kubernetes `Service`，其类型为 `LoadBalancer`：

```console
kubectl expose deployments nginx --port=80 --type=LoadBalancer
```

这将立即导致 Kubernetes 创建具有公共 IP 的 Azure Load Balancer。 更改传播到负载均衡器需要大约 2-3 分钟。

若要观看服务从“挂起”更改为外部 IP 类型，请执行以下语句：
```console
watch 'kubectl get svc'
```

  ![观看从挂起到外部 IP 的转换的映像](media/container-service-kubernetes-walkthrough/kubernetes-nginx3.png)

看到外部 IP 后，可以在浏览器中浏览到它：

  ![浏览到 nginx 的映像](media/container-service-kubernetes-walkthrough/kubernetes-nginx4.png)  


### <a name="browse-the-kubernetes-ui"></a>浏览 Kubernetes UI
若要查看 Kubernetes Web 界面，可以使用：

```console
kubectl proxy
```
此命令在 localhost 上运行简单的经过身份验证的代理，以便查看 [Kubernetes UI](http://localhost:8001/ui)

![Kubernetes 仪表板的映像](media/container-service-kubernetes-walkthrough/kubernetes-dashboard.png)

### <a name="remote-sessions-inside-your-containers"></a>在容器内的远程会话
Kubernetes 允许用户在远程 Docker 容器（在群集中运行）内运行命令。

```console
# Get the name of your nginx pod
kubectl get pods
```

使用 pod 名称，可以在 pod 上运行远程命令。  例如：
```console
kubectl exec nginx-701339712-retbj date
```

也可使用 `-it` 标志获取完全交互式会话：

```console
kubectl exec nginx-701339712-retbj -it bash
```

![Curl 到 podIP 的映像](media/container-service-kubernetes-walkthrough/kubernetes-remote.png)


## <a name="details"></a>详细信息
### <a name="installing-the-kubectl-configuration-file"></a>安装 kubectl 配置文件
运行 `az acs kubernetes get-credentials` 时，用于远程访问的 kube 配置文件存储在主目录 ~/.kube/config 下。

如果需要直接下载该文件，可以在 Linux 或 OS X 上使用 `ssh`，或者在 Windows 上使用 `Putty`：

#### <a name="windows"></a>Windows
使用 [putty](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) 中的 pscp。  确保证书通过[ pageant ](https://github.com/Azure/acs-engine/blob/master/docs/ssh.md#key-management-and-agent-forwarding-with-windows-pageant)公开：
  ```
  # MASTERFQDN is obtained in step1
  pscp azureuser@MASTERFQDN:.kube/config .
  SET KUBECONFIG=%CD%\config
  kubectl get nodes
  ```

#### <a name="os-x-or-linux"></a>OS X 或 Linux：
  ```
  # MASTERFQDN is obtained in step1
  scp azureuser@MASTERFQDN:.kube/config .
  export KUBECONFIG=`pwd`/config
  kubectl get nodes
  ```
## <a name="learning-more"></a>了解更多

### <a name="azure-container-service"></a>Azure 容器服务

1. [Azure 容器服务文档](https://azure.microsoft.com/en-us/documentation/services/container-service/)
2. [Azure 容器服务开源引擎](https://github.com/azure/acs-engine)

### <a name="kubernetes-community-documentation"></a>Kubernetes 社区文档

1. [Kubernetes 训练营](https://katacoda.com/embed/kubernetes-bootcamp/1/) - 介绍如何部署、伸缩、更新和调试容器化应用程序。
2. [Kubernetes 用户指南](http://kubernetes.io/docs/user-guide/) - 介绍如何在现有 Kubernetes 群集中运行程序。
3. [Kubernetes 示例](https://github.com/kubernetes/kubernetes/tree/master/examples) -提供有关如何使用 Kubernetes 运行实际应用程序的一些示例。



<!--HONumber=Jan17_HO4-->


