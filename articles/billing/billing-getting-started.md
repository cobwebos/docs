---
title: 在 Azure 中避免意外的成本和管理计费
description: 了解如何在 Azure 帐单上避免意外费用。 将成本跟踪和管理功能用于 Azure 订阅。
author: bandersmsft
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: b64e84c3fff27675029ff35f27972a4aca014ec3
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2019
ms.locfileid: "68612076"
---
# <a name="prevent-unexpected-charges-with-azure-billing-and-cost-management"></a>通过 Azure 计费和成本管理来防止意外费用

注册 Azure 时，可以采取几种措施来更好地了解自己的支出：

- [定价计算器](https://azure.microsoft.com/pricing/calculator/)可在创建 Azure 资源之前提供成本估计。 

- [Azure 门户](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)可提供订阅的当前成本细分和预测。 

- 如果要对不同项目或团队的成本进行分组和了解，请查看[资源标记](../azure-resource-manager/resource-group-using-tags.md)。 如果组织有希望使用的报告系统，请查看[计费 API](billing-usage-rate-card-overview.md)。

- 如果订阅是通过企业协议 (EA) 创建的，则你可以在 Azure 门户中查看成本。 如果订阅是通过云解决方案提供商 (CSP) 或 Azure 赞助创建的，则以下某些功能可能不适用。 有关详细信息，请参阅[适用于 EA、CSP 和赞助的其他资源](#other-offers)。

- 如果订阅是免费试用套餐、[Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)、Azure 开放许可 (AIO) 或 BizSpark，则在用完所有额度后，订阅将自动禁用。 了解[支出限制](#spending-limit)，避免意外禁用订阅。

- 如果你已注册 [Azure 免费帐户](https://azure.microsoft.com/free/)，[可以免费使用某些最常用的 Azure 服务 12 个月](billing-create-free-services-included-free-account.md)。 请参考下面列出的建议，同时请参阅[避免为免费帐户付费](billing-avoid-charges-free-account.md)。

## <a name="get-estimated-costs-before-adding-azure-services"></a>在添加 Azure 服务之前获取估计成本

下面提供了有关使用以下工具估算成本的一些附加信息：
- Azure 定价计算器
- Azure 门户
- 支出限制

以下部分中的插图以美元显示示例定价。

### <a name="estimate-cost-online-using-the-pricing-calculator"></a>使用定价计算器在线估计成本

查看[定价计算器](https://azure.microsoft.com/pricing/calculator/)，获取感兴趣的服务的每月估计成本。 可以添加任何第一方 Azure 资源来获取估算成本。 在定价计算器中，可以更改货币类型。

![定价计算器菜单的屏幕截图](./media/billing-getting-started/pricing-calc.png)

例如，在定价计算器中，如果将某个 A1 Windows 虚拟机 (VM) 一直保持运行，则会按计算小时数以特定的每月金额估算其成本：

![定价计算器的屏幕截图，其中显示了 A1 Windows VM 的每月预算成本](./media/billing-getting-started/pricing-calcvm.png)

有关定价的详细信息，请参阅[定价常见问题解答](https://azure.microsoft.com/pricing/faq/)。 若要与 Azure 销售人员沟通，请拨打“常见问题解答”页顶部提供的电话号码。

### <a name="review-estimated-costs-in-the-azure-portal"></a>在 Azure 门户中查看估算成本

通常，在 Azure 门户中添加服务时，会有一个视图显示每月的估算成本（采用你的帐单货币）。 例如，选择 Windows VM 的大小时，可看到计算小时数的每月估计成本：

![示例：A1 Windows VM 及其每月估算成本](./media/billing-getting-started/vm-size-cost.png)

### <a name="spending-limit"></a>检查支出限制是否为开启状态

如果有使用信用额度的订阅，则将默认开启支出限制。 这样一来，当信用额度花完时，不再对信用卡计费。 请参阅 [Azure 产品/服务以及支出限制可用性的完整列表](https://azure.microsoft.com/support/legal/offer-details/)。

但是，在达到支出限制时，服务将会禁用。 这意味着 VM 会被解除分配。 若要避免服务中断，必须关闭支出限制。 任何超额都将对保存的信用卡收费。

若要查看支出限制是否为启用状态，请转到[帐户中心的订阅视图](https://account.windowsazure.com/Subscriptions)。 如果支出限制为“打开”，则会显示如下所示的横幅：

![帐户中心中，显示支出限制为开启状态的警告信息的屏幕截图](./media/billing-getting-started/spending-limit-banner.png)

单击该横幅，并按提示删除支出限制。 如果注册时未输入信用卡信息，则必须输入信息才能移除支出限制。 有关详细信息，请参阅 [Azure 支出限制 - 其工作原理以及如何将其启用或移除](https://azure.microsoft.com/pricing/spending-limits/)。

## <a name="use-budgets-and-cost-alerts"></a>使用预算和成本警报

可以创建[预算](../cost-management/tutorial-acm-create-budgets.md)来管理成本，并创建[警报](../cost-management/cost-mgt-alerts-monitor-usage-spending.md)以自动通知利益干系人支出异常和超支风险。 警报基于与预算和成本阈值相比的支出。

## <a name="monitor-costs-when-using-azure-services"></a>使用 Azure 服务时监视成本
可以使用以下工具监视成本：

- Tags
- 成本细分和消耗率
- 成本分析

### <a name="tags"></a> 将标记添加到资源以将计费数据分组

可以使用标记来对受支持服务的计费数据进行分组。 例如，如果为不同的团队运行多个 VM，可以使用标记按成本中心（例如人力资源、营销、财务等）或环境（例如生产、预生产、测试）将成本分类。

![显示在门户中设置标记的屏幕截图](./media/billing-getting-started/tags.png)

各标记显示了完全不同的成本报告视图。 例如，[成本分析视图](#costs)中会立即显示这些标记，而在详细使用情况 CSV 文件中，则需要在第一个计费期之后才会显示。

有关详细信息，请参阅[使用标记来组织 Azure 资源](../azure-resource-manager/resource-group-using-tags.md)。

### <a name="costs"></a> 监视成本细分和消耗率

运行 Azure 服务后，请定期检查费用。 可在 Azure 门户中查看当前支出和消耗率。

1. 访问 [Azure 门户中的“订阅”](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)并选择一个订阅。

2. 如果你的订阅支持该功能，则你可以查看成本细分和消耗率。

    ![Azure 门户中消耗率和明细的屏幕截图](./media/billing-getting-started/burn-rate.PNG)

3. 在左侧列表中单击[“成本分析”](../cost-management/quick-acm-cost-analysis.md)，以按资源查看成本细分。 添加服务后，请等待 24 小时以显示数据。

    ![Azure 门户中成本分析视图的屏幕截图](./media/billing-getting-started/cost-analysis.png)

4. 可按[标记](#tags)、资源类型、资源组和时间跨度等不同的属性进行筛选。 单击“应用”确认筛选条件；若要将视图导出为逗号分隔值 (.csv) 文件，请单击“下载”。  

5. 此外，还可以单击资源，查看每日费用历史记录以及每天的资源成本。

    ![Azure 门户中费用历史记录视图的屏幕截图](./media/billing-getting-started/costhistory.png)

将看到的成本与选择服务时看到的估算值进行比较。 如果看到的成本与估算值相差很大，请检查之前为资源选择的定价计划。

## <a name="optimize-and-reduce-costs"></a>优化和降低成本
如果你不熟悉成本管理的原理，请阅读[如何使用 Azure 成本管理优化云投资](../cost-management/cost-mgt-best-practices.md)。

在 Azure 门户中，还可以使用 VM 自动关闭和顾问建议来优化及降低 Azure 成本。

### <a name="consider-cost-cutting-features-like-auto-shutdown-for-vms"></a>考虑使用 VM 自动关闭等成本削减功能

可在 Azure 门户中为 VM 配置自动关闭，具体视方案而定。 有关详细信息，请参阅[使用 Azure 资源管理器为 VM 设置自动关闭功能](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/)。

![门户中的自动关闭选项的屏幕截图](./media/billing-getting-started/auto-shutdown.png)

自动关闭与在 VM 中使用电源选项进行关闭不同。 自动关闭会停止并解除分配 VM，以便停止额外的使用费。 有关详细信息，请参阅 [Linux VM](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) 和 [Windows VM](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) 有关 VM 状态的定价常见问题解答。

有关开发和测试环境的更多降低成本功能，请参阅 [Azure 开发测试实验室](https://azure.microsoft.com/services/devtest-lab/)。

### <a name="turn-on-and-review-azure-advisor-recommendations"></a>启用和查看 Azure 顾问建议

[Azure 顾问](../advisor/advisor-overview.md)可帮助你识别很少使用的资源，以此降低成本。 在 Azure 门户访问顾问：

![Azure 门户中的 Azure 顾问按钮的屏幕截图](./media/billing-getting-started/advisor-button.png)

可在顾问仪表板的“成本”选项卡中获得可行的建议： 

![顾问成本建议示例的屏幕截图](./media/billing-getting-started/advisor-action.png)

有关成本节省顾问建议的指导教程，请查看[根据建议优化成本](../cost-management/tutorial-acm-opt-recommendations.md)。

## <a name="review-costs-against-your-latest-invoice"></a>根据最新的发票检查成本

计费周期结束时，会提供最新的发票。 也可以[下载发票和详细使用情况文件](billing-download-azure-invoice-daily-usage-date.md)，以确保收费无误。 有关比较每日使用情况和发票的详细信息，请参阅[了解 Microsoft Azure 帐单](billing-understand-your-bill.md)。

### <a name="billing-api"></a>计费 API

使用 Azure 计费 API 以编程方式获取使用情况数据。 同时使用 RateCard API 和使用情况 API 获取计费使用情况。 有关详细信息，请参阅[深入了解 Microsoft Azure 资源消耗](billing-usage-rate-card-overview.md)。

## <a name="other-offers"></a>其他资源和特殊情况

### <a name="ea-csp-and-sponsorship-customers"></a>EA、CSP 和赞助客户
请咨询帐户管理员或 Azure 合作伙伴以开始使用。

| 产品/服务 | 资源 |
|-------------------------------|-----------------------------------------------------------------------------------|
| 企业协议 (EA) | [EA 门户](https://ea.azure.com/)、[帮助文档](https://ea.azure.com/helpdocs)和[ Power BI 报表](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-enterprise/) |
| 云解决方案提供商 (CSP) | 咨询提供商 |
| Azure 赞助 | [赞助门户](https://www.microsoftazuresponsorships.com/) |

如果管理大型组织的 IT，建议阅读 [Azure 企业基架](/azure/architecture/cloud-adoption-guide/subscription-governance)和[企业 IT 白皮书](https://download.microsoft.com/download/F/F/F/FFF60E6C-DBA1-4214-BEFD-3130C340B138/Azure_Onboarding_Guide_for_IT_Organizations_EN_US.pdf)（.pdf 下载，仅英文版）。

#### <a name="EA"></a> Azure 门户中的企业协议成本视图

企业成本视图目前为公共预览版。 注意事项：

- 订阅费用基于使用情况，并且不包括预付金额、超额用量、已包含数量、调整和税。 实际费用按注册级计算。
- Azure 门户中显示的金额可能不同于企业门户中显示的金额。 企业门户中的更新可能要花费几分钟时间来完成，之后，更改才会显示在 Azure 门户中。
- 如果未看到成本，可能是以下几种原因之一所致：
    - 你在订阅级别没有权限。 若要查看企业成本视图，必须在订阅级别是账单读者、读者、参与者或所有者。
    - 你是帐户所有者且你的注册管理员已禁用“AO 查看费用”设置。  请联系你的注册管理员以获取费用访问权限。
    - 你是部门管理员且你的注册管理员已禁用“DA 查看费用”设置。   联系你的注册管理员以获取访问权限。
    - 你是从某个渠道合作伙伴购买的 Azure，而该合作伙伴未发布定价信息。  
- 如果你在企业门户中更新与成本访问相关的设置，则更改过几分钟后才会显示在 Azure 门户中。
- 支出限制和发票指南不适用于 EA 订阅。

### <a name="check-your-subscription-and-access"></a>查看订阅和访问权限

若要查看成本，必须具有[对计费信息的订阅级访问权限](billing-manage-access.md)。 只有帐户管理员可以访问[帐户中心](https://account.azure.com/Subscriptions)、更改计费信息以及管理订阅。 帐户管理员是完成注册过程的人员。 有关详细信息，请参阅[添加或更改管理订阅或服务的 Azure 管理员角色](billing-add-change-azure-subscription-administrator.md)。

若要查看你是否为帐户管理员，请转到 [Azure 门户中的“订阅”](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)。 查看订阅列表并找到“我的角色”。  如果角色是“帐户管理员”，则表示你拥有完全特权。  如果显示“所有者”等其他角色，则表示你不拥有完全特权。 

![Azure 门户中订阅视图中的角色的屏幕截图](./media/billing-getting-started/sub-blade-view.PNG)

若要管理订阅以及更改计费信息，请[查找帐户管理员](billing-subscription-transfer.md#whoisaa)。请求帐户管理员来完成任务，或者[将订阅转移给你](billing-subscription-transfer.md)。

当帐户管理员不再属于组织，并且需要管理帐单时，请[联系我们](https://go.microsoft.com/fwlink/?linkid=2083458)。


### <a name="request-a-service-level-agreement-credit-for-a-service-incident"></a>请求服务事件的服务级别协议额度

服务级别协议 (SLA) 描述 Microsoft 关于运行时间和连接性方面的承诺。 当 Azure 服务遇到影响运行时间或连接的问题（通常称为“服务中断”）时，会报告一个服务事件。  如果我们未根据 SLA 中所述达到并保持每个服务的服务级别，则你有权请求一定的额度并扣除每月服务费的一部分。

若要请求额度：

1. 登录到 [Azure 门户](https://portal.azure.com/)。 如果你有多个帐户，请确保使用受 Azure 停机影响的帐户。 
2. 创建新的支持请求。
3. 在“问题类型”下，选择“计费”。  
4. 在“请求类型”下，选择“退款请求”。  
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
