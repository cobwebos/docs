---
title: Azure SQL 数据库发行说明 | Microsoft Docs
description: 了解 Azure SQL 数据库服务和 Azure SQL 数据库文档中的新功能和改进
services: sql-database
author: stevestein
ms.service: sql-database
ms.subservice: service
ms.devlang: ''
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: sstein
ms.openlocfilehash: ed41ccea0754f3eeffdd0248bac567859db1492c
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/15/2019
ms.locfileid: "71001510"
---
# <a name="sql-database-release-notes"></a>SQL 数据库发行说明

本文列出了当前以公共预览版提供的 SQL 数据库功能。 有关 SQL 数据库的更新和改进，请参阅[Sql 数据库服务更新](https://azure.microsoft.com/updates/?product=sql-database)。 有关对其他 Azure 服务的更新和改进，请参阅[服务更新](https://azure.microsoft.com/updates)。

## <a name="features-in-public-preview"></a>处于公共预览版的功能

### <a name="single-databasetabsingle-database"></a>[单个数据库](#tab/single-database)

| 功能 | 详细信息 |
| ---| --- |
| 通过单一数据库和弹性池加速数据库恢复 | 有关信息，请参阅[加速数据库恢复](sql-database-accelerated-database-recovery.md)。|
|估计非重复计数|有关信息，请参阅[近似计数 Distinct](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#approximate-query-processing)。|
|行存储上的批处理模式（在兼容性级别 150 下）|有关信息，请参阅[行存储上的批处理模式](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#batch-mode-on-rowstore)。|
| 数据发现和分类  |有关信息，请参阅 [Azure SQL 数据库和 SQL 数据仓库数据发现和分类](sql-database-data-discovery-and-classification.md)。|
| 灵活的数据库作业 | 有关信息，请参阅[创建、配置和管理弹性作业](elastic-jobs-overview.md)。 |
| 弹性查询 | 有关信息，请参阅[弹性查询概述](sql-database-elastic-query-overview.md)。 |
| 弹性事务 | [跨云数据库的分布式事务](sql-database-elastic-transactions-overview.md)。 |
|内存授予反馈（行模式）（在兼容性级别 150 下）|有关信息，请参阅[内存授予反馈（行模式）](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#row-mode-memory-grant-feedback)。|
| Azure 门户中的查询编辑器 |有关信息，请参阅[使用 Azure 门户的 SQL 查询编辑器进行连接并查询数据](sql-database-connect-query-portal.md)。|
| 带有单一数据库和弹性池的 R services/机器学习 |有关信息，请参阅[AZURE SQL 数据库中的机器学习服务](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#machine-learning-services-in-azure-sql-database)。|
| 无服务器计算层 | 有关信息，请参阅 [SQL 数据库无服务器（预览版）](sql-database-serverless.md)。|
|SQL Analytics|有关信息，请参阅[Azure SQL Analytics](../azure-monitor/insights/azure-sql.md)。|
|表变量延迟编译（在兼容性级别 150 下）|有关信息，请参阅[表变量延迟的编译](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#table-variable-deferred-compilation)。|
| &nbsp; |

### <a name="managed-instancetabmanaged-instance"></a>[托管实例](#tab/managed-instance)

| 功能 | 详细信息 |
| ---| --- |
| <a href="/azure/sql-database/sql-database-instance-pools">实例池</a> | 将较小的 SQL 实例迁移到云的方便且经济高效的方式。 |
| <a href="https://aka.ms/managed-instance-tde-byok">带有创建自己的密钥的透明数据加密（TDE）（BYOK）</a> |有关信息，请参阅[使用 Azure Key Vault 中由客户管理的密钥进行 Azure SQL 透明数据加密：自带密钥支持](transparent-data-encryption-byok-azure-sql.md)。|
| <a href="https://aka.ms/managed-instance-failover-groups">异地分布式故障转移组</a> | 将实例的副本保留在另一个区域中，并确保数据在区域性灾难方案中可用。 |
| <a href="https://aka.ms/managed-instance-aadlogins">实例级 Azure AD 服务器主体（登录名）</a> | 使用<a href="https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN FROM EXTERNAL PROVIDER</a>语句创建服务器级登录名。 |
| [事务复制](sql-database-managed-instance-transactional-replication.md) | 将表中的更改复制到托管实例、单一数据库或 SQL Server 实例上放置的其他数据库中，或者在其他托管实例或 SQL Server 实例中更改某些行时更新表。 有关信息，请参阅[在 Azure SQL 数据库托管实例数据库中配置复制](replication-with-sql-database-managed-instance.md)。 |
| 威胁检测 |有关信息，请参阅[在 Azure SQL 数据库托管实例中配置威胁检测](sql-database-managed-instance-threat-detection.md)。|
| 使用托管实例重新创建已删除的数据库 |有关信息，请参阅[在 Azure SQL 托管实例中重新创建已删除的数据库](https://medium.com/azure-sqldb-managed-instance/re-create-dropped-databases-in-azure-sql-managed-instance-dc369ed60266)。|
| &nbsp; |

---

## <a name="fixed-known-issues"></a>修复了已知问题

- **8 月 2019** -托管实例完全支持包含的数据库。

## <a name="updates"></a>更新

有关 SQL 数据库更新和改进的列表，请参阅[Sql 数据库服务更新](https://azure.microsoft.com/updates/?product=sql-database)。

有关所有 Azure 服务的更新和改进，请参阅[服务更新](https://azure.microsoft.com/updates)。

## <a name="contribute-to-content"></a>参与内容制作

若要参与 Azure SQL 数据库文档制作，请参阅[文档参与者指南](https://docs.microsoft.com/contribute/)。
