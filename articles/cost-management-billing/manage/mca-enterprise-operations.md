---
title: Microsoft 客户协议中的 EA 任务 - Azure
description: 了解如何在新的计费帐户中完成企业协议任务。
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 05/01/2020
ms.author: banders
ms.openlocfilehash: b8f588df89429bd08b1c74fbc528801fb45377ed
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82692309"
---
# <a name="complete-enterprise-agreement-tasks-in-your-billing-account-for-a-microsoft-customer-agreement"></a>完成 Microsoft 客户协议计费帐户中的企业协议任务

如果你的组织已签署 Microsoft 客户协议以续订企业协议合约，则系统将为该协议创建新的计费帐户。 新帐户中计费的组织方式与企业协议不同。 本文介绍如何使用新的计费帐户来执行企业协议中的任务。

## <a name="billing-organization-in-the-new-account"></a>新帐户中的计费组织方式

下图描绘了计费在新计费帐户中的组织方式。

![ea-mca-post-transition-hierarchy 插图](./media/mca-enterprise-operations/mca-post-transition-hierarchy.png)

| 企业协议   | Microsoft 客户协议    |
|------------------------|--------------------------------------------------------|
| 注册            | 你将使用计费配置文件来为组织管理计费，类似于在企业协议合约中那样。 企业管理员将成为计费配置文件的所有者。 若要详细了解计费配置文件，请参阅[了解计费配置文件](../understand/mca-overview.md#billing-profiles)。
| 部门            | 可以使用发票科目来组织成本，类似于在企业协议合约中使用部门。 部门将成为发票科目，部门管理员将成为各自发票科目的所有者。 若要详细了解发票科目，请参阅[了解发票科目](../understand/mca-overview.md#invoice-sections)。 |
| 帐户               | 新计费帐户不支持企业协议中创建的帐户。 新帐户的订阅属于其部门的相应发票科目。 帐户所有者可以创建和管理其发票科目的订阅。 |

## <a name="changes-for-enterprise-administrators"></a>企业管理员的变化

以下变化适用于企业协议中已续订 Microsoft 客户协议的企业管理员。

- 将为注册创建一个计费配置文件。 你将使用计费配置文件来为组织管理计费，类似于在企业协议合约中那样。 若要详细了解计费配置文件，请参阅[了解计费配置文件](../understand/mca-overview.md#billing-profiles)。
- 将为企业协议合约中的每个部门创建一个发票科目。 你将使用发票科目来管理部门。 可以创建新的发票科目来设置更多部门。 若要详细了解发票科目，请参阅[了解发票科目](../understand/mca-overview.md#invoice-sections)。
- 你将使用发票科目上的 Azure 订阅创建者角色来为其他人授予创建 Azure 订阅的权限，这类似于使用在企业协议合约中创建的帐户。
- 你将使用 [Azure 门户](https://portal.azure.com)（而不是 Azure EA 门户）来为组织管理计费。

将为你分配对新计费帐户的以下角色：

**计费配置文件所有者** - 将为你分配对签署协议时创建的计费配置文件的计费配置文件所有者角色。 使用该角色可以管理组织的计费。 可以查看费用和发票、组织发票中的成本、管理付款方式，以及控制对组织计费的访问权限。

**发票科目所有者** - 将为你分配发票科目所有者角色，该角色针对为企业协议合约中的部门创建的所有发票科目。 使用该角色可以控制谁能创建 Azure 订阅和购买其他产品。

### <a name="view-charges-and-credits-balance-for-your-organization"></a>查看组织的费用和剩余额度

可以使用计费配置文件来跟踪组织的费用和 Azure 剩余额度，类似于在企业协议合约中那样。

若要了解如何查看计费配置文件的剩余额度，请参阅[跟踪计费配置文件的 Azure 剩余额度](mca-check-azure-credits-balance.md)。

若要了解如何查看计费配置文件的费用，请参阅[了解 Microsoft 客户协议发票中的费用](../understand/review-customer-agreement-bill.md)。

### <a name="view-charges-for-a-department"></a>查看部门的费用

将为企业协议中的每个部门创建一个发票科目。 可以在 Azure 门户中查看发票科目的费用。 有关详细信息，请参阅[按发票科目查看交易](../understand/review-customer-agreement-bill.md#view-transactions-by-invoice-sections)。

### <a name="view-charges-for-an-account"></a>查看帐户的费用

新计费帐户不支持企业协议合约中创建的帐户。 新帐户的订阅属于其部门的相应发票科目。 帐户所有者可以创建和管理其发票科目的订阅。

若要查看属于某个帐户的订阅的聚合成本，必须为每个订阅设置一个成本中心。 然后，可以使用 Azure 使用情况和费用 csv 文件按成本中心筛选订阅。

### <a name="download-usage-and-charges-csv-price-sheet-and-tax-documents"></a>下载使用情况和费用 csv、价目表和税务文档

将为计费帐户中的每个计费配置文件生成每月发票。 对于每个发票，可以下载 Azure 使用情况和费用 csv 文件、价目表和税务文档（如果适用）。 还可以下载 Azure 使用情况和费用 csv 文件来了解当月的费用。

若要了解如何下载 Azure 使用情况和费用 csv 文件，请参阅[下载 Microsoft 客户协议的使用情况](../understand/download-azure-daily-usage.md)。

若要了解如何下载价目表，请参阅[下载 Microsoft 客户协议的定价](ea-pricing.md)。

若要了解如何下载税务文档，请参阅[查看 Microsoft 客户协议的税务文档](../understand/mca-download-tax-document.md#view-and-download-tax-documents)。

### <a name="add-an-additional-enterprise-administrator"></a>添加其他企业管理员

为用户授予对计费配置文件的访问权限可让他们查看和管理组织的帐单。 可以使用 Azure 门户中的“访问控制(IAM)”页来授予访问权限。   若要详细了解计费配置文件角色，请参阅[计费配置文件角色和任务](understand-mca-roles.md#billing-profile-roles-and-tasks)。

若要了解如何提供对计费配置文件的访问权限，请参阅[在 Azure 门户中管理计费角色](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)。

### <a name="create-a-new-department"></a>创建新部门

根据需求使用发票科目来组织成本，类似于在企业协议合约中使用部门。 可以在 Azure 门户中创建新的发票科目。 有关详细信息，请参阅[在发票中创建科目以组织成本](mca-section-invoice.md)。

### <a name="create-a-new-account"></a>创建新帐户

为用户分配对发票科目的 Azure 订阅创建者角色，使他们有权创建 Azure 订阅，这类似于使用企业协议合约中创建的帐户。 有关分配角色的详细信息，请参阅[在 Azure 门户中管理计费角色](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)

## <a name="changes-for-department-administrators"></a>部门管理员的变化

以下变化适用于企业协议中已续订 Microsoft 客户协议的部门管理员。

- 将为企业协议合约中的每个部门创建一个发票科目。 你将使用发票科目来管理部门。 若要详细了解发票科目，请参阅[了解发票科目](../understand/mca-overview.md#invoice-sections)。
- 你将使用发票科目上的 Azure 订阅创建者角色来为其他人授予创建 Azure 订阅的权限，这类似于使用在企业协议合约中创建的帐户。
- 你将使用 Azure 门户而不是 Azure EA 门户来为组织管理计费。

将为你分配对新计费帐户的以下角色：

**发票科目所有者** - 将为你分配发票科目所有者角色，该角色针对为企业协议中的部门创建的发票科目。 使用该角色可以查看和跟踪费用，以及控制谁能创建 Azure 订阅和购买发票科目的其他产品。

### <a name="view-charges-for-your-department"></a>查看部门的费用

可以在 Azure 门户的[“成本管理 + 计费”页](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview)中查看为部门创建的发票科目的费用。

### <a name="add-an-additional-department-administrator"></a>添加其他部门管理员

将为企业协议中的每个部门创建一个发票科目。 可以使用 Azure 门户中的“访问控制(IAM)”页为其他人授予查看和管理发票科目的访问权限。  若要详细了解发票科目角色，请参阅[发票科目角色和任务](understand-mca-roles.md#invoice-section-roles-and-tasks)。

若要了解如何提供对发票科目的访问权限，请参阅[在 Azure 门户中管理计费角色](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)。

### <a name="create-a-new-account-in-your-department"></a>在部门中创建新帐户

为用户分配已为部门创建的发票科目上的 Azure 订阅创建者角色。 有关分配角色的详细信息，请参阅[在 Azure 门户中管理计费角色](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)

### <a name="view-charges-for-accounts-in-your-departments"></a>查看部门中帐户的费用

新计费帐户不支持企业协议合约中创建的帐户。 新帐户的订阅属于其部门的相应发票科目。 帐户所有者可以创建和管理其发票科目的订阅。

若要查看属于部门中某个帐户的订阅的聚合成本，必须为每个订阅设置一个成本中心。 然后，可以使用 Azure 使用情况和费用文件按成本中心筛选订阅。

## <a name="changes-for-account-owners"></a>帐户所有者的变化

企业协议中的帐户所有者将获得在新计费帐户中创建 Azure 订阅的权限。 你的现有 Azure 订阅属于为你的部门创建的发票科目。 如果你的帐户不属于某个部门，则你的订阅将属于名为“默认发票科目”的发票科目。

将在新计费帐户中为你分配以下角色来创建其他 Azure 订阅。

**Azure 订阅创建者** - 将为你分配 Azure 订阅创建者角色，该角色针对为企业协议中的部门创建的发票科目。 如果你的帐户不属于某个部门，你将获得对名为“默认发票科目”的部分的 Azure 订阅创建者角色。 使用该角色可为发票科目创建 Azure 订阅。

### <a name="create-an-azure-subscription"></a>创建 Azure 订阅

可以在 Azure 门户中为发票创建 Azure 订阅。 有关详细信息，请参阅[为 Microsoft 客户协议创建其他 Azure 订阅](create-subscription.md)

### <a name="view-charges-for-your-account"></a>查看帐户的费用

若要查看属于某个帐户的订阅的费用，请转到 Azure 门户中的[订阅页](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)。 订阅页显示所有订阅的费用。

### <a name="view-charges-for-a-subscription"></a>查看订阅的费用

可以在[订阅页](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)或 Azure 成本分析中查看订阅的费用。 有关 Azure 成本分析的详细信息，请参阅[使用成本分析浏览和分析成本](../costs/quick-acm-cost-analysis.md)。

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员

如果需要帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。

## <a name="next-steps"></a>后续步骤

- [了解 Microsoft 客户协议的计费帐户](../understand/mca-overview.md)
- [了解发票](../understand/review-individual-bill.md)
- [了解帐单](../understand/understand-invoice.md)
- [从其他用户获取 Azure 订阅的计费所有权](mca-request-billing-ownership.md)
