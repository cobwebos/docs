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
ms.date: 05/08/2017
ms.author: anhowe
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 2ec155129374c03ba7e0ecaa5d2bf29a1d3111aa
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017

---

# <a name="get-started-with-a-kubernetes-cluster-in-container-service"></a>容器服务中的 Kubernetes 群集入门


本演练介绍如何使用 Azure CLI 2.0 命令在 Azure 容器服务中创建 Kubernetes 群集。 然后，可以使用 `kubectl` 命令行工具来开始使用群集中的容器。

下图显示了一个容器服务群集的体系结构，其中具有一个 Linux 主节点和两个 Linux 代理节点。 主节点提供 Kubernetes REST API。 代理节点分组在 Azure 可用性集中，运行用户的容器。 所有 VM 在相同的专用虚拟网络中，并完全可以相互访问。

![Azure 上的 Kubernetes 群集映像](media/container-service-kubernetes-walkthrough/kubernetes.png)

如需更多背景知识，请参阅 [Azure 容器服务简介](container-service-intro.md)和 [Kubernetes documentation](https://kubernetes.io/docs/home/)（Kubernetes 文档）。

## <a name="prerequisites"></a>先决条件
要使用 Azure CLI 2.0 创建 Azure 容器服务群集，用户必须：
* 具有一个 Azure 帐户（[获取免费试用版](https://azure.microsoft.com/pricing/free-trial/)）
* 已安装并设置 [Azure CLI 2.0](/cli/azure/install-az-cli2)

另外，你需要（或者可以使用 Azure CLI 在部署群集期间自动生成）以下项：

* **SSH RSA 公钥**：若要提前创建安全外壳 (SSH) RSA 密钥，请参阅 [macOS 和 Linux](../virtual-machines/linux/mac-create-ssh-keys.md) 或 [Windows](../virtual-machines/linux/ssh-from-windows.md) 指南。 

* **服务主体客户端 ID 和机密**：有关如何创建 Azure Active Directory 服务主体的步骤和其他信息，请参阅[关于 Kubernetes 群集的服务主体](container-service-kubernetes-service-principal.md)。

 本文中的命令示例自动生成 SSH 密钥和服务主体。

## <a name="create-your-kubernetes-cluster"></a>创建 Kubernetes 群集

下面是使用 Azure CLI 2.0 创建群集的简要 bash shell 命令。 

### <a name="create-a-resource-group"></a>创建资源组
若要创建群集，首先需要在[提供了](https://azure.microsoft.com/regions/services/) Azure 容器服务的位置创建资源组。 运行类似于以下内容的命令：

```azurecli
RESOURCE_GROUP=my-resource-group
LOCATION=westus
az group create --name=$RESOURCE_GROUP --location=$LOCATION
```

### <a name="create-a-cluster"></a>创建群集
使用 `az acs create` 命令与 `--orchestrator-type=kubernetes`，在资源组中创建 Kubernetes 群集。 有关命令语法，请参阅 `az acs create` [帮助](/cli/azure/acs#create)。

此版本的命令自动为 Kubernetes 群集生成 SSH RSA 密钥和服务主体。



```azurecli
DNS_PREFIX=some-unique-value
CLUSTER_NAME=any-acs-cluster-name
az acs create --orchestrator-type=kubernetes --resource-group $RESOURCE_GROUP --name=$CLUSTER_NAME --dns-prefix=$DNS_PREFIX --generate-ssh-keys
```

数分钟后，命令完成，你会有一个可以运行的 Kubernetes 群集。

> [!IMPORTANT]
> 如果帐户没有创建 Azure AD 服务主体的权限，该命令会生成类似于`Insufficient privileges to complete the operation.`的错误。有关详细信息，请参阅[关于 Kubernetes 群集的服务主体](container-service-kubernetes-service-principal.md)。
> 



### <a name="connect-to-the-cluster"></a>连接至群集

若要从客户端计算机连接到 Kubernetes 群集，请使用 Kubernetes 命令行客户端 [`kubectl`](https://kubernetes.io/docs/user-guide/kubectl/)。 

如果尚未安装 `kubectl`，可通过 `az acs kubernetes install-cli` 进行安装。 （也可从 [Kubernetes 站点](https://kubernetes.io/docs/tasks/kubectl/install/)下载。）

```azurecli
sudo az acs kubernetes install-cli
```

> [!TIP]
> 默认情况下，此命令将 `kubectl` 二进制文件安装到 Linux 或 macOS 系统上的 `/usr/local/bin/kubectl`，或者安装到 Windows 上的 `C:\Program Files (x86)\kubectl.exe`。 若要指定其他安装路径，请使用 `--install-location` 参数。
>
> 安装 `kubectl` 后，请确保其目录位于系统路径中，否则请将其添加到该路径。 
>


然后运行以下命令，将主 Kubernetes 群集配置下载到 `~/.kube/config` 文件：

```azurecli
az acs kubernetes get-credentials --resource-group=$RESOURCE_GROUP --name=$CLUSTER_NAME
```

有关安装和配置 `kubectl` 的更多选项，请参阅[连接到 Azure 容器服务群集](container-service-connect.md)。

此时应可从计算机访问群集。 可尝试运行：

```bash
kubectl get nodes
```

验证是否可以在群集中看到计算机列表。

## <a name="create-your-first-kubernetes-service"></a>创建第一个 Kubernetes 服务

完成本演练后，用户将知道如何：
* 部署 Docker 应用程序并向世界公开
* 使用 `kubectl exec` 在容器中运行命令 
* 访问 Kubernetes 仪表板

### <a name="start-a-container"></a>启动容器
可以通过运行以下语句来运行容器（本示例中为 Nginx Web 服务器）：

```bash
kubectl run nginx --image nginx
```

此命令将启动其中一个节点上 pod 中的 Nginx Docker 容器。

若要查看正在运行的容器，请运行：

```bash
kubectl get pods
```

### <a name="expose-the-service-to-the-world"></a>向世界公开该服务
若要在全球公开该服务，请创建 `LoadBalancer` 类型的 Kubernetes `Service`：

```bash
kubectl expose deployments nginx --port=80 --type=LoadBalancer
```

此命令将导致 Kubernetes 创建具有公用 IP 地址的 Azure 负载均衡器规则。 更改传播到负载均衡器需要几分钟时间。 有关详细信息，请参阅[对 Azure 容器服务中 Kubernetes 群集内的容器进行负载均衡](container-service-kubernetes-load-balancing.md)。

运行以下命令来监视来自 `pending` 的服务更改，以显示外部 IP 地址：

```bash
watch 'kubectl get svc'
```

  ![图像：观看从挂起到外部 IP 地址的转换](media/container-service-kubernetes-walkthrough/kubernetes-nginx3.png)

看到外部 IP 地址后，可以在浏览器中浏览到它：

  ![浏览到 Nginx 的映像](media/container-service-kubernetes-walkthrough/kubernetes-nginx4.png)  


### <a name="browse-the-kubernetes-ui"></a>浏览 Kubernetes UI
若要查看 Kubernetes Web 界面，可以使用：

```bash
kubectl proxy
```
此命令在 localhost 上运行经过身份验证的代理，以便查看在 [http://localhost:8001/ui](http://localhost:8001/ui) 上运行的 Kubernetes Web UI。 有关详细信息，请参阅 [在 Azure 容器服务中使用 Kubernetes Web UI](container-service-kubernetes-ui.md)。

![Kubernetes 仪表板的映像](media/container-service-kubernetes-walkthrough/kubernetes-dashboard.png)

### <a name="remote-sessions-inside-your-containers"></a>在容器内的远程会话
Kubernetes 允许用户在远程 Docker 容器（在群集中运行）内运行命令。

```bash
# Get the name of your nginx pods
kubectl get pods
```

使用 pod 名称，可以在 pod 上运行远程命令。 例如：

```bash
kubectl exec <pod name> date
```

也可使用 `-it` 标志获取完全交互式会话：

```bash
kubectl exec <pod name> -it bash
```

![容器内的远程会话](media/container-service-kubernetes-walkthrough/kubernetes-remote.png)



## <a name="next-steps"></a>后续步骤

若要使用 Kubernetes 群集完成更多操作，请参阅以下资源：

* [Kubernetes 训练营](https://katacoda.com/embed/kubernetes-bootcamp/1/) - 介绍如何部署、伸缩、更新和调试容器化应用程序。
* [Kubernetes 用户指南](http://kubernetes.io/docs/user-guide/) - 介绍如何在现有 Kubernetes 群集中运行程序。
* [Kubernetes 示例](https://github.com/kubernetes/kubernetes/tree/master/examples) - 提供有关如何使用 Kubernetes 运行实际应用程序的示例。

