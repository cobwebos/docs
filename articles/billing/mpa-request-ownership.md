---
title: 获取 Microsoft 合作伙伴协议 (MPA) 的 Azure 订阅的计费所有权
description: 了解如何从其他用户请求 Azure 订阅的计费所有权。
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2019
ms.author: banders
ms.openlocfilehash: d68d95619b6aa6e8098c282977866c4bc21a357c
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/27/2019
ms.locfileid: "74561057"
---
# <a name="get-billing-ownership-of-azure-subscriptions-to-your-mpa-account"></a>获取 MPA 帐户的 Azure 订阅的计费所有权

若要针对托管服务和 Azure 使用情况提供单一合并发票，云解决方案提供商 (CSP) 可通过直接企业协议 (EA) 从客户处接管 Azure 订阅的计费所有权。

此功能仅适用于以 [Azure 专家 MSP](https://partner.microsoft.com/membership/azure-expert-msp) 形式认证的 CSP 直接计费合作伙伴。 它遵循 Microsoft 的治理和策略规定，某些客户可能需要评审和批准。

若要请求计费所有权，你必须拥有“全局管理员”或“管理员代理”角色。   若要了解详细信息，请参阅[合作伙伴中心 - 为用户分配角色和权限](https://docs.microsoft.com/partner-center/permissions-overview)。

本文适用于 Microsoft 合作伙伴协议的计费帐户。 这些帐户是为云解决方案提供商 (CSP) 创建的，用于在新的商业体验中管理其客户的计费。 新体验仅适用于特定的合作伙伴，他们至少有一个接受 Microsoft 客户协议 (MCA) 且有 Azure 计划的客户。 [检查你是否有权访问 Microsoft 合作伙伴协议](#check-access-to-a-microsoft-partner-agreement)。

## <a name="prerequisites"></a>先决条件

1. 与客户建立[经销商关系](https://docs.microsoft.com/partner-center/request-a-relationship-with-a-customer)。 查看 [CSP 区域授权概述](https://docs.microsoft.com/partner-center/regional-authorization-overview)，确保客户和合作伙伴租户在同一个获得授权的区域内。  

2. [确认客户已接受 Microsoft 客户协议](https://docs.microsoft.com/partner-center/confirm-customer-agreement)。

3. 为客户设置 [Azure 计划](https://docs.microsoft.com/partner-center/purchase-azure-plan)。 如果客户通过多个经销商进行购买，则需为客户和经销商的每种组合设置 Azure 计划。

## <a name="request-billing-ownership"></a>请求计费所有权

1. 使用 CSP 管理员代理凭据登录到 [Azure 门户](https://portal.azure.com)。

2. 搜索“成本管理 + 计费”。 

   ![显示如何在 Azure 门户中搜索“成本管理 + 计费”的屏幕截图](./media/mpa-request-ownership/search-cmb.png)

3. 在左侧选择“客户”，然后从列表中选择一位客户。 
   
   ![屏幕截图，显示正在选择客户](./media/mpa-request-ownership/mpa-select-customers.png)        

4. 选择左下角的“转移请求”，然后选择“添加新请求”。  
 
   ![显示选择转移请求的屏幕截图](./media/mpa-request-ownership/mpa-select-transfer-requests.png)

5. 输入客户组织中将要接受转移请求的用户的电子邮件地址。 此用户必须是企业协议中的帐户所有者。 选择“发送转移请求”。 

   ![显示发送转移请求的屏幕截图](./media/mpa-request-ownership/mpa-send-transfer-requests.png)

6. 用户将收到一封电子邮件，其中包含查看转让请求的说明。

   ![显示查看转移请求电子邮件的屏幕截图](./media/mpa-request-ownership/mpa-review-transfer-request-email.png)

7. 若要批准转移请求，用户需选择电子邮件中的链接，并按说明操作。

    ![显示查看转移请求电子邮件的屏幕截图](./media/mpa-request-ownership/mpa-review-transfer-request.png)

## <a name="check-the-transfer-request-status"></a>检查转移请求状态

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 搜索“成本管理 + 计费”。 

   ![显示如何在 Azure 门户中搜索“成本管理 + 计费”的屏幕截图](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)

3. 在左侧选择“客户”。 

   ![屏幕截图，显示正在选择客户](./media/mpa-request-ownership/mpa-select-customers.png)

4. 从列表中选择已为其发送转移请求的客户。

5. 选择左下角的“转移请求”。  “转移请求”页将显示以下信息：

    ![显示转移请求列表的屏幕截图](./media/mpa-request-ownership/mpa-select-transfer-requests-for-status.png)

   |列|定义|
   |---------|---------|
   |请求日期|发送转移请求的日期|
   |Recipient|已向其发送转移计费所有权请求的用户的电子邮件地址|
   |到期日期|请求过期的日期|
   |状态|转移请求的状态|

    转移请求可能处于以下状态之一：

   |状态|定义|
   |---------|---------|
   |正在进行|用户未接受转移请求|
   |Processing|用户已批准转移请求。 用户选择的订阅的计费正在转移到你的帐户|
   |已完成| 用户选择的订阅的计费正在转移到你的帐户|
   |已完成但出错|请求已完成，但用户选择的某些订阅的计费无法转移|
   |已过期|用户未及时接受请求，请求现已过期|
   |已取消|有权访问转移请求的某人取消了该请求|
   |已拒绝|用户拒绝了转移请求|

6. 选择一个转移请求以查看详细信息。 转移详细信息页将显示以下信息：![显示已转移订阅列表的屏幕截图](./media/mpa-request-ownership/mpa-transfer-completed.png)

   |列  |定义|
   |---------|---------|
   |转移请求 ID|转移请求的唯一 ID。 提交支持请求时，请与 Azure 支持人员共享该 ID，以加速支持请求的处理|
   |转移请求日期|发送转移请求的日期|
   |转移请求者|发送转移请求的用户的电子邮件地址|
   |转移请求过期日期| 转移请求的过期日期|
   |收件人的电子邮件地址|已向其发送转移计费所有权请求的用户的电子邮件地址|
   |发送给收件人的转移链接|发送给用户的 URL，用于查看转移请求|

## <a name="supported-subscription-types"></a>支持的订阅类型

可以请求下面列出的订阅类型的计费所有权。

- [Enterprise 开发/测试](https://azure.microsoft.com/offers/ms-azr-0148p/)\* 
- [Microsoft 企业协议](https://azure.microsoft.com/pricing/enterprise-agreement/)

\* 企业 DevTest 订阅在转移后将按即用即付费率收费，任何按照客户的 EA 通过企业 DevTest 套餐提供的折扣都不能供 CSP 合作伙伴使用。

## <a name="additional-information"></a>其他信息

以下部分提供有关转移订阅的其他信息。

### <a name="no-service-downtime"></a>服务不会中断

订阅中的 Azure 服务将保持正常运行，而不会出现任何中断。 我们只会转换用户选择转移的 Azure 订阅的计费关系。

### <a name="disabled-subscriptions"></a>已禁用的订阅

无法转移已禁用的订阅。 订阅必须处于活动状态才能转移其计费所有权。

### <a name="azure-resources-transfer"></a>Azure 资源转移

订阅中的所有资源（例如 VM、磁盘和网站）都会转移。

### <a name="azure-marketplace-products-transfer"></a>Azure 市场产品转移

提供给订阅（由云解决方案提供商 (CSP) 托管）的 Azure 市场产品将连同各自的订阅一起转移。 如果订阅的 Azure 市场产品不是为 CSP 启用的，则不能转移此类订阅。

### <a name="azure-reservations-transfer"></a>Azure 预留项转移

Azure 预留项不会随订阅一起自动移动。 请[联系 Azure 支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)移动预留项。

### <a name="access-to-azure-services"></a>对 Azure 服务的访问权限

使用 [Azure RBAC（基于角色的访问控制）](../role-based-access-control/overview.md)为现有用户、组或服务主体分配的访问权限在转换期间不受影响。合作伙伴不会获得任何针对订阅的新 RBAC 访问权限。  

合作伙伴应协助客户获取对订阅的访问权限。  合作伙伴需获取[代表管理员 - AOBO](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) 或 [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/concepts/cloud-solution-provider) 访问权限才能开具支持票证。

### <a name="azure-support-plan"></a>Azure 支持计划

Azure 支持不会随订阅一起转移。 如果用户要转移所有 Azure 订阅，请让他们取消其支持计划。 转移后，CSP 合作伙伴负责该支持。 对于任何支持请求，客户都应与 CSP 合作伙伴协作。  

### <a name="charges-for-transferred-subscription"></a>转移的订阅费用

订阅的原始计费所有者负责支付转移完成之前所报告的所有费用。 你负责支付从转移开始时报告的费用。 可能有些费用是在转移之前发生的，但在转移之后才报告。 这些费用将显示在你的发票中。

### <a name="cancel-a-transfer-request"></a>取消转移请求

在批准或拒绝转移请求之前，你可以取消该请求。 若要取消转移请求，请转到[转移详细信息页](#check-the-transfer-request-status)，并在页面底部选择“取消”。

### <a name="software-as-a-service-saas-transfer"></a>软件即服务 (SaaS) 转移

SaaS 产品不会随订阅一起转移。 请让用户[联系 Azure 支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)转移 SaaS 产品的计费所有权。 除了计费所有权以外，用户还可以转移资源所有权。 使用资源所有权可以执行管理操作，例如删除产品，以及查看产品详细信息。 用户必须是 SaaS 产品中的资源所有者才能转移资源所有权。

### <a name="additional-approval-for-certain-customers"></a>针对某些客户的其他审批

考虑到客户的当前企业注册结构的性质，某些客户转换请求可能需要 Microsoft 参与的其他审批过程。 当合作伙伴尝试向客户发送邀请时，系统会将此类要求通知给客户。 合作伙伴需与合作伙伴开发经理以及客户的帐户团队合作完成此审查过程。

### <a name="azure-subscription-directory"></a>Azure 订阅目录 
转移的 Azure 订阅目录必须与 CSP 关系建立过程中选择的客户目录匹配。 

如果这两个目录不匹配，则无法转移这些订阅。 你必须通过选择 Azure 订阅的目录建立与客户的全新 CSP 经销商关系，或者更改 Azure 订阅的目录，使之与客户 CSP 关系目录匹配。 有关详细信息，请参阅[将现有订阅关联到 Azure AD 目录](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-how-subscriptions-associated-directory#to-associate-an-existing-subscription-to-your-azure-ad-directory)。


## <a name="check-access-to-a-microsoft-partner-agreement"></a>检查对 Microsoft 合作伙伴协议的访问权限
[!INCLUDE [billing-check-mpa](../../includes/billing-check-mpa.md)]

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员

如果需要帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。

## <a name="next-steps"></a>后续步骤

- Azure 订阅的计费所有权现已转移给你。 可在 [Azure 门户](https://portal.azure.com)中跟踪这些订阅的费用。

- 协助客户获取对已转移的 Azure 订阅的访问权限。 [使用 RBAC 管理对 Azure 资源的访问权限](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)。
