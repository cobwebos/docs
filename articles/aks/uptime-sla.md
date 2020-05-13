---
title: Azure Kubernetes Service （AKS）高可用性和运行时间 SLA
description: 了解 Azure Kubernetes Service （AKS） API 服务器的可选高可用性运行时间 SLA。
services: container-service
ms.topic: conceptual
ms.date: 05/11/2020
ms.openlocfilehash: 1c340f85a107cac437e1241025d8c9bc6991b965
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83125717"
---
# <a name="azure-kubernetes-service-aks-uptime-sla"></a>Azure Kubernetes 服务（AKS）运行时间 SLA

运行时间 SLA 是一项可选功能，用于为群集启用财务支持的更高 SLA。 运行时间 SLA 保证使用[可用性区域][availability-zones]的群集的 Kubernetes API 服务器终结点99.95% 的可用性，以及不使用可用性区域的群集的可用性99.9%。 AKS 跨更新域和容错域使用主节点副本，以确保满足 SLA 要求。

出于合规性原因或向其客户扩展 SLA 的客户应启用此功能。 具有需要更高可用性的关键工作负荷的客户，其中包括用于启用此功能的 SLA 权益。 启用带有可用性区域的功能，以获得 Kubernetes API 服务器的更高可用性。  

客户可创建具有99.5% 的服务级别目标（SLO）的无限制可用群集。

> [!Important]
> 对于具有出口锁定的群集，请参阅[限制传出流量](limit-egress-traffic.md)打开合适的端口以获取运行时间 SLA。

## <a name="sla-terms-and-conditions"></a>SLA 条款和条件

运行时间 SLA 是一项付费功能，按群集启用。 运行时间 SLA 定价取决于分类的数量，而不是群集的大小。 可以查看[运行时间 SLA 定价详细](https://azure.microsoft.com/pricing/details/kubernetes-service/)信息以获取详细信息。

## <a name="region-availability"></a>区域可用性

运行时间 SLA 在以下区域中提供：

* 澳大利亚东部
* 加拿大中部
* 美国东部
* 美国东部 2
* 美国中南部
* 东南亚
* 美国西部 2

## <a name="before-you-begin"></a>开始之前

* Azure CLI 版本2.7.0 或更高版本

## <a name="creating-a-cluster-with-uptime-sla"></a>创建具有运行时间 SLA 的群集

若要创建具有运行时间 SLA 的新群集，请使用 Azure CLI。

以下示例在“eastus”  位置创建名为“myResourceGroup”  的资源组。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```
使用 [az aks create][az-aks-create] 命令创建 AKS 群集。 以下示例创建一个具有一个节点的名为  myAKSCluster 的群集。 也可通过 *--enable-addons monitoring* 参数启用用于容器的 Azure Monitor。  此操作需要几分钟才能完成。

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster --uptime-sla --node-count 1 --enable-addons monitoring --generate-ssh-keys
```
片刻之后，该命令将会完成，并返回有关群集的 JSON 格式信息。 下面的 JSON 代码片段显示了 SKU 的付费层，指示群集已启用运行时间 SLA。

```output
  },
  "sku": {
    "name": "Basic",
    "tier": "Paid"
  },
  "tags": null,
  "type": "Microsoft.ContainerService/ManagedClusters",
  "windowsProfile": null
```

## <a name="limitations"></a>限制

* 目前不能向现有群集添加运行时间 SLA。
* 目前，无法从 AKS 群集中删除运行时间 SLA。  

## <a name="next-steps"></a>后续步骤

使用[可用性区域][availability-zones]提高 AKS 群集工作负荷的高可用性。

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/linux/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[faq]: ./faq.md
[availability-zones]: ./availability-zones.md
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[limit-egress-traffic]: ./limit-egress-traffic.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
