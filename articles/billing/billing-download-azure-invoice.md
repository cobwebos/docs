---
title: 查看和下载 Microsoft Azure 发票 |Microsoft Docs
description: 介绍如何查看和下载 Microsoft Azure 发票
keywords: 帐单发票, 发票下载, azure 发票, azure 使用情况
services: billing
documentationcenter: ''
author: genlin
manager: jureid
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: banders
ms.openlocfilehash: f71fe9b02765e0fc8fd5f3b7abbd54c87b08132f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60617911"
---
# <a name="view-and-download-your-microsoft-azure-invoice"></a>查看和下载 Microsoft Azure 发票

对于大多数订阅，你可以从 [Azure 门户](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)下载发票，也可通过电子邮件发送发票。 如果你是签署了企业协议的 Azure 客户（EA 客户），则无法下载你的组织的发票。 发票将发送给为合约设置的接收发票的人员。

只有特定角色有权查看发票，如帐户管理员或企业管理员。 若要详细了解如何获取计费信息，请参阅[使用角色管理对 Azure 计费的访问](billing-manage-access.md)。

如果有[Microsoft 客户协议](#check-your-access-to-a-microsoft-customer-agreement)，您必须是账单资料所有者、 参与者、 读取器，或发票管理器以获取您的发票。 若要了解 Microsoft 客户协议计费角色的详细信息，请参阅[计费的配置文件角色和任务](billing-understand-mca-roles.md#billing-profile-roles-and-tasks)。

## <a name="download-your-azure-invoices-pdf"></a>下载您 Azure 发票 (.pdf)

对于大多数订阅，可以从 Azure 门户下载发票。 如果你有 Microsoft 客户协议，请参阅下载发票计费的配置文件。

### <a name="download-invoices-for-an-individual-subscription"></a>下载单个订阅的发票

1. 选择从你的订阅[订阅页](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)在 Azure 门户中，如下[用户有权访问发票](billing-manage-access.md)。

2. 选择“发票”  。

    ![屏幕快照，显示“计费和使用情况”选项](./media/billing-download-azure-invoice-daily-usage-date/billingandusage.png)

3. 单击“下载发票”  ，查看 PDF 发票的副本。 如果它显示“不可用”  ，请参阅[为何看不到最后一个计费周期的发票？](#noinvoice)

    ![屏幕快照，显示计费周期、下载选项，以及每个计费周期的总费用](./media/billing-download-azure-invoice-daily-usage-date/billing4.png)

4. 还可以通过单击计费周期来查看每日使用情况。

有关发票的详细信息，请参阅[了解 Microsoft Azure 帐单](billing-understand-your-bill.md)。 如需有关管理成本的帮助，请参阅[通过 Azure 计费和成本管理来防止意外成本](billing-getting-started.md)。

### <a name="download-invoices-for-a-microsoft-customer-agreement"></a>Microsoft 客户协议下载发票

每个生成发票[计费配置文件](billing-mca-overview.md#understand-billing-profiles)Microsoft 客户协议中。 您必须是一个计费配置文件所有者、 参与者、 读取器，或发票管理器从 Azure 门户下载发票。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在“成本管理 + 计费”  中进行搜索。
1. 选择计费的配置文件。 具体取决于您的访问权限，可能需要首先选择计费帐户。
1. 选择“发票”  。
1. 在发票网格中，找到你想要下载的发票的行。
1. 单击省略号 (`...`) 的行的末尾。
    ![显示在行尾的省略号的屏幕截图](./media/billing-download-azure-invoice/billingprofile-invoicegrid.png)
1. 在下载上下文菜单中，选择**发票**。

    ![显示上下文菜单的屏幕截图](./media/billing-download-azure-invoice/contextmenu.png)

如果您看不到最后一个计费周期的发票，请参阅[为什么没有看见发票最后一个计费周期？](#noinvoice)

## <a name="get-your-invoice-in-email-pdf"></a>通过电子邮件获取发票 (.pdf)

可以选择加入并配置其他收件人来通过电子邮件接收 Azure 发票。 此功能可能不适用于某些订阅，例如支持套餐、企业协议或 Azure 开放许可。 如果你有 Microsoft 客户协议，请参阅获取账单资料发票电子邮件中。

### <a name="get-your-subscriptions-invoices-in-email"></a>通过电子邮件获取订阅的发票

1. 从[订阅页面](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)中选择你的订阅。 为拥有的每个订阅执行选择加入操作。 依次单击“发票”  、“通过电子邮件发送我的发票”  。

    ![显示了“选择加入”流的屏幕截图](./media/billing-download-azure-invoice-daily-usage-date/InvoicesDeepLink.PNG)

2. 单击“选择加入”  并接受条款。

    ![显示了“选择加入”流步骤 2 的屏幕截图](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep2.PNG)
 
3. 接受协议后，即可以配置其他收件人。 删除收件人时，不再存储电子邮件地址。 如果改变主意，则需要重新添加。

    ![显示了“选择加入”流步骤 3 的屏幕截图](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep3.PNG)

如果执行步骤后未收到电子邮件，请确保[个人资料通讯首选项](https://account.windowsazure.com/profile)中的电子邮件地址正确。

### <a name="opt-out-of-getting-your-subscriptions-invoices-in-email"></a>选择退出电子邮件中获取订阅的发票

可以选择禁用更高版本，然后单击的步骤通过电子邮件获取发票**选择不通过电子邮件发送发票**。 此选项将删除任何设置用来通过电子邮件接收发票的电子邮件地址。 如果您选择返回，可以重新配置收件人。

 ![显示了“选择退出”流的屏幕截图](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep4.PNG)

### <a name="get-your-microsoft-customer-agreement-invoices-in-email"></a>电子邮件中获取 Microsoft 客户协议发票

如果你有 Microsoft 客户协议，你可以选择通过电子邮件获取发票。 所有计费配置文件所有者、 参与者、 读者和发票管理器将通过电子邮件获取发票。 读取器不能更新电子邮件发票首选项。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在“成本管理 + 计费”  中进行搜索。
1. 选择计费的配置文件。 具体取决于您的访问权限，可能需要首先选择计费帐户。
1. 在“设置”下，选择“属性”   。
1. 下**电子邮件发送发票**，选择**更新电子邮件发票首选项**。

    ![屏幕截图，显示电子邮件发票属性](./media/billing-download-azure-invoice/billingprofile-email.png)

1. 选择**参与**。
1. 单击“更新”  。

### <a name="opt-out-of-getting-your-microsoft-customer-agreement-invoices-in-email"></a>选择退出电子邮件中获取 Microsoft 客户协议发票

可以选择禁用更高版本，然后单击的步骤通过电子邮件获取发票**选择退出**。所有的所有者、 参与者、 读者和发票管理器将选择退出过通过电子邮件获取发票。 如果您是在读取器，则无法更改的电子邮件发票首选项。

### <a name="noinvoice"></a> 为何看不到最后一个计费周期的发票？

看不到发票的几个可能原因：

- 从订阅 Azure 开始算还不到 30 天。

- 尚未生成发票。 请等到计费周期结束。

- 你没有权限查看发票。 如果你有 Microsoft 客户协议，您必须是账单资料所有者、 参与者、 读取器，或发票管理器。 对于其他订阅，您可能看不到不是帐户管理员如果旧的发票。 若要详细了解如何获取计费信息，请参阅[使用角色管理对 Azure 计费的访问](billing-manage-access.md)。

- 如果尚未超过订阅有免费试用版或每月信用额度，不会获取发票，除非你有 Microsoft 客户协议。

## <a name="check-your-access-to-a-microsoft-customer-agreement"></a>检查你对 Microsoft 客户协议的访问
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>需要帮助？ 联系我们

如果有疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

若要了解有关发票和费用的详细信息，请参阅：

- [查看和下载你的 Microsoft Azure 使用情况和费用](billing-download-azure-daily-usage.md)
- [了解 Microsoft Azure 账单](billing-understand-your-bill.md)
- [了解有关 Azure 发票的术语](billing-understand-your-invoice.md)
- [了解 Microsoft Azure 详细使用条款](billing-understand-your-usage.md)
- [查看你组织的 Azure 定价](billing-ea-pricing.md)

如果你有 Microsoft 客户协议，请参阅：

- [了解账单资料发票费用](billing-mca-understand-your-bill.md)
- [了解术语在发票上的计费配置文件](billing-mca-understand-your-invoice.md)
- [了解你计费的配置文件的 Azure 使用情况和费用文件](billing-mca-understand-your-usage.md)
- [查看和下载你计费的配置文件的税务文档](billing-mca-download-tax-document.md)
- [查看你组织的 Azure 定价](billing-ea-pricing.md)
