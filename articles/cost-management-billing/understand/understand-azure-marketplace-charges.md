---
title: 了解 Azure 外部服务收费 | Microsoft Docs
description: 了解 Azure 中外部服务（以前称为市场）的计费。
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7955e9bc75b2a27ac42d381df1e686ec8a0ed04b
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77587737"
---
# <a name="understand-your-azure-external-services-charges"></a>了解 Azure 外部服务收费
外部服务由 Azure 市场中的第三方软件供应商发布。 例如，SendGrid 是可以在 Azure 中购买的外部服务，但不是 Microsoft 发布的。 某些 Microsoft 产品也通过 Azure 市场销售。

## <a name="how-external-services-are-billed"></a>外部服务的计费方式

- 如果你签订了 Microsoft 客户协议 (MCA) 或 Microsoft 合作伙伴协议 (MPA)，则第三方服务将与其他 Azure 服务一起计费。 [检查计费帐户类型](#check-billing-account-type)，查看你是否有权访问 MCA 或 MPA。
- 如果你未签订 MCA 或 MPA，则外部服务将与 Azure 服务分开计费。 在每个计费周期你将收到两份发票：一份发票针对 Azure 服务，另一份发票针对市场购买项。
- 每个外部服务都有不同的计费模型。 某些服务按即用即付方式计费，而其他服务则按月收取固定费用。
- 购买外部服务时不能使用每月的免费信用额度。 如果所使用的 Azure 订阅包含[免费额度](https://azure.microsoft.com/pricing/spending-limits/)，则不能将其应用于外部服务费用。 预配新的外部服务或资源时，会显示警告：

    ![市场购买警告](./media/understand-azure-marketplace-charges/credit-warning.png)

<!-- ## View external service spending and history in the Azure portal
You can view a list of the external services that are on each subscription within the [Azure portal](https://portal.azure.com/):

1. Sign in to the [Azure portal](https://portal.azure.com/) as the account administrator.
2. In the Hub menu, select **Subscriptions**.

    ![Select Subscriptions in the Hub menu](./media/understand-azure-marketplace-charges/sub-button.png)
3. In the **Subscriptions** blade, select the subscription that you want to view, and then select **External services**.

    ![Select a subscription in the billing blade](./media/understand-azure-marketplace-charges/select-sub-external-services.png)
4. You should see each of your external service orders, the publisher name, service tier you bought, name you gave the resource, and the current order status. To see past bills, select an external service.

    ![Select an external service](./media/understand-azure-marketplace-charges/external-service-blade2.png)
5. From here, you can view past bill amounts including the tax breakdown.

    ![View external services billing history](./media/understand-azure-marketplace-charges/billing-overview-blade.png) -->

## <a name="external-spending-for-ea-customers"></a>EA 客户的外部支出

EA 客户可以在 EA 门户中查看外部服务支出和下载报表。 若要开始使用，请参阅[适用于 EA 客户的 Azure 市场](https://ea.azure.com/helpdocs/azureMarketplace)。

## <a name="view-and-download-invoices-for-external-services"></a>查看和下载外部服务的发票

如果你签订了 Microsoft 客户协议 (MCA) 或 Microsoft 合作伙伴协议 (MPA)，则第三方服务将在一份发票中与其他 Azure 服务一起计费。 [检查计费帐户类型](#check-billing-account-type)，查看你是否有权访问 MCA 或 MPA。 如果你有权访问，请参阅[在 Azure 门户中查看和下载发票](download-azure-invoice.md)以查看第三方费用。

如果你未签订 MCA 或 MPA，则会收到单独的第三方费用发票。 

Azure 市场费用以本地货币显示。

可以按照以下步骤从 Azure 门户查看和下载 Azure 市场发票：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 搜索“成本管理 + 计费”。 
1. 在左侧菜单中，选择“发票”  。
1. 在订阅下拉列表中，选择与市场服务关联的订阅。
1. 查看发票列表中的“类型”列。  如果发票针对市场服务，则类型是“Azure 市场和预留项”。  

    ![发票网格中“Azure 市场”类型的屏幕截图](./media/understand-azure-marketplace-charges/marketplace-type-twd.png)

1. 若要按类型进行筛选，以便仅查看“Azure 市场和预留项”的发票，请选择“类型”筛选器。  然后在下拉列表中选择“Azure 市场和预留项”。 

    ![选择“类型”筛选器的屏幕截图，其中显示已在下拉列表中选择“Azure 市场和预留项”](./media/understand-azure-marketplace-charges/type-filter.png)

1. 在要下载的发票右侧选择下载图标。

    ![显示选择发票对应的下载图标的屏幕截图](./media/understand-azure-marketplace-charges/download-icon-marketplace.png)

1. 在“发票”下，选择蓝色的“下载”按钮。  

    ![在上下文窗格中显示发票下载按钮的屏幕截图](./media/understand-azure-marketplace-charges/invoice-download-marketplace.png)

## <a name="pay-for-external-services-in-the-azure-portal"></a>为 Azure 门户中为外部服务付费

如果你签订了 Microsoft 客户协议 (MCA) 或 Microsoft 合作伙伴协议 (MPA)，则第三方服务将与其他 Azure 服务一起计费。 [检查计费帐户类型](#check-billing-account-type)，查看你是否有权访问 MCA 或 MPA。 如果有权访问，则可以遵循[支付 Microsoft Azure 的帐单](pay-bill.md)中的步骤，在 Azure 门户中为整个发票付费。

如果未签订 MCA 或 MPA，可以遵循以下步骤，在 Azure 门户中为市场发票付款：

1. 遵循上一部分[查看和下载外部服务的发票](#view-and-download-invoices-for-external-services)中的步骤，找到你的市场发票。
1. 选择要付款的发票对应的蓝色“立即支付”链接。 

    ![显示在发票网格中选择“立即支付”链接的屏幕截图](./media/understand-azure-marketplace-charges/pay-now-twd.png)

    >[!NOTE]
    > 仅当发票类型是“Azure 市场和预留项”并且发票付款状态为到期或逾期未付时，才能看到“立即支付”链接。  

1. 在新页面中，单击蓝色的“选择付款方式”链接。 

    ![显示选择“选择付款方式”链接的屏幕截图](./media/understand-azure-marketplace-charges/select-payment-method-pay-now-twd.png)

1. 选择付款方式后，单击页面左下角的蓝色“立即支付”按钮。 
    ![显示选择“立即付款”按钮的屏幕截图](./media/understand-azure-marketplace-charges/pay-now-button-twd.png)

## <a name="change-default-payment-for-external-services"></a>更改外部服务的默认付款方式

购买外部服务时，需要为资源选择 Azure 订阅。 所选 Azure 订阅的付款方式将成为外部服务的付款方式。 若要更改外部服务的付款方式，必须[更改绑定到该外部服务的 Azure 订阅的付款方式](../manage/change-credit-card.md)。 可以通过执行以下步骤来确定外部服务订单所绑定到的订阅：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 单击左侧导航菜单中的“所有资源”  。
     ![“所有资源”菜单项的屏幕截图](./media/understand-azure-marketplace-charges/all-resources.png)
1. 搜索外部服务。
1. 在“订阅”  列中查找订阅的名称。
    ![资源的订阅名称屏幕截图](./media/understand-azure-marketplace-charges/sub-selected.png)
1. 单击订阅名称，并[更新有效付款方式](../manage/change-credit-card.md)。

## <a name="cancel-an-external-service-order"></a>取消外部服务订单

若要取消外部服务订单，请在 [Azure 门户](https://portal.azure.com)中删除资源。

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 单击左侧导航菜单中的“所有资源”  。
    ![“所有资源”菜单项的屏幕截图](./media/understand-azure-marketplace-charges/all-resources.png)
1. 搜索外部服务。
1. 选中要删除的资源旁边的框。
1. 在命令栏中，选择“删除”  。
    ![“删除”按钮的屏幕截图](./media/understand-azure-marketplace-charges/delete-button.png)
1. 在“确认”边栏选项卡中键入“是”  。
    ![删除资源](./media/understand-azure-marketplace-charges/delete-resource.PNG)
1. 单击 **“删除”** 。

## <a name="check-billing-account-type"></a>检查计费帐户类型
[!INCLUDE [billing-check-account-type](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤
- [开始分析成本](../costs/quick-acm-cost-analysis.md)
