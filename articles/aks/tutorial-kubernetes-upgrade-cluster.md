---
title: Azure 上的 Kubernetes 教程 - 升级群集
description: 此 Azure Kubernetes 服务 (AKS) 教程介绍如何将现有 AKS 群集升级到最新可用的 Kubernetes 版本。
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 08/14/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 11e082ae235706613b0a60b12bc2b27896953508
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2018
ms.locfileid: "41919495"
---
# <a name="tutorial-upgrade-kubernetes-in-azure-kubernetes-service-aks"></a>教程：在 Azure Kubernetes 服务 (AKS) 中升级 Kubernetes

在应用程序和群集生命周期中，你可能希望升级到最新可用的 Kubernetes 版本并使用新功能。 可使用 Azure CLI 升级 Azure Kubernetes 服务 (AKS) 群集。 在升级过程中，Kubernetes 节点会被仔细[封锁和排除][kubernetes-drain]以尽量减少对正在运行的应用程序造成中断。

在本教程的第 7 部分中，升级了 Kubernetes 群集。 学习如何：

> [!div class="checklist"]
> * 确定 Kubernetes 的当前版本和可用版本
> * 升级 Kubernetes 节点
> * 验证升级是否成功

## <a name="before-you-begin"></a>开始之前

在前面的教程中，我们已将应用程度打包到容器映像中，将此映像上传到 Azure 容器注册表，并创建了 Kubernetes 群集。 应用程序随后在 Kubernetes 群集上运行。 如果尚未完成这些步骤，并且想要逐一完成，请返回到[教程 1 - 创建容器映像][aks-tutorial-prepare-app]。

本教程需要运行 Azure CLI 2.0.44 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli-install]。

## <a name="get-available-cluster-versions"></a>获取可用的群集版本

在升级群集之前，请使用 [az aks get-upgrades][] 命令检查哪些 Kubernetes 版本可用于升级：

```azurecli
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

在以下示例中，当前版本是 *1.9.6*，可用的版本显示在“升级”列下。

```
Name     ResourceGroup    MasterVersion    NodePoolVersion    Upgrades
-------  ---------------  ---------------  -----------------  ----------------------
default  myResourceGroup  1.9.9            1.9.9              1.10.3, 1.10.5, 1.10.6
```

## <a name="upgrade-a-cluster"></a>升级群集

使用 [az aks upgrade][] 命令升级 AKS 群集。 以下示例将群集升级到 Kubernetes 版本 *1.10.6*。

> [!NOTE]
> 一次只能升级一个次要版本。 例如，可以从 *1.9.6* 升级到 *1.10.3*，但不能从 *1.9.6* 直接升级到 *1.11.x*。 若要从 *1.9.6* 升级到 *1.11.x*，请先从 *1.9.6* 升级到 *1.10.3*，然后再执行一次升级从 *1.10.3* 升级到 *1.11.x*。

```azurecli
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.10.6
```

以下浓缩版的示例输出显示 *kubernetesVersion* 现在报告 *1.10.6*：

```json
{
  "agentPoolProfiles": [
    {
      "count": 3,
      "maxPods": 110,
      "name": "nodepool1",
      "osType": "Linux",
      "storageProfile": "ManagedDisks",
      "vmSize": "Standard_DS1_v2",
    }
  ],
  "dnsPrefix": "myAKSClust-myResourceGroup-19da35",
  "enableRbac": false,
  "fqdn": "myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io",
  "id": "/subscriptions/<Subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "kubernetesVersion": "1.10.6",
  "location": "eastus",
  "name": "myAKSCluster",
  "type": "Microsoft.ContainerService/ManagedClusters"
}
```

## <a name="validate-an-upgrade"></a>验证升级

使用 [az aks show][] 命令确认升级成功，如下所示：

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

以下示例输出显示 AKS 群集运行 *KubernetesVersion 1.10.6*：

```
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.10.6               Succeeded            myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>后续步骤

在本教程中，在 AKS 群集中升级了 Kubernetes。 你已了解如何：

> [!div class="checklist"]
> * 确定 Kubernetes 的当前版本和可用版本
> * 升级 Kubernetes 节点
> * 验证升级是否成功

请单击以下链接了解有关 AKS 的详细信息。

> [!div class="nextstepaction"]
> [AKS 概述][aks-intro]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-intro]: ./intro-kubernetes.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[az aks show]: /cli/azure/aks#az-aks-show
[az aks get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az aks upgrade]: /cli/azure/aks#az-aks-upgrade
[azure-cli-install]: /cli/azure/install-azure-cli