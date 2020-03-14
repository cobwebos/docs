---
title: Azure Kubernetes Service （AKS）中的配额、Sku 和区域可用性
description: 了解 Azure Kubernetes 服务（AKS）的默认配额、受限制的节点 VM SKU 大小和区域可用性。
services: container-service
ms.topic: conceptual
ms.date: 04/09/2019
ms.openlocfilehash: 03e7396932f0813ef4bd00d644dcdaddfe229e6a
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79252826"
---
# <a name="quotas-virtual-machine-size-restrictions-and-region-availability-in-azure-kubernetes-service-aks"></a>Azure Kubernetes 服务中的配额、虚拟机大小限制和区域可用性（AKS）

所有 Azure 服务都为资源和功能设置默认限制和配额。 某些虚拟机（VM） Sku 也已限制使用。

本文详细介绍 Azure Kubernetes Service （AKS）资源的默认资源限制，以及 Azure 区域中 AKS 的可用性。

## <a name="service-quotas-and-limits"></a>服务配额和限制

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>预配的基础结构

所有其他网络、计算和存储限制均适用于预配的基础结构。 有关相关限制，请参阅[Azure 订阅和服务限制](../azure-resource-manager/management/azure-subscription-service-limits.md)。

> [!IMPORTANT]
> 升级 AKS 群集时，会暂时消耗更多的资源。 这些资源包括虚拟网络子网或虚拟机 vCPU 配额中的可用 IP 地址。 如果你使用 Windows Server 容器（目前在 AKS 中为预览版），则将最新更新应用到节点的唯一认可方法是执行升级操作。 群集升级失败过程可能表明没有可用的 IP 地址空间或 vCPU 配额来处理这些临时资源。 有关 Windows Server 节点升级过程的详细信息，请参阅[升级 AKS 中的节点池][nodepool-upgrade]。

## <a name="restricted-vm-sizes"></a>受限的 VM 大小

AKS 群集中的每个节点都包含固定量的计算资源，例如 vCPU 和内存。 如果 AKS 节点包含的计算资源不足，pod 可能无法正常运行。 若要确保所需的*kube 系统*箱和应用程序可以可靠地计划，请**不要在 AKS 中使用以下 VM sku**：

- Standard_A0
- Standard_A1
- Standard_A1_v2
- Standard_B1s
- Standard_B1ms
- Standard_F1
- Standard_F1s

有关 VM 类型及其计算资源的详细信息，请参阅[Azure 中虚拟机的大小][vm-skus]。

## <a name="region-availability"></a>上市区域

有关可以部署和运行群集的最新列表，请参阅[AKS 区域可用性][region-availability]。

## <a name="next-steps"></a>后续步骤

某些默认限制和配额可以提高。 如果资源支持增加，请通过[Azure 支持请求][azure-support]增加请求（对于 "**问题类型**"，选择 "**配额**"）。

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/linux/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
