---
title: 管理和请求资源配额
titleSuffix: Azure Machine Learning service
description: 本操作指南介绍 Azure 机器学习资源的各种配额以及如何查看和请求更多的配额。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nishankgu
ms.author: nigup
ms.date: 05/10/2019
ms.custom: seodec18
ms.openlocfilehash: bc0455b34ebdcb080e0cd702e58d8163228b60ff
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70278067"
---
# <a name="manage-and-request-quotas-for-azure-resources"></a>管理和请求 Azure 资源的配额

与其他 Azure 服务一样，与 Azure 机器学习服务关联的某些资源存在限制。 这些限制范围为可以创建的工作区数的上限，以限制用于模型定型或推理/计分的实际基础计算。 

本文提供关于为你的订阅中各种 Azure 资源预配置的限制的更多详细信息，同时还包括为每种资源类型请求配额增加的可用链接。 这些限制用于防止由于欺诈导致的预算超支，并且符合 Azure 容量限制。

在设计和纵向扩展生产工作负载的 Azure 机器学习服务资源时，请记住这些配额。 例如，如果你的群集无法访问指定的节点数，则你可能已达到订阅的 Azure 机器学习计算核心数限制。 如果想将限制或配额提高到默认值限制以上，可以免费打开联机客户支持请求。 由于 Azure 容量限制，无法将限制提高到超过下表中显示的最大限制值。 如果没有“最大限制”列，则资源没有可调整的限制。

## <a name="special-considerations"></a>特殊注意事项

+ 配额是一种信用限制，不附带容量保证。 如果有大规模容量需求，请与 Azure 支持部门联系。

+ 配额在订阅中的所有服务之间共享，包括 Azure 机器学习服务。 唯一的例外是 Azure 机器学习计算，它的配额独立于核心计算配额。 在评估容量需求时，请务必计算所有服务的配额使用情况。

+ 默认限制根据产品/服务类别类型（例如免费试用、即用即付）和系列（例如 Dv2、F、G 等）而有所不同。

## <a name="default-resource-quotas"></a>默认资源配额

下面是 Azure 订阅中各种资源类型的配额限制的细分条目。

> [!Important]
> 限制随时会变化。 始终可以在所有 Azure 的服务级别配额[文档](https://docs.microsoft.com/azure/azure-subscription-service-limits/)中找到最新的限制。

### <a name="virtual-machines"></a>虚拟机
可以在 Azure 订阅上跨服务或以独立方式预配虚拟机的数量有限。 基于总核心数和每个系列的区域级别也同样有此限制。

必须强调的是，虚拟机核心数既有区域总数限制，也有区域按大小系列（Dv2、F 等）限制，这两种限制单独实施。 例如，某个订阅的美国东部 VM 核心总数限制为 30，A 系列核心数限制为 30，D 系列核心数限制为 30。 该订阅可以部署 30 个 A1 VM，或者 30 个 D1 VM，或者同时部署这二者，但其总数不能超过 30 个核心（例如，10 个 A1 VM 和 20 个 D1 VM）。

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../../includes/azure-subscription-limits-azure-resource-manager.md)]

有关配额限制更详细的最新列表，请在[此处](https://docs.microsoft.com/azure/azure-subscription-service-limits)查看适用于 Azure 的配额文章。

### <a name="azure-machine-learning-compute"></a>Azure 机器学习计算
在 Azure 机器学习计算中，订阅中每个区域所允许的核心数和唯一计算资源数都有默认配额限制。 该配额独立于上述的 VM 核心配额，并且核心限制目前没有在这两种资源类型之间共享。

可用资源：
+ 每个区域的专用核心数的默认限制为 24-300，具体取决于你的订阅产品/服务类型。  可增加每个订阅的专用核心数。 请联系 Azure 支持以讨论增加选项。

+ 每个区域的低优先级核心数的默认限制为 24-300，具体取决于你的订阅产品/服务类型。  可增加每个订阅的低优先级核心数。 请联系 Azure 支持以讨论增加选项。

+ 每个区域的群集数默认限制为 100，最大限制为 200。 如果请求增加的配额超出此限制，请与 Azure 支持部门联系。

+ 下面是“其他严格限制”，不能超出这些限制。

| **资源** | **最大限制** |
| --- | --- |
| 每个资源组的最大工作区数 | 800 |
| 单个 Azure 机器学习计算 (AmlCompute) 资源中的最大节数点 | 100 个节点 |
| 每个节点的最大 GPU MPI 进程数 | 1-4 |
| 每个节点的最大 GPU 辅助角色数 | 1-4 |
| 最长作业生存期 | 90天<sup>1</sup> |
| 低优先级节点上的最大作业生存期 | 1天<sup>2</sup> |
| 每个节点的最大参数服务器数 | 1 |

<sup>1</sup> 最长生存期是指运行从开始到结束的时间。 已完成的运行会无限期保存；最长生存期内未完成的运行的数据不可访问。
对于低优先级节点上的<sup>2</sup>个作业，在存在容量约束时可能会被预先清空。 建议在作业中实施检查点操作。

### <a name="azure-machine-learning-pipelines"></a>Azure 机器学习管道
对于 Azure 机器学习管道，管道中的步骤数和订阅中每个区域基于计划的已发布管道的运行数有限制。
- 管道中允许的最大步骤数为30000
- 基于计划的运行和 blob 请求的最大数目每月每个订阅的已发布管道的已触发计划数为100000

> [!NOTE]
> 如果想要增加此限制，请联系 [Microsoft 支持](https://azure.microsoft.com/support/options/)。

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

   有一个警告提示，专门用于查看 Azure 机器学习计算配额。 如上所述，此配置独立于订阅上的计算配额。

1. 在左侧窗格中，选择 "**机器学习服务**"，然后从显示的列表中选择任何工作区

1. 在下一个边栏选项卡中，在“支持 + 故障排除部分”下，选择“使用情况 + 配额”以查看当前配额限制和使用情况。

1. 选择订阅以查看配额限制。 请记住筛选到所需的区域。


## <a name="request-quota-increases"></a>请求增加配额

如果想将限制或配额提高到默认值限制以上，可以免费[打开联机客户支持请求](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/)。

无法将限制提高到表中所示的最大限制值。 如果没有最大限制，则资源没有可调整的限制。 [这篇文章](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors)详细介绍了配额增加过程。

请求配额增加时，需要选择要请求提高配额的服务，这可能是机器学习服务配额、容器实例或存储配额的服务。 此外，对于 Azure 机器学习计算，只需单击“请求配额”按钮，然后按照上述步骤查看配额。

> [!NOTE]
> [免费试用版订阅](https://azure.microsoft.com/offers/ms-azr-0044p)不符合增加限制或配额的条件。 如果有[免费试用版订阅](https://azure.microsoft.com/offers/ms-azr-0044p)，可将其升级到[即用即付](https://azure.microsoft.com/offers/ms-azr-0003p/)订阅。 有关详细信息，请参阅[将 Azure 免费试用版订阅升级到即用即付订阅](../../billing/billing-upgrade-azure-subscription.md)和[免费试用版订阅常见问题解答](https://azure.microsoft.com/free/free-account-faq)。
