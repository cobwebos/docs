---
title: 在 Azure 中避免意外的成本和管理计费
description: 了解如何在 Azure 帐单上避免意外费用。 将成本跟踪和管理功能用于 Azure 帐户。
author: bandersmsft
manager: amberb
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: e5cdd7181e680dbc7406118afeb4aeb837d3aaf2
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74223910"
---
# <a name="prevent-unexpected-charges-with-azure-billing-and-cost-management"></a>通过 Azure 计费和成本管理来防止意外费用

注册 Azure 时，可以采取几种措施来更好地了解自己的支出：

- 在添加服务之前通过[定价计算器](https://azure.microsoft.com/pricing/calculator/)、Azure 价目表获取估计成本，也可在添加服务时通过 Azure 门户这样做。
- 通过[预算](../cost-management/tutorial-acm-create-budgets.md)、[警报](../cost-management/cost-mgt-alerts-monitor-usage-spending.md)和[成本分析](../cost-management/quick-acm-cost-analysis.md)监视成本。
- 通过与[详细的使用情况文件](billing-download-azure-invoice-daily-usage-date.md)进行比较，查看发票上的费用。
- 使用[计费](https://docs.microsoft.com/rest/api/billing/)和[消耗](https://docs.microsoft.com/rest/api/consumption/) API 将计费和成本数据与你自己的报表系统集成。
- 将其他资源和工具用于企业协议 (EA)、云解决方案提供商 (CSP) 和 Azure 赞助客户。
- [免费使用某些最常用的 Azure 服务 12 个月](billing-create-free-services-included-free-account.md)，这些服务可通过 [Azure 免费帐户](https://azure.microsoft.com/free/)获取。 请参考下面列出的建议，同时请参阅[避免为免费帐户付费](billing-avoid-charges-free-account.md)。

## <a name="get-estimated-costs-before-adding-azure-services"></a>在添加 Azure 服务之前获取估计成本

通过以下工具之一估算使用 Azure 服务的成本：
- Azure 定价计算器
- Azure 价目表
- Azure 门户

以下部分中的插图以美元显示示例定价。

### <a name="estimate-cost-online-using-the-pricing-calculator"></a>使用定价计算器在线估计成本

查看[定价计算器](https://azure.microsoft.com/pricing/calculator/)，获取要添加的服务的每月估计成本。 可以更改货币，以本地货币进行估算。

![定价计算器菜单的屏幕截图](./media/billing-getting-started/pricing-calc.png)

可以查看任何第一方 Azure 服务的估计成本。 例如，在下面的屏幕截图中，如果 A1 Windows 虚拟机 (VM) 一直在运行，则其成本预计为 66.96 美元/月（按计算小时数算）：

![定价计算器的屏幕截图，其中显示了 A1 Windows VM 的每月预算成本](./media/billing-getting-started/pricing-calcvm.png)

有关定价的详细信息，请参阅[定价常见问题解答](https://azure.microsoft.com/pricing/faq/)。 若要与 Azure 销售人员沟通，请拨打“常见问题解答”页顶部提供的电话号码。

### <a name="view-and-download-azure-price-sheet"></a>查看和下载 Azure 价目表

如果通过企业协议 (EA) 或 Microsoft 客户协议 (MCA) 访问 Azure，则可查看和下载 Azure 帐户的价目表。 价目表是一个 Excel 文件，其中包含所有 Azure 服务的价格。 有关详细信息，请参阅[查看和下载 Azure 定价](billing-ea-pricing.md)。

### <a name="review-estimated-costs-in-the-azure-portal"></a>在 Azure 门户中查看估计成本

可以在通过 Azure 门户添加服务时查看每月估计成本。 例如，选择 Windows VM 的大小时，可看到计算小时数的每月估计成本：

![示例：A1 Windows VM 及其每月估算成本](./media/billing-getting-started/vm-size-cost.png)

## <a name="monitor-costs-when-using-azure-services"></a>使用 Azure 服务时监视成本
可以使用以下工具监视成本：

- 预算和成本警报
- 成本分析

### <a name="track-costs-with-budgets-and-cost-alerts"></a>通过预算和成本警报跟踪成本

创建[预算](../cost-management/tutorial-acm-create-budgets.md)来管理成本，并创建[警报](../cost-management/cost-mgt-alerts-monitor-usage-spending.md)来自动通知利益干系人支出异常和超支。

### <a name="costs"></a> 使用成本分析探究和分析成本

运行 Azure 服务后，请定期检查成本以跟踪 Azure 支出。 可以使用成本分析了解 Azure 使用情况成本的来源。

1. 访问 [Azure 门户中的“成本管理 + 计费”页](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade)。

2. 单击屏幕左侧的“成本分析”，  按各种透视图（例如服务、位置和订阅）查看当前的成本细分。 添加服务或进行购买后，请等待 24 小时，以便系统显示数据。 默认情况下，成本分析显示你所在范围的成本。 例如，在下面的屏幕截图中，显示的是 Contoso 计费帐户的成本。 使用“范围”框可在成本分析中切换到不同的范围。 For more information about scopes, see [Understand and work with scopes](../cost-management/understand-work-scopes.md#scopes)

    ![Azure 门户中成本分析视图的屏幕截图](./media/billing-getting-started/cost-analysis.png)

4. 可按各种属性（例如标记、资源类型和时间跨度）进行筛选。 单击“添加筛选器”，添加某个属性的筛选器，然后选择要筛选的值。  选择“导出”  ，将视图导出为逗号分隔值 (.csv) 文件。

5. 此外，还可以单击图表的标签，查看该标签的每日支出历史记录。 For example:In the screenshot below, clicking on virtual machines displays the daily cost of running your VMs.

    ![Azure 门户中费用历史记录视图的屏幕截图](./media/billing-getting-started/costhistory.png)

## <a name="optimize-and-reduce-costs"></a>优化和降低成本
如果你不熟悉成本管理的原理，请阅读[如何使用 Azure 成本管理优化云投资](../cost-management/cost-mgt-best-practices.md)。

在 Azure 门户中，还可以使用 VM 自动关闭和顾问建议来优化及降低 Azure 成本。

### <a name="consider-cost-cutting-features-like-auto-shutdown-for-vms"></a>考虑使用 VM 自动关闭等成本削减功能

可在 Azure 门户中为 VM 配置自动关闭，具体视方案而定。 有关详细信息，请参阅[使用 Azure 资源管理器为 VM 设置自动关闭功能](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/)。

![门户中的自动关闭选项的屏幕截图](./media/billing-getting-started/auto-shutdown.png)

自动关闭与在 VM 中使用电源选项进行关闭不同。 自动关闭会停止并解除分配 VM，以便停止额外的使用费。 有关详细信息，请参阅 [Linux VM](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) 和 [Windows VM](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) 有关 VM 状态的定价常见问题解答。

有关开发和测试环境的更多降低成本功能，请参阅 [Azure 开发测试实验室](https://azure.microsoft.com/services/devtest-lab/)。

### <a name="turn-on-and-review-azure-advisor-recommendations"></a>启用和查看 Azure 顾问建议

[Azure 顾问](../advisor/advisor-overview.md)可帮助你识别很少使用的资源，以此降低成本。 在 Azure 门户中搜索“顾问”  ：

![Azure 门户中的 Azure 顾问按钮的屏幕截图](./media/billing-getting-started/advisor-button.png)

在左侧选择“成本”。  此时会在“成本”选项卡中显示可行的建议： 

![顾问成本建议示例的屏幕截图](./media/billing-getting-started/advisor-action.png)

有关成本节省顾问建议的指导教程，请查看[根据建议优化成本](../cost-management/tutorial-acm-opt-recommendations.md)。

## <a name="review-charges-against-your-latest-invoice"></a>根据最新发票查看费用

发票在计费周期结束时提供。 可以[下载发票和详细使用情况文件](billing-download-azure-invoice-daily-usage-date.md)并对其进行比较，确保收费无误。 有关比较每日使用情况和发票的详细信息，请参阅[了解 Microsoft Azure 帐单](billing-understand-your-bill.md)。

如果通过 Microsoft 客户协议 (MCA) 使用 Azure，也可[将发票与交易进行比较](billing-mca-understand-your-bill.md#view-transactions-for-an-invoice-in-the-azure-portal)，了解发票上的费用。

## <a name="integrate-with-billing-and-consumption-apis"></a>集成计费和消耗 API

使用 Azure [计费](https://docs.microsoft.com/rest/api/billing/)和[消耗](https://docs.microsoft.com/rest/api/consumption/) API 以编程方式获取计费和成本数据。 同时使用 RateCard API 和使用情况 API 获取计费使用情况。 有关详细信息，请参阅[深入了解 Microsoft Azure 资源消耗](billing-usage-rate-card-overview.md)。

## <a name="other-offers"></a>其他资源和特殊情况

### <a name="ea-csp-and-sponsorship-customers"></a>EA、CSP 和赞助客户
请咨询帐户管理员或 Azure 合作伙伴以开始使用。

| 产品/服务 | 资源 |
|-------------------------------|-----------------------------------------------------------------------------------|
| 企业协议 (EA) | [EA 门户](https://ea.azure.com/)、[帮助文档](https://ea.azure.com/helpdocs)和[ Power BI 报表](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-enterprise/) |
| 云解决方案提供商 (CSP) | 咨询提供商 |
| Azure 赞助 | [赞助门户](https://www.microsoftazuresponsorships.com/) |

如果管理大型组织的 IT，建议阅读 [Azure 企业基架](/azure/architecture/cloud-adoption-guide/subscription-governance)和[企业 IT 白皮书](https://download.microsoft.com/download/F/F/F/FFF60E6C-DBA1-4214-BEFD-3130C340B138/Azure_Onboarding_Guide_for_IT_Organizations_EN_US.pdf)（.pdf 下载，仅英文版）。

### <a name="EA"></a> Azure 门户中的企业协议成本视图

企业成本视图目前为公共预览版。 注意事项：

- 订阅费用基于使用情况，并且不包括预付金额、超额用量、已包含数量、调整和税。 实际费用按注册级计算。
- Azure 门户中显示的金额可能不同于企业门户中显示的金额。 企业门户中的更新可能要花费几分钟时间来完成，之后，更改才会显示在 Azure 门户中。
- 如果未看到成本，可能是以下几种原因之一所致：
    - 你在订阅级别没有权限。 若要查看企业成本视图，必须在订阅级别是账单读者、读者、参与者或所有者。
    - 你是帐户所有者且你的注册管理员已禁用“AO 查看费用”设置。  请联系你的注册管理员以获取费用访问权限。
    - 你是部门管理员且你的注册管理员已禁用“DA 查看费用”设置。   请联系你的注册管理员以获取访问权限。
    - 你是从某个渠道合作伙伴购买的 Azure，而该合作伙伴未发布定价信息。  
- 如果在企业门户中更新与成本、访问相关的设置，则更改过几分钟后才会显示在 Azure 门户中。
- 支出限制和发票指南不适用于 EA 订阅。

### <a name="check-your-subscription-and-access"></a>查看订阅和访问权限

若要查看成本，必须对成本或账单信息的帐户或订阅级别具有访问权限。 此访问权限因计费帐户的类型而异。 若要详细了解计费帐户并检查计费帐户的类型，请参阅[在 Azure 门户中查看计费帐户](billing-view-all-accounts.md)。

如果通过 Microsoft Online Service 计划 (MOSP) 计费帐户访问 Azure，请参阅[管理对 Azure 账单信息的访问权限](billing-manage-access.md)。

如果通过企业协议 (EA) 计费帐户访问 Azure，请参阅[了解 Azure 中的 Azure 企业协议管理角色](billing-understand-ea-roles.md)。

如果通过 Microsoft 客户协议 (MCA) 计费帐户访问 Azure，请参阅[了解 Azure 中的 Microsoft 客户协议管理角色](billing-understand-mca-roles.md)。

### <a name="request-a-service-level-agreement-credit-for-a-service-incident"></a>请求服务事件的服务级别协议额度

服务级别协议 (SLA) 描述 Microsoft 关于运行时间和连接性方面的承诺。 当 Azure 服务遇到影响运行时间或连接的问题（通常称为“服务中断”）时，会报告一个服务事件。  如果我们未达到并保持 SLA 中所述的每个服务的服务级别，则你有权请求扣除每月服务费的一部分。

若要请求额度：

1. 登录到 [Azure 门户](https://portal.azure.com/)。 如果你有多个帐户，请确保使用受 Azure 停机影响的帐户。
2. 创建新的支持请求。
3. 在“问题类型”下，选择“计费”。  
4. 在“问题类型”下，选择“退款请求”。  
5. 添加详细信息，指出你要请求 SLA 额度，并指定日期/时间/时区以及受影响的服务（VM、网站等）
6. 确认你的联系详细信息，然后选择“创建”以提交请求。 

SLA 阈值因服务而异。 例如，SQL Web 层的 SLA 为 99.9%，VM 的 SLA 为 99.95%，SQL 标准层的 SLA 为 99.99%。

对于某些服务，必须满足先决条件才能应用 SLA。 例如，对于虚拟机，必须在同一个可用性集中部署两个或更多个实例。

有关详细信息，请参阅[服务级别协议](https://azure.microsoft.com/support/legal/sla/)和 [Azure 服务的 SLA 摘要](https://azure.microsoft.com/support/legal/sla/summary/)文档。

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤
- 了解如何使用[支出限制](billing-spending-limit.md)来防止超支。
- 开始[分析 Azure 成本](../cost-management/quick-acm-cost-analysis.md)。
