---
title: 删除 Azure Kubernetes 服务 (AKS) 群集
description: 使用 CLI 或 Azure 门户删除 AKS 群集。
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 2/05/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 66dcebb702695a6601f6ed17b85a04d5bb4e01f6
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37100037"
---
# <a name="delete-an-azure-kubernetes-service-aks-cluster"></a>删除 Azure Kubernetes 服务 (AKS) 群集

删除 Azure Kubernetes 服务群集时，将保留部署了群集的资源组，但将删除所有与 AKS 相关的资源。 本文档介绍如何使用 Azure CLI 和 Azure 门户删除 AKS 群集。

除了删除群集之外，还可以删除部署了该群集的资源组，这也会删除 AKS 群集。

## <a name="azure-cli"></a>Azure CLI

使用 [az aks delete][az-aks-delete] 命令删除 AKS 群集。

```azurecli-interactive
az aks delete --resource-group myResourceGroup --name myAKSCluster
```

`az aks delete` 命令可以使用以下选项。

| 参数 | 说明 | 必选 |
|---|---|:---:|
| `--name` `-n` | 托管群集的资源名称。 | 是 |
| `--resource-group` `-g` | Azure Kubernetes 服务资源组的名称。 | 是 |
| `--no-wait` | 不等待长时间运行的操作完成。 | 否 |
| `--yes` `-y` | 不提示确认。 | 否 |

## <a name="azure-portal"></a>Azure 门户

在 Azure 门户中时，浏览到包含 Azure Kubernetes 服务 (AKS) 资源的资源组，选择资源，然后单击“删除”。 系统会提示用户确认删除操作。

![删除 AKS 群集门户](media/container-service-delete-cluster/delete-aks-portal.png)

<!-- LINKS - internal -->
[az-aks-delete]: /cli/azure/aks?view=azure-cli-latest#az_aks_delete