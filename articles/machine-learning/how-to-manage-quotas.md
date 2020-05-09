---
title: 管理资源和配额
titleSuffix: Azure Machine Learning
description: 了解 Azure 机器学习资源的配额，以及如何请求更高的配额。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nishankgu
ms.author: nigup
ms.date: 05/08/2020
ms.custom: contperfq4
ms.openlocfilehash: b8af654e14d8a5fa48c60ae62c590c4c99e66edb
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2020
ms.locfileid: "82891518"
---
# <a name="manage--increase-quotas-for-resources-with-azure-machine-learning"></a>管理 & 增加 Azure 机器学习的资源配额
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本文为[Azure 机器学习](overview-what-is-azure-ml.md)用户提供了有关订阅的 Azure 资源预配置限制的详细信息。 此外，说明如何请求提高每种类型的资源的配额。 这些限制用于防止由于欺诈导致的预算超支，并且符合 Azure 容量限制。

与其他 Azure 服务一样，与 Azure 机器学习关联的某些资源存在限制。 这些限制范围从[工作区](concept-workspace.md)数量的上限到用于模型定型或推理/计分的实际基础计算的限制范围内。 

为生产工作负荷设计和缩放 Azure 机器学习资源时，请考虑到这些限制。 例如，如果群集未达到节点目标数量，那么可能是已达到订阅的 Azure 机器学习计算核心限制。 如果想将限制或配额提高到默认值限制以上，可以免费打开联机客户支持请求。 由于 Azure 容量限制，无法将限制提高到超过下表中显示的最大限制值。 如果没有“最大限制”列，则资源没有可调整的限制。

## <a name="special-considerations"></a>特殊注意事项

+ 配额是一种信用限制，不附带容量保证。 如果有大规模容量需求，请与 Azure 支持部门联系。

+ 配额在订阅中的所有服务之间共享，包括 Azure 机器学习。 唯一的例外是 Azure 机器学习计算，它的配额独立于核心计算配额。 在评估容量需求时，请务必计算所有服务的配额使用情况。

+ 默认限制根据产品类别类型（例如免费试用、即用即付和 VM 系列，如 Dv2、F、G 等）而有所不同。

## <a name="default-resource-quotas"></a>默认资源配额

下面是 Azure 订阅中各种资源类型的配额限制的细分条目。

> [!IMPORTANT]
> 限制随时会变化。 始终可以在所有 Azure 的服务级别配额[文档](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits/)中找到最新的限制。

### <a name="virtual-machines"></a>虚拟机
对于每个 Azure 订阅，可以跨服务使用或独立使用的虚拟机数量有限制。 基于总核心数和每个系列的区域级别也同样有此限制。

虚拟机核心数既有区域总数限制，也有区域按大小系列（Dv2、F 等）限制，这两种限制单独实施。 例如，假设某个订阅的美国东部 VM 核心总数限制为 30，A 系列核心数限制为 30，D 系列核心数限制为 30。 该订阅可以部署 30 个 A1 VM、30 个 D1 VM，或者两者的组合，但其总数不能超过 30 个核心（例如，10 个 A1 VM 和 20 个 D1 VM）。

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../includes/azure-subscription-limits-azure-resource-manager.md)]

有关配额限制的更详细和最新列表，请查看[Azure 范围配额一文](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)。

