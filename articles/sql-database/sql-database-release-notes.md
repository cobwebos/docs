---
title: Azure SQL Database 发行说明 |Microsoft Docs
description: 了解有关新功能和改进 Azure SQL 数据库服务中和 Azure SQL 数据库文档中的信息
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.subservice: service
ms.devlang: ''
ms.topic: conceptual
ms.date: 03/05/2019
ms.author: carlrab
ms.openlocfilehash: 6600a578ba9c73c8a2c71466fd0b008f19058b80
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "57861280"
---
# <a name="sql-database-release-notes"></a>SQL Database 发行说明

本文列出了新功能和改进和 SQL 数据库文档中的 SQL 数据库服务。 有关 SQL 数据库服务的改进，另请参阅[SQL 数据库服务更新](https://azure.microsoft.com/updates/?product=sql-database)。 对其他 Azure 服务的改进，请参阅[服务更新](https://azure.microsoft.com/updates)。

## <a name="march-2019"></a>2019 年 3 月

### <a name="service-improvements"></a>服务改进

| 服务改进 | 详细信息 |
| --- | --- |
| 即将支持 ||
| &nbsp; |

### <a name="documentation-improvements"></a>文档改进

| 文档改进 | 详细信息 |
| --- | --- |
| 添加了的日志限制为单一数据库的|有关详细信息，请参阅[单一数据库资源限制 vCore](sql-database-vcore-resource-limits-single-databases.md)。|
| 弹性池和入池的数据库添加的日志限制|有关详细信息，请参阅[弹性池 vCore 的资源限制](sql-database-vcore-resource-limits-elastic-pools.md)。|
| 添加了的事务日志速率调控| 添加新内容[事务日志速率调控](sql-database-resource-limits-database-server.md#transaction-log-rate-governance)。|
| 适用于单一数据库和弹性池，若要使用 az.sql 模块的更新的 PowerShell 示例 | 有关详细信息，请参阅[单一数据库和弹性池的 PowerShell 示例](sql-database-powershell-samples.md#single-database-and-elastic-pools)。|
| &nbsp; |

## <a name="february-2019"></a>2019 年 2 月

### <a name="service-improvements"></a>服务改进

| 服务改进 | 详细信息 |
| --- | --- |
|创建可恢复联机索引现已公开发布| 有关详细信息，请参阅[Create Index](https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql)。|
|托管的实例支持的改进的路由表| 有关详细信息，请参阅[网络要求](sql-database-managed-instance-connectivity-architecture.md#network-requirements)。|
|托管实例支持的数据库重命名 | 有关更多详细信息，请参阅[ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-mi-current)并[sp_rename](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-rename-transact-sql)语法。|
|Stream Analytics 的引用数据的源 SQL 数据库。 | 有关详细信息，请参阅[Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)。|
|数据迁移帮助增加了对托管实例支持。 |有关详细信息，请参阅[DMA 中新增](https://docs.microsoft.com/sql/dma/dma-whatsnew)。|
|SQL Server 迁移助手添加目标准备情况评估托管实例的支持。 | 有关详细信息，请参阅[SQL Server Migration Assistant](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant)。
|数据迁移服务支持从 Amazon RDS 迁移到托管实例 | 有关详细信息，请参阅[教程：RDS SQL Server 迁移到 Azure SQL 数据库或 Azure SQL 数据库上联机托管的实例使用 DMS](../dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online.md)。|
| &nbsp; |

### <a name="documentation-improvements"></a>文档改进

| 文档改进 | 详细信息 |
| --- | --- |
|添加托管实例部署选项说明|更新很多文章，以阐明适用于单个数据库、 弹性池和托管的实例的部署选项。 |
|基于 DTU 的购买模型的已更新的 tempdb 大小 | 有关详细信息，请参阅[SQL 数据库中的 Tempdb 数据库](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database)。|
|更新导入和导出与托管的实例支持的 bacpac 文件| 有关详细信息，请参阅[从 BACPAC 导入](sql-database-import.md)并[导出到 BACPAC](sql-database-export.md)。 |
| &nbsp; |


## <a name="january-2019"></a>2019 年 1 月

### <a name="service-improvements"></a>服务改进

| 服务改进 | 详细信息 |
| --- | --- |
| 计算资源的更多粒度选项 | 常规用途和业务关键服务层[单一数据库](sql-database-vcore-resource-limits-single-databases.md)并[弹性池](sql-database-vcore-resource-limits-elastic-pools.md)现在具有更多的细粒度计算选项。|
| 查看在 Azure 门户中的托管实例的审核记录 | 查看[审核记录的托管实例](sql-database-managed-instance-auditing.md)在 Azure 门户现在支持。|
| 重命名为高级数据安全性的高级威胁检测功能 | 高级威胁检测功能重命名为[高级数据安全](sql-advanced-threat-protection.md)单一数据库、 弹性池和托管的实例。 |
| &nbsp; |

### <a name="documentation-improvements"></a>文档改进

| 文档改进 | 详细信息 |
| --- | --- |
| 托管的实例和事务复制 | 添加了有关使用文章[具有托管实例的事务复制](replication-with-sql-database-managed-instance.md) |
| 添加了 Azure AD 与托管的实例教程 | 这[托管实例的 Azure AD](sql-database-managed-instance-aad-security-tutorial.md)教程，说明您必须配置和测试管理实例安全性使用 Azure AD 登录名。 |
| 使用 Transact SQL 脚本的作业自动化的更新的内容 | 更新，并且阐明了有关使用的内容[使用 Transact SQL 脚本的作业自动化](sql-database-job-automation-overview.md)单一数据库、 弹性池和托管的实例 |
| 更新托管实例的安全内容 | 更新并阐明的内容[托管实例的安全模型](sql-database-security-overview.md)，并对照使用单一数据库和弹性池的安全模型 |
| 刷新所有快速入门和教程 | 快速入门和教程中的所有[文档](https://docs.microsoft.com/azure/sql-database)已更新和刷新以匹配 Azure 门户中的更改 |
| 添加了快速入门概述指南 | 添加的快速入门概述指南[单一数据库](sql-database-quickstart-guide.md)以及[托管实例](sql-database-managed-instance-quickstart-guide.md) |
| 添加了的 SQL 数据库术语词汇表 | 这[术语术语表](sql-database-glossary-terms.md)文章提供了最终的 SQL 数据库条款和说明在上下文中的术语的主要概念页面的链接列表。 |
| &nbsp; |

## <a name="contribute-to-content-improvement"></a>贡献内容改进

Azure SQL 文档集是开放源代码。 开放式工作有以下几点好处：

- 在打开以获取有关哪些文档最需要的反馈计划开放源代码存储库。
- 在发布上我们的第一个版本的最有帮助内容中查看开放源代码存储库。
- 开放源代码存储库更新以使其更轻松地持续改进内容。

若要提供 Azure SQL 数据库文档内容，请参阅[Microsoft Docs 参与者指南概述](https://docs.microsoft.com/contribute/)。 上的用户体验[docs.microsoft.com](https://docs.microsoft.com/)集成[GitHub](https://github.com/)直接要更轻松的工作流。 首先[编辑正在查看的文档](https://docs.microsoft.com/contribute/#quick-edits-to-existing-documents)。 或者，通过帮助[查看新主题](https://docs.microsoft.com/contribute/#review-open-prs)，或[创建质量问题](https://docs.microsoft.com/contribute/#create-quality-issues)。
