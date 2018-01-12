---
title: "避免 Azure 免费帐户产生费用 - Azure | Microsoft Docs"
description: "了解 Azure 免费帐户产生费用的原因。 了解如何避免产生这些费用。"
services: 
documentationcenter: 
author: amberbhargava
manager: amberb
editor: 
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: amberb
ms.openlocfilehash: 0efea55d0e1329ddf4ada35aa0dd6164f0951a70
ms.sourcegitcommit: 094061b19b0a707eace42ae47f39d7a666364d58
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2017
---
# <a name="avoid-getting-charged-for-your-azure-free-account"></a>避免 Azure 免费帐户产生费用

Azure 免费帐户提供 200 美元的 Azure 信用额度（可在前 30 天内使用），以及 12 个月的限量免费服务。 有关详细信息，请参阅 [Azure 免费帐户](https://azure.microsoft.com/free/)。 根据信用额度情况，对于超过免费服务及用量的使用量，可使用信用额度抵扣或直接付费。

## <a name="you-still-have-active-azure-free-account-credit"></a>仍有有效的 Azure 免费帐户信用额度 
如果尚有未到期的 Azure 信用额度，Azure 将使用这些信用额度抵扣超出免费服务及用量的使用量。

## <a name="your-credit-ran-out-or-has-expired"></a>信用额度已用完或已过期
如果信用额度已用完或在 30 天结束后过期，Azure 会禁用订阅。 若要继续使用 Azure 服务，必须将订阅升级为即用即付订阅。 有关详细信息，请参阅[将 Azure 免费试用版订阅升级为即用即付订阅](billing-upgrade-azure-subscription.md)。 升级之后，你的订阅仍可使用 12 个月的免费服务。 只需要对超过免费服务及用量的使用量进行偿付。


让我们来看看可能会让 Azure 免费帐户产生费用的一些原因。


### <a name="your-usage-exceeds-the-limits-of-free-services-included-with-your-azure-free-account"></a>使用量超出 Azure 免费帐户中包含的免费服务限值

使用 Azure 免费帐户时，每个月可获得有限数量的免费服务。 免费数量在每个月结束时过期，不会累计到下一个月。 例如，每个月可获得 5 GB 文件存储。 如果某个月只用了 2 GB，剩下的 3 GB 不会累计到下个月。 为了避免产生费用，请将使用量保持在限值内。 若要了解有关免费服务的限值，请参阅 [Azure 免费帐户是什么](https://azure.microsoft.com/free/free-account-faq/)。 若要查看免费服务使用情况，请参阅[检查 Azure 免费帐户所含免费服务的使用情况](billing-check-free-service-usage.md)。 

### <a name="you-are-using-services-not-included-for-free-with-your-azure-free-account"></a>使用的是 Azure 免费帐户不包含的付费服务

需要按即用即付费率为 Azure 免费帐户未免费提供的服务付款。 若要了解有关免费帐户所含服务的信息，请参阅 [Azure 免费帐户是什么](https://azure.microsoft.com/free/free-account-faq/)。 可在 Azure 门户上或通过 Azure 使用情况文件检查正在使用的服务的使用情况。 若要了解详细信息，请参阅[定期检查门户，获取成本细分和燃速](billing-getting-started.md#costs)和[从帐户中心下载使用文件](billing-download-azure-invoice-daily-usage-date.md)。 

### <a name="you-have-reached-the-end-of-your-12-months-free-services"></a>12 个月的免费服务已到期

免费服务及用量在 12 个月后到期。 可在 Azure 门户上查询到期日期。

1.  登录到 [Azure 门户](http://portal.azure.com)。

2.  在左侧导航窗格底部选择“更多服务”。

3.  选择“订阅”。

4.  选择注册免费帐户时创建的订阅。

5.  向下滚动，查找免费服务网格。 单击位于网格左上方的工具提示。

![显示免费帐户权益到期日期查找位置的屏幕截图](./media/billing-avoid-charges-free-account/freeaccount-benefits-expiration-date.png)


免费服务及用量到期后，Azure 会按即用即付费率对所有使用中的服务收取费用。 Azure 门户可用于删除未使用的服务的资源。 如果不打算使用任何 Azure 服务，则可以[取消订阅](billing-how-to-cancel-azure-subscription.md)。

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员

如果需要帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。
