---
title: 获取 Azure 订阅的帐单所有权
description: 了解如何从其他用户请求 Azure 订阅的计费所有权。
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 110bd975de1d865c2aa3d3b088c98cdc8b9e45ad
ms.sourcegitcommit: b12a25fc93559820cd9c925f9d0766d6a8963703
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2019
ms.locfileid: "69019558"
---
# <a name="get-billing-ownership-of-azure-subscriptions-from-other-accounts"></a>获取来自其他帐户的 Azure 订阅的帐单所有权

如果现有计费所有者离开你的组织, 或者你希望通过计费帐户为订阅付费, 则你可能需要接管 Azure 订阅的所有权。 获取所有权会将订阅的计费责任转移到你的帐户。

本文适用于 Microsoft 客户协议的计费帐户。 [检查你是否有权访问 Microsoft 客户协议](#check-for-access)。

若要请求计费所有权, 必须是**发票部分所有者**或**发票部分参与者**。 若要了解详细信息, 请参阅[发票部分角色任务](billing-understand-mca-roles.md#invoice-section-roles-and-tasks)。

## <a name="request-billing-ownership"></a>请求计费所有权

1. 以 Microsoft 客户协议计费帐户的发票部分所有者或参与者身份登录到[Azure 门户](https://portal.azure.com)。

2. 搜索 "**成本管理 + 计费**"。

   ![显示成本管理 + 计费 Azure 门户搜索的屏幕截图](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)

3. 选择左侧的 "**发票" 部分**。 根据你的访问权限, 可能需要选择计费帐户或计费配置文件。 从计费帐户或配置文件中, 选择 "**发票部分**"。
   
   ![显示选择发票部分的屏幕截图](./media/billing-mca-request-billing-ownership/mca-select-invoice-sections.png)        

4. 从列表中选择一个发票部分。 一旦你接管订阅的所有权, 就会向此发票部分收费。

5. 选择左下角的 "**传输请求**", 然后选择 "**添加**"。
 
   ![显示选择传输请求的屏幕截图](./media/billing-mca-request-billing-ownership/mca-select-transfer-requests.png)

6. 输入你请求从中计费所有权的用户的电子邮件地址。 用户必须是 Microsoft Online Service Program 计费帐户的帐户管理员或企业协议上的帐户所有者。 有关详细信息, 请参阅[在 Azure 门户中查看计费帐户](billing-view-all-accounts.md)。 选择 "**发送传输请求**"。

   ![显示发送传输请求的屏幕截图](./media/billing-mca-request-billing-ownership/mca-send-transfer-requests.png)

7. 用户将收到一封电子邮件, 其中包含查看传输请求的说明。

   ![显示审阅传输请求电子邮件的屏幕截图](./media/billing-mca-request-billing-ownership/mca-review-transfer-request-email.png)

8. 若要批准传输请求, 用户需要选择电子邮件中的链接, 然后按照说明进行操作。

    ![显示审阅传输请求电子邮件的屏幕截图](./media/billing-mca-request-billing-ownership/mca-review-transfer-request.png)

## <a name="check-the-transfer-request-status"></a>检查传输请求状态

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 搜索 "**成本管理 + 计费**"。

   ![显示成本管理 + 计费 Azure 门户搜索的屏幕截图](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)


3. 选择左侧的 "**发票" 部分**。 根据你的访问权限, 可能需要选择计费帐户或计费配置文件。 从计费帐户或配置文件中, 选择 "**发票部分**"。
   
   ![显示选择发票部分的屏幕截图](./media/billing-mca-request-billing-ownership/mca-select-invoice-sections.png)        

4. 从已向其发送传输请求的列表中选择 "发票" 部分。

5. 选择左下角的 "**传输请求**"。 "传输请求" 页显示以下信息:

    ![显示传输请求列表的屏幕截图](./media/billing-mca-request-billing-ownership/mca-select-transfer-requests-for-status.png)

   |柱形图|定义|
   |---------|---------|
   |请求日期|发送传输请求的日期|
   |收件人|向其发送计费所有权请求的用户的电子邮件地址|
   |到期日期|请求过期的日期|
   |状态|传输请求的状态|

    传输请求可以具有以下状态之一:

   |状态|定义|
   |---------|---------|
   |进行中|用户未接受传输请求|
   |正在处理|用户已批准传输请求。 对用户选择的订阅进行计费将转移到你的发票部分|
   |已完成| 将用户选择的订阅的计费转移到发票部分|
   |已完成, 但出现错误|请求已完成, 但无法传输用户选择的某些订阅的计费|
   |已到期|用户在时间上未接受请求, 已过期|
   |已取消|有权访问传输请求的某人取消了请求|
   |已拒绝|用户拒绝了传输请求|

7. 选择一个传输请求以查看详细信息。 "传输详细信息" 页显示以下信息:
   
   ![显示已传输订阅列表的屏幕截图](./media/billing-mca-request-billing-ownership/mca-transfer-completed.png)
    
   |柱形图  |定义|
   |---------|---------|
   |传输请求 ID|传输请求的唯一 ID。 如果提交支持请求, 请与 Azure 支持部门共享 ID 以加速你的支持请求|
   |已请求传输|发送传输请求的日期|
   |请求的传输|发送传输请求的用户的电子邮件地址|
   |传输请求已过期| 传输请求到期的日期|
   |收件人的电子邮件地址|向其发送计费所有权请求的用户的电子邮件地址|
   |发送给收件人的传输链接|发送给用户以查看传输请求的 url|

## <a name="supported-subscription-types"></a>支持的订阅类型

你可以请求下面列出的订阅类型的计费所有权。

- [操作包](https://azure.microsoft.com/offers/ms-azr-0025p/)\* 
- [Azure 开放式许可](https://azure.microsoft.com/offers/ms-azr-0111p/)\*
- [Azure Pass 赞助](https://azure.microsoft.com/offers/azure-pass/)\*
- [Enterprise 开发/测试](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [免费试用版](https://azure.microsoft.com/offers/ms-azr-0044p/)\*
- [即用即付](https://azure.microsoft.com/offers/ms-azr-0003p/) 
- [即用即付开发/测试](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Microsoft Azure 计划](https://azure.microsoft.com/offers/ms-azr-0017g/)\*\*
- [Microsoft Azure 赞助提议](https://azure.microsoft.com/offers/ms-azr-0036p/)\*
- [Microsoft 企业协议](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Microsoft 合作伙伴网络](https://azure.microsoft.com/offers/ms-azr-0025p/)\*
- [MSDN 平台](https://azure.microsoft.com/offers/ms-azr-0062p/)\*
- [Visual Studio Enterprise (BizSpark) 订阅服务器](https://azure.microsoft.com/offers/ms-azr-0064p/)\*
- [Visual Studio Enterprise (MPN) 订阅服务器](https://azure.microsoft.com/offers/ms-azr-0029p/)\*
- [Visual Studio Enterprise 订户](https://azure.microsoft.com/offers/ms-azr-0063p/)\*
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)\*
- [Visual Studio Test Professional 订户](https://azure.microsoft.com/offers/ms-azr-0060p/)\*

\*传输后, 在新帐户中将无法使用订阅上的任何信用额度。

\*\*仅支持在 Azure 网站上注册过程中创建的帐户中的订阅。


## <a name="additional-information"></a>其他信息

以下部分提供有关传输订阅的其他信息。

### <a name="no-service-downtime"></a>服务不会中断

订阅中的 Azure 服务将继续运行, 而不会发生任何中断。 我们只会转换用户选择传输的 Azure 订阅的计费关系。

### <a name="disabled-subscriptions"></a>已禁用订阅

无法传输禁用的订阅。 订阅必须处于活动状态才能传输其计费所有权。

### <a name="azure-resources-transfer"></a>Azure 资源传输

订阅中的所有资源, 例如 Vm、磁盘和网站传输。

### <a name="azure-marketplace-products-transfer"></a>Azure Marketplace 产品转让

Azure Marketplace 产品连同各自的订阅一起传输。

### <a name="azure-reservations-transfer"></a>Azure 预订转让

Azure 预订不会随订阅自动移动。 [联系 Azure 支持](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)部门以移动预订。

### <a name="access-to-azure-services"></a>访问 Azure 服务

使用指定的现有用户、组或服务主体的访问权限 (Azure RBAC (基于角色的访问控制)) [。/role-based-access-control/overview.md] 在转换期间不受影响。

### <a name="azure-support-plan"></a>Azure 支持计划

Azure 支持不会与订阅一起传输。 如果用户传输所有 Azure 订阅, 请要求他们取消其支持计划。

### <a name="charges-for-transferred-subscription"></a>传输的订阅费用

订阅的原始计费所有者负责在传输完成后所报告的任何费用。 您的 "发票" 部分负责从上传时间报告的费用。 在传输之前可能会出现一些费用, 但随后会报告。 这些费用显示在发票部分。

### <a name="cancel-a-transfer-request"></a>取消传输请求

在批准或拒绝请求之前, 你可以取消该请求。 若要取消传输请求, 请转到[传输详细信息页](#check-the-transfer-request-status), 并从页面底部选择 "取消"。

### <a name="software-as-a-service-saas-transfer"></a>软件即服务 (SaaS) 传输

SaaS 产品不会与订阅一起传输。 要求用户[联系 Azure 支持](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)部门, 以转让 SaaS 产品的计费所有权。 除了计费所有权以外, 用户还可以转移资源所有权。 资源所有权允许执行管理操作, 例如删除和查看产品的详细信息。 用户必须是 SaaS 产品上的资源所有者才能传输资源所有权。

## <a name="check-for-access"></a>检查访问权限
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>需要帮助? 联系技术支持

如果需要帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。

## <a name="next-steps"></a>后续步骤

- Azure 订阅的计费所有权将转移到你的发票部分。 在[Azure 门户](https://portal.azure.com)中跟踪这些订阅的费用。
- 向他人提供查看和管理这些订阅的计费的权限。 有关详细信息, 请参阅[发票部分角色和任务](billing-understand-mca-roles.md#invoice-section-roles-and-tasks)。
