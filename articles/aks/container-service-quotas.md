---
title: Azure Kubernetes 服务 (AKS) 配额和区域可用性
description: Azure Kubernetes 服务 (AKS) 的默认配额和区域可用性。
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: overview
ms.date: 04/26/2018
ms.author: nepeters
ms.openlocfilehash: 4fb03df470972a2920854ec18554d1cb4b310e88
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="quotas-and-region-availability-for-azure-kubernetes-service-aks"></a>Azure Kubernetes 服务 (AKS) 的配额和区域可用性

所有 Azure 服务都包括某些针对资源和功能的默认限制和配额。 以下各节详述了多个 Azure Kubernetes 服务 (AKS) 资源的默认资源限制，以及 Azure 区域中 AKS 服务的可用性。

## <a name="service-quotas-and-limits"></a>服务配额和限制

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>预配的基础结构

所有其他网络、计算和存储限制均适用于预配的基础结构。 有关相关限制，请参阅 [Azure 订阅和服务限制](../azure-subscription-service-limits.md)。

## <a name="region-availability"></a>上市区域

Azure Kubernetes 服务 (AKS) 在以下区域提供预览版：
- 美国东部
- 欧洲西部
- 美国中部
- 加拿大中部
- 加拿大东部

## <a name="next-steps"></a>后续步骤

某些默认限制和配额可以提高。 若要请求增加一个或多个资源（如果支持此功能），请提交 [Azure 支持请求][azure-support]（选择“配额”作为“问题类型”）。

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
