---
title: 通过发票支付 Azure 订阅
description: 介绍如何通过发票支付 Azure 订阅。
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 9ca726ef737ce4750018d2461bc4bcd6c7ebb5f5
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491213"
---
# <a name="pay-for-your-azure-subscription-by-invoice"></a>通过发票支付 Azure 订阅

如果切换到用发票付款，这意味着通过检查/电子转账在发票日期的 30 天内支付帐单。 若要成为可通过发票支付 Azure 订阅，提交给 Azure 支持请求。 你的请求经过审批后，您可以切换为转账付款 （检查/电子转账） 中[Azure 门户](https://portal.azure.com)。

> [!IMPORTANT]
> * 转账付款 （检查/网络传输） 是仅适用于公司帐户。
> * 切换为转账付款之前，必须支付所有未付费用。

## <a name="request-to-pay-by-invoice"></a>请求通过发票付款

1. 登录到 [Azure 门户](https://portal.azure.com/)。 选择“帮助+支持”   > “新建支持请求”  。

    ![帮助和支持链接](./media/billing-how-to-pay-by-invoice/help-and-support.png)

2. 选择**计费**作为**问题类型**。 *问题类型*是支持请求类别。 选择你想要用发票付款，选择支持计划，并选择的订阅**下一步**。

3. 在“问题类型”框中选择“通过发票付款”   。 *问题类型*是支持请求子类别。

4. 在“详细信息”  框中输入以下信息，并选择“下一步”  。

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

    - **公司名称**并**公司地址**应匹配提供的 Azure 帐户的信息。 若要查看或更新的信息，请参阅[更改你的 Azure 帐户个人资料信息](billing-how-to-change-azure-account-profile.md)。
    - 可以批准信用额度之前，必须在 Azure 门户中添加你计费的联系信息。 应与公司的 Accounts Payable 或财务部门相关的联系人详细信息。 若要更新的计费的联系信息，请转到[Azure 帐户中心](https://account.azure.com/Profile)。

5. 验证联系人信息和首选联系方法，并单击“创建”  。

如果我们需要以运行信用检查，因为的所需的信用额度，我们会将你信用检查应用程序。

## <a name="switch-to-invoice-pay-checkwire-transfer"></a>切换为转账付款 （检查/网络传输）

一旦获得批准通过发票付款时，可以切换为转账付款 （检查/电子转账） 在 Azure 门户中。

如果你有 Microsoft Online Services 计划帐户，则可以切换 Azure 订阅，检查/网络传输。 如果你有 Microsoft 客户协议，则可以切换计费配置文件以检查/网络传输。 [了解如何查看你的帐户类型](#check-access-to-a-microsoft-customer-agreement)。

### <a name="switch-azure-subscription-to-checkwire-transfer"></a>若要检查/网络传输的 Azure 订阅切换

请按照以下步骤来切换为转账付款 （检查/电子转账） 在 Azure 订阅。 **一旦切换为转账付款 （检查/电子转账） 就无法切换回信用卡**。

1. 以帐户管理员身份登录到 [Azure 门户](https://portal.azure.com)。
1. 搜索**成本管理 + 计费**。

    ![显示搜索的屏幕截图](./media/billing-how-to-pay-by-invoice/search.png)

1. 选择你想要切换为转账付款的订阅。
1. 选择“付款方式”。 
1. 在命令栏中，单击**用发票付款**按钮。

    ![按发票按钮显示的屏幕截图](./media/billing-how-to-pay-by-invoice/pay-by-invoice.png)

### <a name="switch-billing-profile-to-checkwire-transfer"></a>切换计费要检查/网络传输的配置文件

请按照以下步骤来切换计费要检查/网络传输的配置文件。 请注意，仅注册了 Azure 的人员可以更改计费的配置文件的默认付款方式。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在“成本管理 + 计费”  中进行搜索。
1. 在左侧菜单中，单击**计费配置文件**。

    ![在菜单中显示计费的配置文件的屏幕截图](./media/billing-how-to-pay-by-invoice/billing-profile.png)

1. 选择计费的配置文件。
1. 在左侧菜单中，选择**付款方式**。

   ![在菜单中显示支付方法的屏幕截图](./media/billing-how-to-pay-by-invoice/billing-profile-payment-methods.png)

1. 单击蓝色横幅，指出你有资格通过检查/电子转账付款。

    ![显示蓝色横幅以切换到检查/网络的屏幕截图](./media/billing-how-to-pay-by-invoice/customer-led-switch-to-invoice.png)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>检查与 Microsoft 客户协议的访问权限
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如果有疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

- 如果需要更新在你计费的联系信息[Azure 帐户中心](https://account.azure.com/Profile)。
