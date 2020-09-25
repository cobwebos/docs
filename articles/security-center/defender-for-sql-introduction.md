---
title: Azure Defender for SQL-权益和功能
description: 了解适用于 SQL 的 Azure Defender 的优势和功能。
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: conceptual
ms.service: security-center
ms.custom: references_regions
manager: rkarlin
ms.openlocfilehash: dcdbc3efba53f78890816721b6659aa69553d6ea
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91301595"
---
# <a name="introduction-to-azure-defender-for-sql"></a>Azure Defender for SQL 简介

适用于 SQL 的 azure Defender 包括两个 Azure Defender 计划，这些计划可扩展 Azure 安全中心的 [数据安全包](../azure-sql/database/advanced-data-security.md) ，以便在任何位置保护数据库及其数据。 

## <a name="availability"></a>可用性

|方面|详细信息|
|----|:----|
|发布状态：|**适用于 AZURE SQL 数据库服务器的 Azure Defender** -正式发布 (GA) <br>**适用于计算机上的 SQL server 的 Azure Defender** -预览|
|定价：|构成**Azure Defender FOR SQL**的两个计划按[定价页](security-center-pricing.md)中所示的方式计费|
|受保护的 SQL 版本：|Azure SQL 数据库 <br>Azure SQL 托管实例<br>Azure Synapse Analytics（以前称为 SQL DW）<br> (所有受支持的版本的 SQL Server) |
|云：|![是](./media/icons/yes-icon.png) 商业云<br>![是](./media/icons/yes-icon.png) US Gov<br>![否](./media/icons/no-icon.png) 中国 Gov，其他 Gov|
|||

## <a name="what-does-azure-defender-for-sql-protect"></a>Azure Defender for SQL 保护了哪些功能？

**适用于 SQL 的 Azure defender** 包括两个单独的 Azure defender 计划：

- **适用于 AZURE SQL 数据库服务器的 Azure Defender** 保护：
  - [Azure SQL 数据库](../azure-sql/database/sql-database-paas-overview.md)
  - [Azure SQL 托管实例](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)
  - [Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

- **适用于计算机上的 SQL server 的 Azure Defender (预览版) ** 扩展 AZURE 本机 SQL server 的保护，以完全支持混合环境，并保护 SQL server (托管在 Azure、其他云环境中，甚至是本地计算机的所有支持) 版本


## <a name="what-are-the-benefits-of-azure-defender-for-sql"></a>Azure Defender for SQL 有哪些好处？

这两个计划包括用于识别和缓解潜在的数据库漏洞并检测可能指示数据库威胁的异常活动的功能：

- [漏洞评估](../azure-sql/database/sql-vulnerability-assessment.md) -扫描服务，用于发现、跟踪和帮助您修正潜在的数据库漏洞。 评估扫描概述了 SQL 计算机的安全状态以及任何安全发现的详细信息。

- [高级威胁防护](../azure-sql/database/threat-detection-overview.md) -用于持续监视 sql 服务器是否受到威胁（例如 sql 注入、暴力攻击和权限滥用）的检测服务。 此服务在 Azure 安全中心提供面向操作的安全警报，并提供可疑活动的详细信息、有关如何缓解威胁的指导，以及用于继续调查 Azure Sentinel 的选项。


## <a name="what-kind-of-alerts-does-azure-defender-for-sql-provide"></a>Azure Defender for SQL 提供了何种类型的警报？

当存在以下情况时，会触发安全警报：

- **潜在的 SQL 注入攻击** -包括应用程序在数据库中生成错误的 SQL 语句时检测到的漏洞
- **异常数据库访问和查询模式** -例如，使用不同的凭据登录尝试失败的次数过多 (强力尝试) 
- **可疑的数据库活动** -例如，用于 SQL 导入和导出操作的导出存储目标的更改

警报包含触发这些事件的事件的详细信息，并提供有关如何调查和修正威胁的建议。



## <a name="next-steps"></a>后续步骤

本文介绍了适用于 SQL 的 Azure Defender。

如需相关材料，请参阅以下文章： 

- [如何在计算机上为 SQL server 启用 Azure Defender](defender-for-sql-usage.md)
- [如何启用适用于 SQL 数据库服务器的 Azure Defender](../azure-sql/database/advanced-data-security.md)
- [适用于 SQL 的 Azure Defender 警报列表](alerts-reference.md#alerts-sql-db-and-warehouse)