---
title: Azure 上的 Kubernetes 教程 - 更新群集
description: Azure 上的 Kubernetes 教程 - 更新群集
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 04/05/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 0886d13b62b6b8ad1c0dcd430ce48bcc51d6d465
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2018
ms.locfileid: "33933825"
---
# <a name="tutorial-upgrade-kubernetes-in-azure-kubernetes-service-aks"></a>教程：在 Azure Kubernetes 服务 (AKS) 中升级 Kubernetes

可使用 Azure CLI 升级 Azure Kubernetes 服务 (AKS) 群集。 在升级过程中，Kubernetes 节点会被仔细[封锁和排除][kubernetes-drain]以尽量减少对正在运行的应用程序造成中断。

在本教程的第 8 部分（共 8 部分）中，升级了 Kubernetes 群集。 要完成的任务包括：

> [!div class="checklist"]
> * 确定 Kubernetes 的当前版本和可用版本
> * 升级 Kubernetes 节点
> * 验证升级是否成功

## <a name="before-you-begin"></a>开始之前

在前面的教程中，我们已将应用程度打包到容器映像中，将此映像上传到 Azure 容器注册表，并创建了 Kubernetes 群集。 应用程序随后在 Kubernetes 群集上运行。

如果尚未完成这些步骤，并且想要逐一完成，请返回到[教程 1 - 创建容器映像][aks-tutorial-prepare-app]。


## <a name="get-cluster-versions"></a>获取群集版本

在升级群集之前，请使用 `az aks get-upgrades` 命令检查哪些 Kubernetes 版本可用于升级。

```azurecli
az aks get-upgrades --name myAKSCluster --resource-group myResourceGroup --output table
```

在本示例中，当前节点版本为 `1.7.9`，可用的升级版本位于“升级”列下。

```
Name     ResourceGroup    MasterVersion    NodePoolVersion    Upgrades
-------  ---------------  ---------------  -----------------  ----------------------------------
default  myResourceGroup  1.7.9            1.7.9              1.7.12, 1.8.1, 1.8.2, 1.8.6, 1.8.7
```

## <a name="upgrade-cluster"></a>升级群集

使用 `az aks upgrade` 命令升级群集节点。 以下示例将群集更新到版本 `1.8.2`。

```azurecli
az aks upgrade --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.8.2
```

输出：

```json
{
  "id": "/subscriptions/<Subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "location": "eastus",
  "name": "myAKSCluster",
  "properties": {
    "accessProfiles": {
      "clusterAdmin": {
        "kubeConfig": "..."
      },
      "clusterUser": {
        "kubeConfig": "..."
      }
    },
    "agentPoolProfiles": [
      {
        "count": 1,
        "dnsPrefix": null,
        "fqdn": null,
        "name": "myAKSCluster",
        "osDiskSizeGb": null,
        "osType": "Linux",
        "ports": null,
        "storageProfile": "ManagedDisks",
        "vmSize": "Standard_D2_v2",
        "vnetSubnetId": null
      }
    ],
    "dnsPrefix": "myK8sClust-myResourceGroup-4f48ee",
    "fqdn": "myk8sclust-myresourcegroup-4f48ee-406cc140.hcp.eastus.azmk8s.io",
    "kubernetesVersion": "1.8.2",
    "linuxProfile": {
      "adminUsername": "azureuser",
      "ssh": {
        "publicKeys": [
          {
            "keyData": "..."
          }
        ]
      }
    },
    "provisioningState": "Succeeded",
    "servicePrincipalProfile": {
      "clientId": "e70c1c1c-0ca4-4e0a-be5e-aea5225af017",
      "keyVaultSecretRef": null,
      "secret": null
    }
  },
  "resourceGroup": "myResourceGroup",
  "tags": null,
  "type": "Microsoft.ContainerService/ManagedClusters"
}
```

## <a name="validate-upgrade"></a>验证升级

使用 `az aks show` 命令确认升级已成功。

```azurecli
az aks show --name myAKSCluster --resource-group myResourceGroup --output table
```

输出：

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myAKSCluster  eastus     myResourceGroup  1.8.2                Succeeded            myk8sclust-myresourcegroup-3762d8-2f6ca801.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>后续步骤

在本教程中，在 AKS 群集中升级了 Kubernetes。 完成了以下任务：

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