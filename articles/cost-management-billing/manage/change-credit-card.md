---
title: 更改用于 Azure 的信用卡
description: 介绍如何更改用于支付 Azure 订阅的信用卡。
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: 0cf0911193cd379da60edb3064d3c6f195cf0190
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2020
ms.locfileid: "77199851"
---
# <a name="add-update-or-remove-a-credit-card-for-azure"></a>添加、更新或删除用于 Azure 的信用卡

本文档适用于使用信用卡注册 Azure online 的客户。

在 Azure 门户中，可将默认付款方式更改为新信用卡，更新信用卡详细信息，以及删除不使用的信用卡。 只有[帐户管理员](billing-subscription-transfer.md#whoisaa)才能进行这种更改。

Microsoft Azure 支持的付款方式为信用卡和支票/电汇。 若要获得支票/电汇付款方式的批准，请参阅[通过发票为 Azure 订阅付款](pay-by-invoice.md)。

如果已签署 Microsoft 客户协议，则付款方式已与计费配置文件相关联。 了解如何[检查对 Microsoft 客户协议的访问权限](#check-the-type-of-your-account)。 如果已签署 MCA，请转到[管理 Microsoft 客户协议的信用卡](#manage-credit-cards-for-a-microsoft-customer-agreement)。

<a id="addcard"></a>

## <a name="manage-credit-cards-for-an-azure-subscription"></a>管理 Azure 订阅的信用卡

以下部分适用于具有 Microsoft Online Services 计划计费帐户的客户。 了解如何[检查计费帐户类型](#check-the-type-of-your-account)。 如果计费帐户类型为 Microsoft Online Services 计划，则付款方式与单个 Azure 订阅相关联。

### <a name="change-credit-card-for-a-subscription"></a>更改订阅的信用卡

可以在 Azure 门户中，将 Azure 订阅的默认信用卡更改为新信用卡或以前保存的信用卡。 只有帐户管理员才能更改信用卡。 如果多个订阅具有同一有效付款方式，则在其中一个订阅上更改有效付款方式时，也会更新其他订阅上的有效付款方式。


可执行以下步骤，将订阅的默认信用卡更改为新信用卡：

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

还可以执行以下步骤，将订阅的默认信用卡更改为已保存到帐户中的信用卡：

1. 以帐户管理员身份登录到 [Azure 门户](https://portal.azure.com)。
1. 搜索“成本管理 + 计费”。 

    ![显示搜索的屏幕截图](./media/change-credit-card/search.png)

1. 选择要向其添加信用卡的订阅。
1. 选择“付款方式”。 

    ![显示已选择“管理付款方式”选项的屏幕截图。](./media/change-credit-card/payment-methods-blade-x.png)

1. 选择要将其设置为有效付款方式的卡旁边的框。
1. 单击“设置为可用”。 
    ![屏幕截图，显示信用卡已选中且设置为有效。](./media/change-credit-card/sub-change-active-x.png)

### <a name="edit-credit-card-details"></a>编辑信用卡详细信息

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

### <a name="delete-a-credit-card-from-the-account"></a>从帐户中删除信用卡

1. 以帐户管理员身份登录到 [Azure 门户](https://portal.azure.com)。
1. 在页面左侧，选择“成本管理 + 计费”  。

    ![显示搜索的屏幕截图](./media/change-credit-card/search.png)

1. 在“账单”下，选择“付款方式”。  

    ![显示已选择“管理付款方式”选项的屏幕截图。](./media/change-credit-card/payment-methods-blade-x.png)

1. 选择要删除的卡旁边的框。
1. 单击 **“删除”** 。

如果信用卡是任何 Microsoft 订阅的有效付款方式，则无法从 Azure 帐户中将其删除。 更改关联到此信用卡的所有订阅的有效付款方式，然后重试。

## <a name="manage-credit-cards-for-a-microsoft-customer-agreement"></a>管理 Microsoft 客户协议的信用卡

以下部分适用于已签署 Microsoft 客户协议，并已使用信用卡注册了 Azure Online 的客户。 [了解如何检查是否已签署 Microsoft 客户协议](#check-the-type-of-your-account)。

### <a name="change-default-credit-card"></a>更改默认信用卡
如果已签署 Microsoft 客户协议，则信用卡与计费配置文件相关联。 只有注册了 Azure 并创建了计费帐户的人员才能更改计费配置文件的付款方式。

若要将计费配置文件的默认付款方式更改为支票/电汇，请参阅[通过发票为 Azure 订阅付款](pay-by-invoice.md)。

若要更改信用卡，请执行以下步骤：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在“成本管理 + 计费”  中进行搜索。
1. 在左侧菜单中，单击“计费对象信息”。 
1. 选择计费对象信息。
1. 在左侧菜单中选择“付款方式”  。

   ![屏幕截图，显示菜单中的付款方式](./media/change-credit-card/payment-methods-tab-mca.png)

1. 在“默认付款方式”部分，单击“更改”。  

    ![显示更改按钮的屏幕截图](./media/change-credit-card/change-payment-method-mca.png)

1. 在右侧的新边栏选项卡中，从下拉列表中选择现有的卡，或单击蓝色的“添加新付款方式”链接添加一张新卡。

### <a name="edit-or-delete-a-credit-card"></a>编辑或删除信用卡

可以在 Azure 门户中编辑信用卡详细信息（例如更新失效日期）以及从帐户中删除信用卡。 仅当信用卡不与任何 Azure 订阅或计费配置文件关联时，才能删除该信用卡。 如果信用卡与已禁用的 Azure 订阅相关联，则必须等到该订阅被删除（取消订阅 30-90 天后删除）才能删除该信用卡。

若要编辑或删除信用卡，请执行以下步骤：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在“成本管理 + 计费”  中进行搜索。
1. 在左侧菜单中，单击“计费对象信息”。 
1. 选择计费对象信息。
1. 在左侧菜单中选择“付款方式”  。

   ![屏幕截图，显示菜单中的付款方式](./media/change-credit-card/payment-methods-tab-mca.png)

1. 在“你的信用卡”部分，找到要编辑或删除的信用卡。 
1. 选择行尾的省略号图标 (`...`)。

    ![显示省略号图标的屏幕截图](./media/change-credit-card/edit-delete-credit-card-mca.png)

1. 若要编辑信用卡详细信息，请从上下文菜单中选择“编辑”。 
1. 若要删除信用卡，请从上下文菜单中选择“删除”。 

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

## <a name="check-the-type-of-your-account"></a>检查帐户的类型
[!INCLUDE [billing-check-mca](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤
- 了解 [Azure 预留](../reservations/save-compute-costs-reservations.md)，看其能否节省资金。
