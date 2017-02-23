---
title: "如何获取 Azure 帐单发票和每日使用数据 | Microsoft Docs"
description: "介绍如何获取 Azure 帐单发票和每日使用数据"
services: 
documentationcenter: 
author: genlin
manager: ruchic
editor: 
tags: billing
ms.assetid: 6d568d1d-3bd6-4348-97d0-1098b5fe0661
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: 4d974590ee7677a4f3fcebd9e2474c99a974f663
ms.openlocfilehash: 60ac57fd22f237b7fe62773486fe46913aca8dd0


---
# <a name="how-to-get-your-azure-billing-invoice-and-daily-usage-data"></a>如何获取 Azure 帐单发票和每日使用数据
可以选择加入并配置其他收件人来通过电子邮件接收 Azure 发票。 也可以从 [Azure 门户](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)下载发票。 此外，还可以在 [Azure 帐户中心](https://account.windowsazure.com)下载使用情况 CSV。 只有帐户管理员有权访问计费发票和使用情况信息。 若要找出谁是订阅的帐户管理员，请参阅[转让 Azure 订阅的所有权 - 常见问题解答](billing-subscription-transfer.md#faq)。

## <a name="get-your-invoice-over-email-pdf"></a>通过电子邮件获取发票 (.pdf)
1. 从[“订阅”边栏选项卡](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)中选择订阅。 需要为拥有的每个订阅执行选择加入操作。 依次单击“发票”、“通过电子邮件发送我的发票”。 如果不是帐户管理员，则不会看到此选项。

    ![显示了“选择加入”流的屏幕截图](./media/billing-download-azure-invoice-daily-usage-date/InvoicesDeepLink.PNG)
    
2. 单击“选择加入”并接受条款：

    ![显示了“选择加入”流的屏幕截图](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep2.PNG)
 
3. 接受协议后，即可以配置其他收件人：

    ![显示了“选择加入”流的屏幕截图](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep3.PNG)
    
也可以在月度声明通知电子邮件中访问此边栏选项卡深层链接

### <a name="i-cant-access-the-email-settings-blade"></a>我无法访问电子邮件设置边栏选项卡
* 必须是帐户管理员才能配置此设置，不清楚这是什么意思？ [在此处了解更多信息](billing-add-change-azure-subscription-administrator.md)。
* 如果有月度发票，但没有收到电子邮件，请确认[通讯电子邮件设置正确](https://account.windowsazure.com/profile)。
* 此功能可能不适用于某些订阅，例如支持提供、企业协议或 Azure 开放许可。

## <a name="download-invoice-from-azure-portal-pdf"></a>从 Azure 门户下载发票 (.pdf)

1. 以帐户管理员身份登录到 [Azure 门户](https://portal.azure.com)。 
2. 在“中心”菜单上，选择“订阅”。 

    ![屏幕快照，显示“订阅”选项](./media/billing-download-azure-invoice-daily-usage-date/submenu.png) 

3. 在“订阅”边栏选项卡中，选择要查看的订阅，然后选择“计费和使用情况”。 

    ![屏幕快照，显示“计费和使用情况”选项](./media/billing-download-azure-invoice-daily-usage-date/billingandusage.png) 

4. 在“计费和使用情况”边栏选项卡上，单击“下载发票”查看 pdf 发票的副本。 

    ![屏幕快照，显示计费周期、下载选项，以及每个计费周期的总费用](./media/billing-download-azure-invoice-daily-usage-date/billing4.png)

5. 单击计费周期即可查看每日使用情况。 

有关发票的详细信息，请参阅[了解 Microsoft Azure 帐单](billing-understand-your-bill.md)。

## <a name="download-usage-from-the-account-center-csv"></a>从帐户中心下载使用情况 (.csv)
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

有关每日使用情况的详细信息，请参阅[了解 Microsoft Azure 帐单](billing-understand-your-bill.md)。

## <a name="a-namenoinvoicea-why-dont-i-see-an-invoice-for-the-last-billing-period"></a><a name="noinvoice"></a> 为何看不到最后一个计费周期的发票。
看不到发票的几个可能原因：
- 尚未超过订阅的每月信用额度，或者使用的是免费试用版。 除非用户欠钱，否则不生成发票。
- 从订阅 Azure 开始算还不到 30 天。
- 尚未生成发票。 请等到计费周期结束。

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员。
如果仍有疑问，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。




<!--HONumber=Feb17_HO3-->


