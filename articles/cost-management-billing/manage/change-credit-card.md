---
title: 更改用于 Azure 的信用卡
description: 介绍如何更改用于支付 Azure 订阅的信用卡。
author: bandersmsft
manager: jureid
tags: billing
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: banders
ms.openlocfilehash: 6ae55e0075883de08af516b71089f00353975b34
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "75992837"
---
# <a name="add-update-or-remove-a-credit-card-for-azure"></a>添加、更新或删除用于 Azure 的信用卡

本文档适用于使用信用卡注册 Azure online 的客户。

在 Azure 门户中，可以添加新信用卡、更新现有信用卡，或删除不用的信用卡。 只有[帐户管理员](billing-subscription-transfer.md#whoisaa)才能进行这种更改。

如果有 [Microsoft 客户协议](#check-access-to-a-microsoft-customer-agreement)，则付款方式已与计费对象信息相关联。 了解如何[更改计费对象信息的默认付款方式](#change-payment-method-for-a-billing-profile)。 仅注册 Azure 的用户可以更新付款方式。

**要切换到按发票付款（支票/电汇）？** 请参阅[用发票为 Azure 订阅付款](pay-by-invoice.md)。

<a id="addcard"></a>

## <a name="add-a-new-credit-card-to-an-azure-subscription"></a>为 Azure 订阅添加新信用卡

1. 以帐户管理员身份登录到 [Azure 门户](https://portal.azure.com)。
1. 搜索“成本管理 + 计费”。 

    ![显示搜索的屏幕截图](./media/change-credit-card/search.png)

1. 选择要向其添加信用卡的订阅。
1. 选择“付款方式”。 

    ![显示已选择“管理付款方式”选项的屏幕截图。](./media/change-credit-card/payment-methods-blade-x.png)

1. 在左上角选择“+”，以便添加卡。 信用卡窗体将显示在右侧。
1. 输入信用卡的详细信息。

    ![屏幕截图，显示添加新卡。](./media/change-credit-card/sub-add-new-x.png)

1. 若要将该卡设置为有效付款方式，请勾选窗体上方“将此设置为我的可用付款方式”旁边的框。  对于所使用的卡与所选订阅的卡相同的所有订阅，此卡将成为有效付款方式。

1. 选择“**下一页**”。

如果添加信用卡后发生错误，请参阅[注册 Azure 时信用卡遭拒](../../billing/billing-credit-card-fails-during-azure-sign-up.md)。

## <a name="update-existing-credit-card"></a>更新现有信用卡

如果更换了信用卡，但号码保持不变，请更新现有信用卡的详细信息（例如到期日）。 如果信用卡号码由于卡丢失、失窃或过期而更改，则需要按照[将信用卡添加为支付方式](#addcard)中的步骤操作。 无需更新 CVV。

1. 以帐户管理员身份登录到 [Azure 门户](https://portal.azure.com)。
1. 搜索“成本管理 + 计费”。 

    ![显示搜索的屏幕截图](./media/change-credit-card/search.png)

1. 选择“付款方式”。 

    ![显示已选择“管理付款方式”选项的屏幕截图。](./media/change-credit-card/payment-methods-blade-x.png)

1. 单击要编辑的信用卡。 信用卡窗体将显示在右侧。

    ![屏幕截图，显示所选信用卡。](./media/change-credit-card/edit-card-x.png)

1. 更新信用卡的详细信息。
1. 选择“保存”。 

## <a name="use-a-different-credit-card"></a>使用其他信用卡

如果多个订阅具有同一有效付款方式，则在其中一个订阅上更改有效付款方式时，也会更新其他订阅上的有效付款方式。

1. 以帐户管理员身份登录到 [Azure 门户](https://portal.azure.com)。
1. 搜索“成本管理 + 计费”。 

    ![显示搜索的屏幕截图](./media/change-credit-card/search.png)

1. 选择要向其添加信用卡的订阅。
1. 选择“付款方式”。 

    ![显示已选择“管理付款方式”选项的屏幕截图。](./media/change-credit-card/payment-methods-blade-x.png)

1. 选择要将其设置为有效付款方式的卡旁边的框。
1. 单击“设置为可用”。 
    ![屏幕截图，显示信用卡已选中且设置为有效。](./media/change-credit-card/sub-change-active-x.png)

## <a name="remove-a-credit-card-from-the-account"></a>从帐户中删除信用卡

1. 以帐户管理员身份登录到 [Azure 门户](https://portal.azure.com)。
1. 在页面左侧，选择“成本管理 + 计费”  。

    ![显示搜索的屏幕截图](./media/change-credit-card/search.png)

1. 在“账单”下，选择“付款方式”。  

    ![显示已选择“管理付款方式”选项的屏幕截图。](./media/change-credit-card/payment-methods-blade-x.png)

1. 选择要删除的卡旁边的框。
1. 单击 **“删除”** 。

如果信用卡是任何 Microsoft 订阅的有效付款方式，则无法从 Azure 帐户中将其删除。 更改关联到此信用卡的所有订阅的有效付款方式，然后重试。

## <a name="change-payment-method-for-a-billing-profile"></a>更改计费对象信息的付款方式

若要更改计费对象信息的付款方式，你必须是注册 Azure 的人。

若要将默认付款方式转换为支票/电汇，请了解如何[将计费对象信息转换为支票/电汇](pay-by-invoice.md)。

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在“成本管理 + 计费”  中进行搜索。
1. 在左侧菜单中，单击“计费对象信息”。 

    ![屏幕截图，显示菜单中的计费对象信息](./media/change-credit-card/billing-profile.png)

1. 选择计费对象信息。
1. 在左侧菜单中选择“付款方式”  。

   ![屏幕截图，显示菜单中的付款方式](./media/change-credit-card/billing-profile-payment-methods.png)

1. 在默认付款方式上方，单击“更改”。 

    ![显示更改按钮的屏幕截图](./media/change-credit-card/customer-led-switch-credit-card.png)

1. 选择现有卡或添加新卡。

## <a name="troubleshooting"></a>故障排除
我们不支持虚拟卡或预付卡。 如果在添加或更新有效信用卡时遇到错误，请尝试以无痕模式打开浏览器。

## <a name="frequently-asked-questions"></a>常见问题
以下部分回答了一些有关如何更改信用卡信息的常见问题。

### <a name="my-subscription-is-disabled-why-cant-i-remove-my-credit-card-now"></a>我的订阅已禁用。 为何我无法立即删除我的信用卡？

订阅禁用或取消后，我们将在 90 天后永久删除该订阅。 你的付款方式将保留至保留期结束，以便你能够随时根据需要重新激活订阅。 然后，将永久删除订阅。

如果需要在 90 天的保留期结束前删除信用卡，请[重新激活订阅](subscription-disabled.md)。 如果不能重新激活，请[联系 Azure 支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

### <a name="why-do-i-keep-getting-your-login-session-has-expired-please-click-here-to-log-back-in"></a>为何我总是看到“登录会话已过期。 请单击此处以重新登录”？

如果注销并重新登录后，还是不断收到此错误消息，请尝试使用无痕浏览会话再次登录。

### <a name="how-do-i-use-a-different-card-for-each-subscription-i-have"></a>如何分别为每个订阅使用不同的卡？

很遗憾，如果多个订阅已在使用同一张卡，则无法分别为每个订阅使用不同的卡。 但注册新订阅时，可选择为该订阅使用新的付款方式。

### <a name="how-do-i-make-payments"></a>如何付款？

如果已将信用卡设置为付款方式，则会在计费周期后自动在卡中扣款。 无需执行任何操作。

如果[用发票付款](pay-by-invoice.md)，可将付款寄送到发票底部列出的地点。

### <a name="how-do-i-change-the-tax-id"></a>如何更改税务 ID？

若要添加或更新税务 ID，请在 [Azure 帐户中心](https://account.azure.com/Profile)更新个人资料，然后选择“税务记录”。  此税务 ID 用于免税计算，并且会显示在发票上。

## <a name="check-access-to-a-microsoft-customer-agreement"></a>检查对 Microsoft 客户协议的访问权限
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤
- 了解 [Azure 预留](../reservations/save-compute-costs-reservations.md)，看其能否节省资金。
