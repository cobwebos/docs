---
title: 了解 Azure 外部服务收费 | Microsoft Docs
description: 了解 Azure 中外部服务（以前称为市场）的计费。
author: jureid
manager: jureid
tags: billing
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f94b79e03cf97275ec4dacf17e097e1678e81d10
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "75989418"
---
# <a name="understand-your-azure-external-services-charges"></a>了解 Azure 外部服务收费
外部服务由 Azure 市场中的第三方软件供应商发布。 例如，SendGrid 是可以在 Azure 中购买的外部服务，但不是 Microsoft 发布的。 某些 Microsoft 产品也通过 Azure 市场销售。

## <a name="how-external-services-are-billed"></a>外部服务的计费方式

- 如果你签订了 Microsoft 客户协议 (MCA) 或 Microsoft 合作伙伴协议 (MPA)，则第三方服务将与其他 Azure 服务一起计费。 [检查计费帐户类型](#check-billing-account-type)，查看你是否有权访问 MCA 或 MPA。
- 如果你未签订 MCA 或 MPA，则外部服务将与 Azure 服务分开计费。
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

## <a name="view-and-download-invoices-for-external-services"></a>查看和下载适用于外部服务的发票

如果你签订了 Microsoft 客户协议 (MCA) 或 Microsoft 合作伙伴协议 (MPA)，则第三方服务将与其他 Azure 服务一起计费。 [检查计费帐户类型](#check-billing-account-type)，查看你是否有权访问 MCA 或 MPA。 如果你有权访问，请参阅[在 Azure 门户中查看和下载发票](download-azure-invoice.md)以查看第三方费用。

如果你未签订 MCA 或 MPA，则会收到单独的第三方费用发票。 可以按照以下步骤从 Azure 门户查看和下载 Azure 市场发票：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 搜索“成本管理 + 计费”。
1. 在左侧菜单中，选择“发票”。
1. 单击 " **Azure Marketplace 和预订**" 选项卡。 ![Azure marketplace 和预订选项卡的图片](./media/understand-azure-marketplace-charges/invoice-tabs.png)
1. 在“订阅”下拉列表中，选择包含要查看其发票的外部服务的订阅。

## <a name="external-spending-for-ea-customers"></a>EA 客户的外部支出

EA 客户可以在 EA 门户中查看外部服务支出和下载报表。 若要开始使用，请参阅[适用于 EA 客户的 Azure 市场](https://ea.azure.com/helpdocs/azureMarketplace)。

## <a name="manage-payment-for-external-services"></a>管理外部服务的付款

购买外部服务时，需要为资源选择 Azure 订阅。 所选 Azure 订阅的付款方式将成为外部服务的付款方式。 若要更改外部服务的付款方式，必须[更改绑定到该外部服务的 Azure 订阅的付款方式](../manage/change-credit-card.md)。 可以通过执行以下步骤来确定外部服务订单所绑定到的订阅：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 单击左侧导航菜单中的“所有资源”。
     ![“所有资源”菜单项的屏幕截图](./media/understand-azure-marketplace-charges/all-resources.png)
1. 搜索外部服务。
1. 在“订阅”列中查找订阅的名称。
    ![资源的订阅名称屏幕截图](./media/understand-azure-marketplace-charges/sub-selected.png)
1. 单击订阅名称，并[更新有效付款方式](../manage/change-credit-card.md)。

<!-- Update your payment methods for external service orders from the [Account Center](https://account.windowsazure.com/).

> [!NOTE]
> If you purchased your subscription with a Work or School account, [contact support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) to make changes to your payment method.

1. Sign in to the [Account Center](https://account.windowsazure.com/) and [navigate to the **marketplace** tab](https://account.windowsazure.com/Store)

    ![Select marketplace in the account center](./media/understand-azure-marketplace-charges/select-marketplace.png)
2. Select the external service you want to manage

    ![Select the external service you want to manage](./media/understand-azure-marketplace-charges/select-ext-service.png)
3. Click **Change payment method** on the right side of the page. This link brings you to a different portal to manage your payment method.

    ![Order summary](./media/understand-azure-marketplace-charges/change-payment.PNG)
4. Click **Edit info** and follow instructions to update your payment information.

    ![Select edit info](./media/understand-azure-marketplace-charges/edit-info.png) -->

## <a name="cancel-an-external-service-order"></a>取消外部服务订单
若要取消外部服务订单，请在 [Azure 门户](https://portal.azure.com)中删除资源。

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 单击左侧导航菜单中的“所有资源”。
    ![“所有资源”菜单项的屏幕截图](./media/understand-azure-marketplace-charges/all-resources.png)
1. 搜索外部服务。
1. 选中要删除的资源旁边的框。
1. 在命令栏中，选择“删除”。
    ![“删除”按钮的屏幕截图](./media/understand-azure-marketplace-charges/delete-button.png)
1. 在“确认”边栏选项卡中键入“是”。
    ![删除资源](./media/understand-azure-marketplace-charges/delete-resource.PNG)
1. 单击 **“删除”** 。

## <a name="check-billing-account-type"></a>检查计费帐户类型
[!INCLUDE [billing-check-account-type](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>需要帮助？ 联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤
- [开始分析成本](../costs/quick-acm-cost-analysis.md)
