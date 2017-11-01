---
title: "Azure 上的 Kubernertes 教程 - 部署群集 | Microsoft Docs"
description: "AKS 教程 - 部署群集"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: aks, azure-container-service
keywords: "Docker, 容器, 微服务, Kubernetes, DC/OS, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 7f9991d2254011080185a555f5351dce85f73704
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/26/2017
---
# <a name="deploy-an-azure-container-service-aks-cluster"></a>部署 Azure 容器服务 (AKS) 群集

Kubernetes 为容器化应用程序提供一个分布式平台。 通过 AKS，使预配生产就绪的 Kubernetes 群集变得简单快捷。 在本教程的第 3 部分（共 8 部分）中，在 AKS 中部署了 Kubernetes 群集。 已完成的步骤包括：

> [!div class="checklist"]
> * 部署 Kubernetes AKS 群集
> * Kubernetes CLI (kubectl) 的安装
> * Kubectl 的配置

在后续教程中，将向该群集部署 Azure Vote 应用程序并对该应用程序进行缩放和更新，还会将 Operations Management Suite 配置为监视 Kubernetes 群集。

## <a name="before-you-begin"></a>开始之前

在以前的教程中，已创建容器映像并上传到 Azure 容器注册表实例。 如果尚未完成这些步骤，并且想要逐一完成，请返回到[教程 1 – 创建容器映像](./tutorial-kubernetes-prepare-app.md)。

## <a name="enabling-aks-preview-for-your-azure-subscription"></a>为 Azure 订阅启用 AKS 预览版
AKS 为预览版时，创建新群集需要订阅上的功能标记。 可以为任意数量的订阅请求此功能，只要你想使用。 使用 `az provider register` 命令注册 AKS 提供程序：

```azurecli-interactive
az provider register -n Microsoft.ContainerService
```

注册之后，即可通过 AKS 创建 Kubernetes 群集。

## <a name="create-kubernetes-cluster"></a>创建 Kubernetes 群集

下面的示例在 `myResourceGroup` 资源组中创建 `myK8sCluster` 群集。 此资源组是在[上一教程](./tutorial-kubernetes-prepare-acr.md)中进行创建。

```azurecli
az aks create --resource-group myResourceGroup --name myK8sCluster --agent-count 1 --generate-ssh-keys
```

几分钟后，部署完成并返回有关 AKS 部署的 JSON 格式信息。

## <a name="install-the-kubectl-cli"></a>安装 kubectl CLI

若要从客户端计算机连接到 Kubernetes 群集，请使用 [kubectl](https://kubernetes.io/docs/user-guide/kubectl/)（Kubernetes 命令行客户端）。

如果使用的是 Azure CloudShell，则 kubectl 已安装。 如果要在本地安装它，请运行以下命令：

```azurecli
az aks install-cli
```

## <a name="connect-with-kubectl"></a>连接 kubectl

若要配置 kubectl 以连接到 Kubernetes 群集，请运行以下命令：

```azurecli
az aks get-credentials --resource-group=myResourceGroup --name=myK8sCluster
```

若要验证与群集之间的连接，请运行 [kubectl get nodes](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) 命令。

```azurecli
kubectl get nodes
```

输出：

```
NAME                          STATUS    AGE       VERSION
k8s-myk8scluster-36346190-0   Ready     49m       v1.7.7
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
> [在 Kubernetes 中部署应用程序](./tutorial-kubernetes-deploy-application.md)
