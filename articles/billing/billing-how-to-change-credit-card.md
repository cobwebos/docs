---
title: "更改用于 Azure 的信用卡 | Microsoft Docs"
description: "介绍如何更改用于支付 Azure 订阅的信用卡"
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing
ms.assetid: 15252ced-1841-4a66-ae79-2e58af1d3370
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2017
ms.author: genli
ms.openlocfilehash: 03764377b3ea0e17d4a192a7e05bb495ec56f331
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="add-update-or-remove-a-credit-or-debit-card-for-azure"></a>添加、更新或删除用于 Azure 的信用卡或借记卡

在帐户中心，可以添加新信用卡、更新现有信用卡，或删除不用的信用卡。 只有[帐户管理员](billing-subscription-transfer.md#whoisaa)才能进行这种更改。

**要切换到用发票付款？** 请参阅[用发票为 Azure 订阅付款](billing-how-to-pay-by-invoice.md)。
 
<a id="addcard"></a>

## <a name="add-a-new-credit-or-debit-card"></a>添加新的信用卡或借记卡

1. 以帐户管理员身份登录到[帐户中心](https://account.windowsazure.com/Subscriptions)。
1. 选择一个订阅。
1. 在页面右侧，选择“管理付款方式”。

    ![显示已选择“管理付款方式”选项的屏幕截图。](./media/billing-how-to-change-credit-card/changesub_new.png)
1. 选择“+”添加卡。

    ![显示付款方式旁边的编辑选项的屏幕截图。](./media/billing-how-to-change-credit-card/editcard_new.png)
1. 输入信用卡或借记卡的详细信息。
1. 选择“保存”。 

如果添加信用卡后发生错误，请参阅[注册 Azure 时信用卡遭拒](billing-credit-card-fails-during-azure-sign-up.md)。

## <a name="update-existing-credit-or-debit-card"></a>更新现有信用卡或借记卡

如果更换了信用卡，但号码保持不变，请更新现有信用卡的详细信息（例如到期日）。 如果信用卡号码由于卡丢失、失窃或过期而更改，则需要按照[将信用卡添加为支付方式](#addcard)中的步骤操作。 无需更新 CVV。

1. 以帐户管理员身份登录到 [Azure 帐户中心](https://account.windowsazure.com/Subscriptions)。
1. 选择链接到卡的订阅。
1. 选择“管理付款方式”。
1. 选择想要更新的卡旁边的“编辑”。
1. 更新信用卡或借记卡的详细信息。
1. 选择“保存”。

## <a name="use-a-different-credit-card-for-the-azure-subscription"></a>使用另外一张信用卡支付 Azure 订阅

1. 以帐户管理员身份登录到 [Azure 帐户中心](https://account.windowsazure.com/Subscriptions)。
1. 选择链接到卡的订阅。
1. 在页面右侧，选择“管理付款方式”。
1. 单击要使用的信用卡旁边的“改用”。 这还会更新当前与此卡关联的任何其他订阅。 

## <a name="remove-a-credit-or-debit-card-from-the-account"></a>从帐户中删除信用卡或借记卡

1. 以帐户管理员身份登录到 [Azure 帐户中心](https://account.windowsazure.com/Subscriptions)。
1. 选择链接到卡的订阅。
3. 在页面右侧，选择“管理付款方式”。
4. 对想要删除的信用卡单击“删除”。

如果信用卡与其他活动的 Microsoft 订阅关联，则无法从 Azure 帐户中将其删除。 从你拥有的所有 Microsoft 活动订阅中删除该信用卡，然后再次尝试。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="my-subscription-is-disabled-why-cant-i-remove-my-credit-card-now"></a>我的订阅已禁用。 为何我无法立即删除我的信用卡？

订阅禁用或取消后，我们将在 90 天后永久删除该订阅。 你的付款方式将保留至保留期结束，以便你能够随时根据需要重新激活订阅。 保留期结束后，将彻底删除订阅。

如果需要在 90 天的保留期结束前删除信用卡或借记卡，[请联系 Azure 支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

### <a name="why-do-i-keep-getting-your-login-session-has-expired-please-click-here-to-log-back-in"></a>为何我总是看到“登录会话已过期。 请单击此处以重新登录”？

如果注销并重新登陆后，还是不断收到此错误消息，请尝试使用专用浏览会话再次登陆。

### <a name="how-do-i-use-a-different-card-for-each-subscription-i-have"></a>如何分别为每个订阅使用不同的卡？

很遗憾，如果多个订阅已在使用同一张卡，则无法分别为每个订阅使用不同的卡。 但注册新订阅时，可选择为该订阅使用新的付款方式。

### <a name="how-do-i-make-payments"></a>如何付款？

如果已将信用卡或借记卡设置为付款方式，则会在计费周期后自动在卡中扣款。 无需执行任何操作。

如果[用发票付款](billing-how-to-pay-by-invoice.md)，可将付款寄送到发票底部列出的地点。

### <a name="how-do-i-make-a-one-time-payment"></a>如何一次性付款？

很遗憾，Azure 当前不支持信用卡或借记卡一次性付款。 

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员

如果仍需帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。
