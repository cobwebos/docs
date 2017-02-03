---
title: "Azure SQL 数据库功能概述 | Microsoft 文档"
description: "此页面概述了 Azure SQL 数据库逻辑服务器和数据库，并且包括了功能支持矩阵和所列出的每项功能的链接。"
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: d1a46fa4-53d2-4d25-a0a7-92e8f9d70828
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 02/01/2017
ms.author: carlrab; jognanay
translationtype: Human Translation
ms.sourcegitcommit: e5b5751facb68ae4a62e3071fe4dfefc02434a9f
ms.openlocfilehash: c153f09741b9b063d67459bbb127f9c4e7942a5b


---
# <a name="azure-sql-database-features"></a>Azure SQL 数据库功能
本主题概述了 Azure SQL 数据库逻辑服务器和数据库，并且包括了功能支持矩阵和所列出的每项功能的链接。 

## <a name="what-is-an-azure-sql-database-logical-server"></a>什么是 Aure SQL 数据库逻辑服务器？
Azure SQL 数据库逻辑服务器充当多个数据库的中心管理点。 在 SQL 数据库中，服务器是一个逻辑构造，它不同于你在本地环境中可能已熟悉的 SQL Server 实例。 具体而言，SQL 数据库服务不保证数据库相对于其逻辑服务器的位置，并且不公开实例级访问权限或功能。 有关 Azure SQL 逻辑服务器的详细信息，请参阅[逻辑服务器](sql-database-server-overview.md)。 

## <a name="what-is-an-azure-sql-database"></a>什么是 Azure SQL 数据库？
Azure SQL 数据库中的每个数据库都与一个逻辑服务器相关联。 数据库可以是：

