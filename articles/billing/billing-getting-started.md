---
title: 在 Azure 中防止意外成本，管理计费 | Microsoft Docs
description: 了解如何在 Azure 帐单上避免意外费用。 将成本跟踪和管理功能用于 Microsoft Azure 订阅。
services: ''
documentationcenter: ''
author: tonguyen10
manager: tonguyen
editor: ''
tags: billing
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2018
ms.author: tonguyen
ms.openlocfilehash: e4db9911697a4d79032b7a6358db642112a5a54e
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37062155"
---
# <a name="prevent-unexpected-charges-with-azure-billing-and-cost-management"></a>通过 Azure 计费和成本管理来防止意外费用

注册 Azure 时，可以做几件事情，以便更好地了解费用。 [定价计算器](https://azure.microsoft.com/pricing/calculator/)可在创建 Azure 资源之前提供成本估计。 [Azure 门户](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)可提供订阅的当前成本细分和预测。 如果要对不同项目或团队的成本进行分组和了解，请查看[资源标记](../azure-resource-manager/resource-group-using-tags.md)。 如果组织有希望使用的报告系统，请查看[计费 API](billing-usage-rate-card-overview.md)。 

- 如果订阅是企业协议 (EA)，则可在 Azure 门户中通过公共预览查看费用。 如果订阅通过云解决方案提供商 (CSP) 或 Azure 赞助，则以下某些功能可能不适用。 有关详细信息，请参阅[适用于 EA、CSP 和赞助的其他资源](#other-offers)。

- 如果订阅是免费试用版 [Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)、Azure 开放许可 (AIO) 或 BizSpark，则使用所有信用额度后，订阅将被自动禁用。 了解[支出限制](#spending-limit)，避免意外禁用订阅。

- 如果已注册 [Azure 免费帐户](https://azure.microsoft.com/free/)，[则可免费使用某些最常用的 Azure 服务 12 个月](billing-create-free-services-included-free-account.md)。 请参考下面列出的建议，同时请参阅[避免为免费帐户付费](billing-avoid-charges-free-account.md)。

## <a name="get-estimated-costs-before-adding-azure-services"></a>在添加 Azure 服务之前获取估计成本

### <a name="estimate-cost-online-using-the-pricing-calculator"></a>使用定价计算器在线估计成本

查看[定价计算器](https://azure.microsoft.com/pricing/calculator/)，获取感兴趣的服务的每月估计成本。 可以添加任何第一方 Azure 资源，以获取估计成本。

![定价计算器菜单的屏幕截图](./media/billing-getting-started/pricing-calc.png)

例如，如果 A1 Windows 虚拟机 (VM) 保持一直运行，则其计算小时数预计花费 66.96 美元/月：

![定价计算器的屏幕截图，图中 A1 Windows VM 预计每月花费 66.96 美元](./media/billing-getting-started/pricing-calcVM.png)

有关定价的详细信息，请参阅此[常见问题解答](https://azure.microsoft.com/pricing/faq/)。 或者，如果想要与 Azure 销售人员进行交流，请联系 1-800-867-1389。

### <a name="review-the-estimated-cost-in-the-azure-portal"></a>在 Azure 门户中查看估计成本

通常情况下，在 Azure 门户中添加服务时，会出现一个视图显示大概的每月预计成本。 例如，选择 Windows VM 的大小时，可看到计算小时数的每月估计成本：

![示例：一台 A1 Windows VM 预计每月花费 66.96 美元](./media/billing-getting-started/vm-size-cost.PNG)

### <a name="set-up-billing-alerts"></a>设置计费警报

设置计费警报，以便在使用费用超过指定金额时收到电子邮件。 如果有每月信用额度，设置在达到指定金额时发出警报。 有关详细信息，请参阅[为 Microsoft Azure 订阅设置计费警报](billing-set-up-alerts.md)。

![计费警报电子邮件的屏幕截图](./media/billing-getting-started/billing-alert.png)

> [!NOTE]
> 此功能仍处于预览阶段，因此请定期查看使用情况。

设置第一个警报时，建议以定价计算器中的成本预估为准。

### <a name="spending-limit"></a>检查支出限制是否为开启状态

如果有使用信用额度的订阅，则将默认开启支出限制。 这样一来，当信用额度花完时，不再对信用卡计费。 请参阅 [Azure 产品/服务以及支出限制可用性的完整列表](https://azure.microsoft.com/support/legal/offer-details/)。

但是，在达到支出限制时，服务会被禁用。 这意味着 VM 会被解除分配。 若要避免服务中断，必须关闭支出限制。 任何超额都将对保存的信用卡收费。 

若要查看支出限制是否为开启状态，请转到[帐户中心的订阅视图](https://account.windowsazure.com/Subscriptions)。 如果支出限制为开启状态，会显示以下标题：

![帐户中心中，显示支出限制为开启状态的警告信息的屏幕截图](./media/billing-getting-started/spending-limit-banner.PNG)

单击该标题，并按照提示移除支出限制。 如果注册时未输入信用卡信息，则必须输入信息才能移除支出限制。 有关详细信息，请参阅 [Azure 支出限制 - 其工作原理以及如何将其启用或移除](https://azure.microsoft.com/pricing/spending-limits/)。

## <a name="ways-to-monitor-your-costs-when-using-azure-services"></a>使用 Azure 服务时监视成本的方法

### <a name="tags"></a>对资源添加标记以便对计费数据进行分组

可以使用标记来对受支持服务的计费数据进行分组。 例如，当运行不同团队的多个 VM 时，则可以使用标记按成本中心（人力资源、市场营销、财务）或环境（生产，预生产、测试）对成本进行分类。 

![显示在门户中设置标记的屏幕截图](./media/billing-getting-started/tags.PNG)

各标记显示了完全不同的成本报告视图。 例如，在[成本分析视图](#costs)中可立即查看，而在[详细使用情况.csv](#invoice-and-usage) 中则需在第一个计费期之后才能查看。

有关详细信息，请参阅[使用标记来组织 Azure 资源](../azure-resource-manager/resource-group-using-tags.md)。

### <a name="costs"></a>定期查看门户中的成本明细和消耗率

服务开始运行后，请定期查看其费用。 可在 Azure 门户中查看当前费用和消耗率。 

1. 访问 [Azure 门户中的订阅边栏选项卡](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)并选择订阅。

2. 应该在弹出边栏选项卡中查看成本明细和消耗率。 产品/服务可能不支持此项（此时会在顶部附近显示一条警告）。

    ![Azure 门户中消耗率和明细的屏幕截图](./media/billing-getting-started/burn-rate.PNG)

3. 将列表中的“成本分析”单击到左侧，以便按资源查看成本明细。 添加服务后请等待 24 小时让数据进行填充。

    ![Azure 门户中成本分析视图的屏幕截图](./media/billing-getting-started/cost-analysis.PNG)

4. 可按[标记](#tags)、资源组和时间跨度等不同的属性进行筛选。 单击“应用”确认筛选条件，如果想要将视图导出为逗号分隔值 (.csv) 文件，请单击“下载”。

5. 此外，还可以单击资源，查看每日费用历史记录以及每天的资源成本。

    ![Azure 门户中费用历史记录视图的屏幕截图](./media/billing-getting-started/costhistory.PNG)

建议对比选择服务时看到的预计值查看现在的费用。 如果成本和预计值相差很大，请再次查看之前为资源选择的定价计划（例如 A1 或 A0 VM）。 

### <a name="consider-enabling-cost-cutting-features-like-auto-shutdown-for-vms"></a>针对 VM，建议启用自动关闭等降低成本的功能

可在 Azure 门户中为 VM 配置自动关闭，具体视方案而定。 有关详细信息，请参阅[使用 Azure 资源管理器的 VM 的自动关闭功能](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/)。

![门户中的自动关闭选项的屏幕截图](./media/billing-getting-started/auto-shutdown.PNG)

自动关闭与使用电源选项在 VM 中进行关闭不同。 自动关闭会停止并解除分配 VM，以便停止额外使用费。 有关详细信息，请参阅 [Linux VM](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) 和 [Windows VM](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) 有关 VM 状态的定价常见问题解答。

有关开发和测试环境的更多降低成本功能，请参阅 [Azure 开发测试实验室](https://azure.microsoft.com/services/devtest-lab/)。

### <a name="turn-on-and-check-out-azure-advisor-recommendations"></a>打开和查看 Azure 顾问建议

[Azure 顾问](../advisor/advisor-overview.md)是一项预览功能，通过识别很少使用的资源来帮助降低成本。 在 Azure 门户中打开它：

![Azure 门户中的 Azure 顾问按钮的屏幕截图](./media/billing-getting-started/advisor-button.PNG)

然后可在顾问仪表板的“成本”选项卡中获取可行性建议：

![顾问成本建议示例的屏幕截图](./media/billing-getting-started/advisor-action.PNG)

有关详细信息，请参阅[顾问成本建议](../advisor/advisor-cost-recommendations.md)。

## <a name="reviewing-costs-at-the-end-of-your-billing-cycle"></a>在计费周期结束时查看成本

在计费周期结束后，发票将变为可用。 还可以[下载过去的发票和详细使用情况文件](billing-download-azure-invoice-daily-usage-date.md)，确保收费正确。 有关比较每日使用情况和发票的详细信息，请参阅[了解 Microsoft Azure 帐单](billing-understand-your-bill.md)。

### <a name="billing-api"></a>计费 API

使用计费 API 以编程方式获取使用情况数据。 同时使用 RateCard API 和使用情况 API 获取计费使用情况。 有关详细信息，请参阅[深入了解 Microsoft Azure 资源消耗](billing-usage-rate-card-overview.md)。

## <a name="other-offers"></a>其他资源和特殊情况

### <a name="ea-csp-and-sponsorship-customers"></a>EA、CSP 和赞助客户
请咨询帐户管理员或 Azure 合作伙伴以开始使用。

| 产品 | 资源 |
|-------------------------------|-----------------------------------------------------------------------------------|
| 企业协议 (EA) | [EA 门户](https://ea.azure.com/)、[帮助文档](https://ea.azure.com/helpdocs)和[ Power BI 报表](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-enterprise/) |
| 云解决方案提供商 (CSP) | 咨询提供商 |
| Azure 赞助 | [赞助门户](https://www.microsoftazuresponsorships.com/) |

如果管理大型组织的 IT，建议阅读 [Azure 企业基架](/azure/architecture/cloud-adoption-guide/subscription-governance)和[企业 IT 白皮书](http://download.microsoft.com/download/F/F/F/FFF60E6C-DBA1-4214-BEFD-3130C340B138/Azure_Onboarding_Guide_for_IT_Organizations_EN_US.pdf)（.pdf 下载，仅英文版）。

#### <a name="EA"></a>在 Azure 门户中预览“企业协议”成本视图 

企业成本视图目前为公共预览版。 注意事项：
- 订阅费用基于使用情况，且独立于预付金额、超额用量、已包含数量、调整和税。 实际费用按注册级计算。 
- 与企业门户中的值相比，Azure 门户中显示的数量可能会存在延迟。  
- 如果未看见费用，可能是以下几种原因之一所致：
    - 你在订阅级别的 RBAC 权限不足。 若要查看企业成本视图，必须在订阅级别是账单读者、读者、参与者或所有者。
    - 你是帐户所有者且你的注册管理员已禁用“AO 查看费用”设置。  请联系你的注册管理员以获取费用访问权限。 
    - 你是部门管理员且你的注册管理员已禁用“DA 查看费用”设置。  请联系注册管理员以获取访问权限。 
    - 是通过一个通道合作伙伴购买的 Azure，而该合作伙伴尚未发布定价信息。  
- 与费用访问相关的设置在企业门户中更新后，Azure 门户几分钟后才会反映相应更改。
- 支出限制、帐单警报和发票指南不适用于 EA 订阅。

### <a name="check-your-subscription-and-access"></a>查看订阅和访问权限

查看成本需要[对计费信息具有订阅级别访问权限](billing-manage-access.md)，但只有帐户管理员可以访问[帐户中心](https://account.azure.com/Subscriptions)、更改计费信息和管理订阅。 帐户管理员是完成注册过程的人员。 有关详细信息，请参阅[添加或更改管理订阅或服务的 Azure 管理员角色](billing-add-change-azure-subscription-administrator.md)。

要查看你是否是帐户管理员，请转到 [Azure 门户中的订阅边栏选项卡](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)，并查看你有权访问的订阅列表。 在“我的角色”下查看。 如果显示*帐户管理员*，则可以进行操作。 如果显示所有者等其他信息，则表示没有完全权限。

![Azure 门户中订阅视图中的角色的屏幕截图](./media/billing-getting-started/sub-blade-view.PNG)

如果不是帐户管理员，则其他人可以通过 [Azure Active Directory 基于角色的访问控制](../role-based-access-control/role-assignments-portal.md) (RBAC) 授予部分访问权限。 要管理订阅和更改计费信息，请[查找帐户管理员](billing-subscription-transfer.md#whoisaa)，请其执行该任务或请其[将订阅转交给你](billing-subscription-transfer.md)。

当帐户管理员不再属于组织，并且需要管理帐单时，请[与支持人员联系](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。 
## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员

如果需要帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。
