---
title: "升级 Azure 容器服务 (AKS) 群集 | Microsoft Docs"
description: "升级 Azure 容器服务 (AKS) 群集"
services: container-service
documentationcenter: 
author: gabrtv
manager: timlt
editor: 
tags: aks, azure-container-service
keywords: "Kubernetes, Docker, 容器, 微服务, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2017
ms.author: gamonroy
ms.custom: mvc
ms.openlocfilehash: 7b69f770354c9402a3248a1d5a27558ff9aea7c4
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2017
---
# <a name="upgrade-an-azure-container-service-aks-cluster"></a>升级 Azure 容器服务 (AKS) 群集

使用 Azure 容器服务 (AKS) 可以轻松执行常见管理任务，包括升级 Kubernetes 群集。

## <a name="upgrade-an-aks-cluster"></a>升级 AKS 群集

在升级群集之前，请使用 `az aks get-versions` 命令检查哪些 Kubernetes 版本可用于升级。

```azurecli-interactive
az aks get-versions --name myK8sCluster --resource-group myResourceGroup --output table
```

输出：

```console
Name     ResourceGroup    MasterVersion    MasterUpgrades    AgentPoolVersion    AgentPoolUpgrades
-------  ---------------  ---------------  ----------------  ------------------  -------------------
default  myResourceGroup  1.7.7            1.8.1             1.7.7               1.8.1
```

我们有两个版本可用于升级：1.8.0 和 1.8.1。  可使用 `az aks upgrade` 命令升级到最新可用版本。  在升级过程中，节点会被仔细[封锁和排除](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/)以尽量减少对正在运行的应用程序造成中断。

```azurecli-interactive
az aks upgrade --name myK8sCluster --resource-group myResourceGroup --kubernetes-version 1.8.1
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
    "kubernetesVersion": "1.8.1",
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

现在可以使用 `az aks show` 命令确认升级是否成功。

```azurecli-interactive
az aks show --name myK8sCluster --resource-group myResourceGroup --output table
```

输出：

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myK8sCluster  westus2     myResourceGroup  1.8.1                Succeeded            myk8sclust-myresourcegroup-3762d8-2f6ca801.hcp.westus2.azmk8s.io
```

## <a name="next-steps"></a>后续步骤

学习 AKS 教程，了解有关部署和管理 AKS 的详细信息。

> [!div class="nextstepaction"]
> [AKS 教程](./tutorial-kubernetes-prepare-app.md)