- 具有其[自己的一组资源](sql-database-what-is-a-dtu.md#what-are-database-transaction-units-dtus) (DTU) 的单一数据库
- [共享一组资源](sql-database-what-is-a-dtu.md#what-are-elastic-database-transaction-units-edtus) (eDTU) 的[数据库池](sql-database-elastic-pool.md)的一部分
- [向外扩展的一组共享数据库](sql-database-elastic-scale-introduction.md#horizontal-and-vertical-scaling)的一部分，可以是单一数据库，也可以是入池数据库
- 参与[多租户 SaaS 设计模式](sql-database-design-patterns-multi-tenancy-saas-applications.md)的一组数据库的一部分，该模式中可以包含单一数据库或池中的数据库（或两者） 

有关 Azure SQL 数据库的详细信息，请参阅 [SQL 数据库](sql-database-overview.md)。

## <a name="what-features-are-supported"></a>支持哪些功能？

下面的表列出了 Azure SQL 数据库和 SQL Server 的主要功能，指定其支持能力，并提供了关于每个平台上该功能的详细信息的链接。 对于 Transact-SQL 功能，请参阅表中适用于该功能类别的链接。 有关不支持某些类型的功能的原因，另请参阅 [Azure SQL 数据库 Transact-SQL 差异](sql-database-transact-sql-information.md)来了解详细背景。

我们会继续向 V12 添加功能。 因此，我们鼓励访问我们的针对 Azure 的服务更新网页并使用其筛选器：

* 筛选为 [SQL 数据库服务](https://azure.microsoft.com/updates/?service=sql-database)。
* 筛选为针对 SQL 数据库功能的正式发布版本 [(GA) 公告](http://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability)。

> [!TIP]
> 若要测试现有数据库与 Azure SQL 数据库之间的兼容性，请参阅[验证 Azure SQL 数据库兼容性](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)。
>

| **功能** | **SQL Server** | **Azure SQL 数据库** | 
| --- | :---: | :---: | 
| 活动异地复制 | 不支持 - 请参阅 [AlwaysOn 可用性组](https://msdn.microsoft.com/library/hh510230.aspx) | [支持](sql-database-geo-replication-overview.md)
| 始终加密 | [支持](https://msdn.microsoft.com/library/mt163865.aspx) | [支持](sql-database-always-encrypted.md) |
| AlwaysOn 可用性组 | [支持](https://msdn.microsoft.com/library/hh510230.aspx) | 不支持 - 请参阅[活动异地复制](sql-database-geo-replication-overview.md) |
| 附加数据库 | [支持](https://msdn.microsoft.com/library/ms190209.aspx) | 不支持 |
| 应用程序角色 | [支持](https://msdn.microsoft.com/library/ms190998.aspx) | [支持](https://msdn.microsoft.com/library/ms190998.aspx) |
| 自动缩放 | 不支持 | [支持](sql-database-scale-up.md) |
| Azure Active Directory | 不支持 | [支持](sql-database-aad-authentication.md) |
| Azure 数据工厂 | 不支持 - 请参阅 [SQL Server Integration Services (SSIS)](https://msdn.microsoft.com/library/ms141026.aspx) | [支持](https://azure.microsoft.com/services/data-factory/) |
| 审核 | [支持](https://msdn.microsoft.com/library/cc280386.aspx) | [支持](sql-database-auditing-get-started.md) |
| BACPAC 文件（导出） | [支持](https://msdn.microsoft.com/library/hh213241.aspx) | [支持](sql-database-export.md) |
| BACPAC 文件（导入） | [支持](https://msdn.microsoft.com/library/hh710052.aspx) | [支持](sql-database-import.md) |
| BACKUP 和 RESTORE 语句 | [支持](https://msdn.microsoft.com/library/ff848768.aspx) | 不支持 |
| 内置函数 | [支持](https://msdn.microsoft.com/library/ms174318.aspx) | [大多数](https://msdn.microsoft.com/library/ms174318.aspx) |
| 更改数据捕获 | [支持](https://msdn.microsoft.com/library/cc645937.aspx) | 不支持 |
| 更改跟踪 | [支持](https://msdn.microsoft.com/library/bb933875.aspx) | [支持](https://msdn.microsoft.com/library/bb933875.aspx) |
| 排序规则语句 | [支持](https://msdn.microsoft.com/library/ff848763.aspx) | [支持](https://msdn.microsoft.com/library/ff848763.aspx) |
| 列存储索引 | [支持](https://msdn.microsoft.com/library/gg492088.aspx) | [仅限 Premium Edition](https://msdn.microsoft.com/library/gg492088.aspx) |
| 公共语言运行时 (CLR) | [支持](https://msdn.microsoft.com/library/ms131102.aspx) | 不支持 |
| 包含的数据库 | [支持](https://msdn.microsoft.com/library/ff929071.aspx) | 内置 |
| 包含的用户 | [支持](https://msdn.microsoft.com/library/ff929188.aspx) | [支持](sql-database-manage-logins.md#non-administrator-users) |
| 流控制语言关键字 | [支持](https://msdn.microsoft.com/library/ms174290.aspx) | [支持](https://msdn.microsoft.com/library/ms174290.aspx) |
| 跨数据库查询 | [支持](https://msdn.microsoft.com/library/dn584627.aspx) | [弹性查询](sql-database-elastic-query-overview.md) |
| 游标 | [支持](https://msdn.microsoft.com/library/ms181441.aspx) | [支持](https://msdn.microsoft.com/library/ms181441.aspx) | 
| 数据压缩 | [支持](https://msdn.microsoft.com/library/cc280449.aspx) | [支持](https://msdn.microsoft.com/library/cc280449.aspx) |
| 数据库备份 | [为用户公开](https://msdn.microsoft.com/library/ms187048.aspx) | [内置](sql-database-automated-backups.md) |
| 数据库邮件 | [支持](https://msdn.microsoft.com/library/ms189635.aspx) | 不支持 |
| 数据库镜像 | [支持](https://msdn.microsoft.com/library/ms189852.aspx) | 不支持 |
| 数据库配置选项 | [支持](https://msdn.microsoft.com/library/mt629158.aspx) | [支持](https://msdn.microsoft.com/library/mt629158.aspx) |
| Data Quality Services (DQS) | [支持](https://msdn.microsoft.com/library/ff877925.aspx) | 不支持 |
| 数据库快照 | [支持](https://msdn.microsoft.com/library/ms175158.aspx) | 不支持 |
| 数据类型 | [支持](https://msdn.microsoft.com/library/ms187752.aspx) | [支持](https://msdn.microsoft.com/library/ms187752.aspx) |  
| DBCC 语句 | [全部](https://msdn.microsoft.com/library/ms188796.aspx) | [部分](https://msdn.microsoft.com/library/ms188796.aspx) |
| DDL 语句 | [支持](https://msdn.microsoft.com/library/ff848799.aspx) | [大多数](https://msdn.microsoft.com/library/ff848799.aspx)
| DDL 触发器 | [支持](https://msdn.microsoft.com/library/ms175941.aspx) | [仅限数据库](https://msdn.microsoft.com/library/ms175941.aspx) |
| 分布式事务 | [MS DTC](https://msdn.microsoft.com/library/ms131665.aspx) | 仅限有限的 SQL 数据库内方案 |
| DML 语句 | [支持](https://msdn.microsoft.com/library/ff848766.aspx) | [大多数](https://msdn.microsoft.com/library/ff848766.aspx) |
| DML 触发器 | [支持](https://msdn.microsoft.com/library/ms178110.aspx) | [支持](https://msdn.microsoft.com/library/ms178110.aspx) |
| DMV | [全部](https://msdn.microsoft.com/library/ms188754.aspx) | [部分](https://msdn.microsoft.com/library/ms188754.aspx) |
| 弹性池 | 不支持 | [支持](sql-database-elastic-pool.md) |
| 弹性作业 | 不支持 - 请参阅 [SQL Server 代理](https://msdn.microsoft.com/library/ms189237.aspx) | [支持](sql-database-elastic-jobs-getting-started.md) | 
| 弹性查询 | 不支持 - 请参阅[跨数据库查询](https://msdn.microsoft.com/library/dn584627.aspx) | [支持](sql-database-elastic-query-overview.md) |
| 事件通知 | [支持](https://msdn.microsoft.com/library/ms186376.aspx) | [支持](sql-database-insights-alerts-portal.md) |
| 表达式 | [支持](https://msdn.microsoft.com/library/ms190286.aspx) | [支持](https://msdn.microsoft.com/library/ms190286.aspx) |
| 扩展的事件 | [支持](https://msdn.microsoft.com/library/bb630282.aspx) | [部分](sql-database-xevent-db-diff-from-svr.md) |
| 扩展的存储过程 | [支持](https://msdn.microsoft.com/library/ms164627.aspx) | 不支持 |
| 文件组 | [支持](https://msdn.microsoft.com/library/ms189563.aspx#Anchor_2) | [仅限主文件组](https://msdn.microsoft.com/library/ms189563.aspx#Anchor_2) |
| 文件流 | [支持](https://msdn.microsoft.com/library/gg471497.aspx) | 不支持 |
| 全文搜索 | [支持](https://msdn.microsoft.com/library/ms142571.aspx) | [不支持第三方断字符](https://msdn.microsoft.com/library/ms142571.aspx) |
| 函数 | [支持](https://msdn.microsoft.com/library/ms174318.aspx) | [大多数](https://msdn.microsoft.com/library/ms174318.aspx) |
| 内存中优化 | [支持](https://msdn.microsoft.com/library/dn133186.aspx) | [仅限 Premium Edition](https://msdn.microsoft.com/library/dn133186.aspx) |
| 作业 | [SQL Server 代理](https://msdn.microsoft.com/library/ms189237.aspx) | [支持](sql-database-elastic-jobs-getting-started.md) |
| JSON 数据支持 | [支持](https://msdn.microsoft.com/library/dn921897.aspx) | [支持](sql-database-json-features.md) |
| 语言元素 | [支持](https://msdn.microsoft.com/library/ff848807.aspx) | [大多数](https://msdn.microsoft.com/library/ff848807.aspx) |  
| 链接的服务器 | [支持](https://msdn.microsoft.com/library/ms188279.aspx) | 不支持 - 请参阅[弹性查询](sql-database-elastic-query-horizontal-partitioning.md) |
| 日志传送 | [支持](https://msdn.microsoft.com/library/ms187103.aspx) | 不支持 - 请参阅[活动异地复制](sql-database-geo-replication-overview.md) |
| 管理命令 | [支持](https://msdn.microsoft.com/library/ms190286.aspx)| [不支持](https://msdn.microsoft.com/library/ms190286.aspx) |
| Master Data Services (MDS) | [支持](https://msdn.microsoft.com/library/ff487003.aspx) | 不支持 |
| 批量导入中的最小日志记录 | [支持](https://msdn.microsoft.com/library/ms190422.aspx) | 不支持 |
| 修改系统数据 | [支持](https://msdn.microsoft.com/library/ms178028.aspx) | 不支持 |
| 联机索引操作 | [支持](https://msdn.microsoft.com/library/ms177442.aspx) | [按服务层限制的事务大小](https://msdn.microsoft.com/library/ms177442.aspx) |
| 运算符 | [支持](https://msdn.microsoft.com/library/ms174986.aspx) | [大多数](https://msdn.microsoft.com/library/ms174986.aspx) |
| 数据库时间点还原 | [支持](https://msdn.microsoft.com/library/ms179451.aspx) | [支持](sql-database-recovery-using-backups.md#point-in-time-restore) |
| Polybase | [支持](https://msdn.microsoft.com/library/mt143171.aspx) | [不支持]
| 基于策略的管理 | [支持](https://msdn.microsoft.com/library/bb510667.aspx) | 不支持 |
| 谓词 | [支持](https://msdn.microsoft.com/library/ms189523.aspx) | [大多数](https://msdn.microsoft.com/library/ms189523.aspx)
| 资源调控器 | [支持](https://msdn.microsoft.com/library/bb933866.aspx) | [内置](sql-database-service-tiers.md) |
| 从备份还原数据库 | [支持](https://msdn.microsoft.com/library/ms187048.aspx#anchor_6) | [仅限从内置备份还原](sql-database-recovery-using-backups.md) |
| 行级安全性 | [支持](https://msdn.microsoft.com/library/dn765131.aspx) | [支持](https://msdn.microsoft.com/library/dn765131.aspx) |
| 安全语句 | [支持](https://msdn.microsoft.com/library/ff848791.aspx) | [部分](https://msdn.microsoft.com/library/ff848791.aspx) |
| 语义搜索 | [支持](https://msdn.microsoft.com/library/gg492075.aspx) | 不支持 |
| 序列号 | [支持](https://msdn.microsoft.com/library/ff878058.aspx) | [支持](https://msdn.microsoft.com/library/ff878058.aspx) |
| Service Broker | [支持](https://msdn.microsoft.com/library/bb522893.aspx) | 不支持 |
| 服务器配置选项 | [支持](https://msdn.microsoft.com/library/ms189631.aspx) | 不支持 - 请参阅[数据库配置选项](https://msdn.microsoft.com/library/mt629158.aspx) |
| SET 语句 | [支持](https://msdn.microsoft.com/library/ms190356.aspx) | [大多数](https://msdn.microsoft.com/library/ms190356.aspx) 
| 空间 | [支持](https://msdn.microsoft.com/library/bb933790.aspx) | [支持](https://msdn.microsoft.com/library/bb933790.aspx) |
| SQL Server 代理 | [支持](https://msdn.microsoft.com/library/ms189237.aspx) | 不支持 - 请参阅[弹性作业](sql-database-elastic-jobs-getting-started.md) |
| SQL Server Analysis Services (SSAS) | [支持](https://msdn.microsoft.com/library/bb522607.aspx) | 不支持 - 请参阅 [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) |
| SQL Server Integration Services (SSIS) | [支持](https://msdn.microsoft.com/library/ms141026.aspx) | 不支持 - 请参阅 [Azure 数据工厂](https://azure.microsoft.com/services/data-factory/) |
| SQL Server PowerShell | [支持](https://msdn.microsoft.com/library/hh245198.aspx) | [支持](https://msdn.microsoft.com/library/hh245198.aspx) |
| SQL Server Profiler | [支持](https://msdn.microsoft.com/library/ms181091.aspx) | 不支持 - 请参阅[扩展的事件](https://msdn.microsoft.com/library/ms181091.aspx) |
| SQL Server 复制 | [支持](https://msdn.microsoft.com/library/ms151198.aspx) | [仅限事务性和快照复制订户](sql-database-cloud-migrate-compatible-using-transactional-replication.md) |
| SQL Server Reporting Services (SSRS) | [支持](https://msdn.microsoft.com/library/ms159106.aspx) | 不支持 |
| 存储过程 | [支持](https://msdn.microsoft.com/library/ms190782.aspx) | [支持](https://msdn.microsoft.com/library/ms190782.aspx) |
| 系统存储函数 | [支持](https://msdn.microsoft.com/library/ff848780.aspx) | [部分](https://msdn.microsoft.com/library/ff848780.aspx) |
| 系统存储过程 | [支持](https://msdn.microsoft.com/library/ms187961.aspx) | [部分](https://msdn.microsoft.com/library/ms187961.aspx) |
| 系统表 | [支持](https://msdn.microsoft.com/library/ms179932.aspx) | [部分](https://msdn.microsoft.com/library/ms179932.aspx) |
| 系统视图 | [支持](https://msdn.microsoft.com/library/ms177862.aspx) | [部分](https://msdn.microsoft.com/library/ms177862.aspx)
| 表分区 | [支持](https://msdn.microsoft.com/library/ms190787.aspx) | [仅限主文件组](https://msdn.microsoft.com/library/ms190787.aspx) |
| 临时表 | [本地和全局](https://msdn.microsoft.com/library/ms174979.aspx#Anchor_4) | [仅限本地](https://msdn.microsoft.com/library/ms174979.aspx#Anchor_4) |
| 临时表 | [支持](https://msdn.microsoft.com/library/dn935015.aspx) | [支持](sql-database-temporal-tables.md) |
| 事务语句 | [支持](https://msdn.microsoft.com/library/ms174377.aspx) | [支持](https://msdn.microsoft.com/library/ms174377.aspx) |
| 变量 | [支持](https://msdn.microsoft.com/library/ff848809.aspx) | | [支持](https://msdn.microsoft.com/library/ff848809.aspx) | 
| 透明数据加密 (TDE)  | [支持](https://msdn.microsoft.com/library/bb934049.aspx) | [支持](https://msdn.microsoft.com/dn948096.aspx) |
| Windows Server 故障转移群集 | [支持](https://msdn.microsoft.com/library/hh270278.aspx) | 不支持 - 请参阅[活动异地复制](sql-database-geo-replication-overview.md) |
| XML 索引 | [支持](http://msdn.microsoft.com/library/bb934097.aspx) | [支持](http://msdn.microsoft.com/library/bb934097.aspx) |
| XML 语句 | [支持](https://msdn.microsoft.com/library/ff848798.aspx) | [支持](https://msdn.microsoft.com/library/ff848798.aspx) |

## <a name="next-steps"></a>后续步骤

- 有关 Azure SQL 数据库服务的信息，请参阅[什么是 SQL 数据库？](sql-database-technical-overview.md)
- 有关 Azure SQL 逻辑服务器的概述，请参阅 [SQL 数据库逻辑服务器概述](sql-database-server-overview.md)
- 有关 Azure SQL 数据库的概述，请参阅 [SQL 数据库概述](sql-database-overview.md)
- 有关 Transact-SQL 支持和差异的信息，请参阅 [Azure SQL 数据库 Transact-SQL 差异](sql-database-transact-sql-information.md)。
- 了解基于**服务层**的具体资源配额和限制的信息。 有关服务层的概述，请参阅 [SQL 数据库服务层](sql-database-service-tiers.md)。
- 有关安全概述，请参阅 [Azure SQL 数据库安全概述](sql-database-security-overview.md)。
- 有关驱动程序可用性和 SQL 数据库支持的信息，请参阅[用于 SQL 数据库和 SQL Server 的连接库](sql-database-libraries.md)。



<!--HONumber=Dec16_HO4-->


