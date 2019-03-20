---
title: 更改用于 Azure 的信用卡 | Microsoft Docs
description: 介绍如何更改用于支付 Azure 订阅的信用卡
services: ''
documentationcenter: ''
author: genlin
manager: jureid
editor: ''
tags: billing
ms.assetid: 15252ced-1841-4a66-ae79-2e58af1d3370
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 3/13/2019
ms.author: banders
ms.openlocfilehash: b910cb3061b1451ea80b9843e2aa4047a784548f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "57876861"
---
# <a name="add-update-or-remove-a-credit-or-debit-card-for-azure"></a>添加、更新或删除用于 Azure 的信用卡或借记卡

在 Azure 门户中，可以添加新信用卡、 更新现有信用卡，或删除不使用的信用卡。 只有[帐户管理员](billing-subscription-transfer.md#whoisaa)才能进行这种更改。

<!-- If your Angola, Belize, Algeria, Vietnam, or Virgin Islands. -->

**要切换到用发票付款？** 请参阅[用发票为 Azure 订阅付款](billing-how-to-pay-by-invoice.md)。

<a id="addcard"></a>

## <a name="add-a-new-credit-or-debit-card-to-an-azure-subscription"></a>向 Azure 订阅中添加新信用卡或借记卡

1. 以帐户管理员身份登录到 [Azure 门户](https://portal.azure.com)。
1. 在“成本管理 + 计费”中进行搜索。

    ![显示搜索的屏幕截图](./media/billing-how-to-change-credit-card/search.png)

1. 选择你想要添加到信用卡或借记卡的订阅。
1. 选择**付款方式**。

    ![显示已选择“管理付款方式”选项的屏幕截图。](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. 在左上角，选择"+"添加卡。 信用卡窗体将显示在右侧。
1. 输入信用卡或借记卡的详细信息。

    ![说明如何添加新卡的屏幕截图。](./media/billing-how-to-change-credit-card/sub-add-new-x.png)

1. 若要简化此卡可用的付款方式，旁边的复选框**这让我可用的付款方式**上面窗体。 此卡将成为在同一张卡用作所选订阅的所有订阅的 active 付款方式。

1. 选择“**下一步**”。

如果添加信用卡后发生错误，请参阅[注册 Azure 时信用卡遭拒](billing-credit-card-fails-during-azure-sign-up.md)。

## <a name="update-existing-credit-or-debit-card"></a>更新现有信用卡或借记卡

如果数保持不变，但你的信用卡，更新现有信用卡的过期日期等的详细信息。 如果信用卡号码由于卡丢失、失窃或过期而更改，则需要按照[将信用卡添加为支付方式](#addcard)中的步骤操作。 无需更新 CVV。

1. 以帐户管理员身份登录到 [Azure 门户](https://portal.azure.com)。
1. 在“成本管理 + 计费”中进行搜索。

    ![显示搜索的屏幕截图](./media/billing-how-to-change-credit-card/search.png)

1. 选择**付款方式**。

    ![显示已选择“管理付款方式”选项的屏幕截图。](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. 单击信用卡或借记卡你想要编辑。 信用卡窗体将显示在右侧。

    ![显示所选的信用卡或借记卡的屏幕截图。](./media/billing-how-to-change-credit-card/edit-card-x.png)

1. 更新信用卡或借记卡的详细信息。
1. 选择“保存”。

## <a name="use-a-different-credit-card-for-the-azure-subscription"></a>使用另外一张信用卡支付 Azure 订阅

如果多个订阅具有同一个可用的付款方式，然后更改任何这些订阅上可用的付款方式还会更新对其他可用的付款方式。

1. 以帐户管理员身份登录到 [Azure 门户](https://portal.azure.com)。
1. 在“成本管理 + 计费”中进行搜索。

    ![显示搜索的屏幕截图](./media/billing-how-to-change-credit-card/search.png)

1. 选择你想要添加到信用卡或借记卡的订阅。
1. 选择**付款方式**。

    ![显示已选择“管理付款方式”选项的屏幕截图。](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. 选中你想要使可用的付款方式的信用卡旁边的框。
1. 单击**设置活动**。
    ![显示所选的信用卡或借记卡和组活动的屏幕截图。](./media/billing-how-to-change-credit-card/sub-change-active-x.png)

## <a name="remove-a-credit-or-debit-card-from-the-account"></a>从帐户中删除信用卡或借记卡

1. 以帐户管理员身份登录到 [Azure 门户](https://portal.azure.com)。
1. 选择**成本管理 + 计费**在页面的左侧。

    ![显示搜索的屏幕截图](./media/billing-how-to-change-credit-card/search.png)

1. 下**计费**，选择**付款方式**。

    ![显示已选择“管理付款方式”选项的屏幕截图。](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. 选中你想要删除的卡旁边的框。
1. 单击“删除” 。

如果你的信用卡的任何 Microsoft 订阅可用的付款方式，无法从你的 Azure 帐户中将其删除。 更改链接到此信用卡或借记卡的所有订阅可用的付款方式，然后重试
<!-- # Add, update, or remove a credit or debit card for Azure

In the Account Center, you can add a new credit card, update an existing credit card, or delete a credit card that you don't use. You must be an [Account Administrator](billing-subscription-transfer.md#whoisaa) to make these changes.

**Want to switch to pay by invoice?** See [Pay for Azure subscriptions by invoice](billing-how-to-pay-by-invoice.md).
 
<a id="addcard"></a>

## Add a new credit or debit card

1. Sign in to the [Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select a subscription.
1. On the right side of the page, select **Manage payment methods**.

    ![Screenshot that shows Manage payment methods option selected.](./media/billing-how-to-change-credit-card/changesub_new.png)
1. Select “+” to add a card.

    ![Screenshot that shows the edit option next to the payment method.](./media/billing-how-to-change-credit-card/editcard_new.png)
1. Enter credit or debit card details.
1. Select **Save**. 

If you get an error after you add the credit card, see [Credit card declined at Azure sign-up](billing-credit-card-fails-during-azure-sign-up.md).

## Update existing credit or debit card

If your credit card gets renewed and the number remains the same, update the existing credit card details like the expiration date. If your credit card number changes because the card is lost, stolen, or expired, follow the steps in the [Add a credit card as a payment method](#addcard) section. You don't need to update the CVV.

1. Sign in to the [Azure Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select the subscription that's linked to the card.
1. Select **Manage payment methods**.
1. Select **Edit** next to the card you want to update.
1. Update the credit or debit card details.
1. Select **Save**.

## Use a different credit card for the Azure subscription

1. Sign in to the [Azure Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select the subscription that's linked to the card.
1. On the right side of the page, select **Manage payment methods**.
1. Click **Use Instead** next to the card that you want to use. This also updates any other subscriptions currently associated with this card. 

## Remove a credit or debit card from the account

1. Sign in to the [Azure Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select the subscription that's linked to the card.
3. On the right side of the page, select **Manage payment methods**.
4. Click **Delete** for the credit card that you want to delete.

If your credit card is associated with other active Microsoft subscriptions, you can't remove it from your Azure account. Remove the credit card from all active subscriptions that you have with Microsoft and try again. -->
## <a name="frequently-asked-questions"></a>常见问题

### <a name="my-subscription-is-disabled-why-cant-i-remove-my-credit-card-now"></a>我的订阅已禁用。 为何我无法立即删除我的信用卡？

订阅禁用或取消后，我们将在 90 天后永久删除该订阅。 你的付款方式将保留至保留期结束，以便你能够随时根据需要重新激活订阅。 之后，将永久删除该订阅。

如果需要在 90 天的保留期结束前删除信用卡或借记卡，请[重新激活订阅](billing-subscription-become-disable.md)。 如果不能重新激活，请[联系 Azure 支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

### <a name="why-do-i-keep-getting-your-login-session-has-expired-please-click-here-to-log-back-in"></a>为何我总是看到“登录会话已过期。 请单击此处以重新登录”？

如果注销并重新登录后，还是不断收到此错误消息，请尝试使用专用浏览会话再次登录。

### <a name="how-do-i-use-a-different-card-for-each-subscription-i-have"></a>如何分别为每个订阅使用不同的卡？

很遗憾，如果多个订阅已在使用同一张卡，则无法分别为每个订阅使用不同的卡。 但注册新订阅时，可选择为该订阅使用新的付款方式。

### <a name="how-do-i-make-payments"></a>如何付款？

如果已将信用卡或借记卡设置为付款方式，则会在计费周期后自动在卡中扣款。 无需执行任何操作。

如果[用发票付款](billing-how-to-pay-by-invoice.md)，可将付款寄送到发票底部列出的地点。

### <a name="how-do-i-make-a-one-time-payment"></a>如何一次性付款？

很遗憾，Azure 当前不支持信用卡或借记卡一次性付款。 

### <a name="how-do-i-change-the-tax-id"></a>如何更改税务 ID？

若要添加或更新税务 ID，请访问 [Azure 帐户中心中的“配置文件”](https://account.azure.com/Profile)，然后选择“税收记录”。 此税务 ID 用于免税计算，并且会显示在发票上。

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如果有疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。
