---
title: '& 配额管理资源'
titleSuffix: Azure Machine Learning
description: 了解 Azure 机器学习资源的配额以及如何请求更多配额。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nishankgu
ms.author: nigup
ms.date: 11/04/2019
ms.openlocfilehash: 443fcad17e5832a014dc463f0dd8a44d90667b75
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2020
ms.locfileid: "75646449"
---
# <a name="manage-and-request-quotas-for-azure-resources"></a>管理和请求 Azure 资源的配额
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本文详细介绍了订阅的 Azure 资源的预配置限制。 还介绍了如何为每种类型的资源请求配额增强。 这些限制用于防止由于欺诈导致的预算超支，并且符合 Azure 容量限制。

与其他 Azure 服务一样，与 Azure 机器学习关联的某些资源存在限制。 这些限制范围从工作区数量的上限到用于模型定型或推理/计分的实际基础计算的限制范围内。 

为生产工作负荷设计和缩放 Azure 机器学习资源时，请考虑这些限制。 例如，如果群集未达到目标节点数，则可能已达到订阅的 Azure 机器学习计算核心数限制。 如果想将限制或配额提高到默认值限制以上，可以免费打开联机客户支持请求。 由于 Azure 容量限制，无法将限制提高到超过下表中显示的最大限制值。 如果没有“最大限制”列，则资源没有可调整的限制。

## <a name="special-considerations"></a>特殊注意事项

+ 配额是一种信用限制，不附带容量保证。 如果有大规模容量需求，请与 Azure 支持部门联系。

+ 你的配额在你的订阅中的所有服务（包括 Azure 机器学习）之间共享。 唯一的例外是 Azure 机器学习计算，它的配额独立于核心计算配额。 在评估容量需求时，请务必计算所有服务的配额使用情况。

+ 默认限制根据产品类别类型（例如免费试用、即用即付和 VM 系列，如 Dv2、F、G 等）而有所不同。

## <a name="default-resource-quotas"></a>默认资源配额

下面是 Azure 订阅中各种资源类型的配额限制的细分条目。

> [!Important]
> 限制随时会变化。 始终可以在所有 Azure 的服务级别配额[文档](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits/)中找到最新的限制。

### <a name="virtual-machines"></a>虚拟机
对于每个 Azure 订阅，你可以对服务或独立使用的虚拟机数量有限制。 基于总核心数和每个系列的区域级别也同样有此限制。

虚拟机核心有一个区域总限制和一个每个大小系列（Dv2、F 等）限制，这两者都是单独强制执行的。 例如，某个订阅的美国东部 VM 核心总数限制为 30，A 系列核心数限制为 30，D 系列核心数限制为 30。 该订阅可以部署 30 个 A1 VM，或者 30 个 D1 VM，或者同时部署这二者，但其总数不能超过 30 个核心（例如，10 个 A1 VM 和 20 个 D1 VM）。

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../includes/azure-subscription-limits-azure-resource-manager.md)]

有关配额限制更详细的最新列表，请在[此处](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)查看适用于 Azure 的配额文章。

### <a name="azure-machine-learning-compute"></a>Azure 机器学习计算
在 Azure 机器学习计算中，订阅中每个区域所允许的核心数和唯一计算资源数都有默认配额限制。 此配额不同于上面的 VM 核心配额，并且内核限制不会在这两个资源类型之间共享，因为 AmlCompute 是一种托管服务，可在托管的代理模型中部署资源。

可用资源：
+ 每个区域的专用核心数的默认限制为 24-300，具体取决于你的订阅产品/服务类型以及 EA 和 CSP 产品/服务类型的默认值。  每个订阅的专用核心数可以增加，每个 VM 系列的数目各不相同。 某些专用 VM 系列（如 NCv2、NCv3 或 ND 系列）从默认的零核心开始。 请通过提出配额请求来与 Azure 支持部门联系，以讨论增加选项。

+ 每个区域的低优先级核心数的默认限制为 100-3000，具体取决于你的订阅产品/服务类型以及 EA 和 CSP 产品/服务类型的默认值。 每个订阅的低优先级核心数可以增加，并是跨 VM 系列的单个值。 请联系 Azure 支持以讨论增加选项。

+ 每个区域的群集的默认限制为200。 它们在定型群集和计算实例（出于配额目的被视为单个节点群集）之间共享。 如果请求增加的配额超出此限制，请与 Azure 支持部门联系。

+ 有 * * 个其他严格限制，一旦命中就无法超出。

| **资源** | **最大限制** |
| --- | --- |
| 每个资源组的最大工作区数 | 800 |
| 单个 Azure 机器学习计算 (AmlCompute) 资源中的最大节数点 | 100 个节点 |
| 每个节点的最大 GPU MPI 进程数 | 1-4 |
| 每个节点的最大 GPU 辅助角色数 | 1-4 |
| 最长作业生存期 | 90天<sup>1</sup> |
| 低优先级节点上的最大作业生存期 | 7天<sup>2</sup> |
| 每个节点的最大参数服务器数 | 第 |

<sup>1</sup> 最长生存期是指运行从开始到结束的时间。 已完成的运行会无限期保存；最长生存期内未完成的运行的数据不可访问。
如果存在容量限制，则低优先级节点上的<sup>2</sup>个作业可能会被抢占。 建议在作业中实施检查点操作。

### <a name="azure-machine-learning-pipelines"></a>Azure 机器学习管道
对于 Azure 机器学习管道，管道中的步骤数和订阅中每个区域基于计划的已发布管道的运行数有限制。
- 管道中允许的最大步骤数为30000
- 基于计划的运行和 blob 请求的最大数目每月每个订阅的已发布管道的已触发计划数为100000

