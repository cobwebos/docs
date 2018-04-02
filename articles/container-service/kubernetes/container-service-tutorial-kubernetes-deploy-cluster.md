---
title: Azure 容器服务教程 - 部署群集
description: Azure 容器服务教程 - 部署群集
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 09/14/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: cb7bdbaf095cefa72d1de302d90afeebbdb9d027
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2018
---
# <a name="deploy-a-kubernetes-cluster-in-azure-container-service"></a>在 Azure 容器服务中部署 Kubernetes 群集

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

Kubernetes 为容器化应用程序提供一个分布式平台。 通过 Azure 容器服务，使预配生产就绪的 Kubernetes 群集变得简单快捷。 在本教程的第 3 部分（共 7 部分），便成功部署了一个 Azure 容器服务 Kubernetes 群集。 已完成的步骤包括：

> [!div class="checklist"]
> * 部署 Kubernetes ACS 群集
> * Kubernetes CLI (kubectl) 的安装
> * Kubectl 的配置

在后续教程中，将向该群集部署 Azure Vote 应用程序并对该应用程序进行缩放和更新，还会将 Log Analytics 配置为监视 Kubernetes 群集。

## <a name="before-you-begin"></a>开始之前

在以前的教程中，已创建容器映像并上传到 Azure 容器注册表实例。 如果尚未完成这些步骤，并且想要逐一完成，请返回到[教程 1 – 创建容器映像](./container-service-tutorial-kubernetes-prepare-app.md)。

## <a name="create-kubernetes-cluster"></a>创建 Kubernetes 群集

使用 [az acs create](/cli/azure/acs#az_acs_create) 命令在 Azure 容器服务中创建 Kubernetes 群集。 

下面的示例在 `myResourceGroup` 资源组中创建 `myK8sCluster` 群集。 此资源组是在[上一教程](./container-service-tutorial-kubernetes-prepare-acr.md)中进行创建。

```azurecli-interactive 
az acs create --orchestrator-type kubernetes --resource-group myResourceGroup --name myK8SCluster --generate-ssh-keys 
```

在某些情况下（如限时试用），Azure 订阅对 Azure 资源的访问受限。 如果由于可用核心有限而导致部署失败，请将 `--agent-count 1` 添加到 [az acs creat](/cli/azure/acs#az_acs_create) 命令中，以减少默认代理计数。 

几分钟后，部署完成并返回有关 ACS 部署的 JSON 格式信息。

## <a name="install-the-kubectl-cli"></a>安装 kubectl CLI

若要从客户端计算机连接到 Kubernetes 群集，请使用 [kubectl](https://kubernetes.io/docs/user-guide/kubectl/)（Kubernetes 命令行客户端）。 

如果使用的是 Azure CloudShell，则 kubectl 已安装。 如果想在本地安装，请使用 [az acs kubernetes install-cli](/cli/azure/acs/kubernetes#install-cli) 命令。

如果在 Linux 或 macOS 中运行，则可能需要使用 sudo 进行运行。 在 Windows 上，确保以管理员身份运行 shell。

```azurecli-interactive 
az acs kubernetes install-cli 
```

在 Windows 上，默认安装是 *c:\program files (x86)\kubectl.exe*。 可能需要将此文件添加到 Windows 路径。 

## <a name="connect-with-kubectl"></a>连接 kubectl

若要配置 kubectl 以连接到 Kubernetes 群集，请运行 [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes#get-credentials) 命令。

```azurecli-interactive 
az acs kubernetes get-credentials --resource-group myResourceGroup --name myK8SCluster
```

若要验证与群集之间的连接，请运行 [kubectl get nodes](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) 命令。

```azurecli-interactive
kubectl get nodes
```

输出：

```bash
NAME                    STATUS                     AGE       VERSION
k8s-agent-98dc3136-0    Ready                      5m        v1.6.2
k8s-agent-98dc3136-1    Ready                      5m        v1.6.2
k8s-agent-98dc3136-2    Ready                      5m        v1.6.2
k8s-master-98dc3136-0   Ready,SchedulingDisabled   5m        v1.6.2
```

教程完成时，ACS Kubernetes 群集已准备就绪，可用于工作负荷。 在后续教程中，一个多容器应用程序会部署到此群集、进行扩大、更新以及受到监视。

## <a name="next-steps"></a>后续步骤

在本教程中，部署了一个 Azure 容器服务 Kubernetes 群集。 已完成以下步骤：

> [!div class="checklist"]
> * 部署 Kubernetes ACS 群集
> * 安装 Kubernetes CLI (kubectl)
> * 配置 kubectl

继续学习下一个教程，了解如何在群集上运行应用程序。

> [!div class="nextstepaction"]
> [在 Kubernetes 中部署应用程序](./container-service-tutorial-kubernetes-deploy-application.md)
