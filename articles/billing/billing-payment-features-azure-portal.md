---
title: Azure 门户中的帐户管理员任务
description: 描述如何在 Azure 门户中执行付款操作
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: banders
ms.openlocfilehash: 510765778166f007501cfcb21e242c539200ce2f
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2019
ms.locfileid: "73722136"
---
# <a name="account-administrator-tasks-in-the-azure-portal"></a>Azure 门户中的帐户管理员任务

本文介绍如何在 Azure 门户中执行以下任务：
- 管理订阅的付款方式
- 删除订阅的支出限制
- 向 Azure 开放许可订阅添加额度

只有帐户管理员才能执行上述任务。 

## <a name="navigate-to-your-subscriptions-payment-methods"></a>导航到订阅的付款方式

1. 以帐户管理员身份登录到 [Azure 门户](https://portal.azure.com)。
1. 搜索“成本管理 + 计费”。 
    
    ![显示搜索“成本管理 + 计费”的屏幕截图 ](./media/billing-payment-features-azure-portal/search-bar.png)

1. 在“我的订阅”列表中，选择要向其添加信用卡的订阅  。
    ![显示我的订阅网格概览的屏幕截图](./media/billing-payment-features-azure-portal/cost-management-billing-overview-x.png)

1. 选择“付款方式”。 

    ![显示选定的付款方式边栏选项卡的屏幕截图。](./media/billing-payment-features-azure-portal/subscription-payment-methods-blade.png)

你可以在此添加新的信用卡，更改有效付款方式，编辑信用卡详细信息以及删除信用卡。 

### <a name="change-active-payment-method"></a>更改有效付款方式

添加一张信用卡或者选择一张已保存的信用卡就可以更改有效付款方式。 将有效付款方式更改为新信用卡：

1. 在左上角选择“+”添加一张信用卡。
    
    ![显示加号的屏幕截图](./media/billing-payment-features-azure-portal/subscription-payment-methods-plus.png)

1. 在右侧窗体中输入信用卡详细信息。

    ![显示添加信用卡窗体的屏幕截图。](./media/billing-payment-features-azure-portal/subscription-add-payment-method-x.png)

1. 若要将该卡设置为有效付款方式，请勾选窗体上方“将此卡设置为我的有效付款方式”旁边的框。  对于所使用的卡与所选订阅的卡相同的所有订阅，此卡将成为有效付款方式。

    ![显示用于将卡设置为有效付款方式的复选框的屏幕截图。](./media/billing-payment-features-azure-portal/subscription-make-active-payment-method-x.png)

1. 选择“**下一步**”。

将有效付款方式更改为已保存的信用卡：

1. 选择要将其设置为有效付款方式的卡旁边的框。

    ![显示信用卡旁勾选的框的屏幕截图](./media/billing-payment-features-azure-portal/subscription-checked-payment-method-x.png)

1. 在命令栏中单击“设置为可用”  。

    ![显示“设置为可用”按钮的屏幕截图](./media/billing-payment-features-azure-portal/subscription-checked-payment-method-set-active.png)

### <a name="edit-credit-card-details"></a>编辑信用卡详细信息

要编辑信用卡详细信息（例如到期日期或地址），请单击要编辑的信用卡。 信用卡窗体将显示在右侧。

![显示选定的信用卡的屏幕截图](./media/billing-payment-features-azure-portal/subscription-edit-payment-method-x.png)

更新信用卡详细信息，然后单击“保存”  。

### <a name="remove-a-credit-card-from-the-account"></a>从帐户中删除信用卡

1. 选中要删除的卡旁边的复选框。

    ![显示信用卡旁勾选的框的屏幕截图](./media/billing-payment-features-azure-portal/subscription-checked-payment-method-x.png)

1. 在命令栏中，单击“删除”  。

    ![显示“删除”按钮的屏幕截图](./media/billing-payment-features-azure-portal/subscription-checked-payment-method-delete.png)

如果信用卡是任何 Microsoft 订阅的有效付款方式，则无法从 Azure 帐户中将其删除。 更改与此信用卡关联的所有订阅的有效付款方式，然后重试。

### <a name="switch-to-invoice-payment"></a>切换为发票付款

如果有资格通过发票（支票/电汇）付款，可以在 Azure 门户中将订阅切换为发票付款（支票/电汇）。

1. 在命令栏中选择“使用发票付款”  。

    ![显示选定的付款方式边栏选项卡的屏幕截图。](./media/billing-payment-features-azure-portal/subscription-payment-methods-pay-by-invoice.png)

1. 为发票付款方法输入地址。
1. 单击“下一步”。 

如果获批使用发票支付，请参阅[了解如何使用发票付款](billing-how-to-pay-by-invoice.md)。

### <a name="edit-invoice-payment-address"></a>编辑发票付款地址

要为发票付款方法编辑地址，请在订阅的付款方式列表中单击“发票”  。 随即在右侧打开地址窗体。 

## <a name="remove-spending-limit"></a>删除支出限制

Azure 中的支出限制可以防止支出超过额度金额。 只要 Azure 订阅关联了有效的付款方法，即可随时移除支出限制。 对于具有多个月额度的订阅类型（例如 Visual Studio Enterprise 和 Visual Studio Professional），可以在下个计费周期开始时重新启用支出限制。

支出限制不适用于具有承诺计划或使用即用即付定价的订阅。 请参阅 [Azure 订阅类型以及支出限制可用性的完整列表](https://azure.microsoft.com/support/legal/offer-details/)。

1. 以帐户管理员身份登录到 [Azure 门户](https://portal.azure.com)。
1. 搜索“成本管理 + 计费”。 

    ![显示搜索“成本管理 + 计费”的屏幕截图 ](./media/billing-payment-features-azure-portal/search-bar.png)

1. 在“我的订阅”列表中，选择自己的 Visual Studio Enterprise 订阅  。
    
    ![显示我的订阅网格概览的屏幕截图](./media/billing-payment-features-azure-portal/cost-management-overview-msdn-x.png)

1. 在订阅概览中，单击橙色横幅删除支出限制。
    
    ![显示删除支出限制横幅的屏幕截图](./media/billing-payment-features-azure-portal/msdn-remove-spending-limit-banner-x.png)

1. 选择是要永久删除支出限制还是仅为当前计费周期删除。

   ![显示删除支出限制边栏选项卡的屏幕截图](./media/billing-payment-features-azure-portal/remove-spending-limit-blade-x.png)

1. 单击“选择付款方式”，为订阅选择一个付款方式  。 这将成为订阅的有效付款方式。

1. 单击“完成”  。

## <a name="add-credits-to-azure-in-open-subscription"></a>向 Azure 开放许可订阅添加额度

如果你有 Azure 开放许可订阅，可以通过兑换产品密钥或使用信用卡购买额度的方式，向 Azure 门户中的订阅添加额度。

1. 以帐户管理员身份登录到 [Azure 门户](https://portal.azure.com)。
1. 搜索“成本管理 + 计费”。 

    ![显示搜索“成本管理 + 计费”的屏幕截图 ](./media/billing-payment-features-azure-portal/search-bar.png)

1. 在“我的订阅”列表中，选择自己的 Azure 开放许可订阅  。
   
    ![显示我的订阅网格概览的屏幕截图](./media/billing-payment-features-azure-portal/cost-management-overview-aio-x.png)

1. 选择“额度历史记录”  。
    
    ![显示额度历史记录的屏幕截图。](./media/billing-payment-features-azure-portal/aio-credit-history-blade.png)

1. 在左上角选择“+”添加更多额度。

    ![显示额度按钮的屏幕截图](./media/billing-payment-features-azure-portal/aio-credit-history-plus.png)

1. 在下拉列表中选择一种付款方式类型。 可以添加产品密钥或使用信用卡购买额度。
    
    ![屏幕截图，其中“添加额度”边栏选项卡中显示“付款方式”下拉列表](./media/billing-payment-features-azure-portal/add-credits-select-payment-method.png)

1. 如果选择的是产品密钥：
    - 输入产品密钥
    - 单击“验证” 

1. 如果选择的是信用卡：
    - 单击“选择付款方式”以添加信用卡或选择一张现有的信用卡  。
    - 指定希望添加的额度。

1. 单击“应用” 

## <a name="troubleshooting"></a>故障排除
我们不支持虚拟卡或预付卡。 如果在添加或更新有效信用卡时遇到错误，请尝试以专用模式打开浏览器。

## <a name="next-steps"></a>后续步骤
- 详细了解如何[在 Azure 门户中分析和预防预料之外的费用](billing-getting-started.md)
