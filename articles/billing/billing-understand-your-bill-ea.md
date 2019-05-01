---
title: 了解 Azure 企业帐单 |Microsoft Docs
description: 了解如何阅读并理解 Azure 企业协议的使用情况和帐单
services: ''
documentationcenter: ''
author: adpick
manager: dougeby
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/01/2019
ms.author: banders
ms.openlocfilehash: be20ad0583558949f65b9706f0a478669360bf3d
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2019
ms.locfileid: "64571210"
---
# <a name="understand-your-azure-enterprise-agreement-bill"></a>了解 Azure 企业协议计费

当签署企业协议的 Azure 客户超出组织的信用额度或使用信用额度未涵盖的服务时，将会收到发票。

组织的信用额度包括货币承诺。 货币承诺是组织提前支付的 Azure 服务使用费。 可以联系 Microsoft 客户经理或经销商，将货币承诺资金添加到企业协议。  

## <a name="invoices-for-most-customers"></a>对于大多数客户的发票

本部分不能应用于在澳大利亚、 日本、 或新加坡的 Azure 客户。 如果您是在这些国家/地区之一，请参阅[其他客户的发票](#invoices-for-other-customers)。

以下项之一在计费周期内发生时接收 Azure 发票付款：

- **服务超额**：组织的使用费超过了信用余额。
- **单独计费**：组织使用的服务未包括在信用额度中。 不管信用余额是多少，都会开具以下服务的发票：
    - Canonical
    - Citrix XenApp Essentials
    - Citrix XenDesktop
    - 已注册的用户
    - Openlogic
    - 远程访问权限 XenApp Essentials 已注册的用户
    - Ubuntu Advantage
    - Visual Studio Enterprise（每月）
    - Visual Studio Enterprise（每年）
    - Visual Studio Professional（每月）
    - Visual Studio Professional（每年）
- **市场费用**：组织的信用额度不包括 azure Marketplace 购买内容和使用情况。 因此，您通过开发票的 Marketplace 费用而不考虑你的信用额度余额。 在企业门户中，企业管理员可以启用和禁用 Marketplace 购买内容。

## <a name="review-charges-for-most-customers"></a>对于大多数客户查看费用
本部分不能应用于在澳大利亚、 日本、 或新加坡的 Azure 客户。 如果您是在这些国家/地区之一，请参阅[对于其他客户查看费用](#review-charges-for-other-customers)。

只有企业管理员才能查看和确认发票上的费用。 有关详细信息，请参阅[了解 Azure 中的 Azure 企业协议管理角色](billing-understand-ea-roles.md)。 如果你不知道谁是组织的企业管理员，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

发票将显示所有 Azure 使用情况，跟任何 Marketplace 费用。 如果信用额度余额，它被应用于 Azure 使用情况。

比较你的企业门户中所示的组合的总量**报表** > **使用情况摘要**和 Azure 发票。 中的金额**使用情况摘要**不含税。

1. 登录到[企业门户](https://ea.azure.com)。
1. 选择“报告”。
1. 在选项卡的右上角，将视图从“M”切换到“C”，然后匹配发票上的周期。  
    ![显示 M 的屏幕截图 + C 选项中使用情况摘要。](./media/billing-understand-your-bill-ea/ea-portal-usage-sumary-cm-option.png)
1. 总量**总体使用情况**并**Azure Marketplace**应匹配**总金额**发票上。
1. 若要获取有关费用的更多详细信息，请转到**下载使用情况**。  
    ![显示下载使用情况选项卡的屏幕截图](./media/billing-understand-your-bill-ea/ea-portal-download-usage.png)

## <a name="invoices-for-other-customers"></a>其他客户的发票

本部分仅适用于澳大利亚、 日本、 或新加坡的 Azure 客户。

你收到一个或多个 Azure 发票时将发生以下情况：

- **服务超额**：组织的使用费超过了信用余额。
- **单独计费**：组织使用的服务未包括在信用额度中。 不管信用余额是多少，都会开具以下服务的发票：
    - Canonical
    - Citrix XenApp Essentials
    - Citrix XenDesktop
    - 已注册的用户
    - Openlogic
    - 远程访问权限 XenApp Essentials 已注册的用户
    - Ubuntu Advantage
    - Visual Studio Enterprise（每月）
    - Visual Studio Enterprise（每年）
    - Visual Studio Professional（每月）
    - Visual Studio Professional（每年）
- **市场费用**：Azure 市场采购和使用不包括在组织的信用额度中，需单独计费。 在企业门户中，企业管理员可以启用和禁用 Marketplace 购买内容。

由于费用后服务超额用量和计费周期期间单独计费的费用，获得一张发票。 它包括两种类型的费用。 市场费用始终单独开票。

## <a name="review-charges-for-other-customers"></a>有关其他客户查看费用

如果你在澳大利亚、 日本或新加坡，本部分仅适用。

只有企业管理员才能查看和确认发票上的费用。 有关详细信息，请参阅[了解 Azure 中的 Azure 企业协议管理角色](billing-understand-ea-roles.md)。 如果你不知道谁是组织的企业管理员，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

### <a name="review-service-overage-invoice"></a>查看服务超额发票

比较你的企业门户中的总体使用情况量**报表** > **使用情况摘要**和服务超额发票。 服务超额发票包括超出组织信用额度的用量，和/或信用额度未涵盖的服务。 “使用情况摘要”中的金额不含税。

1. 登录到[企业门户](https://ea.azure.com)。
1. 选择“报告”。
1. 在选项卡的右上角，将视图从“M”切换到“C”，然后匹配发票上的周期。  
    ![显示 M 的屏幕截图 + C 选项中使用情况摘要。](./media/billing-understand-your-bill-ea/ea-portal-usage-sumary-cm-option.png)
1. “总用量”应与服务超额发票上的“总应收金额”相匹配。  
1. 若要获取有关费用的其他信息，请转到“下载使用情况” > “高级报告下载”。 报告不包括的税费或保留项的费用或 marketplace 费用。  
      ![屏幕快照，显示高级的报表下载下载使用情况选项卡上。](./media/billing-understand-your-bill-ea/ea-portal-download-usage-advanced.png)

下表列出发票上显示的以及企业门户“使用情况摘要”中显示的款项和说明：

|发票上的款项|“使用情况摘要”中的款项|描述|
|---|---|---|
|总应收金额|总用量|应用信用额度之前特定周期的税前使用费总额。|
|承诺用量|承诺用量|在该特定周期应用的信用额度。|
|总销售额|总超额|超出信用额的使用费总额。 此金额不含税。|
|税额|不适用|应用到特定周期的总销售额的税额。|
|总金额|不适用|应用信用额度并添加税额后应付的发票金额。|

### <a name="marketplace-invoice"></a>Marketplace 发票

如果你在澳大利亚、 日本或新加坡，本部分仅适用。

将企业门户的“报告” > “使用情况摘要”中的 Azure 市场总额与市场发票进行比较。 市场发票仅适用于 Azure 市场采购和使用。 “使用情况摘要”中的金额不含税。

1. 登录到[企业门户](https://ea.azure.com)。
1. 选择“报告”。
1. 在选项卡的右上角，将视图从“M”切换到“C”，然后匹配发票上的周期。  
     ![显示 M 的屏幕截图 + C 选项上使用情况摘要。](./media/billing-understand-your-bill-ea/ea-portal-usage-sumary-cm-option.png)  
1. “Azure 市场”总额应与市场发票上的“总销售额”相匹配。
1. 若要获取有关基于用量的费用的其他信息，请转到“下载使用情况”。 在“市场费用”下，选择“下载”。 此报告不包括税额，也不显示一次性采购。  
     ![显示的屏幕截图下载选项下的 Marketplace 费用。](./media/billing-understand-your-bill-ea/ea-portal-download-usage-marketplace.png)

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如果有疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤
- [查看和下载 Azure 使用情况和费用](billing-download-azure-daily-usage.md)
