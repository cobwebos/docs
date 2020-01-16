---
title: Microsoft 客户协议计费入门-Azure
description: 了解 Microsoft 客户协议计费帐户
author: bandersmsft
manager: amberbhargava
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/10/2019
ms.author: banders
ms.openlocfilehash: 7d26c98ae8e5b9b95038a4a775134282703fa7c6
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "75995112"
---
# <a name="get-started-with-your-microsoft-customer-agreement-billing-account"></a>Microsoft 客户协议计费帐户入门

当你注册使用 Azure 时，系统将为你创建计费帐户。 可以使用计费帐户管理发票、付款并跟踪成本。 可以访问多个计费帐户。 例如，可以为个人项目注册 Azure。 也可通过组织的企业协议或 Microsoft 客户协议访问 Azure。 每个这样的方案都会有单独的计费帐户。

本文适用于 Microsoft 客户协议的计费帐户。 [检查你是否有权访问 Microsoft 客户协议](#check-access-to-a-microsoft-customer-agreement)。

## <a name="your-billing-account"></a>你的计费帐户

Microsoft 客户协议的计费帐户包含一个或多个计费对象信息，用于管理发票和付款方式。 每项计费对象信息包含一个或多个发票科目，用于在计费对象信息的发票上组织成本。

下图显示了计费帐户、计费对象信息与发票科目之间的关系。

![显示 Microsoft 客户协议计费层次结构的关系图](./media/mca-overview/mca-billing-hierarchy.png)

计费帐户上的角色具有最高级别的权限。 默认情况下，仅注册 Azure 的用户可以访问计费帐户。 这些角色应该分配给那些需要查看发票并跟踪整个组织的成本的用户，例如财务或 IT 管理员。 有关详细信息，请参阅[计费帐户角色和任务](../manage/understand-mca-roles.md#billing-account-roles-and-tasks)。

## <a name="billing-profiles"></a>计费对象信息

使用计费对象信息可以管理发票和付款方式。 每月发票是在当月开始时针对帐户中的每项计费对象信息生成的。 发票包含上月的所有 Azure 订阅和其他购买项目的相应费用。

将为计费帐户自动创建一项计费对象信息。 它默认包含一个发票科目。 无论是按项目、部门还是按开发环境，你都可根据需求创建额外的科目来轻松跟踪和组织成本。 你会看到此计费对象信息发票上的这些科目反映了你分配给它们的每个订阅和购买项的使用情况。

计费对象信息上的角色有权查看和管理发票和付款方式。 将这些角色分配给为发票付款的用户，例如组织中会计团队的成员。 有关详细信息，请参阅[计费对象信息角色和任务](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks)。

### <a name="each-billing-profile-gets-a-monthly-invoice"></a>每项计费对象信息都有每月发票

每月发票是在当月开始时针对每项计费对象信息生成的。 该发票包含上个月的所有费用。

可以在 Azure 门户中查看发票、下载单据以及更改设置，以便通过电子邮件获取未来的发票。 有关详细信息，请参阅[下载 Microsoft 客户协议的发票](../manage/download-azure-invoice-daily-usage-date.md#download-invoices-for-a-microsoft-customer-agreement)。

### <a name="invoice-payment-methods"></a>发票付款方式

每项计费对象信息都自带付款方式，用于发票付款。 支持以下付款方式：

| 类型             | 定义  |
|------------------|-------------|
|Azure 额度    |  额度自动应用到发票上符合条件的费用，降低你需要支付的金额。 有关详细信息，请参阅[跟踪计费对象信息的 Azure 剩余额度](../manage/mca-check-azure-credits-balance.md)。 |
|支票/电汇 | 如果你的帐户获准通过支票/电汇来付款， 则可通过支票/电汇来支付发票的应付金额。 发票上提供了付款说明。 |
|信用卡 | 通过 Azure 网站注册 Azure 的客户可以通过信用卡付款。 |

### <a name="apply-policies-to-control-purchases"></a>应用策略来控制购买

应用策略，通过计费对象信息来控制 Azure 市场和预留项购买。 可以设置策略来禁止购买 Azure 预留项和市场产品。 应用策略以后，就不能使用那些按计费对象信息计费的订阅进行这些购买。

### <a name="azure-plans-determine-pricing-and-service-level-agreement-for-subscriptions"></a>Azure 计划决定了订阅的定价和服务级别协议

Azure 计划决定了 Azure 订阅的定价和服务级别协议。 它们是在你创建计费对象信息时自动启用的。 所有与计费对象信息关联的发票科目都可以使用这些计划。 可以访问此发票科目的用户使用这些计划来创建 Azure 订阅。 Microsoft 客户协议的计费帐户支持以下 Azure 计划：

| 套餐             | 定义  |
|------------------|-------------|
|Microsoft Azure 计划   | 允许用户创建能够运行任何工作负荷的订阅。  |
|适用于开发/测试的 Microsoft Azure 计划 | 允许 Visual Studio 订户创建局限于开发或测试工作负荷的订阅。 这些订阅可以获得多种权益，例如费率较低，并且能够访问 Azure 门户中的专用虚拟机映像。 |

## <a name="invoice-sections"></a>发票科目

创建发票科目即可组织发票中的成本。 例如，你可能需要适用于组织的单个发票，但同时需要按部门、团队或项目组织成本。 对于这种情况，可以设置单项计费对象信息，在其中为每个部门、团队或项目创建发票科目。

创建发票科目以后，即可授予其他人创建向该科目计费的 Azure 订阅的权限。 然后，对订阅的任何使用费和购买费都将记入该科目。

发票科目的角色有权控制谁来创建 Azure 订阅。 将这些角色分配给在组织中为团队设置 Azure 环境的用户，例如工程主管和技术架构师。 有关详细信息，请参阅[发票科目角色和任务](../manage/understand-mca-roles.md#invoice-section-roles-and-tasks)。

## <a name="check-access-to-a-microsoft-customer-agreement"></a>检查对 Microsoft 客户协议的访问权限
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员。

如果需要帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。

## <a name="next-steps"></a>后续步骤

请参阅以下文章，了解计费帐户：

- [了解 Azure 中的 Microsoft 客户协议管理角色](../manage/understand-mca-roles.md)
- [为 Microsoft 客户协议创建其他 Azure 订阅](../manage/create-subscription.md)
- [在发票上创建科目以管理成本](../manage/mca-section-invoice.md)
