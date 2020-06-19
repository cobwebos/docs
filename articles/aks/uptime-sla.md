---
title: Azure Kubernetes 服务 (AKS) 与运行时间 SLA
description: 了解 Azure Kubernetes 服务 (AKS) API 服务器的可选运行时间 SLA 产品/服务。
services: container-service
ms.topic: conceptual
ms.date: 05/19/2020
ms.openlocfilehash: e0e1399f69640dddfd618ac99637023390f28a92
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83683220"
---
# <a name="azure-kubernetes-service-aks-uptime-sla"></a>Azure Kubernetes 服务 (AKS) 运行时间 SLA

运行时间 SLA 是一个可选功能，用于为群集启用具有财务支持的更高 SLA。 对于使用[可用性区域][availability-zones]的群集，运行时间 SLA 可保证 Kubernetes API 服务器终结点 99.95% 的可用性，对于不使用可用性区域的群集，可保证 99.9% 的可用性。 AKS 跨更新域和容错域使用主节点副本，以确保满足 SLA 要求。

需要 SLA 以满足法规符合性要求或需要将 SLA 扩展到其最终用户的客户应启用此功能。 具有将从更高的运行时间 SLA 中受益的关键工作负载的客户也可能受益。 将运行时间 SLA 功能与可用性区域结合使用可以提高 Kubernetes API 服务器运行时间的可用性。  

客户仍可以创建服务级别目标 (SLO) 为 99.5% 的无限制免费群集，并根据需要选择首选 SLO 或 SLA 运行时间。

> [!Important]
> 对于出口锁定的群集，请参阅[限制出口流量](limit-egress-traffic.md)以打开适当的端口。

## <a name="sla-terms-and-conditions"></a>SLA 条款和条件

运行时间 SLA 是付费功能，按群集启用。 运行时间 SLA 定价取决于离散群集的数量，而不是各个群集的大小。 有关详细信息，可以查看[运行时间 SLA 定价详细信息](https://azure.microsoft.com/pricing/details/kubernetes-service/)。

## <a name="region-availability"></a>区域可用性

运行时间 SLA 在以下区域可用：

* 澳大利亚东部
* 加拿大中部
* 美国东部
* 美国东部 2
* 美国中南部
* 东南亚
* 美国西部 2

## <a name="before-you-begin"></a>开始之前

* Azure CLI 版本 2.7.0 或更高版本

## <a name="creating-a-cluster-with-uptime-sla"></a>创建具有运行时间 SLA 的群集

若要创建具有运行时间 SLA 的群集，请使用 Azure CLI。

以下示例在“eastus”位置创建名为“myResourceGroup”的资源组。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```
使用 [az aks create][az-aks-create] 命令创建 AKS 群集。 以下示例创建一个具有一个节点的名为 myAKSCluster 的群集。 也可通过 *--enable-addons monitoring* 参数启用用于容器的 Azure Monitor。  此操作需要几分钟才能完成。

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster --uptime-sla --node-count 1 --enable-addons monitoring --generate-ssh-keys
```
片刻之后，该命令将会完成，并返回有关群集的 JSON 格式信息。 下面的 JSON 片段显示了 SKU 的付费层，表明群集已启用了运行时间 SLA。

```output
  },
  "sku": {
    "name": "Basic",
    "tier": "Paid"
  },
```

## <a name="limitations"></a>限制

* 目前无法转换现有群集以启用运行时间 SLA。
* 当前，在创建 AKS 群集并启用运行时间 SLA 后，将无法从该群集中将其删除。  
* 当前不支持专用群集。

## <a name="next-steps"></a>后续步骤

使用[可用性区域][availability-zones]增加 AKS 群集工作负载的高可用性。
将群集配置为[限制出口流量](limit-egress-traffic.md)。

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
