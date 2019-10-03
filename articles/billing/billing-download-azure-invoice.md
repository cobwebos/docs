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
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 862ec629504da4d8ee7eadd8f9b925984d96614c
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2019
ms.locfileid: "71718892"
---
# <a name="view-and-download-your-microsoft-azure-invoice"></a>查看和下载 Microsoft Azure 发票

对于大多数订阅，你可以从 [Azure 门户](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)下载发票，也可通过电子邮件发送发票。 如果你是签署了企业协议的 Azure 客户（EA 客户），则无法下载你的组织的发票。 发票将发送给为合约设置的接收发票的人员。

只有特定角色有权查看发票。 例如，帐户管理员或企业管理员。 有关获取对账单信息的访问权限的详细信息，请参阅[使用角色管理对 Azure 账单的访问权限](billing-manage-access.md)。

如果你签订了 [Microsoft 客户协议](#check-your-access-to-a-microsoft-customer-agreement)，则必须具有以下角色之一才能获取发票：

- 计费对象信息所有者
- 参与者
- 读取器
- 发票管理者

若要详细了解 Microsoft 客户协议的计费角色，请参阅[计费对象信息角色和任务](billing-understand-mca-roles.md#billing-profile-roles-and-tasks)。

## <a name="noinvoice"></a> 为什么可能无法获取发票

看不到发票的几个可能原因：

- 从订阅 Azure 开始算还不到 30 天。

- 在发票周期结束时，Azure 会向你开具账单。 因此，可能尚未生成发票。 请等到计费周期结束。

- 你无权查看发票。 如果你签订了 Microsoft 客户协议，则必须是计费对象信息所有者、参与者、读者或发票管理者，才能查看发票。 对于其他订阅，如果不是帐户管理员，则可能看不到旧发票。 若要详细了解如何获取计费信息，请参阅[使用角色管理对 Azure 计费的访问](billing-manage-access.md)。

- 如果你的订阅有免费试用或每月信用额度，则仅当超过月度信用额度时才会收到发票。 如果你签订了 Microsoft 客户协议，则始终会收到发票。

## <a name="download-your-azure-invoices-pdf"></a>下载 Azure 发票 (.pdf)

对于大多数订阅，可以从 Azure 门户下载发票。 如果你签订了 Microsoft 客户协议，请参阅[下载适用于 Microsoft 客户协议的发票](#download-invoices-for-a-microsoft-customer-agreement)。

### <a name="download-invoices-for-an-individual-subscription"></a>下载个人订阅的发票

1. 作为[有权访问发票的用户](billing-manage-access.md)，在 Azure 门户中的[“订阅”页](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)上选择订阅。

2. 选择“发票”  。

    ![屏幕快照，显示“计费和使用情况”选项](./media/billing-download-azure-invoice-daily-usage-date/billingandusage.png)

3. 单击“下载发票”  ，查看 PDF 发票的副本。 如果它显示“不可用”  ，请参阅[为何看不到最后一个计费周期的发票？](#noinvoice)

    ![屏幕快照，显示计费周期、下载选项，以及每个计费周期的总费用](./media/billing-download-azure-invoice-daily-usage-date/billing4.png)

4. 还可以通过单击计费周期来查看每日使用情况。

有关发票的详细信息，请参阅[了解 Microsoft Azure 帐单](billing-understand-your-bill.md)。 如需有关管理成本的帮助，请参阅[通过 Azure 账单和成本管理来防止意外成本](billing-getting-started.md)。

### <a name="download-invoices-for-a-microsoft-customer-agreement"></a>下载适用于 Microsoft 客户协议的发票

将为 Microsoft 客户协议中的每条[计费对象信息](billing-mca-overview.md#billing-profiles)生成发票。 必须是计费对象信息所有者、参与者、读者或发票管理者才能从 Azure 门户下载发票。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 搜索“成本管理 + 计费”  。
1. 选择计费对象信息。 根据你的访问权限，可能需要先选择一个计费帐户。
1. 选择“发票”  。
1. 在发票网格中，找到要下载的发票行。
1. 单击行末尾的省略号 (`...`)。
    ![显示行尾省略号的屏幕截图](./media/billing-download-azure-invoice/billingprofile-invoicegrid.png)
1. 在下载上下文菜单中，选择“发票”  。

    ![显示上下文菜单的屏幕截图](./media/billing-download-azure-invoice/contextmenu.png)

如果看不到上个计费周期的发票，请参阅[为何看不到上个计费周期的发票？](#noinvoice)

## <a name="get-your-invoice-in-email-pdf"></a>通过电子邮件获取发票 (.pdf)

可以选择加入并配置其他收件人来通过电子邮件接收 Azure 发票。 此功能可能不适用于某些订阅，例如支持套餐、企业协议或 Azure 开放许可。 如果你签订了 Microsoft 客户协议，请参阅下一部分：[通过电子邮件获取计费对象信息发票](#get-your-subscriptions-invoices-in-email)。

### <a name="get-your-subscriptions-invoices-in-email"></a>通过电子邮件获取订阅的发票

1. 在[“订阅”页](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)上选择订阅。 为拥有的每个订阅执行选择加入操作。 依次单击“发票”  、“通过电子邮件发送我的发票”  。

    ![显示了“选择加入”流的屏幕截图](./media/billing-download-azure-invoice-daily-usage-date/InvoicesDeepLink.PNG)

2. 单击“选择加入”  并接受条款。

    ![显示了“选择加入”流步骤 2 的屏幕截图](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep2.PNG)

3. 接受协议后，即可以配置其他收件人。 删除收件人时，不再存储电子邮件地址。 如果改变主意，则需要重新添加。

    ![显示了“选择加入”流步骤 3 的屏幕截图](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep3.PNG)

如果执行步骤后未收到电子邮件，请确保[个人资料通讯首选项](https://account.windowsazure.com/profile)中的电子邮件地址正确。

### <a name="opt-out-of-getting-your-subscriptions-invoices-in-email"></a>选择不通过电子邮件获取订阅的发票

若要选择不通过电子邮件获取发票，请按照前面的步骤进行操作，并单击“选择不通过电子邮件发送发票”  。 此选项将删除任何设置用来通过电子邮件接收发票的电子邮件地址。 如果再次选择接收，则可以重新配置收件人。

 ![显示了“选择退出”流的屏幕截图](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep4.PNG)

### <a name="get-your-microsoft-customer-agreement-invoices-in-email"></a>通过电子邮件获取 Microsoft 客户协议发票

如果签订了 Microsoft 客户协议，可以选择通过电子邮件获取发票。 所有计费对象信息所有者、参与者、读者和发票经理都将通过电子邮件获取发票。 读者无法更新电子邮件发票首选项。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 搜索“成本管理 + 计费”  。
1. 选择计费对象信息。 根据你的访问权限，可能需要先选择一个计费帐户。
1. 在“设置”下，选择“属性”   。
1. 在“电子邮件发票”  下，选择“更新电子邮件发票首选项”  。

    ![显示电子邮件发票属性的屏幕截图](./media/billing-download-azure-invoice/billingprofile-email.png)

1. 选择“选择加入”  。
1. 单击“更新”  。

### <a name="opt-out-of-getting-your-microsoft-customer-agreement-invoices-in-email"></a>选择不通过电子邮件获取 Microsoft 客户协议发票

若要选择不通过电子邮件获取发票，请按照前面的步骤进行操作，并单击“选择退出”  。所有所有者、参与者、读者和发票管理者也会选择不通过电子邮件获取发票。 如果你是读者，则无法更改电子邮件发票首选项。



## <a name="check-your-access-to-a-microsoft-customer-agreement"></a>检查对 Microsoft 客户协议的访问权限
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

若要详细了解发票和费用，请参阅：

- [查看和下载 Microsoft Azure 使用情况与费用](billing-download-azure-daily-usage.md)
- [了解 Microsoft Azure 账单](billing-understand-your-bill.md)
- [了解有关 Azure 发票的术语](billing-understand-your-invoice.md)
- [了解 Microsoft Azure 详细使用条款](billing-understand-your-usage.md)
- [查看组织的 Azure 定价](billing-ea-pricing.md)

如果你签订了 Microsoft 客户协议，请参阅：

- [了解计费对象信息发票上的费用](billing-mca-understand-your-bill.md)
- [了解计费对象信息发票上的术语](billing-mca-understand-your-invoice.md)
- [了解计费对象信息的 Azure 使用情况和费用文件](billing-mca-understand-your-usage.md)
- [查看和下载计费对象信息的税单](billing-mca-download-tax-document.md)
- [查看组织的 Azure 定价](billing-ea-pricing.md)
