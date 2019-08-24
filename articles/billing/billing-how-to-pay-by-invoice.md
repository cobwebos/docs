---
title: 按发票支付 Azure 订阅费用
description: 描述如何按发票为 Azure 订阅付款。
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/23/2019
ms.author: banders
ms.openlocfilehash: 9e4e05acd88e9b0f0c17d4dd4caf5eb5a883d63d
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2019
ms.locfileid: "70012598"
---
# <a name="pay-for-your-azure-subscription-by-invoice"></a>按发票为你的 Azure 订阅付费

如果切换到 "按发票付款", 这意味着你将在发票日期的30天内按支票/无线传输来支付帐单。 若要通过发票支付 Azure 订阅的资格, 请向 Azure 支持提交请求。 批准你的请求后, 你可以在[Azure 门户](https://portal.azure.com)中切换到发票支付 (支票/电汇)。

> [!IMPORTANT]
> * 发票支付 (支票/电汇) 仅适用于企业帐户。
> * 在切换到发票支付之前支付所有未付费用。
> * 目前, 发票支付不支持全局中国区 Azure。

## <a name="request-to-pay-by-invoice"></a>请求按发票付款

1. 登录到 [Azure 门户](https://portal.azure.com/)。 选择“帮助+支持” > “新建支持请求”。

    !["帮助和支持" 链接](./media/billing-how-to-pay-by-invoice/help-and-support.png)

2. 选择 "**计费**" 作为 "**问题类型**"。 *问题类型*是支持请求类别。 选择要按发票付款的订阅, 选择支持计划, 然后选择 "**下一步**"。

3. 选择 "**支付**" 作为**问题类型**。 *问题类型*是支持请求子类别。

4. 选择 "**切换为按发票付款**" 作为**问题子类型**

5. 在“详细信息”框中输入以下信息，并选择“下一步”。

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

    - **公司名称**和**公司地址**应与你为 Azure 帐户提供的信息匹配。 若要查看或更新信息, 请参阅[更改 Azure 帐户配置文件信息](billing-how-to-change-azure-account-profile.md)。
    - 在 Azure 门户中添加帐单联系信息, 然后才能批准信用额度。 联系详细信息应与公司的应付帐款或财务部门相关。 若要更新计费联系信息, 请访问[Azure 帐户中心](https://account.azure.com/Profile)。

6. 验证联系人信息和首选联系方法，并单击“创建”。

如果需要运行信用检查, 因为需要支付多少信用额度, 我们将向你发送信用检查应用程序。

## <a name="switch-to-invoice-pay-checkwire-transfer"></a>切换到发票支付 (支票/无线传输)

一旦批准通过发票支付, 就可以切换到 Azure 门户中的发票支付 (支票/电汇)。

如果有 Microsoft Online Services 程序帐户, 可以将 Azure 订阅切换为 "检查/传输"。 使用 Microsoft 客户协议, 你可以将计费配置文件切换到支票/有线传输。 [了解如何检查你的帐户类型](#check-access-to-a-microsoft-customer-agreement)。

### <a name="switch-azure-subscription-to-checkwire-transfer"></a>将 Azure 订阅切换到支票/无线传输

按照以下步骤将 Azure 订阅切换到发票支付 (支票/电汇)。 **切换到发票支付 (支票/电汇) 后, 无法切换回信用卡**。

1. 以帐户管理员身份登录到 [Azure 门户](https://portal.azure.com)。
1. 搜索 "**成本管理 + 计费**"。

    ![显示搜索的屏幕截图](./media/billing-how-to-pay-by-invoice/search.png)

1. 选择要切换到发票付款的订阅。
1. 选择“付款方式”。
1. 在命令栏中, 单击 "**按发票付款**" 按钮。

    ![显示 "按发票付款" 按钮的屏幕截图](./media/billing-how-to-pay-by-invoice/pay-by-invoice.png)

### <a name="switch-billing-profile-to-checkwire-transfer"></a>将计费配置文件切换到支票/无线传输

请按照以下步骤将计费配置文件切换到支票/有线传输。 只有注册 Azure 的人员才能更改计费配置文件的默认付款方式。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在“成本管理 + 计费”中进行搜索。
1. 在左侧菜单中, 单击 "**计费配置文件**"。

    ![显示菜单中的计费配置文件的屏幕截图](./media/billing-how-to-pay-by-invoice/billing-profile.png)

1. 选择计费配置文件。
1. 在左侧菜单中, 选择 "**付款方式**"。

   ![显示菜单中付款方式的屏幕截图](./media/billing-how-to-pay-by-invoice/billing-profile-payment-methods.png)

1. 单击蓝色横幅, 告诉您有资格按支票/电汇方式支付。

    ![显示蓝色横幅以切换到支票/有线的屏幕截图](./media/billing-how-to-pay-by-invoice/customer-led-switch-to-invoice.png)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>检查对 Microsoft 客户协议的访问权限
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如果你有疑问或需要帮助, 请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

- 如果需要, 请在[Azure 帐户中心](https://account.azure.com/Profile)更新帐单联系信息。
