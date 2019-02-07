---
title: 了解 Azure Enterprise 的帐单 | Microsoft Docs
description: 了解如何阅读和了解签署企业协议的 Azure 客户的用量和帐单
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
ms.date: 11/02/2018
ms.author: banders
ms.openlocfilehash: 36ce4d96e02bac1eae1791acf811da468726b4a6
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/25/2019
ms.locfileid: "54902697"
---
# <a name="understand-your-bill-for-azure-customers-with-an-enterprise-agreement"></a>了解签署了企业协议的 Azure 客户的帐单

当签署企业协议的 Azure 客户超出组织的信用额度或使用信用额度未涵盖的服务时，将会收到发票。 

组织的信用额度包括货币承诺。 货币承诺是组织提前支付的 Azure 服务使用费。 可以联系 Microsoft 客户经理或经销商，将货币承诺资金添加到企业协议。  

## <a name="when-credit-exceeded-or-doesnt-apply"></a>超出信用额度时或不适用

发生以下情况时，你会收到一份或多份发票：

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
- **市场费用**：Azure 市场采购和使用不包括在组织的信用额度中，需单独计费。 企业管理员可在企业门户中为其组织启用和禁用市场采购。 

在计费周期内，如果由于服务超额和单独计费而产生费用，则你会收到一份包括这两种费用类型的发票。 市场费用始终单独开票。

## <a name="review-charges"></a>查看费用

只有企业管理员才能查看和确认发票上的费用。 有关详细信息，请参阅[了解 Azure 中的 Azure 企业协议管理角色](billing-understand-ea-roles.md)。 如果你不知道谁是组织的企业管理员，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

### <a name="review-service-overage-invoice"></a>查看服务超额发票

在企业门户的“报告” > “使用情况摘要”中的总用量与服务超额发票进行比较。 服务超额发票包括超出组织信用额度的用量，和/或信用额度未涵盖的服务。 “使用情况摘要”中的金额不含税。 

1. 登录到[企业门户](https://ea.azure.com)。
1. 选择“报告”。
1. 在选项卡的右上角，将视图从“M”切换到“C”，然后匹配发票上的周期。
 
   ![显示“使用情况摘要”中“M + C”选项的屏幕截图。](./media/billing-understand-your-bill-ea/ea-portal-usage-sumary-cm-option.png)

1. “总用量”应与服务超额发票上的“总应收金额”相匹配。 下表列出发票上显示的以及企业门户“使用情况摘要”中显示的款项和说明：

   |发票上的款项|“使用情况摘要”中的款项|说明|
   |---|---|---|
   |总应收金额|总用量|应用信用额度之前特定周期的税前使用费总额。|
   |承诺用量|承诺用量|在该特定周期应用的信用额度。|
   |总销售额|总超额|超出信用额的使用费总额。 此金额不含税。|
   |税额|不适用|应用到特定周期的总销售额的税额。|
   |总金额|不适用|应用信用额度并添加税额后应付的发票金额。|
1. 若要获取有关费用的其他信息，请转到“下载使用情况” > “高级报告下载”。 此报告不包括预订的税额或费用，也不包括市场费用。

      ![显示“下载使用情况”选项卡上的“高级报告下载”的屏幕截图。](./media/billing-understand-your-bill-ea/ea-portal-download-usage-advanced.png)

### <a name="review-marketplace-invoice"></a>查看市场发票

将企业门户的“报告” > “使用情况摘要”中的 Azure 市场总额与市场发票进行比较。 市场发票仅适用于 Azure 市场采购和使用。 “使用情况摘要”中的金额不含税。 

1. 登录到[企业门户](https://ea.azure.com)。
1. 选择“报告”。
1. 在选项卡的右上角，将视图从“M”切换到“C”，然后匹配发票上的周期。

     ![显示“使用情况摘要”中“M + C”选项的屏幕截图。](./media/billing-understand-your-bill-ea/ea-portal-usage-sumary-cm-option.png)

1. “Azure 市场”总额应与市场发票上的“总销售额”相匹配。
1. 若要获取有关基于用量的费用的其他信息，请转到“下载使用情况”。 在“市场费用”下，选择“下载”。 此报告不包括税额，也不显示一次性采购。

     ![显示“市场费用”下的“下载”选项的屏幕截图。](./media/billing-understand-your-bill-ea/ea-portal-download-usage-marketplace.png)

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。