### <a name="azure-machine-learning-compute"></a>Azure 机器学习计算
对于[Azure 机器学习计算](concept-compute-target.md#azure-machine-learning-compute-managed)，订阅中每个区域所允许的核心数和唯一计算资源数都有默认的配额限制。 此配额不同于上述 VM 核心配额，核心限制不会在两种资源类型之间共享，因为 AmlCompute 是在托管的代理模型中部署资源的托管服务。

可用资源：
+ 每个区域的专用核心数的默认限制为 24 - 300 个，具体取决于订阅套餐的类型，EA 和 CSP 套餐类型的默认限制较高。  每个订阅的专用核心数可以提高，每个 VM 系列的此项限制各不相同。 某些专业化 VM 系列（例如 NCv2、NCv3 或 ND 系列）最初的默认限制为零个核心。 可以通过提出配额请求来联系 Azure 支持部门，以讨论限制提升选项。

+ 每个区域的低优先级核心数的默认限制为 100 - 3000 个，具体取决于订阅套餐的类型，EA 和 CSP 套餐类型的默认限制较高。 每个订阅的低优先级核心数可以提高，对不同的 VM 系列采用单个值。 请联系 Azure 支持以讨论增加选项。

+ 每个区域的群集数的默认限制为 200。 该数字在训练群集与计算实例（在配额消耗中被视为单节点群集）之间共享。 如果请求增加的配额超出此限制，请与 Azure 支持部门联系。

+ 命中后，不能超过其他严格限制。

| **资源** | **最大限制** |
| --- | --- |
| 每个资源组的最大工作区数 | 800 |
| 单个 Azure 机器学习计算 (AmlCompute) 资源中的最大节数点 | 100 个节点 |
| 每个节点的最大 GPU MPI 进程数 | 1-4 |
| 每个节点的最大 GPU 辅助角色数 | 1-4 |
| 最长作业生存期 | 90 天<sup>1</sup> |
| 低优先级节点上的最大作业生存期 | 7 天<sup>2</sup> |
| 每个节点的最大参数服务器数 | 1 |

<sup>1</sup> 最长生存期是指运行从开始到结束的时间。 已完成的运行会无限期保存；最长生存期内未完成的运行的数据不可访问。
<sup>2</sup> 每当存在容量约束时，低优先级节点上的作业可能会预先清空。 我们建议在作业中实施检查点。

### <a name="azure-machine-learning-pipelines"></a>Azure 机器学习管道
对于[Azure 机器学习管道](concept-ml-pipelines.md)，管道中的步骤数和订阅中每个区域基于计划的已发布管道的运行数有限制。
- 管道中允许的最大步骤数为 30,000
- 每个月根据每个订阅的已发布管道的博客触发计划执行的基于计划的运行数与 Blob 提取数之和的最大数目为 100,000

> [!NOTE]
> 如果想要增加此限制，请联系 [Microsoft 支持](https://azure.microsoft.com/support/options/)。

### <a name="container-instances"></a>容器实例

可以在给定时间段内（以小时为范围）或在你的整个订阅中启动的容器实例数量也有限制。

[!INCLUDE [container-instances-limits](../../includes/container-instances-limits.md)]

有关配额限制更详细的最新列表，请在[此处](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#container-instances-limits)查看适用于 Azure 的配额文章。

### <a name="storage"></a>存储
给定订阅中每个区域的存储帐户数量也有限制。 默认限制数量为 250，包括标准和高级存储帐户。 如果在某特定区域中需要的存储帐户多于 250 个，请通过 [Azure 支持](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/)提出请求。 Azure 存储团队将评审业务案例，对于特定区域最多可以批准 250 个存储帐户。


## <a name="workspace-level-quota"></a>工作区级别的配额

为了更好地管理不同[工作区](concept-workspace.md)之间 Azure 机器学习计算目标（Amlcompute）的资源分配，我们引入了一项功能，该功能允许你分发订阅级别配额（按 VM 系列）并在工作区级别配置它们。 默认行为是所有工作区的配额与任何 VM 系列的订阅级配额相同。 但是，随着工作区数量的增大，不同优先级的工作负荷将开始共享相同的资源，用户需要通过某种方式来更好地共享容量和避免资源争用问题。 Azure 机器学习允许用户针对每个工作区中的特定 VM 系列设置最大配额，通过其托管计算产品/服务来提供解决方案。 这类似于在工作区之间分配容量，而用户也可以选择过度分配资源来促成最大利用率。 

若要在工作区级别设置配额，请转到订阅中的任何工作区，并在左侧窗格中单击“用量 + 配额”。  然后选择“配置配额”选项卡查看配额，展开任一 VM 系列，并针对该 VM 系列下面列出的任何工作区设置配额限制。  请记住，不能设置负值或大于订阅级配额的值。 此外，可以看到，默认将为所有工作区分配整个订阅配额，以充分利用分配的配额。

[![Azure 机器学习工作区级别的配额](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)


> [!NOTE]
> 此功能仅在“企业”版中提供。 如果订阅中同时具有 "[基本" 和 "企业版](overview-what-is-azure-ml.md#sku)" 工作区，则可以使用此方法仅在企业工作区上设置配额。 “基本”工作区将继续采用订阅级配额，这是默认行为。
>
> 需要拥有订阅级别的权限才能在工作区级别设置配额。 这是一项强制性的要求，目的是避免单个工作区所有者编辑或提高其配额，然后开始侵占为其他工作区预留的资源。 因此，最适合由订阅管理员分配这些配额并将其分摊到各个工作区。



## <a name="view-your-usage-and-quotas"></a>查看使用情况和配额

通过 Azure 门户可以轻松查看各种资源的配额，例如虚拟机、存储和网络。

1. 在左窗格上，选择“所有服务”，然后在“一般”类别下选择“订阅”   。

1. 从订阅列表中选择要查找其配额的订阅。

   有一个警告提示，专门用于查看 Azure 机器学习计算配额  。 如上所述，此配置独立于订阅上的计算配额。

1. 在左侧窗格中选择“机器学习服务”，然后从显示的列表中选择任一工作区 

1. 在下一个边栏选项卡中，在“支持 + 故障排除部分”下，选择“使用情况 + 配额”以查看当前配额限制和使用情况   。

1. 选择订阅以查看配额限制。 请记住筛选到所需的区域。

1. 现在，可以在订阅级视图与工作区级视图之间切换：
    + **订阅视图：** 在此视图中可以按 VM 系列查看核心配额的用量，按工作区展开此节点，然后按实际群集名称进一步展开此节点。 此视图最适合用于快速了解特定 VM 系列的核心用量详细信息，以查看各工作区的细分，以及按每个工作区的基础群集进一步提供的细分。 此视图中的一般约定是“(用量/配额)”，其中，用量是当前的已扩展核心数，配额是资源可以扩展到的逻辑最大核心数。 对于每个**工作区**，配额是工作区级配额（如前所述），表示特定 VM 系列可扩展到的最大核心数。 同理，对于**群集**，配额实际上是对应于群集可扩展到的最大节点数（由 max_nodes 属性定义）的核心数。

    + **工作区视图：** 在此视图中可以按工作区查看核心配额的用量，按 VM 系列展开此节点，然后按实际群集名称进一步展开此节点。 此视图最适合用于快速了解特定工作区的核心用量详细信息，以查看各 VM 系列的细分，以及按每个系列的基础群集进一步提供的细分。

## <a name="request-quota-increases"></a>请求增加配额

如果想将限制或配额提高到默认值限制以上，可以免费[打开联机客户支持请求](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/)。

无法将限制提高到表中所示的最大限制值。 如果没有最大限制，则资源没有可调整的限制。 [请参阅分步说明如何增加配额](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors)。

请求配额增加时，需要选择要请求提高配额的服务，这可能是机器学习服务配额、容器实例或存储配额的服务。 此外，对于 Azure 机器学习计算，可以单击“请求配额”按钮，然后按照上述步骤查看配额  。

> [!NOTE]
> [免费试用版订阅](https://azure.microsoft.com/offers/ms-azr-0044p)不符合增加限制或配额的条件。 如果有[免费试用版订阅](https://azure.microsoft.com/offers/ms-azr-0044p)，可将其升级到[即用即付](https://azure.microsoft.com/offers/ms-azr-0003p/)订阅。 有关详细信息，请参阅[将 Azure 免费试用版订阅升级到即用即付订阅](../billing/billing-upgrade-azure-subscription.md)和[免费试用版订阅常见问题解答](https://azure.microsoft.com/free/free-account-faq)。

## <a name="next-steps"></a>后续步骤

通过以下文章，了解详细信息：

+ [计划 & 管理 Azure 机器学习的成本](concept-plan-manage-cost.md)

+ [如何增加配额](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors)。
