---
title: 更改用于 Azure 的信用卡
description: 介绍如何更改用于支付 Azure 订阅的信用卡。
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: banders
ms.openlocfilehash: 0f1b4751ae2448b519387d57e4dbfa1eecf8a576
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90984308"
---
# <a name="add-or-update-a-credit-card-for-azure"></a>添加或更新用于 Azure 的信用卡

本文档适用于使用信用卡注册 Azure online 的客户。

在 Azure 门户中，可将默认付款方式更改为新信用卡，并更新信用卡详细信息。 只有[帐户管理员](billing-subscription-transfer.md#whoisaa)才能进行这种更改。

如果要删除信用卡，请参阅[删除 Azure 账单付款方式](delete-azure-payment-method.md)。

Microsoft Azure 支持的付款方式为信用卡和支票/电汇。 若要获得支票/电汇付款方式的批准，请参阅[通过发票为 Azure 订阅付款](pay-by-invoice.md)。

就 Microsoft 客户协议来说，付款方式与计费对象信息相关联。 了解如何[检查对 Microsoft 客户协议的访问权限](#check-the-type-of-your-account)。 如果已签署 MCA，请转到[管理 Microsoft 客户协议的信用卡](#manage-credit-cards-for-a-microsoft-customer-agreement)。

<a id="addcard"></a>

## <a name="manage-credit-cards-for-an-azure-subscription"></a>管理 Azure 订阅的信用卡

以下部分适用于具有 Microsoft Online Services 计划计费帐户的客户。 了解如何[检查计费帐户类型](#check-the-type-of-your-account)。 如果计费帐户类型为 Microsoft Online Services 计划，则付款方式与单个 Azure 订阅相关联。 如果添加信用卡后发生错误，请参阅[注册 Azure 时信用卡遭拒](/troubleshoot-declined-card.md)。

### <a name="change-credit-card-for-a-subscription-by-adding-a-new-credit-card"></a>通过添加新信用卡更改订阅的信用卡

可以在 Azure 门户中，将 Azure 订阅的默认信用卡更改为新信用卡或以前保存的信用卡。 只有帐户管理员才能更改信用卡。 如果多个订阅具有同一有效付款方式，则在其中一个订阅上更改有效付款方式时，也会更新其他订阅上的有效付款方式。

可执行以下步骤，将订阅的默认信用卡更改为新信用卡：

1. 以帐户管理员身份登录到 [Azure 门户](https://portal.azure.com)。
1. 搜索“成本管理 + 计费”。  
    ![显示搜索的屏幕截图](./media/change-credit-card/search.png)
1. 选择要向其添加信用卡的订阅。
1. 选择“付款方式”。  
    ![显示已选择“管理付款方式”选项的屏幕截图](./media/change-credit-card/payment-methods-blade-x.png)
1. 在左上角选择“+”，以便添加卡。 信用卡窗体将显示在右侧。
1. 输入信用卡的详细信息。  
    ![演示如何添加新卡的屏幕截图](./media/change-credit-card/sub-add-new-x.png)
1. 若要将该卡设置为有效付款方式，请勾选窗体上方“将此设置为我的可用付款方式”旁边的框。 对于所使用的卡与所选订阅的卡相同的所有订阅，此卡将成为有效付款方式。
1. 选择“**下一页**”。

### <a name="change-credit-card-for-a-subscription-to-a-previously-saved-credit-card"></a>将订阅的信用卡更改为之前保存的信用卡

还可以执行以下步骤，将订阅的默认信用卡更改为已保存到帐户中的信用卡：

1. 以帐户管理员身份登录到 [Azure 门户](https://portal.azure.com)。
1. 搜索“成本管理 + 计费”。  
    ![显示搜索的屏幕截图](./media/change-credit-card/search.png)
1. 选择要向其添加信用卡的订阅。
1. 选择“付款方式”。
    ![显示已选择“管理付款方式”选项的屏幕截图](./media/change-credit-card/payment-methods-blade-x.png)
1. 选择要将其设置为有效付款方式的卡旁边的框。
1. 选择“设置为可用”。
    ![显示信用卡已选中且设置为可用的屏幕截图](./media/change-credit-card/sub-change-active-x.png)

### <a name="edit-credit-card-details"></a>编辑信用卡详细信息

如果更换了信用卡，但号码保持不变，请更新现有信用卡的详细信息（例如到期日）。 如果信用卡号码由于卡丢失、失窃或过期而更改，则需要按照[将信用卡添加为支付方式](#addcard)中的步骤操作。 无需更新 CVV。

1. 以帐户管理员身份登录到 [Azure 门户](https://portal.azure.com)。
1. 搜索“成本管理 + 计费”。
    ![显示搜索的屏幕截图](./media/change-credit-card/search.png)
1. 选择“付款方式”。
    ![显示已选择“管理付款方式”选项的屏幕截图](./media/change-credit-card/payment-methods-blade-x.png)
1. 选择要编辑的信用卡。 信用卡窗体将显示在右侧。
    ![显示已选定信用卡的屏幕截图](./media/change-credit-card/edit-card-x.png)
1. 更新信用卡的详细信息。
1. 选择“保存”。

## <a name="manage-credit-cards-for-a-microsoft-customer-agreement"></a>管理 Microsoft 客户协议的信用卡

以下部分适用于已签署 Microsoft 客户协议，并已使用信用卡注册了 Azure Online 的客户。 [了解如何检查是否已签署 Microsoft 客户协议](#check-the-type-of-your-account)。

### <a name="change-default-credit-card"></a>更改默认信用卡

如果已签署 Microsoft 客户协议，则信用卡与计费配置文件相关联。 只有注册了 Azure 并创建了计费帐户的人员才能更改计费配置文件的付款方式。

若要将计费配置文件的默认付款方式更改为支票/电汇，请参阅[通过发票为 Azure 订阅付款](pay-by-invoice.md)。

若要更改信用卡，请执行以下步骤：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在“成本管理 + 计费”中进行搜索。
1. 在左侧菜单中，选择“计费对象信息”。
1. 选择计费对象信息。
1. 在左侧菜单中选择“付款方式”。  
   ![屏幕截图，显示菜单中的付款方式](./media/change-credit-card/payment-methods-tab-mca.png)
1. 在“默认付款方式”部分，选择“替换”。   
    :::image type="content" source="./media/change-credit-card/change-payment-method-mca.png" alt-text="显示“替换”选项的屏幕截图" :::
1. 在右侧的新区域中，从下拉列表中选择现有的卡，或选择蓝色的“添加新付款方式”链接添加一张新卡。

### <a name="edit-a-credit-card"></a>编辑信用卡

可以在 Azure 门户中编辑信用卡详细信息（例如更新到期日期）。 

若要编辑信用卡，请执行以下步骤：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在“成本管理 + 计费”中进行搜索。
1. 在左侧菜单中，选择“计费对象信息”。
1. 选择计费对象信息。
1. 在左侧菜单中选择“付款方式”。  
   ![屏幕截图，显示菜单中的付款方式](./media/change-credit-card/payment-methods-tab-mca.png)
1. 在“你的信用卡”部分，找到要编辑的信用卡。
1. 选择行尾的省略号图标 (`...`)。  
    :::image type="content" source="./media/change-credit-card/edit-delete-credit-card-mca.png" alt-text="显示“替换”选项的屏幕截图" :::
1. 若要编辑信用卡详细信息，请从上下文菜单中选择“编辑”。

## <a name="troubleshooting"></a>疑难解答

Azure 不支持虚拟卡或预付卡。 如果在添加或更新有效信用卡时遇到错误，请尝试以无痕模式打开浏览器。

## <a name="frequently-asked-questions"></a>常见问题

以下部分回答了一些有关如何更改信用卡信息的常见问题。

### <a name="why-do-i-keep-getting-your-login-session-has-expired-please-click-here-to-log-back-in"></a>为何我总是看到“登录会话已过期。 请单击此处以重新登录”？

如果注销并重新登录后，还是不断收到此错误消息，请尝试使用无痕浏览会话再次登录。

### <a name="how-do-i-use-a-different-card-for-each-subscription-i-have"></a>如何分别为每个订阅使用不同的卡？

很遗憾，如果多个订阅已在使用同一张卡，则无法分别为每个订阅使用不同的卡。 但注册新订阅时，可选择为该订阅使用新的付款方式。

### <a name="how-do-i-make-payments"></a>如何付款？

如果已将信用卡设置为付款方式，则会在计费周期后自动在卡中扣款。 无需执行任何操作。

如果[用发票付款](pay-by-invoice.md)，可将付款寄送到发票底部列出的地点。

### <a name="how-do-i-change-the-tax-id"></a>如何更改税务 ID？

若要添加或更新税务 ID，请在 [Azure 门户](https://portal.azure.com)更新个人资料，然后选择“税务记录”。 此税务 ID 用于免税计算，并且会显示在发票上。

## <a name="check-the-type-of-your-account"></a>检查帐户的类型

[!INCLUDE [billing-check-mca](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

- 了解 [Azure 预留](../reservations/save-compute-costs-reservations.md)，看其能否节省资金。
- 如果要删除信用卡，请参阅[删除 Azure 账单付款方式](delete-azure-payment-method.md)。