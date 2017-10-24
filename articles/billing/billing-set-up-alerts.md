---
title: "为 Azure 订阅设置计费或信用额度警报 | Microsoft Docs"
description: "介绍如何对 Azure 帐单设置警报，以免帐单出乎意料。"
keywords: "信用额度警报, 计费警报"
services: 
documentationcenter: 
author: vikdesai
manager: tonguyen
editor: 
tags: billing
ms.assetid: 9b7b3eeb-cd9d-4690-86a3-51b1e2a8974f
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/9/2017
ms.author: vikdesai
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1fc0cb2b036e835450ee0fc404cce12439fabc77
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-billing-or-credit-alerts-for-your-microsoft-azure-subscriptions"></a>为 Microsoft Azure 订阅设置计费或信用额度警报
如果用户是 Azure 订阅的帐户管理员，可以使用 Azure 计费警报服务来创建自定义的计费警报，以便监控和管理 Azure 帐户的计费活动。

此服务为预览功能，因此需要先在预览功能页面中启用它。

## <a name="set-the-alert-threshold-and-email-recipients"></a>设置警报阈值和电子邮件收件人
1. 访问[预览功能页](https://account.windowsazure.com/PreviewFeatures)并启用**账单警报服务**。

1. 收到已为订阅启用计费服务的确认电子邮件后，请访问帐户门户中的[订阅页面](https://account.windowsazure.com/Subscriptions)。 单击想要监控的订阅，并单击“警报”。

    ![Azure 帐户中心的订阅视图的屏幕截图，其中突出显示了“警报”][Image1]

2. 接下来，单击“添加警报”以创建第一个警报。 对于每个订阅，总共可以设置五个帐单警报，每个警报有一个不同的阈值和最多两个电子邮件收件人。

    ![“警报”视图的屏幕截图，可在其中添加警报][Image2]

3. 添加警报时，需要为其指定唯一的名称，选择费用阈值，并选择要向其发送警报的电子邮件地址。 设置阈值时，可以从“警报条件”列表中选择“帐单合计”或“资金信用”。 对于帐单合计，订阅费用超出阈值时会发送警报。 对于资金信用，当其降至限制以下时会发送警报。 资金信用通常适用于免费试用版以及 Visual Studio 订阅。

    ![警报添加视图的屏幕截图，可在其中配置收件人][Image3]

Azure 支持任何电子邮件地址并且不验证电子邮件地址是否可正常使用，因此请仔细检查以避免拼写错误。

## <a name="check-on-your-alerts"></a>检查警报
在设置警报后，帐户中心会列出它们并显示你还可以设置多少警报。 对于每个警报，可以看到其发送日期和时间，是针对帐单合计的警报还是针对资金信用的警报，以及设置的限制。 日期和时间格式为 24 小时制通用协调时间 (UTC)，日期为 yyyy-mm-dd 格式。 单击列表中某个警报的加号可对其进行编辑，单击垃圾桶可将其删除。

## <a name="billing-alerts-for-enterprise-agreement-ea-customers"></a>用于企业协议 (EA) 客户的账单警报
EA 客户可以通过设置支出配额为注册的每个部门获取警报。 若要快速入门，请参阅 EA 门户中的[部门支出配额](https://ea.azure.com/helpdocs/departmentSpendingQuotas)。

## <a name="learn-more-about-azure-cost-management"></a>详细了解 Azure 成本管理
- 使用[定价计算器](https://azure.microsoft.com/pricing/calculator/)、[总体拥有成本计算器](https://aka.ms/azure-tco-calculator)和添加服务时估计成本。
- [在 Azure 门户中定期查看使用情况和成本](billing-getting-started.md#costs)。
- 打开 [Azure 顾问成本建议](../advisor/advisor-cost-recommendations.md)。

若要了解详细信息，请参阅 [Azure 成本管理指南](billing-getting-started.md)。

[Image1]: ./media/azure-billing-set-up-alerts/billingalert1.png 
[Image2]: ./media/azure-billing-set-up-alerts/billingalert2.png
[Image3]: ./media/azure-billing-set-up-alerts/billingalerts3.png 
