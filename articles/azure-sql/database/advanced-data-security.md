---
title: 高级数据安全
description: 了解用于发现和分类敏感数据、管理数据库漏洞以及检测可能表明对 Azure SQL 数据库、Azure SQL 托管实例或 Azure Synapse 中的数据库存在威胁的异常活动的功能。
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.devlang: ''
ms.custom: sqldbrb=2
ms.topic: conceptual
ms.author: memildin
manager: rkarlin
author: memildin
ms.reviewer: vanto
ms.date: 04/23/2020
ms.openlocfilehash: 53765ee97f0f253db4df4ecca3c1c90d6068fb07
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/06/2020
ms.locfileid: "85983988"
---
# <a name="advanced-data-security"></a>高级数据安全
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]


高级数据安全（ADS）是一个适用于高级 SQL 安全功能的统一包。 适用于 Azure SQL 数据库、Azure SQL 托管实例和 Azure Synapse Analytics 的广告。 它包括用于发现和分类敏感数据、呈现和减少潜在数据库漏洞，以及检测可能表明数据库有威胁的异常活动的功能。 它提供用于启用和管理这些功能的一个转到位置。

## <a name="overview"></a>概述

ADS 提供一套高级 SQL 安全功能，包括数据发现 & 分类、SQL 漏洞评估和高级威胁防护。
- [数据发现 & 分类](data-discovery-and-classification-overview.md)提供 Azure sql 数据库、azure sql 托管实例和 azure Synapse 中内置的功能，用于发现、分类、标记和报告数据库中的敏感数据。 它可用于直观查看数据库分类状态，以及跟踪对数据库内和其边界外的敏感数据的访问。
- [漏洞评估](sql-vulnerability-assessment.md)是一项易于配置的服务，可以发现、跟踪和帮助您修正潜在的数据库漏洞。 它提供安全状态的可见性，其中包括用于解决安全问题并增强数据库 fortifications 的可操作步骤。
- [高级威胁防护](threat-detection-overview.md)检测异常活动，指出尝试访问或利用数据库的行为异常且可能有害。 它会持续监视你的数据库中的可疑活动，并提供有关潜在漏洞、Azure SQL 注入攻击和异常数据库访问模式的即时安全警报。 高级威胁防护警报提供可疑活动的详细信息，并建议如何调查和缓解威胁。

启用高级数据安全一次，以启用所有这些包括的功能。 只需要单击一次，即可在 Azure 或 SQL 托管实例中的[服务器](logical-servers.md)上启用所有数据库的广告。 启用或管理广告设置需要属于[SQL 安全管理员](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager)角色，或属于某个数据库或服务器管理员角色。

广告定价与 Azure 安全中心标准层，其中每个受保护的服务器或托管实例计为一个节点。 新的受保护资源符合安全中心标准层免费试用版资格。 有关详细信息，请参阅 [Azure 安全中心定价页](https://azure.microsoft.com/pricing/details/security-center/)。

## <a name="getting-started-with-ads"></a>广告入门

以下步骤帮助你开始使用 ADS。

## <a name="1-enable-ads"></a>1.启用 ADS

通过导航到服务器或托管实例的 "**安全**" 标题下的 "**高级数据安全性**"，启用广告。

> [!NOTE]
> 系统会自动创建一个存储帐户用于存储**漏洞评估**的扫描结果。 如果为同一个资源组和区域中的另一台服务器启用了 ADS，则使用现有的存储帐户。

![启用 ADS](./media/advanced-data-security/enable_ads.png)

> [!NOTE]
> 广告的成本与每个节点的 Azure 安全中心标准层定价（节点是整个服务器或托管实例）一致。 因此，只需要支付一次，就能用广告保护服务器或托管实例上的所有数据库。 你可以从免费试用版开始试用 ADS。

## <a name="2-start-classifying-data-tracking-vulnerabilities-and-investigating-threat-alerts"></a>2.开始对数据分类、跟踪漏洞和调查威胁警报

单击“数据发现和分类”卡，查看建议进行分类的敏感列，并使用永久性敏感标签对数据分类  。 单击“漏洞评估”卡，查看和管理漏洞扫描和报告，并跟踪安全状况  。 如果收到安全警报，请单击“高级威胁防护”卡，查看警报的详细信息，并通过 Azure 安全中心安全警报页面查看 Azure 订阅中所有警报的综合报告  。

## <a name="3-manage-ads-settings"></a>3. 管理广告设置

若要查看和管理广告设置，请在服务器或托管实例的**安全**标题下，导航到 "**高级数据安全性**"。 在此页上，可以启用或禁用广告，并修改整个服务器或托管实例的漏洞评估和高级威胁防护设置。

![服务器设置](./media/advanced-data-security/server_settings.png)

## <a name="4-manage-ads-settings-for-a-database"></a>4. 管理数据库的广告设置

要重写特定数据库的 ADS 设置，请勾选“在数据库级别启用高级数据安全”复选框  。 仅当你有特定要求接收单个数据库的单独高级威胁防护警报或漏洞评估结果时，或除了为服务器或托管实例上的所有数据库收到的警报和结果之外，才使用此选项。

选中该复选框后，可以配置此数据库的相关设置。

![数据库和高级威胁防护设置](./media/advanced-data-security/database_threat_detection_settings.png)

你的服务器或托管实例的高级数据安全设置也可以从 "广告数据库" 窗格中访问。 单击主 ADS 窗格中的“设置”  ，然后单击“查看高级数据安全服务器设置”  。

![数据库设置](./media/advanced-data-security/database_settings.png)

## <a name="next-steps"></a>后续步骤

- 详细了解[数据发现和分类](data-discovery-and-classification-overview.md)
- 详细了解[漏洞评估](sql-vulnerability-assessment.md)
- 详细了解[高级威胁防护](threat-detection-configure.md)
- 了解有关 [Azure 安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)的详细信息
