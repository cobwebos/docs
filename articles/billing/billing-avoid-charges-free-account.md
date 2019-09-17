---
title: 避免 Azure 免费帐户产生费用
description: 了解 Azure 免费帐户产生费用的原因。 了解如何避免产生这些费用。
author: amberbhargava
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: ac5bcf0a4dd9c7ee593c70a63509a21f28ba1456
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2019
ms.locfileid: "67491478"
---
# <a name="avoid-charges-with-your-azure-free-account"></a>避免 Azure 免费帐户产生费用

Azure 免费帐户提供 200 美元的 Azure 信用额度（可在前 30 天内使用），以及 12 个月的限量免费服务。 有关详细信息，请参阅 [Azure 免费帐户](https://azure.microsoft.com/free/)。 根据信用额度情况，对于超过免费服务及用量的使用量，可使用信用额度抵扣或直接付费。

## <a name="azure-free-account-might-use-account-credit"></a>Azure 免费帐户可以使用帐户额度
如果尚有未到期的 Azure 信用额度，Azure 将使用这些信用额度抵扣超出免费服务及用量的使用量。

## <a name="your-credit-runs-out-or-is-expired"></a>额度已用完或已过期
如果你的额度在 30 天结束时用完或到期，则 Azure 会停用你的订阅。 若要继续使用 Azure 服务，必须将订阅升级为采用即用即付费率的个人订阅。 有关详细信息，请参阅[升级免费试用版 Azure 订阅](billing-upgrade-azure-subscription.md)。 升级之后，你的订阅仍可使用 12 个月的免费服务。 只需对超过免费服务及用量的使用量付费即可。

让我们来看看可能会让 Azure 免费帐户产生费用的一些原因。

### <a name="usage-exceeds-the-limits-of-free-services"></a>使用量超出了免费服务的限值

使用 Azure 免费帐户时，每个月可获得有限数量的免费服务。 免费数量在每个月结束时过期，不会累计到下一个月。 例如，每个月可获得 5 GB 文件存储。 如果某个月只用了 2 GB，剩下的 3 GB 不会累计到下个月。 为了避免产生费用，请将使用量保持在限值内。 若要了解有关免费服务的限值，请参阅 [Azure 免费帐户常见问题解答](https://azure.microsoft.com/free/free-account-faq/)。 若要查看免费服务使用情况，请参阅[检查 Azure 免费帐户所含免费服务的使用情况](billing-check-free-service-usage.md)。

### <a name="some-services-are-not-free"></a>有些服务不是免费的

需要按即用即付费率为 Azure 免费帐户未免费提供的服务付款。 若要了解有关免费帐户所含服务的信息，请参阅 [Azure 免费帐户常见问题解答](https://azure.microsoft.com/free/free-account-faq/)。 可以在 Azure 门户或 Azure 使用情况文件中检查服务使用情况。 若要了解详细信息，请参阅[定期检查门户，获取成本细分和燃速](billing-getting-started.md#costs)和[从帐户中心下载使用文件](billing-download-azure-invoice-daily-usage-date.md)。

### <a name="you-reached-the-end-of-your-free-12-months"></a>你的免费使用 12 个月已结束

免费服务及用量在 12 个月后到期。 可在 Azure 门户中了解免费服务的到期时间。

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 在左侧的导航区域中，选择“所有服务”  。

3.  选择 **订阅**。

4.  选择注册免费帐户时创建的订阅。

5.  向下滚动，查找免费服务网格。 单击位于网格左上方的工具提示。

![显示免费帐户权益到期时间的查找位置的屏幕截图](./media/billing-avoid-charges-free-account/freeaccount-benefits-expiration-date.png)


免费服务及用量到期后，Azure 会按即用即付费率对所有使用中的服务收取费用。 Azure 门户可用于删除未使用的服务的资源。 如果不打算使用任何 Azure 服务，则可以[取消订阅](billing-how-to-cancel-azure-subscription.md)。

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤
- [升级免费试用版 Azure 订阅](billing-upgrade-azure-subscription.md)
