---
title: 查看 Azure 企业协议帐单
description: 了解如何阅读并理解 Azure 企业协议的使用情况和帐单。
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.topic: tutorial
ms.date: 02/24/2020
ms.author: banders
ms.openlocfilehash: 017f617bff14eb0c031c40240ca6c1ac62c9c941
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77598215"
---
# <a name="understand-your-azure-enterprise-agreement-bill"></a>了解 Azure 企业协议帐单

当签署企业协议的 Azure 客户超出组织的信用额度或使用信用额度未涵盖的服务时，将会收到发票。

组织的信用额度包括货币承诺。 货币承诺是组织提前支付的 Azure 服务使用费。 可以联系 Microsoft 客户经理或经销商，将货币承诺资金添加到企业协议。

本教程仅适用于签订了 Azure 企业协议的 Azure 客户。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 查看已开票费用
> * 查看服务超额费用
> * 查看市场发票

## <a name="prerequisites"></a>必备条件

只有企业管理员才能查看和确认发票上的费用。 有关详细信息，请参阅[了解 Azure 中的 Azure 企业协议管理角色](../manage/understand-ea-roles.md)。 如果你不知道谁是组织的企业管理员，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

## <a name="review-invoiced-charges-for-most-customers"></a>查看大多数客户的已开票费用

本部分不适用于澳大利亚、日本或新加坡的 Azure 客户。

当计费周期中发生以下任何事件时，将收到 Azure 发票：

- **服务超额**：组织的使用费超过了信用余额。
- **单独计费**：组织使用的服务未包括在信用额度中。 无论信用余额如何，都会为以下服务开具发票：
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
- **市场费用**：组织的额度不涵盖 Azure 市场购买和使用。 因此，不管信用余额如何，都会收到市场费用的发票。 在 Enterprise Portal 中，企业管理员可以启用和禁用市场购买。

你的发票将首先显示Azure 使用费用及其相关成本，然后显示任何市场费用。 如果你有额度余额，它将其应用于 Azure 使用情况，并且发票将显示 Azure 使用情况和市场使用情况，而不会产生任何费用。

将 Enterprise Portal 的“报告” > “使用情况摘要”中显示的合计总金额与 Azure 发票进行比较。   “使用情况摘要”  中的金额不含税。

登录到 [Azure EA 门户](https://ea.azure.com)。 然后，选择“报告”。  在选项卡的右上角，将视图从“M”切换到“C”，然后匹配发票上的周期。    

![显示“使用情况摘要”中“M + C”选项的屏幕截图。](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)

**总使用量**和 **Azure 市场**的合计金额应与发票上的**总应收金额**相匹配。 若要获取有关费用的详细信息，请转到**下载使用情况**。  

![显示“下载使用情况”选项卡的屏幕截图](./media/review-enterprise-agreement-bill/ea-portal-download-usage.png)

## <a name="review-invoiced-charges-for-other-customers"></a>查看其他客户的已开票费用

本部分仅适用于澳大利亚、日本或新加坡的 Azure 客户。

当发生以下任何事件时，将收到一张或多张 Azure 发票：

- **服务超额**：组织的使用费超过了信用余额。
- **单独计费**：组织使用的服务未包括在信用额度中。 将为以下服务开发票：
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
- **市场费用**：Azure 市场采购和使用不包括在组织的额度中，需单独计费。 在 Enterprise Portal 中，企业管理员可以启用和禁用市场购买。

在计费周期内，如果由于服务超额和单独计费而产生费用，你会收到一个发票。 它包括这两种类型的费用。 市场费用始终单独开票。

## <a name="review-service-overage-charges-for-other-customers"></a>查看其他客户的服务超额费用

本部分仅适用于你在澳大利亚、日本或新加坡的情况。

将 Enterprise Portal 的“报告” > “使用情况摘要”中的总用量与服务超额发票进行比较。   服务超额发票包括超出组织信用额度的用量，和/或信用额度未涵盖的服务。 “使用情况摘要”中的金额不含税。 

登录到 [Azure EA 门户](https://ea.azure.com)，然后选择“报告”  。 在选项卡的右上角，将视图从“M”切换到“C”，然后匹配发票上的周期。    

![显示“使用情况摘要”中“M + C”选项的屏幕截图。](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)

“总用量”应与服务超额发票上的“总应收金额”相匹配。   若要获取有关费用的其他信息，请转到“下载使用情况” > “高级报告下载”。   此报告不包括预留的税款或费用，也不包括市场费用。  

![显示“下载使用情况”选项卡上的“高级报告下载”的屏幕截图。](./media/review-enterprise-agreement-bill/ea-portal-download-usage-advanced.png)

下表列出发票上显示的以及企业门户“使用情况摘要”中显示的款项和说明： 

|发票上的款项|“使用情况摘要”中的款项|说明|
|---|---|---|
|总应收金额|总用量|应用信用额度之前特定周期的税前使用费总额。|
|承诺用量|承诺用量|在该特定周期应用的信用额度。|
|总销售额|总超额|超出信用额的使用费总额。 此金额不含税。|
|税额|不适用|应用到特定周期的总销售额的税额。|
|总金额|不适用|应用信用额度并添加税额后应付的发票金额。|

### <a name="review-marketplace-invoice"></a>查看市场发票

本部分仅适用于你在澳大利亚、日本或新加坡的情况。

将企业门户的“报告” > “使用情况摘要”中的 Azure 市场总额与市场发票进行比较。   市场发票仅适用于 Azure 市场采购和使用。 “使用情况摘要”中的金额不含税。 

登录到[企业门户](https://ea.azure.com)，然后选择“报告”  。 在选项卡的右上角，将视图从“M”切换到“C”，然后匹配发票上的周期。    

![显示“使用情况摘要”中“M + C”选项的屏幕截图。](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)  

“Azure 市场”总额应与市场发票上的“总销售额”相匹配。   若要获取有关基于用量的费用的其他信息，请转到“下载使用情况”。  在“市场费用”下，选择“下载”。   市场价格包括由发布者确定的税款。 客户不会从发布者那里收到用于收取交易税的单独发票。

![显示“市场费用”下的“下载”选项的屏幕截图。](./media/review-enterprise-agreement-bill/ea-portal-download-usage-marketplace.png)

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：

> [!div class="checklist"]
> * 查看已开票费用
> * 查看服务超额费用
> * 查看市场发票

继续阅读下一篇文章，详细了解如何使用 Azure EA 门户。

> [!div class="nextstepaction"]
> [Azure EA 门户入门](../manage/ea-portal-get-started.md)
