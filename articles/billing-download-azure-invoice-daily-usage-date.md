---
title: "如何下载 Azure 帐单发票和每日使用数据 | Microsoft Docs"
description: "介绍如何下载 Azure 帐单发票和每日使用数据"
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
ms.date: 12/2/2016
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: 9b864a55d413ec7775e96fced4770b414f379a23
ms.openlocfilehash: ffc5cae2c4559dd8c7a9042dc676485966b976f4


---
# <a name="how-to-download-your-azure-billing-invoice-and-daily-usage-data"></a>如何下载 Azure 帐单发票和每日使用数据
帐单发票和每日使用情况可从 [Azure 帐户中心](https://account.windowsazure.com/subscriptions)下载。 只有帐户管理员有权访问计费发票和使用情况信息。 若要找出谁是订阅的帐户管理员，请参阅[转让 Azure 订阅的所有权 - 常见问题解答](billing-subscription-transfer.md#faq)。

## <a name="get-invoice-and-usage-from-azure-account-center"></a>从 Azure 帐户中心获取发票和使用情况
1. 以帐户管理员身份登录到 [Azure 帐户中心](https://account.windowsazure.com/subscriptions)。
2. 选择需要下载其发票和使用信息的订阅。
3. 选择“帐单”。 

    ![屏幕快照，显示帐单选项](./media/billing-download-azure-invoice-daily-usage-date/Billinghisotry.png)

4. 可以查看过去六个计费周期以及当前未计费周期的对帐单。 

    ![屏幕快照，显示计费周期、下载发票和日常使用情况的选项，以及每个计费周期的总费用](./media/billing-download-azure-invoice-daily-usage-date/billingSum.png)

5. 选择“查看当前对帐单”，查看截至估算生成时的估计费用。 此信息每天只会更新一次，而且可能不包含所有使用信息。 用户的每月发票可能与这项估计有所不同。

    ![屏幕快照，显示“查看当前对帐单”选项](./media/billing-download-azure-invoice-daily-usage-date/billingSum2.png)

    ![屏幕快照，显示当前费用的估计值](./media/billing-download-azure-invoice-daily-usage-date/billingSum3.png)

6. 选择“下载发票”来查看最后一份 pdf 发票的副本。 

    ![屏幕快照，显示“下载发票”选项](./media/billing-download-azure-invoice-daily-usage-date/DLInvoice1.png)

7. 选择“下载使用信息”，以 CSV 文件形式下载每日使用数据。 如果看到两个版本可用，请下载版本 2。

    ![屏幕快照，显示“下载使用情况”选项](./media/billing-download-azure-invoice-daily-usage-date/DLusage.png)

有关发票和每日使用情况的详细信息，请参阅[了解 Microsoft Azure 帐单](./billing/billing-understand-your-bill.md)。

## <a name="get-invoice-from-azure-portal"></a>从 Azure 门户获取发票
可以在 Azure 门户中查看每日使用情况，但只能下载发票。

1. 以帐户管理员身份登录到 [Azure 门户](https://portal.azure.com)。 
2. 在“中心”菜单上，选择“订阅”。 

    ![屏幕快照，显示“订阅”选项](./media/billing-download-azure-invoice-daily-usage-date/submenu.png) 

3. 在“订阅”边栏选项卡中，选择要查看的订阅，然后选择“计费和使用情况”。 

    ![屏幕快照，显示“计费和使用情况”选项](./media/billing-download-azure-invoice-daily-usage-date/billingandusage.png) 

4. 在“计费和使用情况”边栏选项卡上，单击“下载发票”查看 pdf 发票的副本。 

    ![屏幕快照，显示计费周期、下载选项，以及每个计费周期的总费用](./media/billing-download-azure-invoice-daily-usage-date/billing4.png)

5. 单击计费周期即可查看每日使用情况。 

有关发票和每日使用情况的详细信息，请参阅[了解 Microsoft Azure 帐单](./billing/billing-understand-your-bill.md)。

## <a name="a-namenoinvoicea-why-dont-i-see-an-invoice-for-the-last-billing-period"></a><a name="noinvoice"></a> 为何看不到最后一个计费周期的发票。
看不到发票的几个可能原因：
- 尚未超过订阅的每月信用额度，或者使用的是免费试用版。 除非用户欠钱，否则不生成发票。
- 从订阅 Azure 开始算还不到 30 天。
- 尚未生成发票。 请等到计费周期结束。

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员。
如果仍有疑问，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。




<!--HONumber=Dec16_HO2-->


