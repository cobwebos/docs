---
title: 资源、SKU、区域的限制
titleSuffix: Azure Kubernetes Service
description: 了解 Azure Kubernetes 服务 (AKS) 中的默认配额、受限制的节点 VM SKU 大小和适用地区。
services: container-service
ms.topic: conceptual
ms.date: 04/09/2019
ms.openlocfilehash: 9592b03f5bd9e9436db5665e040b4afd3b68b764
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87562729"
---
# <a name="quotas-virtual-machine-size-restrictions-and-region-availability-in-azure-kubernetes-service-aks"></a>Azure Kubernetes 服务 (AKS) 中的配额、虚拟机大小限制和适用地区

所有 Azure 服务都设置针对资源和功能的默认限制和配额。 某些虚拟机 (VM) SKU 也被限制使用。

本文详述了 Azure Kubernetes 服务 (AKS) 资源的默认资源限制，以及 Azure 区域中 AKS 的可用性。

## <a name="service-quotas-and-limits"></a>服务配额和限制

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>预配的基础结构

所有其他网络、计算和存储限制均适用于预配的基础结构。 若要了解相关限制，请参阅 [Azure 订阅和服务限制](../azure-resource-manager/management/azure-subscription-service-limits.md)。

> [!IMPORTANT]
> 升级 AKS 群集时，会临时使用其他资源。 这些资源包括虚拟网络子网中的可用 IP 地址，或者虚拟机 vCPU 配额。 如果使用 Windows Server 容器，则唯一认可的将最新更新应用于节点的方法是执行升级操作。 群集升级过程失败可能表明没有可用的 IP 地址空间或 vCPU 配额来处理这些临时资源。 有关 Windows Server 节点升级过程的详细信息，请参阅[升级 AKS 中的节点池][nodepool-upgrade]。

## <a name="restricted-vm-sizes"></a>受限制的 VM 大小

AKS 群集中的每个节点都包含固定数量的计算资源，例如 vCPU 和内存。 如果某个 AKS 节点包含的计算资源不足，则 Pod 可能无法正常运行。 若要确保所需的 *kube-system* Pod 和你的应用程序能够可靠地进行计划，**请勿在 AKS 中使用以下 VM SKU**：

- Standard_A0
- Standard_A1
- Standard_A1_v2
- Standard_B1s
- Standard_B1ms
- Standard_F1
- Standard_F1s

有关 VM 类型及其计算资源的详细信息，请参阅 [Azure 中的虚拟机的大小][vm-skus]。

## <a name="region-availability"></a>上市区域

有关可以在其中部署和运行群集的地点的最新列表，请参阅 [AKS 适用地区][region-availability]。

## <a name="next-steps"></a>后续步骤

某些默认限制和配额可以提高。 如果资源支持增加，请通过 [Azure 支持请求][azure-support] 增加 (对于 " **问题类型**"，选择 " **配额**) "。

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
