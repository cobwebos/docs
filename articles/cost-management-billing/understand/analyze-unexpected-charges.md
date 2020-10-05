---
title: 分析意外的 Azure 费用
description: 了解如何分析 Azure 订阅的意外费用。
author: bandersmsft
ms.reviewer: adwise
ms.service: cost-management-billing
ms.subservice: cost-management
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: banders
ms.custom: contperfq1
ms.openlocfilehash: ae15fc59137e9bf6fb7bfd9fedfb410aa4e0931c
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91336856"
---
# <a name="analyze-unexpected-charges"></a>分析意外费用

为组织构建的云资源基础结构可能很复杂。 许多 Azure 资源类型可能会产生不同类型的费用。 Azure 资源可能由组织中的不同团队拥有，而这些团队可能对各种资源应用了不同类型的计费模型。 为了更好地了解费用，请使用以下部分中所述的一种或多种策略开始进行分析。

## <a name="review-invoice-for-resource-responsible-for-charge"></a>查看产生费用的资源的发票

在识别与某笔费用关联的资源时，知道 Azure 服务的购买方式将有助于确定可用的方法和工具。 若要确定哪种方法适合自己，请先[确定自己的 Azure 套餐类型](../costs/understand-cost-mgt-data.md#determine-your-offer-type)。 然后，在[支持的 Azure 套餐](../costs/understand-cost-mgt-data.md#supported-microsoft-azure-offers)列表中识别自己的客户类别。

以下文章中的详细步骤说明了如何根据客户类型审查帐单。 其中每篇文章都说明了如何下载包含给定计费周期内的用量和成本详细信息的 CSV 文件。

- [即用即付帐单审查过程](review-individual-bill.md#compare-invoiced-charges-with-usage-file)
- [企业协议帐单审查过程](review-enterprise-agreement-bill.md)
- [Microsoft 客户协议审查过程](review-customer-agreement-bill.md#analyze-your-azure-usage-charges)
- [Microsoft 合作伙伴协议审查过程](review-partner-agreement-bill.md#analyze-your-azure-usage-charges)

Azure 帐单按计量器聚合了每月的费用。  计量器用于跟踪某个资源在一段时间内的用量，并用于计算帐单费用。 当你创建单个 Azure 资源（例如虚拟机）时，将为该资源创建一个或多个计量实例。

根据要分析的帐单上显示的“计量器名称”筛选使用情况 CSV 文件，可以查看适用于该计量器的所有细目。  细目的“实例 ID”对应于生成了费用的实际 Azure 资源。 

识别相关的资源后，可以使用 Azure 成本管理中的成本分析进一步分析与该资源相关的成本。 若要详细了解如何使用成本分析，请参阅[开始分析成本](../costs/quick-acm-cost-analysis.md)。

## <a name="review-invoiced-charges-in-cost-analysis"></a>在成本分析中查看已开票费用

若要在 Azure 门户中查看发票详细信息，请导航到“成本分析”，查找与要分析的发票相关的范围。 选择“发票详细信息”视图。 发票详细信息会显示发票上的费用。

[![显示发票详细信息的示例](./media/analyze-unexpected-charges/invoice-details.png)](./media/analyze-unexpected-charges/invoice-details.png#lightbox)

查看发票详细信息即可在成本分析中确定具有意外成本的服务，并确定与该资源直接关联的资源。 例如，若要分析虚拟机服务的费用，请导航到“累计成本”视图。 然后，将粒度设为“每日”并筛选费用“服务名称:虚拟机”费用，然后按“资源”将费用分组。

[![显示虚拟机累计成本的示例](./media/analyze-unexpected-charges/virtual-machines.png)](./media/analyze-unexpected-charges/virtual-machines.png#lightbox)

## <a name="identify-spikes-in-cost-over-time"></a>识别一段时间内的成本高峰

有时，你可能不知道最近的哪些成本导致计费费用发生变化。 若要了解发生了哪些变化，可以使用成本分析来[查看一段时间内的每日或每月成本细分](../costs/cost-analysis-common-uses.md#view-costs-per-day-or-by-month)。 创建视图后，按“服务”或“资源”将费用分组，以识别发生的变化。   还可以将视图更改为折线图，以更好地可视化数据。 

![在成本分析中显示一段时间内的成本的示例](./media/analyze-unexpected-charges/costs-over-time.png)

## <a name="determine-resource-pricing-and-billing-model"></a>确定资源定价和计费模型

单个资源可能会在多个 Azure 产品和服务中产生应计费用。 请查看[各项 Azure 产品的定价](https://azure.microsoft.com/pricing/#product-pricing)页来详细了解每项 Azure 服务的定价。 例如，如果在 Azure 中创建了单个虚拟机 (VM)，则可能会创建以下计量器来跟踪其用量。 每个计量器可能应用不同的定价。

- 计算小时数
- IP 地址小时数
- 数据传入
- 数据发送
- 标准托管磁盘
- 标准托管磁盘操作数
- 标准 IO 磁盘
- 标准 IO 块 Blob 读取
- 标准 IO 块 Blob 写入
- 标准 IO 块 Blob 删除

创建 VM 后，每个计量器都会开始发出使用情况记录。 用量和计量器的价格在 Azure 计量系统中进行跟踪。 可以在使用情况 CSV 文件中查看用于计算账单的计量。

## <a name="find-people-responsible-for-the-resource-and-engage"></a>查找资源的负责人并与其沟通

通常，给定资源的负责团队会知道该资源发生了哪些更改。 在确定出现某些费用的原因时，与这些团队沟通会很有用。 例如，拥有方团队最近可能创建了资源、更新了资源的 SKU（从而更改了资源费率），或者由于代码更改而增大了资源上的负载。 请继续阅读以下部分，了解有关确定谁拥有某个资源的其他方法。

### <a name="analyze-the-audit-logs-for-the-resource"></a>分析资源的审核日志

如果你有权查看某个资源，应该可以访问其审核日志。 请查看日志以找出最近对资源进行更改的负责用户。 有关详细信息，请参阅[查看和检索 Azure 活动日志事件](../../azure-monitor/platform/activity-log-view.md)。

### <a name="analyze-user-permissions-to-the-resources-parent-scope"></a>分析用户在资源父范围的权限

对订阅或资源组拥有写入权限的人员通常会获得有关创建了资源的信息。 他们应该能够解释资源的用途，或者将你引领到知情的人。 若要识别在订阅范围拥有权限的人员，请参阅[查看角色分配](../../role-based-access-control/check-access.md#view-role-assignments)。 对于资源组，可以使用类似的过程。

## <a name="get-help-to-identify-charges"></a>获得识别费用方面的帮助

如果你已采用上述策略，但仍不知道为何会收到某笔费用，或者在计费问题方面需要其他帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

- 了解[如何通过 Azure 成本管理优化云投资](../costs/cost-mgt-best-practices.md)。