---
title: 删除 Azure 账单付款方式
description: 介绍如何删除 Azure 订阅使用的付款方式。
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 09/24/2020
ms.author: banders
ms.openlocfilehash: a579dd22aa814340b4b72d74907739c942570c23
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91270659"
---
# <a name="delete-an-azure-billing-payment-method-preview"></a>删除 Azure 账单付款方式（预览）

本文档提供了相关说明，可帮助你从不同类型的 Azure 订阅中删除付款方式（如信用卡）。 你可删除以下付款方式：

- Microsoft 客户协议 (MCA)
- Microsoft Online Services 计划 (MOSP)，也称为“即用即付”

无论你使用哪种 Azure 订阅类型，都必须将它取消，然后才能删除其关联的付款方式。

不支持删除其他 Azure 订阅类型（例如 Microsoft 合作伙伴协议和企业协议）的付款方式。

## <a name="delete-an-mca-payment-method"></a>删除 MCA 付款方式

只有创建了 Microsoft 客户协议帐户的用户才能删除付款方式。

若要删除 Microsoft 客户协议的付款方式，请执行以下步骤。

1. 通过 https://portal.azure.com/ 登录到 Azure 门户。
1. 导航到“成本管理 + 计费”。
1. 如有必要，请选择计费范围。
1. 在“计费”下的左侧菜单列表中，选择“计费对象信息” 。  
    :::image type="content" source="./media/delete-azure-payment-method/billing-profiles.png" alt-text="显示 Azure 门户中计费对象信息的示例屏幕截图" lightbox="./media/delete-azure-payment-method/billing-profiles.png" :::
1. 在计费对象信息列表中，选择使用付款方式的帐户。  
    :::image type="content" source="./media/delete-azure-payment-method/select-billing-profile.png" alt-text="显示 Azure 门户中计费对象信息的示例屏幕截图" :::
1. 在左侧菜单列表中的“设置”下，选择“付款方式” 。
1. 在计费对象信息的“付款方式”页面上，“你的信用卡”部分下显示了一张付款方式的表。 找到要删除的信用卡，选择省略号 (…)，然后选择“删除” 。  
    :::image type="content" source="./media/delete-azure-payment-method/delete-credit-card.png" alt-text="显示 Azure 门户中计费对象信息的示例屏幕截图" :::
1. 此时会出现“删除付款方式”页面。 Azure 会检查该付款方式是否正在使用。
    - 如果该付款方式未在使用，则已启用“删除”选项。 将其选中以删除信用卡信息。
    - 如果付款方式正在使用中，则必须将其替换或拆离。 继续阅读以下各部分。 它们介绍了如何拆离订阅正在使用的付款方式。

### <a name="detach-payment-method-used-by-an-mca-billing-profile"></a>拆离 MCA 计费对象信息使用的付款方式

如果 MCA 计费对象信息正在使用你的付款方式，则你将看到一条如下例所示的消息。

:::image type="content" source="./media/delete-azure-payment-method/payment-method-in-use-microsoft-customer-agreement.png" alt-text="显示 Azure 门户中计费对象信息的示例屏幕截图" :::

若要拆离付款方式，必须满足一系列条件。 如果不满足某一条件，则会出现说明，指出如何满足该项条件。 还会出现一个链接，它将转到你可解决该问题的位置。

当所有条件都满足后，你就可从计费对象信息中拆离付款方式。

> [!NOTE]
> 拆离默认付款方式时，计费对象信息将进入“非活动”状态。 在此过程中删除的所有内容都将无法恢复。 在计费对象信息被设置为非活动之后，你必须注册新的 Azure 订阅才能创建新资源。

#### <a name="to-detach-a-payment-method"></a>拆离付款方式

1. 在“删除付款方式”区域中，选择“拆离当前付款方式”链接。
1. 如果满足所有条件，请选择“拆离”。 否则，继续执行下一步。
1. 如果“拆离”选项不可用，会显示条件列表。 执行所列出的操作。 选择“拆离默认付款方式”区域中显示的链接。 下面是纠正措施的示例，它介绍了需要执行的操作。  
    :::image type="content" source="./media/delete-azure-payment-method/azure-subscriptions.png" alt-text="显示 Azure 门户中计费对象信息的示例屏幕截图" :::
1. 选择“纠正措施”链接时，你会被重定向到执行操作的 Azure 页面。 执行所需的任何纠正措施。
1. 如有必要，请完成其他所有纠正措施。
1. 导航回到“成本管理 + 计费” > “计费对象信息” > “付款方式”  。 选择“拆离”。 在“拆离默认付款方式”页面的底部，选择“拆离”。

> [!NOTE]
> - 取消订阅后，最多可能需要 90 天才能删除该订阅。 如果希望缩短等待时间，请打开 Azure 支持请求，请求立即删除订阅。
> - 只有结算了计费对象信息之前所有的费用后，才能删除付款方式。 如果还在有效的计费周期内，则必须等到计费周期结束后才能删除付款方式。 请确保在等待计费周期结束的期间满足其他所有拆离条件。

## <a name="delete-a-mosp-payment-method"></a>删除 MOSP 付款方式

只有帐户管理员才能删除付款方式。

如果 MOSP 订阅正在使用付款方式，请执行以下步骤。

1. 通过 https://portal.azure.com/ 登录到 Azure 门户。
1. 导航到“成本管理 + 计费”。
1. 如有必要，请选择计费范围。
1. 在左侧菜单列表中的“计费”下，选择“付款方式” 。
1. 在“付款方式”区域中，选择付款方式所在的行。 请勿选择付款方式链接。 可能没有视觉指示来指出你已选择付款方式。
1. 选择“删除” 。  
    :::image type="content" source="./media/delete-azure-payment-method/delete-mosp-payment-method.png" alt-text="显示 Azure 门户中计费对象信息的示例屏幕截图" :::
1. 如果满足所有条件，请在“删除付款方式”区域中选择“删除”。 如果“删除”选项不可用，请继续执行下一步。
1. 这会显示条件列表。 执行所列出的操作。 选择“删除付款方式”区域中显示的链接。  
    :::image type="content" source="./media/delete-azure-payment-method/payment-method-in-use-mosp.png" alt-text="显示 Azure 门户中计费对象信息的示例屏幕截图" :::
1. 选择“纠正措施”链接时，你会被重定向到执行操作的 Azure 页面。 执行所需的任何纠正措施。
1. 如有必要，请完成其他所有纠正措施。
1. 导航回到“成本管理 + 计费” > “计费对象信息” > “付款方式”，然后删除付款方式  。

> [!NOTE]
> 取消订阅后，最多可能需要 90 天才能删除该订阅。 如果希望缩短等待时间，请打开 Azure 支持请求，请求立即删除订阅。

## <a name="next-steps"></a>后续步骤

- 如需详细了解如何取消 Azure 订阅，请参阅[取消 Azure 订阅](cancel-azure-subscription.md)。
- 若要详细了解如何添加或更新信用卡，请参阅[添加或更新用于 Azure 的信用卡](change-credit-card.md)。