---
title: 配额、 Sku 和区域可用性在 Azure Kubernetes 服务 (AKS)
description: 了解 Azure Kubernetes 服务 (AKS) 中的默认配额、受限制的节点 VM SKU 大小和适用地区。
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: iainfou
ms.openlocfilehash: 8d4ed8f791858747814972bcf16a9672a7f12610
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2019
ms.locfileid: "65901458"
---
# <a name="quotas-virtual-machine-size-restrictions-and-region-availability-in-azure-kubernetes-service-aks"></a>Azure Kubernetes 服务 (AKS) 中的配额、虚拟机大小限制和适用地区

所有 Azure 服务设置资源和功能的默认限制和配额。 某些虚拟机 (VM) Sku 也是受限制，使用的。

本文详细介绍有关 Azure Kubernetes 服务 (AKS) 资源和可用性的 Azure 区域中 AKS 的默认资源限制。

## <a name="service-quotas-and-limits"></a>服务配额和限制

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>预配的基础结构

所有其他网络、计算和存储限制均适用于预配的基础结构。 有关相关的限制，请参阅[Azure 订阅和服务限制](../azure-subscription-service-limits.md)。

> [!IMPORTANT]
> 升级 AKS 群集时，会暂时会占用附加的资源。 这些资源包括虚拟网络子网或虚拟机 vCPU 配额中可用的 IP 地址。 如果在使用 Windows Server 容器 （目前以预览版在 AKS 中），将最新的更新应用到节点的唯一认可的方法是执行升级操作。 故障的群集升级过程可能指示没有可用 IP 地址空间或 vCPU 配额来处理这些临时资源。 Windows Server 节点升级过程的详细信息，请参阅[升级在 AKS 中的节点池][nodepool-upgrade]。

## <a name="restricted-vm-sizes"></a>受限制的 VM 大小

AKS 群集中的每个节点都包含固定数量的计算资源，例如 vCPU 和内存。 如果 AKS 节点包含没有足够的计算资源，pod 可能无法正常运行。 若要确保所需*kube 系统*pod 和您的应用程序可以可靠地计划，不要在 AKS 中使用以下 VM Sku:

- Standard_A0
- Standard_A1
- Standard_A1_v2
- Standard_B1s
- Standard_B1ms
- Standard_F1
- Standard_F1s

有关 VM 类型及其计算资源的详细信息，请参阅 [Azure 中的虚拟机的大小][vm-skus]。

## <a name="region-availability"></a>适用地区

有关可以在其中部署和运行群集的地点的最新列表，请参阅 [AKS 适用地区][region-availability]。

## <a name="next-steps"></a>后续步骤

某些默认限制和配额可以提高。 如果你的资源支持增加，请求通过增加[Azure 支持请求][ azure-support] (对于**问题类型**，选择**配额**).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/linux/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
