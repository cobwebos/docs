---
title: "Kubernetes on Azure 教程 - 部署群集"
description: "AKS 教程 - 部署群集"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 11/15/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: e0d5bd57a40fca837ead42e691e1fa0c802dc013
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2018
---
# <a name="deploy-an-azure-container-service-aks-cluster"></a>部署 Azure 容器服务 (AKS) 群集

Kubernetes 为容器化应用程序提供一个分布式平台。 通过 AKS，使预配生产就绪的 Kubernetes 群集变得简单快捷。 在本教程的第 3 部分（共 8 部分）中，在 AKS 中部署了 Kubernetes 群集。 已完成的步骤包括：

> [!div class="checklist"]
> * 部署 Kubernetes AKS 群集
> * Kubernetes CLI (kubectl) 的安装
> * Kubectl 的配置

在后续教程中，将向该群集部署 Azure Vote 应用程序并对该应用程序进行缩放和更新，还会将 Operations Management Suite 配置为监视 Kubernetes 群集。

## <a name="before-you-begin"></a>开始之前

在以前的教程中，已创建容器映像并上传到 Azure 容器注册表实例。 如果尚未完成这些步骤，并且想要逐一完成，请返回到[教程 1 – 创建容器映像][aks-tutorial-prepare-app]。

## <a name="enabling-aks-preview-for-your-azure-subscription"></a>为 Azure 订阅启用 AKS 预览版
AKS 为预览版时，创建新群集需要订阅上的功能标记。 可以为任意数量的订阅请求此功能，只要你想使用。 使用 `az provider register` 命令注册 AKS 提供程序：

```azurecli-interactive
az provider register -n Microsoft.ContainerService
```

注册之后，即可通过 AKS 创建 Kubernetes 群集。

## <a name="create-kubernetes-cluster"></a>创建 Kubernetes 群集

下面的示例在 `myResourceGroup` 资源组中创建 `myAKSCluster` 群集。 此资源组是在[上一教程][aks-tutorial-prepare-acr]中创建的。

```azurecli
az aks create --resource-group myResourceGroup --name myAKSCluster --node-count 1 --generate-ssh-keys
```

几分钟后，部署完成并返回有关 AKS 部署的 JSON 格式信息。

## <a name="install-the-kubectl-cli"></a>安装 kubectl CLI

若要从客户端计算机连接到 Kubernetes 群集，请使用 [kubectl][kubectl]（Kubernetes 命令行客户端）。

如果使用的是 Azure CloudShell，则 kubectl 已安装。 如果要在本地安装它，请运行以下命令：

```azurecli
az aks install-cli
```

## <a name="connect-with-kubectl"></a>连接 kubectl

若要配置 kubectl 以连接到 Kubernetes 群集，请运行以下命令：

```azurecli
az aks get-credentials --resource-group=myResourceGroup --name=myAKSCluster
```

若要验证与群集之间的连接，请运行 [kubectl get nodes][kubectl-get] 命令。

```azurecli
kubectl get nodes
```

输出：

```
NAME                          STATUS    AGE       VERSION
k8s-myAKSCluster-36346190-0   Ready     49m       v1.7.7
```

教程完成时，AKS 群集已准备就绪，可用于工作负荷。 在后续教程中，一个多容器应用程序会部署到此群集、进行扩大、更新以及受到监视。

## <a name="next-steps"></a>后续步骤

在本教程中，在 AKS 中部署了 Kubernetes 群集。 已完成以下步骤：

> [!div class="checklist"]
> * 部署 Kubernetes AKS 群集
> * 安装 Kubernetes CLI (kubectl)
> * 配置 kubectl

继续学习下一个教程，了解如何在群集上运行应用程序。

> [!div class="nextstepaction"]
> [在 Kubernetes 中部署应用程序][aks-tutorial-deploy-app]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-tutorial-deploy-app]: ./tutorial-kubernetes-deploy-application.md
[aks-tutorial-prepare-acr]: ./tutorial-kubernetes-prepare-acr.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md