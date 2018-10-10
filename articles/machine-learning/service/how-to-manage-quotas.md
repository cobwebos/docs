---
title: 如何管理和请求 Azure 机器学习服务的配额
description: 本操作指南介绍 Azure 机器学习资源的各种配额以及如何查看和请求更多的配额。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nishankgu
ms.author: nigup
ms.date: 9/24/2018
ms.openlocfilehash: 0ee0a29be6a313e09cd77051dc29bc0131b57a40
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46997805"
---
# <a name="manage-and-request-quotas-for-azure-resources"></a>管理和请求 Azure 资源的配额

与其他 Azure 服务一样，与 Azure 机器学习服务关联的某些资源存在限制。 这些限制包括可以创建的工作区的数量上限以及用于训练或推断模型的实际基础计算的限制等。 本文提供关于为你的订阅中各种 Azure 资源预配置的限制的更多详细信息，同时还包括为每种资源类型请求配额增加的可用链接。

在设计和增加 Azure ML 资源时，请记住这些配额。 例如，如果你的群集没有达到指定的节点目标数量，那么可能是已达到订阅的 Batch AI 核心限制。 如果打算在 Batch AI 中运行生产工作负荷，可能需要将一个或多个配额提高到默认值以上。 如果想将限制或配额提高到默认值限制以上，可以免费打开联机客户支持请求。 无法将限制提高到超过下表中所示的最大限制值。 如果没有“最大限制”列，则资源没有可调整的限制。 

## <a name="special-considerations"></a>特殊注意事项

+ 配额是一种信用限制，不附带容量保证。 如果有大规模容量需求，请与 Azure 支持部门联系。

+ 除了 Batch AI 之外，你的配额在你订阅中的所有服务之间共享，Azure 机器学习是其中之一。 在评估容量需求时，请务必计算所有服务的配额使用情况。

+ 默认限制根据产品/服务类别类型（例如免费试用、即用即付）和系列（例如 Dv2、F、G 等）而有所不同。

## <a name="default-resource-quotas"></a>默认资源配额

下面是 Azure 订阅中各种资源类型的配额限制的细分条目。 

> [!Important]
> 限制随时会变化。 始终可以在所有 Azure 的服务级别配额[文档](https://docs.microsoft.com/azure/azure-subscription-service-limits/)中找到最新的限制。

### <a name="virtual-machines"></a>虚拟机 
可以在 Azure 订阅上跨服务或以独立方式预配虚拟机的数量有限。 基于总核心数和每个系列的区域级别也同样有此限制。

必须强调的是，虚拟机核心数既有区域总数限制，也有区域按大小系列（Dv2、F 等）限制，这两种限制单独实施。 例如，某个订阅的美国东部 VM 核心总数限制为 30，A 系列核心数限制为 30，D 系列核心数限制为 30。 该订阅可以部署 30 个 A1 VM，或者 30 个 D1 VM，或者同时部署这二者，但其总数不能超过 30 个核心（例如，10 个 A1 VM 和 20 个 D1 VM）。

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../../includes/azure-subscription-limits-azure-resource-manager.md)]

有关配额限制更详细的最新列表，请在[此处](https://docs.microsoft.com/azure/azure-subscription-service-limits#subscription-limits-1)查看适用于 Azure 的配额文章。

### <a name="batch-ai-clusters"></a>Batch AI 群集
在 Batch AI 中，订阅中按每个区域所允许的核心数和群集数都有默认配额限制。 Batch AI 配额独立于上述的 VM 核心配额，并且核心限制目前没有在这两种资源类型之间共享。

可用资源：
+ 每个区域的专用核心数的默认限制为 10 - 24。  可以增加每个 Batch AI 订阅的专用核心数。 请联系 Azure 支持以讨论增加选项。

+ 每个区域的低优先级核心数的默认限制为 10 - 24。  可以增加每个 Batch AI 订阅的低优先级核心数。 请联系 Azure 支持以讨论增加选项。

+ 每个区域的群集数的默认限制为 20，最大限制为 200。 如果请求增加的配额超出此限制，请与 Azure 支持部门联系。

有关配额限制更详细的最新列表，请在[此处](https://docs.microsoft.com/azure/azure-subscription-service-limits#batch-ai-limits)查看适用于 Azure 的配额文章。

### <a name="container-instances"></a>容器实例

可以在给定时间段内（以小时为范围）或在你的整个订阅中启动的容器实例数量也有限制。

[!INCLUDE [container-instances-limits](../../../includes/container-instances-limits.md)]

有关配额限制更详细的最新列表，请在[此处](https://docs.microsoft.com/azure/azure-subscription-service-limits#container-instances-limits)查看适用于 Azure 的配额文章。

### <a name="storage"></a>存储
给定订阅中每个区域的存储帐户数量也有限制。 默认限制数量为 200，包括标准和高级存储帐户。 如果在某特定区域中需要的存储帐户多于 200 个，请通过 [Azure 支持](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/)提出请求。 Azure 存储团队将评审业务案例，对于特定区域最多可以批准 250 个存储帐户。


## <a name="find-your-quotas"></a>查找你的配额

通过 Azure 门户可以轻松查看各种资源的配额，例如虚拟机、存储和网络。

1. 在左窗格上，选择“所有服务”，然后在“一般”类别下选择“订阅”。

1. 从订阅列表中选择要查找其配额的订阅。

   有一点需要注意，特别是查看 Batch AI 配额。 如上所述，此配置独立于订阅上的计算配额。 
   对于 Batch AI，在选择“所有服务”后，请搜索 Batch AI，然后打开服务。

1. 在“设置”下，选择“使用情况 + 配额”以查看当前的配额限制和使用情况。

1. 选择你的订阅以查看配额限制。 请记住筛选到所需的服务和区域。


## <a name="request-quota-increases"></a>请求增加配额

如果想将限制或配额提高到默认值限制以上，可以免费[打开联机客户支持请求](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/)。 

无法将限制提高到表中所示的最大限制值。 如果没有最大限制，则资源没有可调整的限制。 [这篇文章](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors)详细介绍了配额增加过程。

请求配额增加时，需要选择要请求提高配额的服务，这可能是 AzureML 配额、Batch AI 配额或存储配额的服务。 

> [!NOTE]
> [免费试用版订阅](https://azure.microsoft.com/offers/ms-azr-0044p)不符合增加限制或配额的条件。 如果有[免费试用版订阅](https://azure.microsoft.com/offers/ms-azr-0044p)，可将其升级到[即用即付](https://azure.microsoft.com/offers/ms-azr-0003p/)订阅。 有关详细信息，请参阅[将 Azure 免费试用版订阅升级到即用即付订阅](../../billing/billing-upgrade-azure-subscription.md)和[免费试用版订阅常见问题解答](https://azure.microsoft.com/free/free-account-faq)。
