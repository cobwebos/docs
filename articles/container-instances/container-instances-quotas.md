---
title: 服务配额和区域可用性
description: Azure 容器实例服务的配额、限制和区域可用性。
ms.topic: article
ms.date: 07/22/2020
ms.openlocfilehash: eaaa8e0b2d72aaea546a1bc351da40932c1deb14
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87384816"
---
# <a name="quotas-and-limits-for-azure-container-instances"></a>Azure 容器实例的配额和限制

所有 Azure 服务都包括某些针对资源和功能的默认限制和配额。 本文详细介绍了 Azure 容器实例的默认配额和限制。

Azure 容器实例的计算、内存和存储资源的可用性因区域和操作系统而异。 有关详细信息，请参阅 [Azure 容器实例的资源可用性](container-instances-region-availability.md)。

使用 " [列出使用情况](/rest/api/container-instances/location/listusage) API" 查看订阅的某个区域的当前配额使用量。

## <a name="service-quotas-and-limits"></a>服务配额和限制

[!INCLUDE [container-instances-limits](../../includes/container-instances-limits.md)]

## <a name="next-steps"></a>后续步骤

某些默认限制和配额可以提高。 若要请求增加一个或多个资源（如果支持此功能），请提交 [Azure 支持请求][azure-support]（选择“配额”作为“问题类型”****）。

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
