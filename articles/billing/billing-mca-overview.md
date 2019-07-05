---
title: 开始使用 Microsoft 客户协议计费帐户-Azure
description: 了解你的 Microsoft 客户协议计费帐户
author: bandersmsft
manager: amberbhargava
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 87483c967641489e9548f38c99eebbf121d0d252
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490740"
---
# <a name="get-started-with-your-microsoft-customer-agreement-billing-account"></a>开始使用您的 Microsoft 客户协议计费帐户

注册以使用 Azure 时创建计费帐户。 使用您的计费帐户来管理发票付款，并跟踪成本。 可以具有多个计费帐户的访问权限。 例如，你可能已注册 Azure 为您个人的项目。 也可以通过组织的企业协议或 Microsoft 客户协议的 Azure 访问权。 对于每种方案，必须具有单独的计费帐户。

本文适用于 Microsoft 客户协议向计费帐户。 [检查是否有权访问 Microsoft 客户协议](#check-access-to-a-microsoft-customer-agreement)。

## <a name="your-billing-account"></a>您的计费帐户

您的 Microsoft 客户协议的计费帐户包含一个或多个计费的配置文件让你管理你的发票和付款方法。 每个计费的配置文件包含一个或多个发票部分，可以将组织计费配置文件的发票上的成本。

下图显示了计费帐户、 计费配置文件和发票部分之间的关系。

![显示 Microsoft 客户协议计费层次结构的关系图](./media/billing-mca-overview/mca-billing-hierarchy.png)

计费帐户上的角色具有最高级别的权限。 默认情况下，只有 Azure 获取对计费帐户的访问的已注册的用户。 应将这些角色分配给用户需要查看发票，并跟踪类似于财务和 IT 经理在整个组织的成本。 有关详细信息，请参阅[计费帐户角色和任务](billing-understand-mca-roles.md#billing-account-roles-and-tasks)。

## <a name="billing-profiles"></a>计费配置文件

使用计费的配置文件来管理你的发票和付款方法。 每月的发票生成您的帐户中每个计费配置文件的月份开始处。 发票包含所有 Azure 订阅和其他的上个月购买的相应费用。

自动为您的计费帐户创建计费的配置文件。 默认情况下，它包含一个发票部分。 可以创建其他部分轻松跟踪和组织根据需求的成本是否是其每个项目、 部门或开发环境。 专用于反映将每个订阅和购买已分配给它的使用情况的计费配置文件的发票上看到这些部分。

计费的配置文件上的角色有权查看和管理发票和付款方法。 将这些角色分配给你的组织中支付发票等记帐团队的成员的用户。 有关详细信息，请参阅[计费的配置文件角色和任务](billing-understand-mca-roles.md#billing-profile-roles-and-tasks)。

### <a name="each-billing-profile-gets-a-monthly-invoice"></a>每个计费的配置文件获取每月发票

每月的发票生成每个计费的配置文件的月份开始处。 发票包含在前一个月的所有费用。

可以查看发票、 下载的文档和更改设置，以通过电子邮件，在 Azure 门户中获取未来的发票。 有关详细信息，请参阅[下载发票的 Microsoft 客户协议](billing-download-azure-invoice-daily-usage-date.md#download-invoices-for-a-microsoft-customer-agreement)。

### <a name="invoice-payment-methods"></a>发票支付方法

每个计费的配置文件具有其自己使用支付其发票的付款方法。 支持以下的付款方法：

| Type             | 定义  |
|------------------|-------------|
|Azure 额度    |  信用额度会自动应用到的有资格发票，减少您需要支付费用。 有关详细信息，请参阅[跟踪你计费的配置文件的 Azure 信用额度余额](billing-mca-check-azure-credits-balance.md)。 |
|检查/网络传输 | 如果通过检查/电子转账付款批准了你的帐户。 您可以支付的金额到期并需要通过检查/电子转账发票。 在发票上给予了付款说明 |
|信用卡 | 通过 Azure 网站注册 azure 客户可以通过信用卡进行付款。 |

### <a name="apply-policies-to-control-purchases"></a>应用策略来控制购买

将策略应用于控制 Azure Marketplace 和计费配置文件使用的预订购买。 可以设置策略以禁用购买 Azure 保留项和 Marketplace 产品。 当应用策略时，不能使用到计费的配置文件按计费的订阅进行这些购买。

### <a name="azure-plans-determine-pricing-and-service-level-agreement-for-subscriptions"></a>Azure 计划确定订阅的定价和服务级别的协议

Azure 计划确定的定价和服务级别协议为 Azure 订阅。 创建计费的配置文件时，它们会自动启用。 与计费的配置文件关联的所有发票部分可以都使用这些计划。 用户有权访问发票部分使用计划创建 Azure 订阅。 中的 Microsoft 客户协议计费帐户支持以下 Azure 计划：

| 计划             | 定义  |
|------------------|-------------|
|Microsoft Azure Plan   | 允许用户创建订阅，可以运行任何工作负荷。 有关详细信息，请参阅[Microsoft Azure 计划](https://azure.microsoft.com/offers/ms-azr-0017g/) |
|Microsoft Azure 开发/测试计划 | 允许 Visual Studio 订阅服务器创建订阅的且限制用于开发或测试工作负荷。 这些订阅在 Azure 门户中获取权益，例如较低费率并可以访问独占的虚拟机映像。 有关详细信息，请参阅[Microsoft Azure 开发测试的计划](https://azure.microsoft.com/offers/ms-azr-0148g/)|

## <a name="invoice-sections"></a>发票部分

创建发票的分区来组织你的发票上的成本。 例如，您可能需要一张发票为你的组织但是想要将组织部门、 团队或项目的成本。 对于此方案中，您必须单一计费配置文件在其中创建每个部门、 团队或项目的发票节。

创建发票部分时，可以创建的部分收费的 Azure 订阅的权限授予其他人。 任何使用费用和购买的订阅则计费的部分。

上的发票部分的角色有权控制将创建 Azure 订阅。 将这些角色分配到用户设置 Azure 环境中我们的工程主管和技术架构师等组织团队。 有关详细信息，请参阅[发票部分角色和任务](billing-understand-mca-roles.md#invoice-section-roles-and-tasks)。

## <a name="check-access-to-a-microsoft-customer-agreement"></a>检查与 Microsoft 客户协议的访问权限
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员。

如果需要帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。

## <a name="next-steps"></a>后续步骤

请参阅以下文章，了解有关您的计费帐户信息：

- [了解 Microsoft 客户协议在 Azure 中的管理角色](billing-understand-mca-roles.md)
- [为 Microsoft 客户协议创建其他 Azure 订阅](billing-mca-create-subscription.md)
- [在你开发票来组织你的成本创建部分](billing-mca-section-invoice.md)
