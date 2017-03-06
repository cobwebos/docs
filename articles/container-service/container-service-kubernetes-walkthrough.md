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
ms.date: 02/21/2017
ms.author: anhowe
translationtype: Human Translation
ms.sourcegitcommit: 94a3481d4598701de583969554ed501d2da6dc45
ms.openlocfilehash: 8282a9d34b8f4aa8db193bb13ba4008137d18e5a
ms.lasthandoff: 03/01/2017


---

# <a name="azure-container-service---kubernetes-walkthrough"></a>Azure 容器服务 - Kubernetes 演练


本文中的说明介绍如何使用 Azure CLI 2.0 命令来创建 Kubernetes 群集。 然后，可以使用 `kubectl` 命令行工具来开始使用群集中的容器。

下图显示了具有一个主机、两个代理的容器服务群集的体系结构。 主节点提供 Kubernetes REST API。 代理节点分组在 Azure 可用性集中，运行用户的容器。 所有 VM 在相同的专用虚拟网络中，并完全可以相互访问。

![Azure 上的 Kubernetes 群集映像](media/container-service-kubernetes-walkthrough/kubernetes.png)

## <a name="prerequisites"></a>先决条件
本演练假定用户已安装并设置 [Azure CLI v.2.0](/cli/azure/install-az-cli2)。 还必须在 `~/.ssh/id_rsa.pub` 处具有 SSH RSA 公钥。 如果没有该公钥，请参阅用于 [OS X 和 Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md) 或 [Windows](../virtual-machines/virtual-machines-linux-ssh-from-windows.md) 的步骤。






## <a name="create-your-kubernetes-cluster"></a>创建 Kubernetes 群集

以下是使用 Azure CLI 2.0 创建群集的简要 shell 命令。 有关详细信息，请参阅[使用 Azure CLI 2.0 创建 Azure 容器服务群集](container-service-create-acs-cluster-cli.md)。

### <a name="create-a-resource-group"></a>创建资源组
若要创建群集，首先需在特定位置创建一个资源组。 运行类似于以下内容的命令：

```console
RESOURCE_GROUP=my-resource-group
LOCATION=westus
az group create --name=$RESOURCE_GROUP --location=$LOCATION
```

### <a name="create-a-cluster"></a>创建群集
有了资源组之后，即可在该组中创建群集：

```console
DNS_PREFIX=some-unique-value
CLUSTER_NAME=any-acs-cluster-name
az acs create --orchestrator-type=kubernetes --resource-group $RESOURCE_GROUP --name=$CLUSTER_NAME --dns-prefix=$DNS_PREFIX
```

> [!NOTE]
> 在部署期间，CLI 将 `~/.ssh/id_rsa.pub` 上传到 Linux VM。
>

该命令执行完以后，你应该有一个工作的 Kubernetes 群集。

### <a name="connect-to-the-cluster"></a>连接至群集

以下 Azure CLI 命令通过使用 Kubernetes 命令行客户端 `kubectl`，从客户端计算机连接到 Kubernetes 群集。 有关详细信息，请参阅[连接到 Azure 容器服务群集](container-service-connect.md)。

如果尚未安装 `kubectl`，可通过以下方式安装：

```console
az acs kubernetes install-cli
```

安装 `kubectl` 以后，运行以下命令即可将主 Kubernetes 群集配置下载到 ~/.kube/config 文件：

```console
az acs kubernetes get-credentials --resource-group=$RESOURCE_GROUP --name=$CLUSTER_NAME
```

此时应可从计算机访问群集。 可尝试运行：
```console
kubectl get nodes
```

验证是否可以在群集中看到计算机列表。

## <a name="create-your-first-kubernetes-service"></a>创建第一个 Kubernetes 服务

完成本演练后，用户将知道如何：
 * 部署 Docker 应用程序并向世界公开
 * 使用 `kubectl exec` 在容器中运行命令 
 * 访问 Kubernetes 仪表板

### <a name="start-a-simple-container"></a>启动一个简单的容器
可以通过运行以下语句来运行简单的容器（本示例中为 Nginx Web 服务器）：

```console
kubectl run nginx --image nginx
```

此命令将启动其中一个节点上 pod 中的 Nginx Docker 容器。

若要查看正在运行的容器，请运行：

```console
kubectl get pods
```

### <a name="expose-the-service-to-the-world"></a>向世界公开该服务
若要在全球公开该服务，请创建 `LoadBalancer` 类型的 Kubernetes `Service`：

```console
kubectl expose deployments nginx --port=80 --type=LoadBalancer
```

这将导致 Kubernetes 创建具有公用 IP 地址的 Azure 负载均衡器规则。 更改传播到负载均衡器需要几分钟时间。 有关详细信息，请参阅[对 Azure 容器服务中 Kubernetes 群集内的容器进行负载均衡](container-service-kubernetes-load-balancing.md)。

运行以下命令来监视来自 `pending` 的服务更改，以显示外部 IP 地址：

```console
watch 'kubectl get svc'
```

  ![图像：观看从挂起到外部 IP 地址的转换](media/container-service-kubernetes-walkthrough/kubernetes-nginx3.png)

看到外部 IP 地址后，可以在浏览器中浏览到它：

  ![浏览到 Nginx 的映像](media/container-service-kubernetes-walkthrough/kubernetes-nginx4.png)  


### <a name="browse-the-kubernetes-ui"></a>浏览 Kubernetes UI
若要查看 Kubernetes Web 界面，可以使用：

```console
kubectl proxy
```
此命令在 localhost 上运行简单的经过身份验证的代理，以便查看 [Kubernetes Web UI](http://localhost:8001/ui)。 有关详细信息，请参阅 [在 Azure 容器服务中使用 Kubernetes Web UI](container-service-kubernetes-ui.md)。

![Kubernetes 仪表板的映像](media/container-service-kubernetes-walkthrough/kubernetes-dashboard.png)

### <a name="remote-sessions-inside-your-containers"></a>在容器内的远程会话
Kubernetes 允许用户在远程 Docker 容器（在群集中运行）内运行命令。

```console
# Get the name of your nginx pods
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

![容器内的远程会话](media/container-service-kubernetes-walkthrough/kubernetes-remote.png)



## <a name="next-steps"></a>后续步骤

若要使用 Kubernetes 群集完成更多操作，请参阅以下资源：

* [Kubernetes 训练营](https://katacoda.com/embed/kubernetes-bootcamp/1/) - 介绍如何部署、伸缩、更新和调试容器化应用程序。
* [Kubernetes 用户指南](http://kubernetes.io/docs/user-guide/) - 介绍如何在现有 Kubernetes 群集中运行程序。
* [Kubernetes 示例](https://github.com/kubernetes/kubernetes/tree/master/examples) - 提供有关如何使用 Kubernetes 运行实际应用程序的示例。

