---
title: Azure Kubernetes 服务 (AKS) 配额和区域可用性
description: Azure Kubernetes 服务 (AKS) 的默认配额和区域可用性。
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: iainfou
ms.openlocfilehash: 8feeaf2e8ee99405ed0de8291fc97dc50db6a386
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "59798838"
---
# <a name="quotas-and-region-availability-for-azure-kubernetes-service-aks"></a>Azure Kubernetes 服务 (AKS) 的配额和区域可用性

所有 Azure 服务都包括某些针对资源和功能的默认限制和配额。 以下各节详述了多个 Azure Kubernetes 服务 (AKS) 资源的默认资源限制，以及 Azure 区域中 AKS 服务的可用性。

## <a name="service-quotas-and-limits"></a>服务配额和限制

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>预配的基础结构

所有其他网络、计算和存储限制均适用于预配的基础结构。 有关相关限制，请参阅 [Azure 订阅和服务限制](../azure-subscription-service-limits.md)。

## <a name="region-availability"></a>上市区域

Azure Kubernetes 服务 (AKS) 在以下区域提供预览版：

- 澳大利亚东部
- 澳大利亚东南部
- 加拿大中部
- 加拿大东部
- 印度中部
- 美国中部
- 东亚
- 美国东部
- 美国东部 2
- 法国中部
- 日本东部
- 韩国中部
- 韩国南部
- 北欧
- 东南亚
- 美国中南部
- 印度南部
- 英国南部
- 英国西部
- 西欧
- 美国西部
- 美国西部 2

## <a name="next-steps"></a>后续步骤

某些默认限制和配额可以提高。 若要请求增加一个或多个资源（如果支持此功能），请提交 [Azure 支持请求][azure-support]（选择“配额”作为“问题类型”）。

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
