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
ms.date: 11/16/2018
ms.author: cwatson
ms.openlocfilehash: fe3cb2d15602407b54ff47763d409c2e77614f0a
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/12/2018
ms.locfileid: "53311127"
---
# <a name="download-or-view-your-azure-billing-invoice-and-daily-usage-data"></a>下载或查看 Azure 帐单发票和每日使用数据

对于大多数订阅，你可以从 [Azure 门户](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)下载发票，也可通过电子邮件发送发票。 如果你是签署了企业协议的 Azure 客户（EA 客户），则无法下载你的组织的发票。 发票将发送给为合约设置的接收发票的人员。

如果你希望作为 EA 客户下载使用情况数据，则可以从 [Azure 门户](https://portal.azure.com/) > “成本管理 + 计费” > “使用情况 + 费用”获取此数据。 对于其他订阅，请转到 [Azure 帐户中心](https://account.azure.com/Subscriptions)。

只有某些角色（例如帐户管理员或企业管理员）有权获取计费发票和使用情况信息。 若要详细了解如何获取计费信息，请参阅[使用角色管理对 Azure 计费的访问](billing-manage-access.md)。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="download-or-view-your-invoice"></a>下载或查看发票

 如果你是 EA 客户，则无法下载你的组织的发票。 发票将发送给为合约设置的接收发票的人员。 对于其他订阅，可以通过电子邮件获取发票或从 Azure 门户下载发票。

### <a name="get-your-invoice-in-email-pdf"></a>通过电子邮件获取发票 (.pdf)
可以选择加入并配置其他收件人来通过电子邮件接收 Azure 发票。 此功能可能不适用于某些订阅，例如支持套餐、企业协议或 Azure 开放许可。

1. 从[订阅页面](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)中选择你的订阅。 为拥有的每个订阅执行选择加入操作。 依次单击“发票”、“通过电子邮件发送我的发票”。 

    ![显示了“选择加入”流的屏幕截图](./media/billing-download-azure-invoice-daily-usage-date/InvoicesDeepLink.PNG)
    
2. 单击“选择加入”并接受条款。

    ![显示了“选择加入”流步骤 2 的屏幕截图](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep2.PNG)
 
3. 接受协议后，即可以配置其他收件人。 删除收件人时，不再存储电子邮件地址。 如果改变主意，则需要重新添加。

    ![显示了“选择加入”流步骤 3 的屏幕截图](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep3.PNG)
    
如果执行步骤后未收到电子邮件，请确保[个人资料通讯首选项](https://account.windowsazure.com/profile)中的电子邮件地址正确。

### <a name="opt-out-from-getting-your-invoice-in-email"></a>选择不通过电子邮件获取发票
如果不想在电子邮件中获取发票，请单击“选择不通过电子邮件获取发票”。 此选项将删除任何设置用来通过电子邮件接收发票的电子邮件地址。 如果再次选择接收，则需要重新配置收件人。

 ![显示了“选择退出”流的屏幕截图](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep4.PNG)

### <a name="download-invoice-from-azure-portal-pdf"></a>从 Azure 门户下载发票 (.pdf)

1. [作为有权访问发票的用户](billing-manage-access.md)，从 Azure 门户中的[“订阅”页面](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)选择订阅。

2. 选择“发票”。 

    ![屏幕快照，显示“计费和使用情况”选项](./media/billing-download-azure-invoice-daily-usage-date/billingandusage.png) 

3. 单击“下载发票”，查看 PDF 发票的副本。 如果它显示“不可用”，请参阅[为何看不到最后一个计费周期的发票？](#noinvoice)

    ![屏幕快照，显示计费周期、下载选项，以及每个计费周期的总费用](./media/billing-download-azure-invoice-daily-usage-date/billing4.png)

4. 还可以通过单击计费周期来查看每日使用情况。 

有关发票的详细信息，请参阅[了解 Microsoft Azure 帐单](billing-understand-your-bill.md)。 如需有关管理成本的帮助，请参阅[通过 Azure 计费和成本管理来防止意外成本](billing-getting-started.md)。

### <a name="noinvoice"></a> 为何看不到最后一个计费周期的发票？

看不到发票的几个可能原因：

- 尚未超过订阅的每月信用额度，或者使用的是免费试用版。 仅当欠费时，才会生成发票。

- 从订阅 Azure 开始算还不到 30 天。

- 尚未生成发票。 请等到计费周期结束。

- 如果不是帐户管理员，可能无法向你提供较旧的发票。

## <a name="download-usage"></a>下载使用情况数据

 对于大多数订阅，可以在 [Azure 帐户中心](https://account.azure.com/Subscriptions)内找到你的每日使用情况文件。 如果你希望作为 EA 客户下载使用情况数据，则可以从 [Azure 门户](https://portal.azure.com/) > “成本管理 + 计费” > “使用情况 + 费用”获取此数据。 

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

若要作为 EA 客户查看和下载使用情况数据，你必须是启用了“查看费用策略”的企业管理员、帐户所有者或部门管理员。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在“成本管理 + 计费”中进行搜索。

    ![显示了 Azure 门户搜索的屏幕截图](./media/billing-download-azure-invoice-daily-usage-date/portal-cm-billing-search.png)

1. 选择“使用情况 + 费用”。
1. 针对要下载的月份，选择“下载”。

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。
