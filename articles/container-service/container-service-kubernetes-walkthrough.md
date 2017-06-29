---
title: "快速入门 - 适用于 Linux 的 Azure Kubernetes 群集 | Microsoft Docs"
description: "快速学习在 Azure 容器服务中使用 Azure CLI 为 Linux 容器创建 Kubernetes 群集。"
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
ms.date: 05/31/2017
ms.author: anhowe
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: 25043f6bf5e5ab3def8563bd2c096b79706bfec1
ms.contentlocale: zh-cn
ms.lasthandoff: 06/20/2017

---

# <a name="deploy-kubernetes-cluster-for-linux-containers"></a>为 Linux 容器部署 Kubernetes 群集

Azure CLI 用于从命令行或脚本创建和管理 Azure 资源。 本指南详细介绍如何在 [Azure 容器服务](container-service-intro.md)中使用 Azure CLI 部署 [Kubernetes](https://kubernetes.io/docs/home/) 群集。 部署群集后，使用 Kubernetes `kubectl` 命令行工具连接到群集，并部署第一个 Linux 容器。

本教程需要 Azure CLI 2.0.4 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果你还没有 Azure 订阅，可以在开始前创建一个[免费](https://azure.microsoft.com/free/)帐户。

## <a name="log-in-to-azure"></a>登录 Azure 

使用 [az login](/cli/azure/#login) 命令登录到 Azure 订阅，并按照屏幕上的说明进行操作。

```azurecli-interactive
az login
```

## <a name="create-a-resource-group"></a>创建资源组

使用 [az group create](/cli/azure/group#create) 命令创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑组。 

以下示例在“eastus”位置创建名为“myResourceGroup”的资源组。

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-kubernetes-cluster"></a>创建 Kubernetes 群集
使用 [az acs create](/cli/azure/acs#create) 命令在 Azure 容器服务中创建 Kubernetes 群集。 

以下示例创建名为 myK8sCluster 的群集，其中包含一个 Linux 主节点和两个 Linux 代理节点。 本示例创建 SSH 密钥（如果默认位置中不存在的话）。 若要使用特定的一组密钥，请使用 `--ssh-key-value` 选项。 更新群集名称，使其适应环境。 



```azurecli-interactive 
az acs create --orchestrator-type=kubernetes \
    --resource-group myResourceGroup \
    --name=myK8sCluster \
    --agent-count=2 \
    --generate-ssh-keys 
```

几分钟后，该命令完成并显示有关部署的信息。

## <a name="install-kubectl"></a>安装 kubectl

若要从客户端计算机连接到 Kubernetes 群集，请使用 Kubernetes 命令行客户端 [`kubectl`](https://kubernetes.io/docs/user-guide/kubectl/)。 

如果使用 Azure CloudShell，则 `kubectl` 已安装。 如果想在本地安装，可以使用 [az acs kubernetes install-cli](/cli/azure/acs/kubernetes#install-cli) 命令。

以下 Azure CLI 示例向系统安装 `kubectl`。 如果在 macOS 或 Linux 上运行 Azure CLI，可能需要将该命令与 `sudo` 一起运行。

```azurecli-interactive 
az acs kubernetes install-cli 
```

## <a name="connect-with-kubectl"></a>连接 kubectl

若要配置 `kubectl` 以连接到 Kubernetes 群集，请运行 [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes#get-credentials) 命令。 下面的示例下载 Kubernetes 群集的群集配置。

```azurecli-interactive 
az acs kubernetes get-credentials --resource-group=myResourceGroup --name=myK8sCluster
```

若要验证从计算机到群集的连接，请尝试运行：

```azurecli-interactive
kubectl get nodes
```

`kubectl` 列出主节点和代理节点。

```azurecli-interactive
NAME                    STATUS                     AGE       VERSION
k8s-agent-98dc3136-0    Ready                      5m        v1.5.3
k8s-agent-98dc3136-1    Ready                      5m        v1.5.3
k8s-master-98dc3136-0   Ready,SchedulingDisabled   5m        v1.5.3

```


## <a name="deploy-an-nginx-container"></a>部署 NGINX 容器

可在包含一个或多个容器的 Kubernetes pod 内运行 Docker 容器。 

下面的命令将启动其中一个节点上 Kubernetes pod 中的 NGINX Docker 容器。 在此情况下，容器会运行从 [Docker 中心](https://hub.docker.com/_/nginx/)的一个映像中拉取的 NGINX Web 服务器。

```azurecli-interactive
kubectl run nginx --image nginx
```
若要查看容器是否正在运行，请运行：

```azurecli-interactive
kubectl get pods
```

## <a name="view-the-nginx-welcome-page"></a>查看 NGINX 欢迎页
若要使用公共 IP 地址公开 NGINX 服务器，请键入以下命令：

```azurecli-interactive
kubectl expose deployments nginx --port=80 --type=LoadBalancer
```

通过使用此命令，Kubernetes 创建服务，并使用该服务公共 IP 地址创建 [Azure 负载均衡器规则](container-service-kubernetes-load-balancing.md)。 

运行以下命令，以便查看服务的状态。

```azurecli-interactive
kubectl get svc
```

IP 地址最初显示为 `pending`。 几分钟后，将设置服务的外部 IP 地址：
  
```azurecli-interactive
NAME         CLUSTER-IP     EXTERNAL-IP     PORT(S)        AGE       
kubernetes   10.0.0.1       <none>          443/TCP        21h       
nginx        10.0.111.25    52.179.3.96     80/TCP         22m
```

可以使用所选的 Web 浏览器在外部 IP 地址查看默认 NGINX 欢迎页：

![浏览到 Nginx 的映像](media/container-service-kubernetes-walkthrough/kubernetes-nginx4.png)  


## <a name="delete-cluster"></a>删除群集
如果不再需要群集，可以使用 [az group delete](/cli/azure/group#delete) 命令删除资源组、容器服务及所有相关资源。

```azurecli-interactive 
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>后续步骤

在此快速入门中，已部署了 Kubernetes 群集、使用 `kubectl` 进行连接并部署了具有 NGINX 容器的 pod。 若要详细了解 Azure 容器服务，请继续学习 Kubernetes 群集教程。

> [!div class="nextstepaction"]
> [管理 ACS Kubernetes 群集](./container-service-tutorial-kubernetes-prepare-app.md)

