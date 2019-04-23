---
title: 用发票为 Azure 订阅付款 | Microsoft Docs
description: 说明如何用发票为 Azure 订阅付款
services: ''
documentationcenter: ''
author: genlin
manager: jureid
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: banders
ms.openlocfilehash: 0a17821cc69900c7f24375cbf06a61811bbd52d1
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "59786958"
---
# <a name="submit-a-request-to-pay-azure-subscription-by-invoice"></a>提交用发票为 Azure 订阅付款的请求

可以通过向 Azure 支持提交请求将 Azure 订阅的付款方法更改为发票。 请求获批以后，即可获得有关如何针对发票付款方式设置订阅的说明。

> [!IMPORTANT]
> * 转账付款仅适用于公司帐户。
> * 使用转账付款无法购买或支付[第三方和外部服务](billing-understand-your-azure-marketplace-charges.md)。 如果订阅包含来自 SendGrid 等外部服务的资源，则需要在更改为转账付款之前将其删除。 若要在切换为转账付款后购买外部服务，需要使用信用卡或借记卡的单独订阅。
> * 切换为转账付款后，无法切换回信用卡或借记卡付款。

## <a name="request-pay-by-invoice"></a>请求使用转账付款

1. 登录到 [Azure 门户](https://portal.azure.com/)。 选择“帮助+支持” > “新建支持请求”。

    ![“帮助和支持”按钮](./media/billing-how-to-pay-by-invoice/helpandsupport.png)

2. 选择“计费”作为问题类型，选择要用发票付款的订阅，选择支持计划，并选择“下一步”。

3. 在“问题类型”框中选择“通过发票付款”。

 4. 在“详细信息”框中输入以下信息，并选择“下一步”。

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
 
    - **公司名称**并**公司地址**应与提供的 Azure 帐户的信息匹配。  若要查看或更新的信息，请参阅[更改你的 Azure 帐户个人资料信息](billing-how-to-change-azure-account-profile.md)。
    - 可以批准信用额度之前，必须在 Azure 门户中添加计算联系人。 应与公司的 Accounts Payable 或财务部门相关的联系人详细信息。 若要更新计费联系人，请转到[Azure 帐户中心](https://account.azure.com/Profile)。

5. 验证联系人信息和首选联系方法，并单击“创建”。

如果由于所需的信用额度，我们需要运行信用检查，我们会发送给你信用检查应用程序。 提交该应用程序后，信用应用程序可能需要 5-7 天的时间进行处理。

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如果有疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。