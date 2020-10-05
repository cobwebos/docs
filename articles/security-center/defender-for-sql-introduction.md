---
title: 适用于 SQL 的 Azure Defender - 优点和功能
description: 了解适用于 SQL 的 Azure Defender 的优点和功能。
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
ms.custom: references_regions
manager: rkarlin
ms.openlocfilehash: 328a565dbb22a13c71a7001d43941e7be062dff9
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91449055"
---
# <a name="introduction-to-azure-defender-for-sql"></a>适用于 SQL 的 Azure Defender 简介

适用于 SQL 的 Azure Defender 包括两个 Azure Defender 计划，这些计划可扩展 Azure 安全中心的[数据安全包](../azure-sql/database/advanced-data-security.md)以保护任何位置的数据库及其数据。 

## <a name="availability"></a>可用性

|方面|详细信息|
|----|:----|
|发布状态：|**适用于 Azure SQL 数据库服务器的 Azure Defender** - 正式发布 (GA)<br>**适用于计算机上的 SQL 服务器的 Azure Defender** - 预览|
|定价：|适用于 SQL 的 Azure Defender 包含的两个计划按[定价页](security-center-pricing.md)中的定价计费|
|受保护的 SQL 版本：|Azure SQL Database <br>Azure SQL 托管实例<br>Azure Synapse Analytics（以前称为 SQL DW）<br>SQL Server（所有受支持的版本）|
|云：|![是](./media/icons/yes-icon.png) 商业云<br>![是](./media/icons/yes-icon.png) US Gov<br>![否](./media/icons/no-icon.png) China Gov，其他 Gov|
|||

## <a name="what-does-azure-defender-for-sql-protect"></a>适用于 SQL 的 Azure Defender 保护了哪些项目？

适用于 SQL 的 Azure Defender 包含两个单独的 Azure Defender 计划：

- 适用于 Azure SQL 数据库服务器的 Azure Defender 保护：
  - [Azure SQL 数据库](../azure-sql/database/sql-database-paas-overview.md)
  - [Azure SQL 托管实例](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)
  - [Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

- 适用于计算机上的 SQL 服务器的 Azure Defender（预览版）会扩展对 Azure 原生 SQL Server 的保护以完全支持混合环境，并保护在 Azure、其他云环境甚至本地计算机上托管的 SQL server（所有受支持的版本）


## <a name="what-are-the-benefits-of-azure-defender-for-sql"></a>适用于 SQL 的 Azure Defender 有哪些优点？

这两项计划包括用于识别和减少潜在的数据库漏洞的功能，以及用于检测可能表明数据库有威胁的异常活动的功能：

- [漏洞评估](../azure-sql/database/sql-vulnerability-assessment.md) - 一种扫描服务，用于发现、跟踪并帮助修正潜在的数据库漏洞。 评估扫描概述了 SQL 计算机的安全状态以及任何安全发现结果的详细信息。

- [高级威胁防护](../azure-sql/database/threat-detection-overview.md) -一种检测服务，用于持续监视 SQL 服务器的威胁，例如 SQL 注入、暴力攻击和特权滥用。 此服务在 Azure 安全中心提供面向操作的安全警报，其中包括可疑活动的详细信息、有关如何减少威胁的指导以及使用 Azure Sentinel 继续调查的选项。


## <a name="what-kind-of-alerts-does-azure-defender-for-sql-provide"></a>适用于 SQL 的 Azure Defender 提供哪种类型的警报？

当存在以下情况时，会触发安全警报：

- **潜在的 SQL 注入攻击** - 包括应用程序在数据库中生成错误的 SQL 语句时检测到的漏洞
- **异常的数据库访问和查询模式** - 例如，使用不同的凭据尝试登陆，但登录失败的次数异常多（强制尝试）
- **可疑的数据库活动** - 例如，SQL 导入和导出操作的导出存储目标发生了变化

警报包含触发警报的事件的详细信息，并提供有关如何调查和消除威胁的建议。



## <a name="next-steps"></a>后续步骤

本文介绍了适用于 SQL 的 Azure Defender。

如需相关材料，请参阅以下文章： 

- [如何启用适用于计算机上的 SQL 服务器的 Azure Defender](defender-for-sql-usage.md)
- [如何启用适用于 SQL 数据库服务器的 Azure Defender](../azure-sql/database/advanced-data-security.md)
- [适用于 SQL 的 Azure Defender 警报列表](alerts-reference.md#alerts-sql-db-and-warehouse)