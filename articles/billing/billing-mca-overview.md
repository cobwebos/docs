---
title: 开始使用您的计费帐户的 Microsoft 客户协议-Azure |Microsoft Docs
description: 了解 Microsoft 客户协议的计费帐户
services: billing
documentationcenter: ''
author: amberbhargava
manager: amberbhargava
editor: banders
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/28/2019
ms.author: banders
ms.openlocfilehash: ea625a61ed600dbaa22fef85987e9570a6fb7dbc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60371440"
---
# <a name="get-started-with-your-billing-account-for-a-microsoft-customer-agreement"></a>开始使用您的计费帐户的 Microsoft 客户协议

为每个协议，则使用签署 Microsoft 使用 Azure 创建计费帐户。 使用您的计费帐户来管理帐单和跟踪成本。 可以具有多个计费帐户的访问权限。 例如，你可能已注册 Azure 为您个人的项目。 也可以通过组织的企业协议或 Microsoft 客户协议的 Azure 访问权。 对于每种方案，必须具有单独的计费帐户。

本文适用于 Microsoft 客户协议向计费帐户。 [检查是否有权访问 Microsoft 客户协议](#check-access-to-a-microsoft-customer-agreement)。

## <a name="understand-billing-account"></a>了解计费帐户

您的 Microsoft 客户协议的计费帐户包含一个或多个计费的配置文件让你管理你的发票和付款方法。 每个计费的配置文件包含一个或多个发票部分，可以将组织计费配置文件的发票上的成本。

下图显示了计费帐户、计费配置文件与发票部分之间的关系。

![显示 Microsoft 客户协议的计费层次结构的关系图](./media/billing-mca-overview/mca-billing-hierarchy.png)

计费帐户上的角色具有最高级别的权限。 默认情况下，只有全局管理员在贵组织的 Azure Active Directory 获取计费帐户的访问权限。 应将这些角色分配给用户需要查看发票，并跟踪类似于财务和 IT 经理在整个组织的成本。 有关详细信息，请参阅[计费帐户角色和任务](billing-understand-mca-roles.md#billing-account-roles-and-tasks)。

## <a name="understand-billing-profiles"></a>了解计费的配置文件

使用计费的配置文件来管理你的发票和付款方法。 每月发票为 Azure 订阅和计费配置文件使用购买其他产品生成。 使用的付款方法按发票。

自动为您的计费帐户创建计费的配置文件。 您可以创建新的计费配置文件来设置其他发票。 例如，可能想为每个部门或项目不同的发票中你的组织。

此外可以创建发票的分区来组织计费配置文件的发票上的成本。 在部分显示 Azure 订阅和发票部分所购买产品的费用。 计费配置文件的发票包括所有发票部分费用。

计费的配置文件上的角色有权查看和管理发票和付款方法。 将这些角色分配给你的组织中支付发票等记帐团队的成员的用户。 有关详细信息，请参阅[计费的配置文件角色和任务](billing-understand-mca-roles.md#billing-profile-roles-and-tasks)。

### <a name="monthly-invoice-generated-for-each-billing-profile"></a>为每个计费的配置文件生成的每月发票

每月的发票上计费的每个配置文件在发票日期生成。 发票上个月包含所有费用。

可以查看发票、 下载的文档和更改设置，以通过电子邮件，在 Azure 门户中获取未来的发票。 有关详细信息，请参阅[下载发票的 Microsoft 客户协议](billing-download-azure-invoice-daily-usage-date.md#download-invoices-for-a-microsoft-customer-agreement)。

### <a name="invoices-paid-through-payment-methods"></a>通过付款方式已付款的发票

每个计费的配置文件具有其自己使用支付其发票的付款方法。 支持以下的付款方法：

| Type             | 定义  |
|------------------|-------------|
|Azure 额度    |  信用额度将自动应用于您计算需要支付的金额的发票上计费总金额。 有关详细信息，请参阅[跟踪你计费的配置文件的 Azure 信用额度余额](billing-mca-check-azure-credits-balance.md)。 |
|支票或电汇 | 您可以支付应付金额的发票，可以通过检查或网络传输。 在发票上给予了付款说明 |

### <a name="control-azure-marketplace-and-reservation-purchases-by-applying-policies"></a>通过应用策略来控制 Azure Marketplace 和预订购买

应用策略来控制使用计费的配置文件的购买。 可以设置策略以禁用购买 Azure 保留项和 Marketplace 产品。 策略时，对于计费的配置文件中的发票部分创建的订阅不能用于购买 Azure 保留和 Marketplace 产品。

### <a name="allow-users-to-create-azure-subscriptions-by-enabling-azure-plans"></a>允许用户通过启用 Azure 计划创建 Azure 订阅

创建计费的配置文件时，会自动启用 azure 计划。 计费的配置文件中的所有发票部分都有权访问这些计划。 用户有权访问发票部分使用计划创建 Azure 订阅。 除非为计费的配置文件启用了 Azure 计划，它们不能创建 Azure 订阅。 中的 Microsoft 客户协议计费帐户支持以下 Azure 计划：

| 计划             | 定义  |
|------------------|-------------|
|Microsoft Azure 计划   | 允许用户创建订阅，可以运行任何工作负荷。 有关详细信息，请参阅[Microsoft Azure 计划](https://azure.microsoft.com/offers/ms-azr-0017g/) |
|用于开发/测试的 Microsoft Azure 计划 | 允许 Visual Studio 订阅服务器创建订阅的且限制用于开发或测试工作负荷。 这些订阅在 Azure 门户中获取权益，例如较低费率并可以访问独占的虚拟机映像。 有关详细信息，请参阅[Microsoft Azure 开发测试的计划](https://azure.microsoft.com/offers/ms-azr-0148g/)|

## <a name="understand-invoice-sections"></a>了解发票部分

创建发票的分区来组织计费配置文件的发票上的成本。 例如，您可能需要一张发票为你的组织但是想要将组织部门、 团队或项目的成本。 对于此方案中，您必须单一计费配置文件在其中创建每个部门、 团队或项目的发票节。

创建发票部分时，可以创建部分中的 Azure 订阅的权限授予其他人。 然后，任何使用费用和购买的订阅将反映在发票的相应部分。

上的发票部分的角色有权控制将创建 Azure 订阅。 将这些角色分配到用户设置 Azure 环境中我们的工程主管和技术架构师等组织团队。 有关详细信息，请参阅[发票部分角色和任务](billing-understand-mca-roles.md#invoice-section-roles-and-tasks)。

## <a name="check-access-to-a-microsoft-customer-agreement"></a>检查与 Microsoft 客户协议的访问权限
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员

如果需要帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。

## <a name="next-steps"></a>后续步骤

请参阅以下文章，了解有关您的计费帐户信息：

- [了解 Microsoft 客户协议在 Azure 中的管理角色](billing-understand-mca-roles.md)
- [为 Microsoft 客户协议创建其他 Azure 订阅](billing-mca-create-subscription.md)
- [在你开发票来组织你的成本创建部分](billing-mca-section-invoice.md)