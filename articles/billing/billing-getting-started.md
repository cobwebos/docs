---
title: "防止意外成本，管理计费 - Azure | Microsoft Docs"
description: "了解如何在你的 Azure 帐单上避免意外费用。 将成本跟踪和管理功能用于 Microsoft Azure 订阅。"
services: 
documentationcenter: 
author: jlian
manager: mattstee
editor: 
tags: billing
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/20/2017
ms.author: jlian
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: c69334279e79352e3f7ad58d356f6891dd060b0a
ms.lasthandoff: 04/03/2017


---
# <a name="prevent-unexpected-costs-with-azure-billing-and-cost-management"></a>通过 Azure 计费和成本管理来防止意外成本

注册 Azure 时，可以做几件事情，以便更好地了解费用。 在 [Azure 门户](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)中选择订阅时，可查看当前成本明细和消耗率。 还可以[下载过去的发票和详细的使用情况文件](billing-download-azure-invoice-daily-usage-date.md)。 如果要对不同项目或团队使用的资源的成本进行分组，请查看[资源标记](../azure-resource-manager/resource-group-using-tags.md)。 如果组织有你希望使用的报告系统，请查看[计费 API](billing-usage-rate-card-overview.md)。 

有关每日使用情况的详细信息，请参阅[了解 Microsoft Azure 帐单](billing-understand-your-bill.md)。

