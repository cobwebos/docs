---
title: 管理资源和配额
titleSuffix: Azure Machine Learning
description: 了解 Azure 机器学习资源的配额以及如何请求更多配额。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: jmartens
author: nishankgu
ms.author: nigup
ms.date: 10/13/2020
ms.topic: conceptual
ms.custom: troubleshooting,contperfq4, contperfq2
ms.openlocfilehash: a81af14992c8557c245ab3a1073f031a6c505084
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92019386"
---
# <a name="manage--increase-quotas-for-resources-with-azure-machine-learning"></a>管理和增加 Azure 机器学习资源的配额

Azure 使用限制和配额来防止因欺诈而超出运行的预算，并遵循 Azure 容量限制。 对于生产工作负荷，请考虑这些限制。 在本文中，你将了解：

> [!div class="checklist"]
> + 与 [Azure 机器学习](overview-what-is-azure-ml.md)相关的 Azure 资源的默认限制。
> + 查看配额和限制。
> + 创建工作区级配额。
> + 请求配额增加。
> + 专用终结点和 DNS 配额。

除了管理配额，还可以了解如何[计划和管理 Azure 机器学习的成本](concept-plan-manage-cost.md)。

## <a name="special-considerations"></a>特殊注意事项

+ 配额是一种信用限制，不附带容量保证。 如果有大规模的容量需求， [请联系 Azure 支持人员以增加配额](#request-quota-increases)。

+ 配额在你的订阅中的所有服务之间共享，包括 Azure 机器学习。 计算容量时计算所有服务的使用量。
    + Azure 机器学习计算是一个例外，它具有与核心计算配额不同的配额。 

+ 默认限制根据产品类别类型（例如免费试用、即用即付和虚拟机 (VM) 系列（如 Dv2、F、G 等）而有所不同。

## <a name="default-resource-quotas"></a>默认资源配额

在本部分中，你将了解以下资源的默认和最大配额限制：

+ 虚拟机
+ Azure 机器学习计算
+ Azure 机器学习管道
+ 容器实例
+ 存储

> [!IMPORTANT]
> 限制随时会变化。 始终可以在所有 Azure 的服务级别配额[文档](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits/)中找到最新的限制。

### <a name="virtual-machines"></a>虚拟机
每个 Azure 订阅对所有服务中的虚拟机数量有限制。 虚拟机核心的每个大小系列有一个区域总限制和一个地区限制 (Dv2、F 等 ) 。 这两个限制都是单独强制的。

例如，假设某个订阅的美国东部 VM 核心总数限制为 30，A 系列核心数限制为 30，D 系列核心数限制为 30。 此订阅允许部署30个 A1 Vm 或30个 D1 Vm，或者两个以上的组合（不超过30个核心）。

对于虚拟机的限制，不能高于下表中显示的值。

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="azure-machine-learning-compute"></a>Azure 机器学习计算
[Azure 机器学习计算](concept-compute-target.md#azure-machine-learning-compute-managed) 在一个订阅中每个区域所允许的核心数和唯一计算资源数都有默认的配额限制。 此配额与上一部分中的 VM 核心配额不同。

[请求增加配额](#request-quota-increases) 以提高此部分中显示的 **最大限制** 。

可用资源：
+ **每个区域的专用核心** 数的默认限制为 24-300，具体取决于你的订阅产品/服务类型。  每个 VM 系列可以增加每个订阅的专用核心数。 专用 VM 系列（如 NCv2、NCv3 或 ND 系列）从默认的零核心开始。

+ **每个区域的低优先级核心** 数的默认限制为 100-3000，具体取决于你的订阅产品/服务类型。 每个订阅的低优先级核心数可以提高，对不同的 VM 系列采用单个值。

+ **每个区域的群集** 的默认限制为200。 该数字在训练群集与计算实例（在配额消耗中被视为单节点群集）之间共享。

下表显示了不能超过的其他限制。

| **资源** | **最大限制** |
| --- | --- |
| 每个资源组的工作区 | 800 |
| 单个 Azure 机器学习中的节点计算 (AmlCompute) 资源 | 100 个节点 |
| 每个节点的 GPU MPI 进程数 | 1-4 |
| 每个节点的 GPU 辅助角色 | 1-4 |
| 作业生存期 | 21 天<sup>1</sup> |
| Low-Priority 节点上的作业生存期 | 7 天<sup>2</sup> |
| 每个节点的参数服务器 | 1 |

<sup>1</sup> 最大生存期是指运行开始和结束之间的持续时间。 已完成的运行无限期保留。 在最大生存期内未完成的运行的数据不可访问。
<sup>2</sup> 每当存在容量约束时，低优先级节点上的作业可能会预先清空。 建议你在作业中实施检查点。

### <a name="azure-machine-learning-pipelines"></a>Azure 机器学习管道
[Azure 机器学习管道](concept-ml-pipelines.md) 具有以下限制。

| **资源** | **限制** |
| --- | --- |
| 管道中的步骤 | 30,000 |
| 每个资源组的工作区 | 800 |

### <a name="container-instances"></a>容器实例

有关详细信息，请参阅 [容器实例限制](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#container-instances-limits)。

### <a name="storage"></a>存储
每个订阅每个区域的 Azure 存储帐户限制为250个存储帐户。 这包括标准和高级存储帐户。

若要增加此限制，请通过 [Azure 支持](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/)提出请求。 Azure 存储团队将评审你的案例，并且最多可为一个区域批准250个存储帐户。


## <a name="workspace-level-quota"></a>工作区级别配额

使用工作区级配额来管理同一订阅中多个 [工作区](concept-workspace.md) 之间 Azure 机器学习计算目标分配。

默认情况下，所有工作区与 VM 系列的订阅级别配额共享相同的配额。 但是，你可以在订阅中的工作区上设置单个 VM 系列的最大配额。 这使你可以共享容量并避免资源争用问题：

1. 导航到订阅中的任何工作区。
1. 在左窗格中，选择 " **使用情况 + 配额**"。
1. 选择 " **配置配额** " 选项卡以查看配额。
1. 展开 VM 系列。
1. 在该 VM 家族下列出的任何工作区上设置配额限制。

不能将负值或大于订阅级别配额的值设置为。

[![Azure 机器学习工作区级别的配额](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)

> [!NOTE]
> 需要拥有订阅级别的权限才能在工作区级别设置配额。

## <a name="view-your-usage-and-quotas"></a>查看使用情况和配额

若要查看各种 Azure 资源（例如虚拟机、存储、网络）的配额，请使用 Azure 门户：

1. 在左窗格上，选择“所有服务”，然后在“一般”类别下选择“订阅” 。

2. 从订阅列表中选择要查找其配额的订阅。

3. 选择“使用情况 + 配额”以查看当前的配额限制和使用情况。 使用筛选器选择提供者和位置。 

订阅上的 Azure 机器学习计算配额与其他 Azure 配额分开管理。 

1. 导航到 Azure 门户中的 **Azure 机器学习** 工作区。

2. 在左窗格中的 " **支持 + 故障排除" 部分** 下，选择 " **使用情况 + 配额** " 以查看当前配额限制和使用情况。

3. 选择订阅以查看配额限制。 请记住筛选到所需的区域。

4. 可以在订阅级别视图和工作区级别视图之间切换。

## <a name="request-quota-increases"></a>请求增加配额

若要提高限制或配额超过默认限制，请免费 [打开联机客户支持请求](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/) 。

限制不能超过上表中显示的最大限制值。 如果没有最大限制，则无法调整资源的限制。

请求增加配额时，请选择要向其发出配额的服务。 例如 Azure 机器学习、容器实例、存储等。对于 Azure 机器学习计算，你可以在按上述步骤查看配额时选择 " **请求配额** " 按钮。

> [!NOTE]
> [免费试用版订阅](https://azure.microsoft.com/offers/ms-azr-0044p)不符合增加限制或配额的条件。 如果有[免费试用版订阅](https://azure.microsoft.com/offers/ms-azr-0044p)，可将其升级到[即用即付](https://azure.microsoft.com/offers/ms-azr-0003p/)订阅。 有关详细信息，请参阅[将 Azure 免费试用版订阅升级到即用即付订阅](../billing/billing-upgrade-azure-subscription.md)和[免费试用版订阅常见问题解答](https://azure.microsoft.com/free/free-account-faq)。

## <a name="private-endpoint-and-private-dns-quota-increases"></a>专用终结点和专用 DNS 配额增加

可在订阅中创建的专用终结点和专用 DNS 区域的数目存在限制。

尽管 Azure 机器学习会在 (customer) 订阅中创建资源，但在某些情况下，会在 Microsoft 拥有的订阅中创建资源。

 在以下方案中，可能需要在 Microsoft 拥有的订阅中请求配额限制：

* __启用了专用链接的工作区，其中包含客户托管的密钥 (CMK) __
* __虚拟网络后的工作区的 Azure 容器注册表__
* 将__启用了专用链接的 Azure Kubernetes Service 群集附加到你的工作区__。

若要请求对这些方案进行限制，请使用以下步骤：

1. [创建 Azure 支持请求](/azure/azure-portal/supportability/how-to-create-azure-support-request#create-a-support-request) ，并从 " __基本__ 信息" 部分中选择以下选项：

    | 字段 | 选项 |
    | ----- | ----- |
    | 问题类型 | 技术条件 |
    | 服务 | 我的服务。 在下拉列表中选择 " __机器学习__ "。 |
    | 问题类型 | 工作区安装、SDK 和 CLI |
    | 问题子类型 | 预配或管理工作区时出现问题 |

2. 在 __详细信息__ 部分中，使用 " __说明__ " 字段提供要使用的 Azure 区域以及计划使用的方案。 如果需要为多个订阅请求配额增加，还请在此字段中列出订阅 Id。

3. 选择 " __创建__ " 以创建请求。

## <a name="next-steps"></a>后续步骤

+ [计划和管理 Azure 机器学习的成本](concept-plan-manage-cost.md)
