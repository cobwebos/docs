---
title: 查看和下载 Microsoft Azure 发票
description: 介绍如何查看和下载 Microsoft Azure 发票。
keywords: 帐单发票, 发票下载, azure 发票, azure 使用情况
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: banders
ms.openlocfilehash: 4dc5f88f7a5994e5bcd50d71c86bf1ba35b10734
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2019
ms.locfileid: "68321772"
---
# <a name="view-and-download-your-microsoft-azure-invoice"></a>查看和下载 Microsoft Azure 发票

对于大多数订阅，你可以从 [Azure 门户](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)下载发票，也可通过电子邮件发送发票。 如果你是签署了企业协议的 Azure 客户（EA 客户），则无法下载你的组织的发票。 发票将发送给为合约设置的接收发票的人员。

只有特定角色有权查看发票。 例如, 帐户管理员或企业管理员。 有关获取计费信息访问权限的详细信息, 请参阅[使用角色管理对 Azure 计费的访问](billing-manage-access.md)。

如果你有[Microsoft 客户协议](#check-your-access-to-a-microsoft-customer-agreement), 则必须具有以下角色之一才能获取发票:

- 计费配置文件所有者
- 参与者
- 读取器
- 发票管理器

有关 Microsoft 客户协议的计费角色的详细信息, 请参阅[计费配置文件角色和任务](billing-understand-mca-roles.md#billing-profile-roles-and-tasks)。

## <a name="noinvoice"></a>为什么你可能无法获取发票

看不到发票的几个可能原因：

- 从订阅 Azure 开始算还不到 30 天。

- 在发票期结束时, Azure 会向你收费。 因此, 可能尚未生成发票。 请等到计费周期结束。

- 您无权查看发票。 如果你有 Microsoft 客户协议, 则必须是计费配置文件所有者、参与者、读者或发票管理器。 对于其他订阅, 如果不是帐户管理员, 则可能看不到旧发票。 若要详细了解如何获取计费信息，请参阅[使用角色管理对 Azure 计费的访问](billing-manage-access.md)。

- 如果你的订阅有免费试用版或每月信用额度, 则当你超出每月信用额度时, 只会获得一张发票。 如果你有 Microsoft 客户协议, 你始终会收到一张发票。

## <a name="download-your-azure-invoices-pdf"></a>下载 Azure 发票 (.pdf)

对于大多数订阅, 可以从 Azure 门户下载发票。 如果你有 Microsoft 客户协议, 请参阅[下载 Microsoft 客户协议的发票](#download-invoices-for-a-microsoft-customer-agreement)。

### <a name="download-invoices-for-an-individual-subscription"></a>下载单个订阅的发票

1. 在 "[订阅" 页](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)上的 "订阅" 页中选择你的订阅,[用户可以访问发票](billing-manage-access.md)Azure 门户。

2. 选择“发票”  。

    ![屏幕快照，显示“计费和使用情况”选项](./media/billing-download-azure-invoice-daily-usage-date/billingandusage.png)

3. 单击“下载发票”  ，查看 PDF 发票的副本。 如果它显示“不可用”  ，请参阅[为何看不到最后一个计费周期的发票？](#noinvoice)

    ![屏幕快照，显示计费周期、下载选项，以及每个计费周期的总费用](./media/billing-download-azure-invoice-daily-usage-date/billing4.png)

4. 还可以通过单击计费周期来查看每日使用情况。

有关发票的详细信息，请参阅[了解 Microsoft Azure 帐单](billing-understand-your-bill.md)。 有关管理成本的帮助, 请参阅[通过 Azure 计费和成本管理来防止意外成本](billing-getting-started.md)。

### <a name="download-invoices-for-a-microsoft-customer-agreement"></a>下载 Microsoft 客户协议的发票

为 Microsoft 客户协议中的每个[计费配置文件](billing-mca-overview.md#billing-profiles)生成发票。 您必须是帐单配置文件所有者、参与者、读者或发票经理才能从 Azure 门户下载发票。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 搜索 "*成本管理 + 计费*"。
1. 选择计费配置文件。 根据你的访问权限, 可能需要先选择一个计费帐户。
1. 选择“发票”  。
1. 在 "发票" 网格中, 找到要下载的发票的行。
1. 单击行末尾的`...`省略号 ()。
    ![显示行尾的省略号的屏幕截图](./media/billing-download-azure-invoice/billingprofile-invoicegrid.png)
1. 在下载上下文菜单中, 选择 "**发票**"。

    ![显示上下文菜单的屏幕截图](./media/billing-download-azure-invoice/contextmenu.png)

如果看不到最后一个计费周期的发票, 请参阅[为何我看不到最后一个计费周期的发票？](#noinvoice)

## <a name="get-your-invoice-in-email-pdf"></a>通过电子邮件获取发票 (.pdf)

可以选择加入并配置其他收件人来通过电子邮件接收 Azure 发票。 此功能可能不适用于某些订阅，例如支持套餐、企业协议或 Azure 开放许可。 如果你有 Microsoft 客户协议, 请参阅下一部分,[通过电子邮件获取你的计费配置文件发票](#get-your-subscriptions-invoices-in-email)。

### <a name="get-your-subscriptions-invoices-in-email"></a>通过电子邮件获取订阅的发票

1. 在 "[订阅" 页](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)上选择订阅。 为拥有的每个订阅执行选择加入操作。 依次单击“发票”  、“通过电子邮件发送我的发票”  。

    ![显示了“选择加入”流的屏幕截图](./media/billing-download-azure-invoice-daily-usage-date/InvoicesDeepLink.PNG)

2. 单击“选择加入”  并接受条款。

    ![显示了“选择加入”流步骤 2 的屏幕截图](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep2.PNG)

3. 接受协议后, 可以配置其他收件人。 删除收件人时，不再存储电子邮件地址。 如果改变主意, 则需要重新添加。

    ![显示了“选择加入”流步骤 3 的屏幕截图](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep3.PNG)

如果执行步骤后未收到电子邮件，请确保[个人资料通讯首选项](https://account.windowsazure.com/profile)中的电子邮件地址正确。

### <a name="opt-out-of-getting-your-subscriptions-invoices-in-email"></a>选择不通过电子邮件获取订阅的发票

若要选择不通过电子邮件获取发票, 请按照前面的步骤进行操作, 并单击 "**选择退出通过电子邮件发送的发票**"。 此选项将删除任何设置用来通过电子邮件接收发票的电子邮件地址。 如果选择返回, 则可以重新配置收件人。

 ![显示了“选择退出”流的屏幕截图](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep4.PNG)

### <a name="get-your-microsoft-customer-agreement-invoices-in-email"></a>通过电子邮件获取 Microsoft 客户协议发票

如果有 Microsoft 客户协议, 可以选择在电子邮件中获取发票。 所有计费配置文件所有者、参与者、读者和发票经理都将通过电子邮件获取发票。 读者无法更新电子邮件发票首选项。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 搜索 "**成本管理 + 计费**"。
1. 选择计费配置文件。 根据你的访问权限, 可能需要先选择一个计费帐户。
1. 在“设置”下，选择“属性”   。
1. 在 "**电子邮件发票**" 下, 选择**更新电子邮件发票首选项**。

    ![显示电子邮件发票属性的屏幕截图](./media/billing-download-azure-invoice/billingprofile-email.png)

1. 选择 **"选择加入"** 。
1. 单击“更新”  。

### <a name="opt-out-of-getting-your-microsoft-customer-agreement-invoices-in-email"></a>选择不通过电子邮件获取 Microsoft 客户协议发票

若要选择不通过电子邮件获取发票, 请按照前面的步骤进行操作, 并单击 "**选择退出**"。所有所有者、参与者、读者和发票经理都未选择通过电子邮件获取发票。 如果你是一个读者, 则无法更改电子邮件发票首选项。



## <a name="check-your-access-to-a-microsoft-customer-agreement"></a>检查对 Microsoft 客户协议的访问权限
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

若要了解有关发票和费用的详细信息, 请参阅:

- [查看并下载你的 Microsoft Azure 使用情况和费用](billing-download-azure-daily-usage.md)
- [了解 Microsoft Azure 账单](billing-understand-your-bill.md)
- [了解 Azure 发票上的术语](billing-understand-your-invoice.md)
- [了解 Microsoft Azure 详细使用条款](billing-understand-your-usage.md)
- [查看组织的 Azure 定价](billing-ea-pricing.md)

如果你有 Microsoft 客户协议, 请参阅:

- [了解计费配置文件发票上的费用](billing-mca-understand-your-bill.md)
- [了解帐单配置文件发票上的条款](billing-mca-understand-your-invoice.md)
- [了解计费配置文件的 Azure 使用情况和费用文件](billing-mca-understand-your-usage.md)
- [查看和下载计费配置文件的税务文档](billing-mca-download-tax-document.md)
- [查看组织的 Azure 定价](billing-ea-pricing.md)
