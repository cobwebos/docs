---
title: 下载 Azure 帐单发票和每日使用数据 | Microsoft Docs
description: 介绍如何下载或查看 Azure 帐单发票和每日使用数据。
keywords: 帐单发票, 发票下载, azure 发票, azure 使用情况
services: billing
documentationcenter: ''
author: genlin
manager: adpick
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: banders
ms.openlocfilehash: b78fb7d697f8a72b3c2f99c4509ea6ac5c5e5566
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "59281176"
---
# <a name="download-or-view-your-azure-billing-invoice-and-daily-usage-data"></a>下载或查看 Azure 帐单发票和每日使用数据

对于大多数订阅，你可以从 [Azure 门户](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)下载发票，也可通过电子邮件发送发票。 如果你是签署了企业协议的 Azure 客户（EA 客户），则无法下载你的组织的发票。 发票将发送给为合约设置的接收发票的人员。

如果你是 EA 客户，或者具有[Microsoft 客户协议](#check-access-to-a-microsoft-customer-agreement)，可以下载中的使用情况[Azure 门户](https://portal.azure.com/)。 有关其他订阅，请转到[Azure 帐户中心](https://account.azure.com/Subscriptions)若要下载使用情况。

只有特定角色有权获取帐单发票和使用情况信息，如帐户管理员或企业管理员。 若要详细了解如何获取计费信息，请参阅[使用角色管理对 Azure 计费的访问](billing-manage-access.md)。

如果你有 Microsoft 客户协议，您必须是账单资料所有者、 参与者、 读取器，或发票管理器来查看计费和使用情况信息。 若要了解 Microsoft 客户协议计费角色的详细信息，请参阅[计费的配置文件角色和任务](billing-understand-mca-roles.md#billing-profile-roles-and-tasks)。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="download-your-azure-invoices-pdf"></a>下载您 Azure 发票 (.pdf)

对于大多数订阅，可以从 Azure 门户下载发票。 如果你有 Microsoft 客户协议，请参阅下载发票计费的配置文件。

### <a name="download-invoices-for-an-individual-subscription"></a>下载单个订阅的发票

1. 选择从你的订阅[订阅页](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)在 Azure 门户中，如下[用户有权访问发票](billing-manage-access.md)。

2. 选择“发票”。

    ![屏幕快照，显示“计费和使用情况”选项](./media/billing-download-azure-invoice-daily-usage-date/billingandusage.png)

3. 单击“下载发票”，查看 PDF 发票的副本。 如果它显示“不可用”，请参阅[为何看不到最后一个计费周期的发票？](#noinvoice)

    ![屏幕快照，显示计费周期、下载选项，以及每个计费周期的总费用](./media/billing-download-azure-invoice-daily-usage-date/billing4.png)

4. 还可以通过单击计费周期来查看每日使用情况。

有关发票的详细信息，请参阅[了解 Microsoft Azure 帐单](billing-understand-your-bill.md)。 如需有关管理成本的帮助，请参阅[通过 Azure 计费和成本管理来防止意外成本](billing-getting-started.md)。

### <a name="download-invoices-for-a-microsoft-customer-agreement"></a>Microsoft 客户协议下载发票

每个生成发票[计费配置文件](billing-mca-overview.md#understand-billing-profiles)Microsoft 客户协议中。 您必须是一个计费配置文件所有者、 参与者、 读取器，或发票管理器从 Azure 门户下载发票。

1. 在“成本管理 + 计费”中进行搜索。
2. 选择计费的配置文件。
3. 选择“发票”。
4. 在发票网格中，找到你想要下载的发票的行。
5. 单击省略号 (`...`) 的行的末尾。
6. 在下载上下文菜单中，选择**发票**。

如果您看不到最后一个计费周期的发票，请参阅**的其他信息**。 <!-- Fix this -->
### <a name="noinvoice"></a> 为何看不到最后一个计费周期的发票？

看不到发票的几个可能原因：

- 从订阅 Azure 开始算还不到 30 天。

- 尚未生成发票。 请等到计费周期结束。

- 你没有权限查看发票。 如果你有 Microsoft 客户协议，您必须是账单资料所有者、 参与者、 读取器，或发票管理器。 对于其他订阅，您可能看不到不是帐户管理员如果旧的发票。 若要详细了解如何获取计费信息，请参阅[使用角色管理对 Azure 计费的访问](billing-manage-access.md)。

- 如果尚未超过订阅有免费试用版或每月信用额度，不会获取发票，除非你有 Microsoft 客户协议。

## <a name="get-your-invoice-in-email-pdf"></a>通过电子邮件获取发票 (.pdf)

可以选择加入并配置其他收件人来通过电子邮件接收 Azure 发票。 此功能可能不适用于某些订阅，例如支持套餐、企业协议或 Azure 开放许可。 如果你有 Microsoft 客户协议，请参阅获取账单资料发票电子邮件中。

### <a name="get-your-subscriptions-invoices-in-email"></a>通过电子邮件获取订阅的发票

1. 从[订阅页面](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)中选择你的订阅。 为拥有的每个订阅执行选择加入操作。 依次单击“发票”、“通过电子邮件发送我的发票”。

    ![显示了“选择加入”流的屏幕截图](./media/billing-download-azure-invoice-daily-usage-date/InvoicesDeepLink.PNG)

2. 单击“选择加入”并接受条款。

    ![显示了“选择加入”流步骤 2 的屏幕截图](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep2.PNG)

3. 接受协议后，即可以配置其他收件人。 删除收件人时，不再存储电子邮件地址。 如果改变主意，则需要重新添加。

    ![显示了“选择加入”流步骤 3 的屏幕截图](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep3.PNG)

如果执行步骤后未收到电子邮件，请确保[个人资料通讯首选项](https://account.windowsazure.com/profile)中的电子邮件地址正确。

### <a name="opt-out-of-getting-your-subscriptions-invoices-in-email"></a>选择退出电子邮件中获取订阅的发票

可以选择禁用更高版本，然后单击的步骤通过电子邮件获取发票**选择不通过电子邮件发送发票**。 此选项将删除任何设置用来通过电子邮件接收发票的电子邮件地址。 如果您选择返回，可以重新配置收件人。

 ![显示了“选择退出”流的屏幕截图](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep4.PNG)

### <a name="get-your-microsoft-customer-agreement-invoices-in-email"></a>电子邮件中获取 Microsoft 客户协议发票

如果你有 Microsoft 客户协议，你可以选择通过电子邮件获取发票。 所有计费配置文件所有者、 参与者、 读者和发票管理器将通过电子邮件获取发票。 读取器不能更新电子邮件发票首选项。

1. 在“成本管理 + 计费”中进行搜索。
1. 选择计费的配置文件。
1. 在“设置”下，选择“属性”。
1. 下**电子邮件发送发票**，选择**更新电子邮件发票首选项**。
1. 选择**参与**。
1. 单击“更新”。

### <a name="opt-out-of-getting-your-billing-profile-invoices-in-email"></a>选择退出电子邮件中获取计费的配置文件发票

可以选择禁用更高版本，然后单击的步骤通过电子邮件获取发票**选择退出**。所有的所有者、 参与者、 读者和发票管理器将选择退出过通过电子邮件获取发票。 如果您是在读取器，则无法更改的电子邮件发票首选项。

## <a name="download-usage"></a>下载使用情况数据

 对于大多数订阅，可以在 [Azure 帐户中心](https://account.azure.com/Subscriptions)内找到你的每日使用情况文件。 如果你是 EA 客户，或有 Microsoft 客户协议，则可以下载中的使用情况[Azure 门户](https://portal.azure.com/)。 <!-- TO DO: update PayG experience to Ibiza once it ships-->

### <a name="download-usage-from-the-account-center-csv"></a>从帐户中心下载使用情况 (.csv)

1. 以帐户管理员身份登录到 [Azure 帐户中心](https://account.windowsazure.com/subscriptions)。

2. 选择需要下载其发票和使用信息的订阅。

3. 选择“帐单”。

    ![屏幕快照，显示帐单选项](./media/billing-download-azure-invoice-daily-usage-date/Billinghisotry.png)

4. 可以查看过去六个计费周期以及当前未计费周期的对帐单。

    ![屏幕快照，显示计费周期、下载发票和日常使用情况的选项，以及每个计费周期的总费用](./media/billing-download-azure-invoice-daily-usage-date/billingSum.png)

5. 选择“查看当前对帐单”，查看截至估算生成时的估计费用。 此信息每天只会更新一次，而且可能不包含所有使用信息。 用户的每月发票可能与这项估计有所不同。

    ![屏幕快照，显示“查看当前对帐单”选项](./media/billing-download-azure-invoice-daily-usage-date/billingSum2.png)

    ![屏幕快照，显示当前费用的估计值](./media/billing-download-azure-invoice-daily-usage-date/billingSum3.png)

6. 选择“下载使用信息”，以 CSV 文件形式下载每日使用数据。 如果看到两个版本可用，请下载版本 2。

    ![屏幕快照，显示“下载使用情况”选项](./media/billing-download-azure-invoice-daily-usage-date/DLusage.png)

只有帐户管理员可以访问 Azure 帐户中心。 其他计费管理员（例如所有者），可以使用[计费 API](billing-usage-rate-card-overview.md) 获取使用情况信息。

有关每日使用情况的详细信息，请参阅[了解 Microsoft Azure 帐单](billing-understand-your-bill.md)。 如需有关管理成本的帮助，请参阅[通过 Azure 计费和成本管理来防止意外成本](billing-getting-started.md)。

### <a name="download-usage-for-ea-customers"></a>下载 EA 客户的使用情况数据

若要查看和下载 EA 客户的使用情况数据，必须是企业管理员，帐户所有者，或与视图的部门管理费用启用策略。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 搜索*成本管理 + 计费*。

    ![显示了 Azure 门户搜索的屏幕截图](./media/billing-download-azure-invoice-daily-usage-date/portal-cm-billing-search.png)

1. 选择“使用情况 + 费用”。
1. 针对要下载的月份，选择“下载”。

### <a name="download-usage-for-your-microsoft-customer-agreement"></a>为 Microsoft 客户协议下载使用情况

若要查看和下载帐单的配置文件的使用情况数据，您必须是账单资料所有者、 参与者、 读取器，或发票管理器。

#### <a name="download-usage-for-billed-charges"></a>下载使用情况的计费费用

1. 在“成本管理 + 计费”中进行搜索。
2. 选择计费的配置文件。
3. 选择“发票”。
4. 在发票网格中，查找对应于你想要下载的使用情况的发票。
5. 单击省略号 (`...`) 的行的末尾。
6. 在下载上下文菜单中，选择**Azure 使用情况和费用**。

#### <a name="download-usage-for-open-charges"></a>下载使用情况的未结费用

您还可以下载月-日使用情况当前计费期间，这意味着不尚未计费费用。

1. 在“成本管理 + 计费”中进行搜索。
2. 选择计费的配置文件。
3. 在中**概述**边栏选项卡中，单击**下载 Azure 使用情况和费用**。

## <a name="check-access-to-a-microsoft-customer-agreement"></a>检查与 Microsoft 客户协议的访问权限
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>需要帮助？ 联系我们

如果有疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

若要了解有关发票和费用的详细信息，请参阅：

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
