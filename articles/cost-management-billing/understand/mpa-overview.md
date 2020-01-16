---
title: Microsoft 合作伙伴协议计费帐户入门 - Azure CSP
description: 了解 Microsoft 合作伙伴协议计费帐户 (CSP)
author: bandersmsft
manager: amberbhargava
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2019
ms.author: banders
ms.openlocfilehash: 2a15ad41850fdf574262c396bf70e43b3b08399c
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "75989678"
---
# <a name="get-started-with-your-microsoft-partner-agreement-billing-account"></a>Microsoft 合作伙伴协议计费帐户入门

当你注册使用 Azure 时，系统将为你创建计费帐户。 可以使用计费帐户管理发票、付款并跟踪成本。 可以访问多个计费帐户。 例如，可以为个人项目注册 Azure。 也可通过组织的企业协议、Microsoft 客户协议或 Microsoft 合作伙伴协议访问 Azure。 每个这样的方案都会有单独的计费帐户。

本文适用于 Microsoft 合作伙伴协议的计费帐户。 这些帐户是为云解决方案提供商 (CSP) 创建的，用于在新的商业体验中管理其客户的计费。 新体验仅适用于特定的合作伙伴，他们至少有一个接受 Microsoft 客户协议且有 Azure 计划的客户。 [检查你是否有权访问 Microsoft 合作伙伴协议](#check-access-to-a-microsoft-partner-agreement)。 通过 [Azure 计划](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/)，客户可以按 Microsoft 客户协议下的即用即付费率访问 Azure 服务。

## <a name="your-billing-account"></a>你的计费帐户

Microsoft 合作伙伴协议的计费帐户包含开展业务时使用的每种货币的计费对象信息。 使用计费对象信息可以管理其货币的发票。 与客户建立关系时，Azure 订阅和其他购买项会根据相应的计费对象信息计费，具体取决于客户的货币。

下图显示了计费帐户、计费对象信息、客户和经销商之间的关系。

![显示 Microsoft 合作伙伴协议计费层次结构的关系图](./media/mpa-overview/mpa-hierarchy.svg)

在组织中具有“全局管理员”和“管理员代理”角色的用户可以管理计费帐户、计费对象信息和客户。 若要了解详细信息，请参阅[合作伙伴中心 - 为用户分配角色和权限](https://docs.microsoft.com/partner-center/permissions-overview)。

## <a name="billing-profiles"></a>计费对象信息

使用计费对象信息可以管理某种货币的发票。 每月发票是在当月开始时针对帐户中的每项计费对象信息生成的。 发票包含上月的所有 Azure 订阅和其他购买项目的费用（使用计费对象信息的货币）。

可以在 Azure 门户中查看发票并下载相关的文档（例如使用情况文件和价目表）。 有关详细信息，请参阅[下载 Microsoft 合作伙伴协议的发票](download-azure-invoice.md)。

> [!IMPORTANT]
>
> 计费对象信息的发票包含使用 Azure 计划、SaaS 和 Azure 市场的客户的费用，以及尚未接受 Microsoft 客户协议，因此没有 Azure 计划的客户的预留购买项。

## <a name="customers"></a>客户

可以在 Azure 门户中查看和管理已接受 Microsoft 客户协议，因此有 Azure 计划的客户。 可以查看费用和交易，以及为这些客户创建并管理 Azure 订阅。

### <a name="enable-policy-to-give-visibility-into-cost"></a>启用用于了解成本的策略

应用策略来控制是否允许客户的组织中的用户查看和分析其 Azure 消耗的成本（采用即用即付费率）。 默认情况下，此策略处于关闭状态，用户不能查看成本。 启用以后，在订阅上有相应的 [Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview) 访问权限的用户即可查看和分析订阅的成本。

若要启用此策略，请执行以下操作：

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 在“成本管理 + 计费”中进行搜索。

   ![显示了 Azure 门户搜索的屏幕截图](./media/mpa-overview/search-cmb.png)

1. 在左侧选择“客户”，然后从列表中选择一位客户。

   ![屏幕截图，显示正在选择客户](./media/mpa-overview/mpa-customers.png)

1. 在左侧选择“策略”。

   ![显示策略的屏幕截图](./media/mpa-overview/mpa-change-policy.png)

1. 请选择“是”。

## <a name="resellers"></a>经销商

在 Azure 门户中，CSP [双层模型](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview#azure-csp-direct-and-azure-csp-indirect)中的间接提供商可以在为客户创建订阅的同时选择一位经销商。 创建以后，他们可以查看订阅的列表（按经销商筛选），并在 Azure 成本分析中按经销商分析客户的成本。

## <a name="check-access-to-a-microsoft-partner-agreement"></a>检查对 Microsoft 合作伙伴协议的访问权限
[!INCLUDE [billing-check-mpa](../../../includes/billing-check-mpa.md)]

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员

如果需要帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。

## <a name="next-steps"></a>后续步骤

请参阅以下文章，了解计费帐户：

- [为 Microsoft 合作伙伴协议创建其他 Azure 订阅](../manage/create-subscription.md)
- 使用 [Azure 计费 API](https://docs.microsoft.com/rest/api/billing/) 将计费数据与你自己的报表系统集成
- [适用于合作伙伴的 Azure 成本管理快速入门指南](https://go.microsoft.com/fwlink/?linkid=2106482)
