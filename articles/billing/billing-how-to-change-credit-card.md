---
title: 更改 Azure 的信用卡
description: 介绍如何更改用于支付 Azure 订阅的信用卡。
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: banders
ms.openlocfilehash: 7719ae83525883a6d3f014dbb99877b7319f2ccd
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2019
ms.locfileid: "68383660"
---
# <a name="add-update-or-remove-a-credit-card-for-azure"></a>为 Azure 添加、更新或删除信用卡

在 Azure 门户中, 可以添加新的信用卡、更新现有信用卡或删除不使用的信用卡。 只有[帐户管理员](billing-subscription-transfer.md#whoisaa)才能进行这种更改。

如果你有[Microsoft 客户协议](#check-access-to-a-microsoft-customer-agreement), 则支付方法与计费配置文件相关联。 了解如何[更改计费配置文件的默认付款方式](#change-payment-method-for-a-billing-profile)。 只有注册 Azure 的用户才能更新付款方式。

**要切换为按发票支付 (支票/电汇)？** 请参阅[用发票为 Azure 订阅付款](billing-how-to-pay-by-invoice.md)。

<a id="addcard"></a>

## <a name="add-a-new-credit-card-to-an-azure-subscription"></a>向 Azure 订阅添加新的信用卡

1. 以帐户管理员身份登录到 [Azure 门户](https://portal.azure.com)。
1. 搜索 "**成本管理 + 计费**"。

    ![显示搜索的屏幕截图](./media/billing-how-to-change-credit-card/search.png)

1. 选择要将信用卡添加到的订阅。
1. 选择“付款方式”。

    ![显示已选择“管理付款方式”选项的屏幕截图。](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. 在左上角选择“+”，以便添加卡。 信用卡窗体将显示在右侧。
1. 输入信用卡详细信息。

    ![显示添加新卡片的屏幕截图。](./media/billing-how-to-change-credit-card/sub-add-new-x.png)

1. 若要将此卡设为活动付款方式, 请选中 "**将此我的活动付款方式设**为" 窗体上的复选框。 对于所使用的卡与所选订阅的卡相同的所有订阅，此卡将成为有效付款方式。

1. 选择“**下一步**”。

如果添加信用卡后发生错误，请参阅[注册 Azure 时信用卡遭拒](billing-credit-card-fails-during-azure-sign-up.md)。

## <a name="update-existing-credit-card"></a>更新现有信用卡

如果信用卡已续订, 并且该号码保持不变, 则更新现有的信用卡详细信息, 如到期日期。 如果信用卡号码由于卡丢失、失窃或过期而更改，则需要按照[将信用卡添加为支付方式](#addcard)中的步骤操作。 无需更新 CVV。

1. 以帐户管理员身份登录到 [Azure 门户](https://portal.azure.com)。
1. 搜索 "**成本管理 + 计费**"。

    ![显示搜索的屏幕截图](./media/billing-how-to-change-credit-card/search.png)

1. 选择“付款方式”。

    ![显示已选择“管理付款方式”选项的屏幕截图。](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. 单击要编辑的信用卡。 信用卡窗体将显示在右侧。

    ![显示选中信用卡的屏幕截图。](./media/billing-how-to-change-credit-card/edit-card-x.png)

1. 更新信用卡详细信息。
1. 选择**保存**。

## <a name="use-a-different-credit-card"></a>使用其他信用卡

如果有多个订阅具有相同的有效付款方式, 则对这些订阅中的任何一个订阅更改活动支付方法还会更新其他订阅上的活动付款方式。

1. 以帐户管理员身份登录到 [Azure 门户](https://portal.azure.com)。
1. 搜索 "**成本管理 + 计费**"。

    ![显示搜索的屏幕截图](./media/billing-how-to-change-credit-card/search.png)

1. 选择要将信用卡添加到的订阅。
1. 选择“付款方式”。

    ![显示已选择“管理付款方式”选项的屏幕截图。](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. 选择要使其成为活动付款方式的卡旁边的复选框。
1. 单击 "**设置活动**"。
    ![屏幕截图, 显示选中并设置为活动状态的信用卡。](./media/billing-how-to-change-credit-card/sub-change-active-x.png)

## <a name="remove-a-credit-card-from-the-account"></a>从帐户中删除信用卡

1. 以帐户管理员身份登录到 [Azure 门户](https://portal.azure.com)。
1. 选择页面左侧的 "**成本管理 + 计费**"。

    ![显示搜索的屏幕截图](./media/billing-how-to-change-credit-card/search.png)

1. 在 "**计费**" 下, 选择 "**付款方式**"。

    ![显示已选择“管理付款方式”选项的屏幕截图。](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. 选择要删除的卡旁边的复选框。
1. 单击“删除” 。

如果您的信用卡是任何 Microsoft 订阅的有效付款方式, 则不能将其从 Azure 帐户中删除。 更改链接到此信用卡的所有订阅的有效付款方式, 然后重试
<!-- # Add, update, or remove a credit card for Azure

In the Account Center, you can add a new credit card, update an existing credit card, or delete a credit card that you don't use. You must be an [Account Administrator](billing-subscription-transfer.md#whoisaa) to make these changes.

**Want to switch to pay by invoice?** See [Pay for Azure subscriptions by invoice](billing-how-to-pay-by-invoice.md).

<a id="addcard"></a>

## Add a new credit card

1. Sign in to the [Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select a subscription.
1. On the right side of the page, select **Manage payment methods**.

    ![Screenshot that shows Manage payment methods option selected.](./media/billing-how-to-change-credit-card/changesub_new.png)
1. Select “+” to add a card.

    ![Screenshot that shows the edit option next to the payment method.](./media/billing-how-to-change-credit-card/editcard_new.png)
1. Enter credit card details.
1. Select **Save**.

If you get an error after you add the credit card, see [Credit card declined at Azure sign-up](billing-credit-card-fails-during-azure-sign-up.md).

## Update existing credit card

If your credit card gets renewed and the number remains the same, update the existing credit card details like the expiration date. If your credit card number changes because the card is lost, stolen, or expired, follow the steps in the [Add a credit card as a payment method](#addcard) section. You don't need to update the CVV.

1. Sign in to the [Azure Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select the subscription that's linked to the card.
1. Select **Manage payment methods**.
1. Select **Edit** next to the card you want to update.
1. Update the credit card details.
1. Select **Save**.

## Use a different credit card for the Azure subscription

1. Sign in to the [Azure Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select the subscription that's linked to the card.
1. On the right side of the page, select **Manage payment methods**.
1. Click **Use Instead** next to the card that you want to use. This also updates any other subscriptions currently associated with this card.

## Remove a credit card from the account

1. Sign in to the [Azure Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select the subscription that's linked to the card.
3. On the right side of the page, select **Manage payment methods**.
4. Click **Delete** for the credit card that you want to delete.

If your credit card is associated with other active Microsoft subscriptions, you can't remove it from your Azure account. Remove the credit card from all active subscriptions that you have with Microsoft and try again. -->

## <a name="change-payment-method-for-a-billing-profile"></a>更改计费配置文件的付款方式

若要更改计费配置文件的付款方式, 必须是注册 Azure 的人员。

如果要将默认付款方式切换为 "支票/有线传输", 请学习如何将[计费配置文件切换到支票/有线传输](billing-how-to-pay-by-invoice.md)。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在“成本管理 + 计费”中进行搜索。
1. 在左侧菜单中, 单击 "**计费配置文件**"。

    ![显示菜单中的计费配置文件的屏幕截图](./media/billing-how-to-change-credit-card/billing-profile.png)

1. 选择计费配置文件。
1. 在左侧菜单中, 选择 "**付款方式**"。

   ![显示菜单中付款方式的屏幕截图](./media/billing-how-to-change-credit-card/billing-profile-payment-methods.png)

1. 在 "默认付款方式" 上方, 单击 "**更改**"。

    ![显示 "更改" 按钮的屏幕截图](./media/billing-how-to-change-credit-card/customer-led-switch-credit-card.png)

1. 选择现有卡或添加新卡。

## <a name="frequently-asked-questions"></a>常见问题
以下部分回答了有关更改信用卡信息的常见问题。

### <a name="my-subscription-is-disabled-why-cant-i-remove-my-credit-card-now"></a>我的订阅已禁用。 为何我无法立即删除我的信用卡？

订阅禁用或取消后，我们将在 90 天后永久删除该订阅。 你的付款方式将保留至保留期结束，以便你能够随时根据需要重新激活订阅。 之后, 将永久删除该订阅。

如果需要在90天的保留期结束前删除信用卡, 请[重新激活订阅](billing-subscription-become-disable.md)。 如果不能重新激活，请[联系 Azure 支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

### <a name="why-do-i-keep-getting-your-login-session-has-expired-please-click-here-to-log-back-in"></a>为何我总是看到“登录会话已过期。 请单击此处以重新登录”？

如果注销并重新登录后，还是不断收到此错误消息，请尝试使用专用浏览会话再次登录。

### <a name="how-do-i-use-a-different-card-for-each-subscription-i-have"></a>如何分别为每个订阅使用不同的卡？

很遗憾，如果多个订阅已在使用同一张卡，则无法分别为每个订阅使用不同的卡。 但注册新订阅时，可选择为该订阅使用新的付款方式。

### <a name="how-do-i-make-payments"></a>如何付款？

如果将信用卡设置为付款方式, 则会在每个计费周期后自动对卡进行收费。 无需执行任何操作。

如果[用发票付款](billing-how-to-pay-by-invoice.md)，可将付款寄送到发票底部列出的地点。

### <a name="how-do-i-change-the-tax-id"></a>如何更改税务 ID？

若要添加或更新税务 ID, 请在[Azure 帐户中心](https://account.azure.com/Profile)中更新您的配置文件, 然后选择 "**税务记录**"。 此税务 ID 用于免税计算，并且会显示在发票上。

## <a name="check-access-to-a-microsoft-customer-agreement"></a>检查对 Microsoft 客户协议的访问权限
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>需要帮助? 请联系我们。

如果你有疑问或需要帮助, 请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤
- 了解[Azure 保留](billing-save-compute-costs-reservations.md)项以了解是否可以为你节省资金。
