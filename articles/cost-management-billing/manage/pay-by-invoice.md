---
title: 按发票为 Azure 订阅付款
description: 说明如何按发票为 Azure 订阅付款。
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: 12ad1f09c988171b0d6212d79ca61d93acf92d80
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2020
ms.locfileid: "77199596"
---
# <a name="pay-for-your-azure-subscription-by-invoice"></a>按发票为 Azure 订阅付款

如果切换到按发票付款，这意味着需要在开票日期后的 30 天内以支票/电汇方式支付账单费用。 必须向 Azure 支持部门提交请求，才能按发票支付 Azure 订阅费用。 请求获批后，可以在 [Azure 门户](https://portal.azure.com)中切换到发票付款（支票/电汇）。

> [!IMPORTANT]
> * 发票付款（支票/电汇）仅适用于代表公司使用 Azure 的客户。
> * 在切换到发票付款之前，请支付所有未付费用。
> * 目前，中国的全球 Azure 不支持发票付款。

## <a name="request-to-pay-by-invoice"></a>请求按发票付款

1. 若要提交支持请求，请转到 [Azure 门户](https://portal.azure.com)。 搜索并选择“帮助 + 支持”  。

    ![搜索“帮助和支持”、Microsoft Azure 门户](./media/pay-by-invoice/search-for-help-and-support.png)

2. 选择“新建支持请求”。 

    ![“新建支持请求”链接、“帮助和支持”屏幕、Microsoft Azure 门户](./media/pay-by-invoice/help-and-support.png)

2. 选择“计费”作为“问题类型”。   问题类型是支持请求类别。  选择要按发票付款的订阅，选择支持计划，然后选择“下一步”。 

3. 选择“付款”作为“问题类型”。   问题类型是支持请求子类别。 

4. 选择“切换到按发票付款”作为“问题子类型”   。

5. 在“详细信息”  框中输入以下信息，并选择“下一步”  。

         New or existing customer:
         If existing, current payment method:
         Order ID (requesting for invoice option):
         Account Admins Live ID (or Org ID) (should be company domain):
         Commerce Account ID:
         Company Name (as registered under VAT or Government Website):
         Company Address (as registered under VAT or Government Website):
         Company Website:
         Country:
         TAX ID/ VAT ID:
         Company Established on (Year):
         Any prior business with Microsoft:
         Contact Name:
         Contact Phone:
         Contact Email:
         Justification on why you prefer Invoice option over credit card:

         For cores increase, provide the following additional information:

         (Old quota) Existing Cores:
         (New quota) Requested cores:
         Specific region & series of Subscription:

    - “公司名称”和“公司地址”应与你为 Azure 帐户提供的信息匹配。   若要查看或更新信息，请参阅[更改 Azure 帐户个人资料信息](change-azure-account-profile.md)。
    - 请在 Azure 门户中添加计费联系人信息，然后额度限制才会获得批准。 联系人详细信息应该与公司的应付账款或财务部门相关。 若要更新计费联系人信息，请转到 [Azure 帐户中心](https://account.azure.com/Profile)。

6. 验证联系人信息和首选联系方法，并选择“创建”  。

如果由于所需的信用额度，我们需要运行信用检查，我们会发送给你信用检查应用程序。

## <a name="switch-to-invoice-pay-checkwire-transfer"></a>切换到发票付款（支票/电汇）

获准按发票付款后，即可在 Azure 门户中切换到发票付款（支票/电汇）。

如果你有一个 Microsoft Online Services 计划帐户，可将 Azure 订阅切换为支票/电汇付款。 有了 Microsoft 客户协议，即可将计费对象信息切换为“支票/电汇”。 [了解如何检查帐户类型](#check-access-to-a-microsoft-customer-agreement)。

### <a name="switch-azure-subscription-to-checkwire-transfer"></a>将 Azure 订阅切换为支票/电汇

执行以下步骤，将 Azure 订阅切换为发票付款（支票/电汇）。 *切换为按发票付款（支票/电汇）后，无法切换回信用卡付款*。

1. 转到 [Azure 门户](https://portal.azure.com)，以帐户管理员身份登录。 搜索并选择“成本管理 + 计费”  。

    ![搜索成本管理和计费, Microsoft Azure 门户](./media/pay-by-invoice/search.png)

1. 选择要切换为发票付款的订阅。
1. 选择“付款方式”。 
1. 在命令栏中，选择“按发票付款”按钮。 

    ![“按发票付款”按钮、付款方式、Microsoft Azure 门户](./media/pay-by-invoice/pay-by-invoice.png)

### <a name="switch-billing-profile-to-checkwire-transfer"></a>将计费对象信息切换为支票/电汇

执行以下步骤，将计费对象信息切换为支票/电汇。 只有注册 Azure 的人才能更改计费对象信息的默认付款方式。

1. 转到 [Azure 门户](https://portal.azure.com)，查看计费信息。 搜索并选择“成本管理 + 计费”  。
1. 在菜单中，选择“计费对象信息”。 

    ![计费对象信息菜单项、成本管理和计费、Microsoft Azure 门户](./media/pay-by-invoice/billing-profile.png)

1. 选择计费对象信息。
1. 在“计费对象信息”  菜单中，选择“付款方式”  。

   ![付款方式菜单项、计费对象信息、成本管理、Microsoft Azure 门户](./media/pay-by-invoice/billing-profile-payment-methods.png)

1. 选择指出你有资格用支票/电汇付款的横幅。

    ![用于切换到支票/电汇的横幅、付款方式、Microsoft Azure 门户](./media/pay-by-invoice/customer-led-switch-to-invoice.png)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>检查对 Microsoft 客户协议的访问权限
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

- 根据需要在 [Azure 帐户中心](https://account.azure.com/Profile)更新计费联系人信息。