> [!NOTE]
> 如果想要增加此限制，请联系 [Microsoft 支持](https://azure.microsoft.com/support/options/)。

### <a name="container-instances"></a>容器实例

可以在给定时间段内（以小时为范围）或在你的整个订阅中启动的容器实例数量也有限制。

[!INCLUDE [container-instances-limits](../../includes/container-instances-limits.md)]

有关配额限制更详细的最新列表，请在[此处](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#container-instances-limits)查看适用于 Azure 的配额文章。

### <a name="storage"></a>存储空间
给定订阅中每个区域的存储帐户数量也有限制。 默认限制为250，同时包含标准存储帐户和高级存储帐户。 如果在给定区域中需要250个以上的存储帐户，请通过[Azure 支持](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/)提出请求。 Azure 存储团队将评审业务案例，对于特定区域最多可以批准 250 个存储帐户。


## <a name="workspace-level-quota"></a>工作区级别配额

为了更好地管理不同工作区之间的 Amlcompute 资源分配，我们引入了一项功能，该功能允许你按 VM 系列分发订阅级别配额并在工作区级别配置它们。 默认行为是所有工作区都具有与任何 VM 系列的订阅级别配额相同的配额。 但是，随着工作区数量的增加以及不同优先级的工作负载开始共享相同的资源，用户需要一种方法来更好地共享容量并避免资源争用问题。 Azure 机器学习通过允许用户在每个工作区上设置特定 VM 系列的最大配额，来提供解决方案及其托管计算产品。 这类似于在工作区之间分布产能，用户也可以选择过度分配来推动最大利用率。 

若要在工作区级别设置配额，请在订阅中中转到任何工作区，然后单击左窗格中的 "**使用情况 + 配额**"。 然后选择 "**配置配额**" 选项卡以查看配额，展开任何 vm 系列，并为该 VM 系列下列出的任何工作区设置配额限制。 请记住，不能将负值或大于订阅级别配额的值设置为。 而且，正如您所看到的，默认情况下，所有工作区都分配了整个订阅配额，以允许充分利用分配的配额。

[![Azure 机器学习工作区级别配额](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)


> [!NOTE]
> 这只是一个企业版功能。 如果订阅中同时具有 "基本" 和 "企业版" 工作区，则可以使用此方法仅在企业工作区上设置配额。 你的基本工作区将继续具有订阅级别配额，这是默认行为。
>
> 你需要订阅级别的权限才能在工作区级别设置配额。 强制执行此操作，以使单个工作区所有者不会编辑或增加其配额，并开始 encroaching 为其他工作区预留的资源。 因此，订阅管理员最适合在工作区中分配和分发这些配额。



## <a name="view-your-usage-and-quotas"></a>查看使用情况和配额

通过 Azure 门户可以轻松查看各种资源的配额，例如虚拟机、存储和网络。

1. 在左窗格上，选择“所有服务”，然后在“一般”类别下选择“订阅”。

1. 从订阅列表中选择要查找其配额的订阅。

   有一个警告提示，专门用于查看 Azure 机器学习计算配额。 如上所述，此配置独立于订阅上的计算配额。

1. 在左侧窗格中，选择 "**机器学习服务**"，然后从显示的列表中选择任何工作区

1. 在下一个边栏选项卡中，在“支持 + 故障排除部分”下，选择“使用情况 + 配额”以查看当前配额限制和使用情况。

1. 选择订阅以查看配额限制。 请记住筛选到所需的区域。

1. 你现在可以在订阅级别视图和工作区级别视图之间切换：
    + **订阅视图：** 这样，你就可以查看 VM 系列使用的核心配额，按工作区展开它，并通过实际群集名称进一步扩展它。 此视图最适合用于快速了解特定 VM 系列的核心使用情况的详细信息，以查看工作区的细分，并使每个工作区的基础群集更进一步。 此视图中的常规约定是（使用情况/配额），其中使用情况是当前扩大的内核数，配额是资源可缩放到的逻辑最大核心数。 对于每个**工作区**，配额将是工作区级配额（如上文所述），它表示可针对特定 VM 系列扩展到的最大核心数。 对于**群集**，该配额实际上是与群集可缩放到 max_nodes 属性定义的节点的最大数量相对应的内核数。

    + **工作区视图：** 这允许你查看按工作区使用的核心配额，并按 VM 系列展开它，并通过实际群集名称进一步扩展它。 此视图非常适合于快速查看特定工作区的核心使用情况的详细信息，以查看 VM 系列的细分，并使每个系列的基础群集更进一步。

## <a name="request-quota-increases"></a>请求增加配额

如果想将限制或配额提高到默认值限制以上，可以免费[打开联机客户支持请求](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/)。

无法将限制提高到表中所示的最大限制值。 如果没有最大限制，则资源没有可调整的限制。 [这篇文章](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors)详细介绍了配额增加过程。

请求配额增加时，需要选择要请求提高配额的服务，这可能是机器学习服务配额、容器实例或存储配额的服务。 除了 Azure 机器学习计算，你还可以单击 "**请求配额**" 按钮，同时查看按上述步骤操作的配额。

> [!NOTE]
> [免费试用版订阅](https://azure.microsoft.com/offers/ms-azr-0044p)不符合增加限制或配额的条件。 如果有[免费试用版订阅](https://azure.microsoft.com/offers/ms-azr-0044p)，可将其升级到[即用即付](https://azure.microsoft.com/offers/ms-azr-0003p/)订阅。 有关详细信息，请参阅[将 Azure 免费试用版订阅升级到即用即付订阅](../billing/billing-upgrade-azure-subscription.md)和[免费试用版订阅常见问题解答](https://azure.microsoft.com/free/free-account-faq)。
