---
title: "Azure 上的 Kubernertes 教程 - 更新群集 | Microsoft Docs"
description: "Azure 上的 Kubernertes 教程 - 更新群集"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: aks, azure-container-service
keywords: "Docker, 容器, 微服务, Kubernetes, DC/OS, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: aurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 2cb81b5cd8b70df8077d9574e0232bc6b3d37c52
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/09/2017
---
# <a name="upgrade-kubernetes-in-azure-container-service-aks"></a>在 Azure 容器服务 (AKS) 中升级 Kubernetes

可使用 Azure CLI 升级 Azure 容器服务 (AKS) 群集。 在升级过程中，Kubernetes 节点会被仔细[封锁和排除](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/)以尽量减少对正在运行的应用程序造成中断。

在本教程的第 8 部分（共 8 部分）中，升级了 Kubernetes 群集。 要完成的任务包括：

> [!div class="checklist"]
> * 确定 Kubernetes 的当前版本和可用版本
> * 升级 Kubernetes 节点
> * 验证升级是否成功

## <a name="before-you-begin"></a>开始之前

在前面的教程中，我们已将应用程度打包到容器映像中，将此映像上传到 Azure 容器注册表，并创建了 Kubernetes 群集。 应用程序随后在 Kubernetes 群集上运行。 

如果尚未完成这些步骤，并且想要逐一完成，请返回到[教程 1 – 创建容器映像](./tutorial-kubernetes-prepare-app.md)。


## <a name="get-cluster-versions"></a>获取群集版本

在升级群集之前，请使用 `az aks get-versions` 命令检查哪些 Kubernetes 版本可用于升级。

```azurecli-interactive
az aks get-versions --name myK8sCluster --resource-group myResourceGroup --output table
```

这里可以看到，当前节点版本为 `1.7.7`，且版本 `1.7.9`、`1.8.1` 和 `1.8.2` 可用。

```
Name     ResourceGroup    MasterVersion    MasterUpgrades       AgentPoolVersion    AgentPoolUpgrades
-------  ---------------  ---------------  -------------------  ------------------  -------------------
default  myAKSCluster     1.7.7            1.8.2, 1.7.9, 1.8.1  1.7.7               1.8.2, 1.7.9, 1.8.1
```

## <a name="upgrade-cluster"></a>升级群集

使用 `az aks upgrade` 命令升级群集节点。 以下示例将群集更新到版本 `1.8.2`。

```azurecli-interactive
az aks upgrade --name myK8sCluster --resource-group myResourceGroup --kubernetes-version 1.8.2
```

输出：

```json
{
  "id": "/subscriptions/4f48eeae-9347-40c5-897b-46af1b8811ec/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myK8sCluster",
  "location": "westus2",
  "name": "myK8sCluster",
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
        "name": "myK8sCluster",
        "osDiskSizeGb": null,
        "osType": "Linux",
        "ports": null,
        "storageProfile": "ManagedDisks",
        "vmSize": "Standard_D2_v2",
        "vnetSubnetId": null
      }
    ],
    "dnsPrefix": "myK8sClust-myResourceGroup-4f48ee",
    "fqdn": "myk8sclust-myresourcegroup-4f48ee-406cc140.hcp.westus2.azmk8s.io",
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

现在可以使用 `az aks show` 命令确认升级是否成功。

```azurecli-interactive
az aks show --name myK8sCluster --resource-group myResourceGroup --output table
```

输出：

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myK8sCluster  westus2     myResourceGroup  1.8.2                Succeeded            myk8sclust-myresourcegroup-3762d8-2f6ca801.hcp.westus2.azmk8s.io
```

## <a name="next-steps"></a>后续步骤

在本教程中，在 AKS 群集中升级了 Kubernetes。 完成了以下任务：

> [!div class="checklist"]
> * 确定 Kubernetes 的当前版本和可用版本
> * 升级 Kubernetes 节点
> * 验证升级是否成功

请单击以下链接了解有关 AKS 的详细信息。

> [!div class="nextstepaction"]
> [AKS 概述](./intro-kubernetes.md)