如果是通过企业协议 (EA)、云解决方案提供商 (CSP) 或 Azure 赞助订阅的，则本文中的许多功能不适用。 但是，我们另一组可用于成本管理的工具。 请参阅[适用于 EA、CSP 和赞助的其他资源](#other-offers)。

如果你的订阅是免费试用版、[Visual Studio ](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)、Azure 开放许可 (AIO) 或 BizSpark，请了解[支出限制](#spending-limit)以避免订阅被意外禁用。 

## <a name="day-0-before-you-add-azure-services"></a>第 0 天：添加 Azure 服务之前

### <a name="estimate-cost-online-using-the-pricing-calculator"></a>使用定价计算器在线估计成本

查看[定价计算器](https://azure.microsoft.com/pricing/calculator/)和[总拥有成本计算器](https://aka.ms/azure-tco-calculator)，以估算感兴趣的服务的每月成本。 例如，如果 A1 Windows 虚拟机 (VM) 保持一直运行，则其计算小时数预计花费 66.96 美元/月：

![定价计算器的屏幕截图，图中 A1 Windows VM 预计每月花费 66.96 美元](./media/billing-getting-started/pricing-calc.PNG)

有关详细信息，请参阅[定价常见问题解答](https://azure.microsoft.com/pricing/faq/)。 如果想咨询具体人员，请拨打 1-800-867-1389。

### <a name="check-your-subscription-and-access"></a>查看订阅和访问权限

查看成本需要[订阅级别访问权限](../active-directory/role-based-access-control-configure.md)，但只有帐户管理员可以访问[帐户中心](https://account.windowsazure.com/Home/Index)、更改计费信息和管理订阅。 帐户管理员是完成注册过程的人员。 有关详细信息，请参阅[如何添加或更改 Azure 管理员角色](billing-add-change-azure-subscription-administrator.md)。

若要查看你是否是帐户管理员，请转到 [Azure 门户中的订阅边栏选项卡](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)，并查看你有权访问的订阅列表。 在**我的角色**下进行查看。 如果显示*帐户管理员*，则可以进行操作。 如果显示*所有者*等其他信息，则你不具有完全权限。

![Azure 门户中订阅视图中的角色的屏幕截图](./media/billing-getting-started/sub-blade-view.PNG)

如果不是帐户管理员，则其他人可以通过 [Azure Active Directory 基于角色的访问控制](../active-directory/role-based-access-control-configure.md) (RBAC) 授予部分访问权限。 若要管理订阅和更改计费信息，请[查找帐户管理员](billing-subscription-transfer.md#whoisaa)，请其执行该任务或请其[将订阅转交给你](billing-subscription-transfer.md)。

当帐户管理员不再属于组织，并且需要管理帐单时，请[与支持人员联系](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。 

### <a name="spending-limit"></a>检查支出限制是否为开启状态

如果有使用信用额度的订阅，则将默认开启支出限制。 这样一来，当信用额度花完时，将不再对信用卡计费。 请参阅 [Azure 产品/服务以及支出限制可用性的完整列表](https://azure.microsoft.com/support/legal/offer-details/)。

但是，在达到支出限制时，服务将被禁用。 这意味着你的 VM 将被解除分配。 若要避免服务中断，必须关闭支出限制。 任何超额都将对保存的信用卡收费。 

若要查看支出限制是否为开启状态，请转到[帐户中心的订阅视图](https://account.windowsazure.com/Subscriptions)。 如果支出限制为开启状态，将显示以下标题：

![帐户中心中，显示支出限制为开启状态的警告信息的屏幕截图](./media/billing-getting-started/spending-limit-banner.PNG)

单击该标题，并按照提示移除支出限制。 如果注册时未输入信用卡信息，则必须输入信息才能移除支出限制。 有关详细信息，请参阅 [Azure 支出限制 - 其工作原理以及如何将其启用或移除](https://azure.microsoft.com/pricing/spending-limits/)。

### <a name="set-up-billing-alerts"></a>设置计费警报

设置计费警报，以便在使用费用超过指定金额时收到电子邮件。 如果有每月信用额度，设置在达到指定金额时发出警报。 有关详细信息，请参阅[为 Microsoft Azure 订阅设置计费警报](billing-set-up-alerts.md)。

![计费警报电子邮件的屏幕截图](./media/billing-getting-started/billing-alert.png)

> [!NOTE]
> 此功能仍处于预览阶段，因此请定期查看使用情况。

设置第一个警报时，建议以定价计算器中的成本预估为准。

### <a name="understand-limits-and-quotas-for-your-subscription"></a>了解订阅的限制和配额

每个订阅的 CPU 内核数、IP 地址等内容有默认限制。 请注意这些限制。 有关详细信息，请参阅 [Azure 订阅和服务限制、配额与约束](../azure-subscription-service-limits.md)。 可以通过[与支持人员联系](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)来请求增加限制或配额。

## <a name="day-1-as-you-add-services"></a>第 1 天：添加服务时

### <a name="review-the-estimated-cost-in-the-portal"></a>在门户中查看预计成本

通常情况下，在 Azure 门户中添加服务时，会出现一个视图显示大概的每月预计成本。 例如，选择 Windows VM 的大小时，将看到计算小时数的每月预计成本：

![示例：一台 A1 Windows VM 预计每月花费 66.96 美元](./media/billing-getting-started/vm-size-cost.PNG)

### <a name="tags"></a>对资源添加标记以便对计费数据进行分组

可以使用标记来对受支持服务的计费数据进行分组。 例如，当运行不同团队的多个 VM 时，则可以使用标记按成本中心（人力资源、市场营销、财务）或环境（生产，预生产、测试）对成本进行分类。 

![显示在门户中设置标记的屏幕截图](./media/billing-getting-started/tags.PNG)

各标记显示了完全不同的成本报告视图。 例如，在[成本分析视图](#costs)中可立即查看，而在[详细使用情况.csv](#invoice-and-usage) 中则需在第一个计费期之后才能查看。

有关详细信息，请参阅[使用标记来组织 Azure 资源](../azure-resource-manager/resource-group-using-tags.md)。

### <a name="consider-enabling-cost-cutting-features-like-auto-shutdown-for-vms"></a>针对 VM，建议启用自动关闭等降低成本的功能

可在 Azure 门户中为 VM 配置自动关闭，具体视你的方案而定。 有关详细信息，请参阅[使用 Azure Resource Manager 的 VM 的自动关闭功能](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/)。

![门户中的自动关闭选项的屏幕截图](./media/billing-getting-started/auto-shutdown.PNG)

自动关闭与使用电源选项在 VM 中进行关闭不同。 自动关闭会停止并解除分配 VM，以便停止额外使用费。 有关详细信息，请参阅 [Linux VM](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) 和 [Windows VM](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) 有关 VM 状态的定价常见问题解答。

有关开发和测试环境的更多降低成本功能，请参阅 [Azure 开发测试实验室](https://azure.microsoft.com/services/devtest-lab/)。

## <a name="cost-reporting"></a> 两天及以上：使用服务后，查看使用情况

### <a name="costs"></a>定期查看门户中的成本明细和消耗率

服务开始运行后，请定期查看其费用。 可在 Azure 门户中查看当前费用和消耗率。 

1. 请访问 [Azure 门户中的订阅边栏选项卡](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)。

2. 选择要查看的订阅。 可能只有一个可供选择的选项。

3. 应该在弹出边栏选项卡中查看成本明细和消耗率。 你的产品/服务可能不支持此项（此时将在顶部附近显示一条警告）。 添加服务后请等待 24 小时让数据进行填充。
    
    ![Azure 门户中消耗率和明细的屏幕截图](./media/billing-getting-started/burn-rate.PNG)

4. 建议将视图固定到仪表板。

    ![将视图固定到仪表板的屏幕截图](./media/billing-getting-started/pin.PNG)

5. 将列表中的“成本分析”单击到左侧，以便按资源查看成本明细。

    ![Azure 门户中成本分析视图的屏幕截图](./media/billing-getting-started/cost-analysis.PNG)

6. 可按[标记](#tags)、资源组和时间跨度等不同的属性进行筛选。 单击“应用”确认筛选条件，单击“下载”可将视图导出为逗号分隔值 (.csv) 文件。

7. 单击某个资源，查看费用历史记录和其每天的费用。

    ![Azure 门户中费用历史记录视图的屏幕截图](./media/billing-getting-started/spend-history.PNG)

建议对比选择服务时看到的预计值查看现在的费用。 如果成本和预计值相差很大，请再次查看之前为资源选择的定价计划（例如 A1 或 A0 VM）。 

#### <a name="view-costs-for-all-your-subscriptions-in-the-billing-blade"></a>在计费边栏选项卡中查看所有订阅的成本

以帐户管理员的身份管理多个订阅时，可在[计费边栏选项卡](https://portal.azure.com/#blade/Microsoft_Azure_Billing/BillingBlade)中查看所有订阅的总账单金额和明细。 

<!-- Add screenshots of multiple subs each with billed usage -->

### <a name="turn-on-and-check-out-azure-advisor-recommendations"></a>打开和查看 Azure 顾问建议

[Azure 顾问](../advisor/advisor-overview.md)是一项预览功能，通过识别很少使用的资源来帮助你降低成本。 在 Azure 门户中打开它：

![Azure 门户中的 Azure 顾问按钮的屏幕截图](./media/billing-getting-started/advisor-button.PNG)

然后可在顾问仪表板的“成本”选项卡中获取可行性建议：

![顾问成本建议示例的屏幕截图](./media/billing-getting-started/advisor-action.PNG)

有关详细信息，请参阅[顾问成本建议](../advisor/advisor-cost-recommendations.md)。

### <a name="invoice-and-usage"></a>第一个计费期之后获取发票和详细使用情况

第一个计费期之后，可下载可移植文档格式 (.pdf) 的发票和逗号分隔值 (.csv) 的使用情况详细信息。 也可以选择使用电子邮件发送发票。 这些文件有助于了解纳税、折扣和信用额度后最终向你收费的项目。 如果订阅未附有付款方式，则可能无法使用这些文件。 有关详细信息，请参阅[如何获取 Azure 帐单发票和每日使用数据](billing-download-azure-invoice-daily-usage-date.md)和[了解你的 Microsoft Azure 帐单](billing-understand-your-bill.md)。

![.pdf 发票的屏幕截图](./media/billing-getting-started/invoice.png)

之前设置的标记将显示在详细使用情况 .csv 文件中：

![使用情况 .csv 中的标记的屏幕截图](./media/billing-getting-started/csv.png)

### <a name="billing-api"></a>计费 API

使用计费 API 以编程方式获取使用情况数据。 同时使用 RateCard API 和使用情况 API 获取计费使用情况。 有关详细信息，请参阅[深入了解 Microsoft Azure 资源消耗](billing-usage-rate-card-overview.md)。

## <a name="other-offers"></a>EA、CSP 和赞助的其他资源

请咨询帐户管理员或 Azure 合作伙伴以开始使用。

| 产品 | 资源 |
|-------------------------------|-----------------------------------------------------------------------------------|
| 企业协议 (EA) | [EA 门户](https://ea.azure.com/)、[帮助文档](https://ea.azure.com/helpdocs)和[ Power BI 报表](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-enterprise/) |
| 云解决方案提供商 (CSP) | 咨询提供商 |
| Azure 赞助 | [赞助门户](https://www.microsoftazuresponsorships.com/) |

如果你管理大型组织的 IT，建议阅读 [Azure 企业基架](../azure-resource-manager/resource-manager-subscription-governance.md)和[企业 IT 白皮书](http://download.microsoft.com/download/F/F/F/FFF60E6C-DBA1-4214-BEFD-3130C340B138/Azure_Onboarding_Guide_for_IT_Organizations_EN_US.pdf)（.pdf 下载，仅英文版）。

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员

如果需要帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。
