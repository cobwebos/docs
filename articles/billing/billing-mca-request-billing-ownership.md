---
title: 获取计费 Azure 订阅的所有权
description: 了解如何从其他用户请求的 Azure 订阅的账单所有权。
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
ms.openlocfilehash: 4c9a8c866a157757121e6a9dd07a0a8559937c5e
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490856"
---
# <a name="get-billing-ownership-of-azure-subscriptions-from-other-accounts"></a>获取计费的其他帐户的 Azure 订阅的所有权

您可能想要接管的 Azure 订阅所有权，如果现有的账单所有者离开你的组织，或者你想要为通过您的计费帐户订阅付款。 取得所有权将订阅的计费职责移交给您的帐户。

本文适用于 Microsoft 客户协议向计费帐户。 [检查是否有权访问 Microsoft 客户协议](#check-for-access)。

若要请求的计费所有权，必须是**发票部分所有者**或**发票部分参与者**。 若要了解详细信息，请参阅[发票部分角色任务](billing-understand-mca-roles.md#invoice-section-roles-and-tasks)。

## <a name="request-billing-ownership"></a>请求计费所有权

1. 登录到[Azure 门户](http://portal.azure.com)为发票部分所有者或参与者的 Microsoft 客户协议的计费帐户。

2. 搜索**成本管理 + 计费**。

   ![显示有关成本管理 + 计费的 Azure 门户搜索的屏幕截图](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)

3. 选择**发票部分**从左上方。 具体取决于您的访问权限，可能需要选择计费帐户或帐单的配置文件。 从计费帐户或配置文件中选择**发票部分**。
   
   ![显示选择的屏幕截图发票部分](./media/billing-mca-request-billing-ownership/mca-select-invoice-sections.png)        

4. 从列表中选择发票部分。 后接管订阅的所有权时，它们将按此发票部分。

5. 选择**转移请求**从左下方，然后选择**添加**。
 
   ![屏幕截图，显示选择传输请求](./media/billing-mca-request-billing-ownership/mca-select-transfer-requests.png)

6. 输入正在请求从计费所有权的用户的电子邮件地址。 用户必须是 Microsoft 在线服务计划的计费帐户上的帐户管理员或企业协议的帐户所有者。 有关详细信息，请参阅[在 Azure 门户中查看计费帐户](billing-view-all-accounts.md)。 选择**发送传输请求**。

   ![显示发送传输请求的屏幕截图](./media/billing-mca-request-billing-ownership/mca-send-transfer-requests.png)

7. 用户会收到一封电子邮件的说明以查看你传输的请求。

   ![屏幕截图，显示查看传输请求电子邮件](./media/billing-mca-request-billing-ownership/mca-review-transfer-request-email.png)

8. 若要批准该传输请求，用户的电子邮件中选择的链接并按照的说明。

    ![屏幕截图，显示查看传输请求电子邮件](./media/billing-mca-request-billing-ownership/mca-review-transfer-request.png)

## <a name="check-the-transfer-request-status"></a>检查传输请求状态

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 搜索**成本管理 + 计费**。

   ![显示有关成本管理 + 计费的 Azure 门户搜索的屏幕截图](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)


3. 选择**发票部分**从左上方。 具体取决于您的访问权限，可能需要选择计费帐户或帐单的配置文件。 从计费帐户或配置文件中选择**发票部分**。
   
   ![显示选择的屏幕截图发票部分](./media/billing-mca-request-billing-ownership/mca-select-invoice-sections.png)        

4. 从为其发送传输请求列表中选择发票部分。

5. 选择**转移请求**从左下方。 传输请求页显示以下信息：

    ![显示传输请求的列表的屏幕截图](./media/billing-mca-request-billing-ownership/mca-select-transfer-requests-for-status.png)

   |列|定义|
   |---------|---------|
   |请求日期|发送传输请求的日期|
   |Recipient|发送转移计费所有权的请求用户的电子邮件地址|
   |到期日期|请求的到期日期|
   |状态|传输请求的状态|

    转移请求可以具有以下状态之一：

   |状态|定义|
   |---------|---------|
   |正在进行|在用户尚未接受转让请求|
   |Processing|用户批准该传输请求。 用户选择的订阅的计费被传输到发票部分|
   |已完成| 用户选择的订阅的计费将传输到发票部分|
   |已完成但出现错误|无法传输请求已完成但在用户选择了某些订阅的计费|
   |已过期|用户未在时间上接受请求，它已过期|
   |已取消|人有权访问该传输请求已取消请求|
   |已拒绝|用户拒绝了传输请求|

7. 选择传输请求，以查看详细信息。 传输的详细信息页显示以下信息：
   
   ![显示已转移订阅的列表的屏幕截图](./media/billing-mca-request-billing-ownership/mca-transfer-completed.png)
    
   |列  |定义|
   |---------|---------|
   |传输请求 ID|在传输请求的唯一 ID。 如果你提交支持请求，ID 与 Azure 支持共享加快你的支持请求|
   |请求转移的日期|发送传输请求的日期|
   |传输请求|发送传输请求的用户电子邮件地址|
   |传输请求的截止日期| 转移请求过期时的日期|
   |收件人的电子邮件地址|发送转移计费所有权的请求用户的电子邮件地址|
   |传输发送给接收方链接|已发送给用户以查看传输请求的 url|

## <a name="supported-subscription-types"></a>支持的订阅类型

你可以请求下面列出的订阅类型的计费所有权。

- [操作包](https://azure.microsoft.com/offers/ms-azr-0025p/)\* 
- [Azure 开放许可](https://azure.microsoft.com/offers/ms-azr-0111p/)\*
- [Azure Pass 赞助](https://azure.microsoft.com/offers/azure-pass/)\*
- [Enterprise 开发/测试](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [免费试用版](https://azure.microsoft.com/offers/ms-azr-0044p/)\*
- [即用即付](https://azure.microsoft.com/offers/ms-azr-0003p/) 
- [即用即付开发/测试](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Microsoft Azure Plan](https://azure.microsoft.com/offers/ms-azr-0017g/)\*\*
- [Microsoft Azure 赞助产品/服务](https://azure.microsoft.com/offers/ms-azr-0036p/)\*
- [Microsoft 企业协议](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Microsoft 合作伙伴网络](https://azure.microsoft.com/offers/ms-azr-0025p/)\*
- [MSDN 平台](https://azure.microsoft.com/offers/ms-azr-0062p/)\*
- [Visual Studio Enterprise (BizSpark) 订户](https://azure.microsoft.com/offers/ms-azr-0064p/)\*
- [Visual Studio Enterprise (MPN) 订户](https://azure.microsoft.com/offers/ms-azr-0029p/)\*
- [Visual Studio Enterprise 订阅者](https://azure.microsoft.com/offers/ms-azr-0063p/)\*
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)\*
- [Visual Studio Test Professional 订户](https://azure.microsoft.com/offers/ms-azr-0060p/)\*

\* 订阅上的任何信用额度在转移后将不可用新帐户中。

\*\* 仅支持过程中创建的帐户中的订阅在 Azure 网站上注册。


## <a name="additional-information"></a>其他信息

以下部分提供有关转让订阅的其他信息。

### <a name="no-service-downtime"></a>服务不会中断

在订阅中的 azure 服务不间断地继续运行。 我们仅转换用户选择要传输的 Azure 订阅的计费关系。

### <a name="disabled-subscriptions"></a>已禁用的订阅

无法传输已禁用的订阅。 订阅必须处于活动状态，将其记帐所有权转让。

### <a name="azure-resources-transfer"></a>Azure 资源传输

Vm、 磁盘和网站传输等订阅中的所有资源。

### <a name="azure-marketplace-products-transfer"></a>Azure Marketplace 产品传输

Azure Marketplace 产品传输以及其各自的订阅。

### <a name="azure-reservations-transfer"></a>Azure 保留传输

Azure 保留不自动移动订阅。 [请联系 Azure 支持部门](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)移动预订。

### <a name="access-to-azure-services"></a>访问 Azure 服务

使用 (Azure RBAC （基于角色的访问控制）) 分配为现有用户、 组或服务主体访问 [...在转换期间不受影响 /role-based-access-control/overview.md]。

### <a name="azure-support-plan"></a>Azure 支持计划

Azure 支持不传输的订阅。 如果用户要传输的所有 Azure 订阅，要求他们取消其支持计划。

### <a name="charges-for-transferred-subscription"></a>传输订阅的费用

订阅计费的原始所有者负责直到完成传输中未报告任何费用。 发票部分负责所报告的传输及更高版本的费用。 可能有一些转让之前发生，但之后已报告的费用。 这些费用显示在发票部分中。

### <a name="cancel-a-transfer-request"></a>取消转让请求

批准或拒绝该请求之前，您可以取消传输请求。 若要取消转让请求，请转到[转移详细信息页](#check-the-transfer-request-status)和选择从页面底部的取消。

### <a name="software-as-a-service-saas-transfer"></a>软件即服务 (SaaS) 传输

SaaS 产品的订阅无法转移。 要求对用户[Azure 支持部门联系](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)要传输的 SaaS 产品的账单所有权。 账单所有权，以及用户还可以传输资源的所有权。 资源的所有权，可以执行管理操作，例如删除和查看产品的详细信息。 用户必须是 SaaS 产品的资源所有者将资源的所有权。

## <a name="check-for-access"></a>访问权限的检查
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员

如果需要帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。

## <a name="next-steps"></a>后续步骤

- Azure 订阅的计费所有权将转移到发票部分。 跟踪的这些订阅中的费用[Azure 门户](https://portal.azure.com)。
- 授予其他人有权查看和管理这些订阅的计费。 有关详细信息，请参阅[发票部分角色和任务](billing-understand-mca-roles.md#invoice-section-roles-and-tasks)。
