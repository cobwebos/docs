---
title: "缩放 Azure 容器服务 (AKS) 群集 | Microsoft Docs"
description: "缩放 Azure 容器服务 (AKS) 群集。"
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
ms.date: 11/15/2017
ms.author: gamonroy
ms.custom: mvc
ms.openlocfilehash: b2fa3ebb7a22b9d19678d45cc50806627ab80e90
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2017
---
# <a name="scale-an-azure-container-service-aks-cluster"></a>缩放 Azure 容器服务 (AKS) 群集

可轻松将 AKS 群集缩放为不同的节点数。  选择所需的节点数，然后运行 `az aks scale` 命令。  节点数减少时，节点会被仔细[封锁和排除](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/)以尽量减少对正在运行的应用程序造成中断。  节点数增加时，`az` 命令将等待，直到 Kubernetes 群集将节点标记为 `Ready`。

## <a name="scale-the-cluster-nodes"></a>缩放群集节点

使用 `az aks scale` 命令缩放群集节点。 以下示例将名为 *myK8SCluster* 的群集缩放为单个节点。

```azurecli-interactive
az aks scale --name myK8sCluster --resource-group myResourceGroup --node-count 1
```

输出：

```json
{
  "id": "/subscriptions/4f48eeae-9347-40c5-897b-46af1b8811ec/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myK8sCluster",
  "location": "eastus",
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
    "fqdn": "myk8sclust-myresourcegroup-4f48ee-406cc140.hcp.eastus.azmk8s.io",
    "kubernetesVersion": "1.7.7",
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

## <a name="next-steps"></a>后续步骤

学习 AKS 教程，了解有关部署和管理 AKS 的详细信息。

> [!div class="nextstepaction"]
> [AKS 教程](./tutorial-kubernetes-prepare-app.md)
