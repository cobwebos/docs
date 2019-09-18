---
title: 跟踪 Microsoft 客户协议的 Azure 额度余额
description: 了解如何查看 Microsoft 客户协议的 Azure 额度余额。
author: bandersmsft
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 352737b3ea61a51a39e066d4211c8f4ceae74184
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2019
ms.locfileid: "67490969"
---
# <a name="track-microsoft-customer-agreement-azure-credit-balance"></a>跟踪 Microsoft 客户协议的 Azure 额度余额

可以在 Azure 门户中查看 Microsoft 客户协议的 Azure 额度余额。 使用额度来支付额度涵盖的费用。

如果你使用的产品不在额度范围内，或者使用量超出额度余额，我们会对你收费。 有关详细信息，请参阅 [Azure 额度不涵盖的产品] (#products-that-arent-covered-by-azure-credits)。

本文适用于 Microsoft 客户协议的计费帐户。 [检查你是否有权访问 Microsoft 客户协议](#check-access-to-a-microsoft-customer-agreement)。

## <a name="check-your-credit-balance"></a>查看额度余额

1. 登录到 [Azure 门户]( https://portal.azure.com)。

2. 搜索“成本管理 + 计费”。 

    ![显示在门户中搜索“成本管理 + 计费”的屏幕截图](./media/billing-mca-check-azure-credits-balance/billing-search-cost-management-billing.png)

3.  在左侧选择“Azure 额度”。  根据你的访问权限，可能需要选择计费帐户或计费对象信息，然后选择“Azure 额度”  。

4. Azure 额度页显示以下信息：

   ![计费对象信息的额度余额和交易的屏幕截图](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-overview.png)

   | 术语               | 定义                           |
   |--------------------|--------------------------------------------------------|
   | 估计余额  | 在考虑所有计费交易和挂起交易后估算出的额度金额 |
   | 当前余额    | 自上次发票以来的额度金额。 它不包括任何挂起交易。 |
   | 事务       | 受 Azure 额度余额影响的所有计费交易 |

   当预估的余额降到 0 时，所有使用量都收费，包括对额度涵盖的产品收费。

6. 选择“额度列表”，查看计费对象信息的额度的列表。  额度列表提供以下信息：

   ![计费对象信息的额度列表的屏幕截图](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-list.png)

   | 术语 | 定义 |
   |---|---|
   | 估计余额 | 从当前余额中减去未计费的符合额度资格的费用后获得的 Azure 额度的金额|
   | 当前余额 | 在考虑未计费的符合额度资格的费用之前获得的 Azure 额度的金额。 其计算方式是：将接收的新 Azure 额度添加到生成上次发票时的额度余额|
   | 源 | 额度的获取源 |
   | 开始日期 | 获得额度的日期 |
   | 到期日期 | 额度过期的日期 |
   | Balance | 自上次发票以来的余额 |
   | 原始金额 | 原始额度金额 |
   | 状态 | 额度的当前状态。 状态可能为活动、已使用、已过期或将要过期 |

## <a name="how-credits-are-used"></a>额度使用方式

在 Microsoft 客户协议的计费帐户中，请使用计费对象信息来管理发票和付款方式。 将为每个计费对象信息生成每月发票，你可以使用付款方式为发票付款。

Azure 额度是一种付款方式。 可以从 Microsoft 获得额度，例如促销额度和服务级别额度。 这些额度分配给计费对象信息。 为计费对象信息生成发票时，额度自动应用到总计费金额，这样就能计算出你需要支付的金额。 你使用另一付款方式（例如支票或电汇）支付剩余金额。

## <a name="products-that-arent-covered-by-azure-credits"></a>Azure 额度不涵盖的产品

 Azure 额度不涵盖以下产品。 无论你的额度余额是多少，使用这些产品都需付费。

- Canonical
- Citrix XenApp Essentials
- Citrix XenDesktop
- 已注册的用户
- Openlogic
- 远程访问权限 XenApp Essentials 已注册的用户
- Ubuntu Advantage
- Visual Studio Enterprise（每月）
- Visual Studio Enterprise（每年）
- Visual Studio Professional（每月）
- Visual Studio Professional（每年）
- Azure 市场产品
- Azure 支持计划

## <a name="check-access-to-a-microsoft-customer-agreement"></a>检查对 Microsoft 客户协议的访问权限
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员。

如果需要帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。

## <a name="next-steps"></a>后续步骤

- [了解 Microsoft 客户协议的计费帐户](billing-mca-overview.md)
- [了解 Microsoft 客户协议发票中的术语](billing-mca-understand-your-invoice.md)
