---
title: 获取计费的 Azure 订阅的所有权 |Microsoft Docs
description: 了解如何从其他用户请求的 Azure 订阅的账单所有权。
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/28/2019
ms.author: banders
ms.openlocfilehash: be8c7fcebca224196d9eac7d22387989b1bdfd46
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60371873"
---
# <a name="get-billing-ownership-of-azure-subscriptions-from-other-users"></a>获取计费的其他用户的 Azure 订阅的所有权

您可能想要获取 Azure 订阅的所有权，如果现有的账单所有者离开你的组织，或者你想要为通过您的计费帐户订阅付款。

可以发送请求以从其他计费帐户中的现有所有者需要 Azure 订阅的所有权。 取得所有权将订阅的计费职责移交给发票部分。

若要请求的计费所有权，必须是**发票部分所有者**或**发票部分参与者**。 若要了解详细信息，请参阅[发票部分角色任务](billing-understand-mca-roles.md#invoice-section-roles-and-tasks)。

本文适用于 Microsoft 客户协议向计费帐户。 [检查是否有权访问 Microsoft 客户协议](#check-access-to-a-microsoft-customer-agreement)。

## <a name="request-billing-ownership-in-the-azure-portal"></a>请求在 Azure 门户中的计费所有权

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 在“成本管理 + 计费”  中进行搜索。

   ![显示有关成本管理 + 计费的 Azure 门户搜索的屏幕截图](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)

3. 转到发票部分。 具体取决于您的访问权限，可能需要选择计费帐户或帐单的配置文件。 从计费帐户或配置文件中选择**发票部分**，然后发票部分。
   <!-- Todo - Add a screenshot -->

4. 选择**转移请求**从左下方。

5. 在页面顶部选择“添加”。 

6. 输入正在请求从计费所有权的用户的电子邮件地址。 用户必须是 Microsoft 在线服务计划的计费帐户的帐户管理员或企业协议的帐户所有者。 有关详细信息，请参阅[在 Azure 门户中查看计费帐户](billing-view-all-accounts.md)。

   ![显示添加新的转移请求的屏幕截图](./media/billing-mca-request-billing-ownership/mca-new-transfer-request.png)

7. 选择**发送传输请求**。

8. 用户会收到一封电子邮件的说明以查看你传输的请求。

   ![屏幕截图，显示查看传输请求电子邮件](./media/billing-mca-request-billing-ownership/mca-review-transfer-request-email.png)

9. 若要批准该传输请求，用户的电子邮件中选择的链接并按照的说明。

    ![屏幕截图，显示查看传输请求电子邮件](./media/billing-mca-request-billing-ownership/mca-review-transfer-request.png)

## <a name="check-the-status-of-your-transfer-request-in-the-azure-portal"></a>检查你在 Azure 门户中的传输请求的状态

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 在“成本管理 + 计费”  中进行搜索。

   ![显示有关成本管理 + 计费的 Azure 门户搜索的屏幕截图](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)

3. 转到发票部分。 具体取决于您的访问权限，可能需要选择计费帐户或帐单的配置文件。 从计费帐户或配置文件中选择**发票部分**，然后发票部分。
   <!-- Todo - Add a screenshot -->

4. 选择**转移请求**从左下方。

5. 传输请求页显示以下信息：

    ![显示传输请求的列表的屏幕截图](./media/billing-mca-request-billing-ownership/mca-view-transfer-requests.png)

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

6. 选择传输请求，以查看详细信息。 传输的详细信息页显示以下信息：
   <!-- Todo - Add a screenshot -->

   |列  |定义|
   |---------|---------|
   |传输请求 ID|在传输请求的唯一 ID。 如果你提交支持请求，ID 与 Azure 支持共享加快你的支持请求|
   |请求转移的日期|发送传输请求的日期|
   |传输请求|发送传输请求的用户电子邮件地址|
   |传输请求的截止日期| 转移请求过期时的日期|
   |收件人的电子邮件地址|发送转移计费所有权的请求用户的电子邮件地址|
   |传输发送给接收方链接|已发送给用户以查看传输请求的 url|

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

使用 Azure RBAC （基于角色的访问控制） 设置到 Azure 资源的访问权限在转换期间不受影响。

### <a name="azure-support-plan"></a>Azure 支持计划

Azure 支持不传输的订阅。 如果用户要传输的所有 Azure 订阅，要求他们取消其支持计划。

### <a name="charges-for-transferred-subscription"></a>传输订阅的费用

订阅计费的原始所有者负责直到完成传输中未报告任何费用。 发票部分负责所报告的传输及更高版本的费用。 可能有一些转让之前发生，但之后已报告的费用。 这些费用显示在发票部分中。

### <a name="supported-offers"></a>受支持的产品/服务

可以传输任何类型或产品/服务，但 CSP 提供的订阅。

### <a name="cancel-a-transfer-request"></a>取消转让请求

批准或拒绝该请求之前，您可以取消传输请求。 若要取消转让请求，请转到传输的详细信息页并选择取消从页面底部。

### <a name="software-as-a-service-saas-transfer"></a>软件即服务 (SaaS) 传输

SaaS 产品的订阅无法转移。 要求对用户[Azure 支持部门联系](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)要传输的 SaaS 产品的账单所有权。 账单所有权，以及用户还可以传输资源的所有权。 资源的所有权，可以执行管理操作，例如删除和查看产品的详细信息。 用户必须是 SaaS 产品的资源所有者将资源的所有权。

## <a name="check-access-to-a-microsoft-customer-agreement"></a>检查与 Microsoft 客户协议的访问权限
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员

如果需要帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。

## <a name="next-steps"></a>后续步骤

- Azure 订阅的计费所有权将转移到发票部分。 跟踪的这些订阅中的费用[Azure 门户](https://portal.azure.com)。
- 授予其他人有权查看和管理这些订阅的计费。 有关详细信息，请参阅[发票部分角色和任务](billing-understand-mca-roles.md#invoice-section-roles-and-tasks)。
