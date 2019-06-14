---
title: 完成 Microsoft 客户协议-Azure 中的企业协议任务 |Microsoft Docs
description: 了解如何完成新的计费帐户中的企业协议任务。
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
ms.date: 02/24/2018
ms.author: banders
ms.openlocfilehash: 9b76fa935263904957c87cd062c84d0607771369
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60371321"
---
# <a name="complete-enterprise-agreement-tasks-in-your-billing-account-for-a-microsoft-customer-agreement"></a>Microsoft 客户协议完成您的计费帐户中的企业协议任务

如果你的组织已签名 Microsoft 客户协议续订你的企业协议注册，协议创建一个新的计费帐户。 新帐户中的计费方式不同于你的企业协议的组织。 本文介绍如何使用新的计费帐户以执行在你的企业协议中执行的任务。

## <a name="how-billing-is-organized-in-the-new-account"></a>计费中的新帐户的组织方式

下图描述计费中新的计费帐户的组织方式。

![Ea-mca 开机自检-转换-层次结构的图像](./media/billing-mca-setup-account/mca-post-transition-hierarchy.png)

| 企业协议   | Microsoft 客户协议    |
|------------------------|--------------------------------------------------------|
| 注册            | 计费的配置文件用于为你的组织，类似于你的企业协议注册管理计费。 企业管理员成为计费的配置文件的所有者。 若要了解有关计费的配置文件的详细信息，请参阅[了解计费资料](billing-mca-overview.md#understand-billing-profiles)。
| 系            | 发票部分用于组织你的成本，类似于在企业协议注册中的部门。 部门变为发票部分，部门管理员成为相应发票部分的所有者。 若要了解有关发票部分的详细信息，请参阅[了解发票部分](billing-mca-overview.md#understand-invoice-sections)。 |
| 帐户               | 在你的企业协议中创建的帐户不支持在新的计费帐户中。 为其院系的相应发票部分属于帐户的订阅。 帐户所有者可以创建和管理其发票的部分的订阅。 |

## <a name="changes-for-enterprise-administrators-in-the-new-billing-account"></a>在新的计费帐户中的企业管理员的更改

以下更改适用于根据企业协议获得续订 Microsoft 客户协议的企业管理员。

- 你的注册创建计费的配置文件。 计费的配置文件将用于与你的企业协议注册相同的管理为你的组织，计费。 若要了解有关计费的配置文件的详细信息[了解计费资料](billing-mca-overview.md#understand-billing-profiles)。
- 为在企业协议注册中的每个部门创建发票部分。 将使用发票部分来管理你的部门。 您可以创建新发票部分来设置其他部门。 若要了解有关发票部分的详细信息，请参阅[了解发票部分](billing-mca-overview.md#understand-invoice-sections)。
- 发票部分中，将使用 Azure 订阅创建者角色来授予其他人创建 Azure 订阅，例如在企业协议注册期间创建的帐户的权限。
- 将使用[Azure 门户](https://portal.azure.com)管理为你的组织，而不是 Azure EA 门户的帐单。

新的计费帐户上指定了以下角色：

**计费配置文件所有者**-分配签署协议时创建的计费配置文件上的计费配置文件所有者角色。 该角色允许你管理为你的组织计费。 可以查看费用和发票、 组织在发票上的成本、 管理付款方式，并控制对组织的帐单的访问。

**发票部分所有者**-分配为在企业协议注册中的部门创建的所有发票部分上的发票部分所有者角色。 角色使您能够控制可以创建 Azure 订阅和购买其他产品。

### <a name="view-charges-and-credits-balance-for-your-organization"></a>查看你的组织的费用和信用额度余额

使用你计费的配置文件来跟踪费用和 Azure 信用额度余额为类似于你的企业协议注册你的组织。

若要了解如何查看账单资料的信用额度余额，请参阅[跟踪你计费的配置文件的 Azure 信用额度余额](billing-mca-check-azure-credits-balance.md)。

若要了解如何查看费用计费的配置文件，请参阅[了解 Microsoft 客户协议的发票上的费用](billing-mca-understand-your-bill.md)。

### <a name="view-charges-for-a-department"></a>查看某个部门的费用

为你的企业协议中的每个部门创建发票部分。 可以在 Azure 门户中查看发票部分的费用。 有关详细信息，请参阅[按发票部分查看交易记录](billing-mca-understand-your-bill.md#view-transactions-by-invoice-sections)。

### <a name="view-charges-for-an-account"></a>查看费用的帐户

在新的计费帐户中不支持在你的企业协议注册期间创建的帐户。 为其院系的相应发票部分属于帐户的订阅。 帐户所有者可以创建和管理其发票的部分的订阅。

若要查看聚合成本属于同一个帐户的订阅，必须设置每个订阅的成本中心。 然后，可以使用 Azure 使用情况和费用 csv 文件来筛选通过成本中心的订阅。

### <a name="download-usage-and-charges-csv-price-sheet-and-tax-documents"></a>下载使用情况和费用 csv、 价目表和税务文档

为计费帐户中每个计费配置文件生成一个每月的发票。 为每张发票，您可以下载 Azure 使用情况和费用 csv 文件、 价目表和税务文档 （如果适用）。 此外可以下载当前月份的费用的 Azure 使用情况和费用 csv 文件。

若要了解如何下载 Azure 使用情况和费用 csv 文件，请参阅[下载使用情况适用于你的 Microsoft 客户协议](billing-download-azure-daily-usage.md#download-usage-for-your-microsoft-customer-agreement)。

若要了解如何下载价目表，请参阅[下载了 Microsoft 客户协议的定价](billing-ea-pricing.md#view-and-download-pricing-for-your-microsoft-customer-agreement)。

若要了解如何下载税务文档，请参阅[为 Microsoft 客户协议查看税务文档](billing-mca-download-tax-document.md#view-and-download-tax-documents)。

### <a name="add-an-additional-enterprise-administrator"></a>添加其他企业管理员

向用户访问计费的配置文件，以允许这些视图，管理为你的组织的帐单。 可以使用**访问控制 (IAM)** 以便访问在 Azure 门户中的页。  若要了解有关计费配置文件角色的详细信息，请参阅[计费的配置文件角色和任务](billing-understand-mca-roles.md#billing-profile-roles-and-tasks)。

若要了解如何提供、 对你计费的配置文件的访问，请参阅[管理在 Azure 门户中的计费角色](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)。

### <a name="create-a-new-department"></a>创建新系

创建发票部分来组织你基于你的需求，如部门在企业协议注册中的成本。 可以在 Azure 门户中创建新的发票部分。 若要了解详细信息，请参阅[来组织你的成本在发票上创建部分](billing-mca-section-invoice.md)。

### <a name="create-a-new-account"></a>创建新帐户

将用户分配发票部分来为他们提供权限来创建 Azure 订阅，例如在企业协议注册中创建的帐户上的 Azure 订阅创建者角色。 有关详细信息，请参阅[创建 Azure 订阅的权限授予其他人](billing-mca-create-subscription.md#give-others-permission-to-create-azure-subscriptions)。

## <a name="changes-for-department-administrators-in-the-new-billing-account"></a>部门管理员在新的计费帐户中的更改

以下更改适用于部门管理员根据企业协议获得对 Microsoft 客户协议续订。

- 为在企业协议注册中的每个部门创建发票部分。 发票区域将用于管理你的部门。 若要了解有关发票部分的详细信息，请参阅[了解发票部分](billing-mca-overview.md#understand-invoice-sections)。
- 上的发票部分中，将使用 Azure 订阅创建者角色来授予其他人创建 Azure 订阅，例如在企业协议注册中创建的帐户的权限。
- 将使用 Azure 门户为你的组织，而不是 Azure EA 门户中管理帐单。

新的计费帐户上指定了以下角色：

**发票部分所有者**-分配为企业协议中的部门创建发票部分上的发票部分所有者角色。 角色使您能够查看和跟踪费用，并控制可以创建 Azure 订阅和购买其他产品后进行的发票部分。

### <a name="view-charges-for-your-department"></a>查看针对你所在部门的费用

可以为所在部门，在 Azure 门户中查看创建的发票部分的费用[成本管理 + 计费页面](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview)。

### <a name="add-an-additional-department-administrator"></a>添加其他部门管理员

为你的企业协议中的每个部门创建发票部分。 可以使用**访问控制 （iam)** 授予其他人查看和管理的发票部分访问权限在 Azure 门户中的页。 若要了解有关发票部分角色的详细信息，请参阅[发票部分角色和任务](billing-understand-mca-roles.md#invoice-section-roles-and-tasks)。

若要了解如何提供、 到发票部分访问，请参阅[管理在 Azure 门户中的计费角色](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)。

### <a name="create-a-new-account-in-your-department"></a>在您的部门创建新的帐户

将用户分配为你的部门创建发票部分上的 Azure 订阅创建者角色。 有关详细信息，请参阅[创建 Azure 订阅的权限授予其他人](billing-mca-create-subscription.md#give-others-permission-to-create-azure-subscriptions)。

### <a name="view-charges-for-accounts-in-your-departments"></a>查看你部门中的帐户的费用

在新的计费帐户中不支持在你的企业协议注册期间创建的帐户。 为其院系的相应发票部分属于帐户的订阅。 帐户所有者可以创建和管理其发票的部分的订阅。

若要查看聚合成本属于你的部门中的帐户的订阅，必须设置每个订阅的成本中心。 然后，可以使用 Azure 使用情况和费用文件进行筛选的成本中心的订阅。

## <a name="changes-for-account-owners-in-the-new-billing-account"></a>在新的计费帐户中的帐户所有者的更改

帐户所有者的企业协议获取 Azure 订阅创建新的计费帐户上的权限。 为你的部门创建发票部分属于现有的 Azure 订阅。 如果你的帐户不属于一个部门，你的订阅属于名为默认发票部分发票部分。

若要创建更多的 Azure 订阅，系统将在新的计费帐户提供以下角色。

**Azure 订阅创建者**-为你企业协议中的部门分配创建的发票部分上的 azure 订阅创建者角色。 如果你的帐户不属于一个部门上一个名为默认发票部分部分, 将获取 Azure 订阅的创建者角色。 该角色允许你创建 Azure 订阅的发票部分。

### <a name="create-an-azure-subscription"></a>创建 Azure 订阅

您可以为您的发票部分在 Azure 门户中创建 Azure 订阅。 有关详细信息，请参阅[为 Microsoft 客户协议创建其他 Azure 订阅](billing-mca-create-subscription.md)

### <a name="view-charges-for-your-account"></a>查看你的帐户的费用

若要查看属于同一个帐户的订阅费用，请转到[订阅页](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)在 Azure 门户中。 订阅页显示所有订阅的费用。

### <a name="view-charges-for-a-subscription"></a>查看订阅的费用

或者，您可以查看订阅的费用上[订阅页](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)或 Azure 成本分析。 有关 Azure 成本分析的详细信息，请参阅[探索和分析使用成本分析成本](../cost-management/quick-acm-cost-analysis.md)。

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员

如果需要帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。

## <a name="next-steps"></a>后续步骤

- [了解 Microsoft 客户协议的计费帐户](billing-mca-overview.md)
- [了解发票](billing-understand-your-bill.md)
- [了解帐单](billing-understand-your-invoice.md)
- [获取计费的其他用户的 Azure 订阅的所有权](billing-mca-request-billing-ownership.md)
