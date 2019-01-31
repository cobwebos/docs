---
title: Azure SQL 数据仓库发行说明（2018 年 10 月）| Microsoft Docs
description: Azure SQL 数据仓库发行说明。
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 12/04/2018
ms.author: mausher
ms.reviewer: twounder
ms.openlocfilehash: 81096eeb1edcd6681bdc887d267ee477d78a8578
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2019
ms.locfileid: "55472076"
---
# <a name="whats-new-in-azure-sql-data-warehouse-october-2018"></a>Azure SQL 数据仓库中的新增功能 2018 年 10 月
Azure SQL 数据仓库持续得到改进。 本文介绍了 2018 年 10 月发行的版本中所引入的新功能和所做的更改。

## <a name="devops-for-data-warehousing"></a>数据仓库的 DevOps
SQL 数据仓库 (SQL DW) 的高要求功能现可供预览，它支持 Visual Studio 中的 SQL Server Data Tools (SSDT)！ 开发团队现可通过单一的版本控制基本代码进行协作，并快速将更改部署到世界上的任何实例。 是否有兴趣加入？ 此功能目前以预览版提供！ 可以通过访问 [SQL 数据仓库 Visual Studio SQL Server Data Tools (SSDT) - 预览注册表单](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR4-brmKy3TZOjoktwuHd7S1UODkwQ1lVMEw1NDBGRjNLRDNWOFlQRUpIRi4u)进行注册。 鉴于需求量很大，我们正在对预览进行验收，以确保为客户提供最佳体验。 注册后，我们的目标是在七个工作日内确认你的状态。

## <a name="row-level-security-generally-available"></a>行级别安全性已公开发布
Azure SQL 数据仓库 (SQL DW) 现在支持行级别安全性 (RLS)，增加了保护敏感数据的强大功能。 通过引入 RLS，可以实现安全策略以控制对表中行的访问（例如哪些人员可以访问哪些行）。 RLS 支持这种精确的访问控制，而无需重新设计数据仓库。 由于访问限制逻辑位于数据库层本身，而不是脱离另一应用程序中的数据，因此 RLS 简化了整体安全模型。 RLS 还消除了引入视图以筛选出用于访问控制管理的行的需求。 我们为所有客户提供此企业级安全功能，而用户无需额外付费。

## <a name="advanced-advisors"></a>高级顾问
其他数据仓库建议和指标使 Azure SQL 数据仓库 (SQL DW) 的高级优化变得更加简单。 Azure 顾问还提供其他高级性能建议供你参考，包括：
1.  自适应缓存 - 在应进行缩放以优化缓存利用率时通知用户。
2.  表分配 - 确定何时复制表，以减少数据移动，提高工作负载性能。 
3.  Tempdb - 了解何时缩放和配置资源类，以减少 tempdb 争用。

数据仓库指标与 [Azure Monitor](https://azure.microsoft.com/blog/enhanced-capabilities-to-monitor-manage-and-integrate-sql-data-warehouse-in-the-azure-portal/) 深度集成，包括增强的可自定义监视图表，以在概览边栏选项卡中实现近实时指标。 监视使用情况或验证和应用数据仓库建议时，无需离开数据仓库概览边栏选项卡即可访问 Azure Monitor 指标。 此外，推出了新的指标（例如 tempdb 和自适应缓存使用率）作为性能建议的补充。

## <a name="advanced-tuning-with-integrated-advisors"></a>使用集成顾问进行高级优化
通过其他数据仓库建议和指标，以及重新设计的提供 Azure Advisor 和 Azure Monitor 的集成体验的门户概述边栏选项卡，Azure SQL 数据仓库 (SQL DW) 的高级优化变得更加简单了。

## <a name="accelerated-database-recovery-adr"></a>加速的数据库恢复 (ADR)
Azure SQL 数据仓库加速的数据库恢复 (ADR) 现在处于公共预览状态。 ADR 是一项新的 SQL Server 引擎功能，通过完全从头开始重新设计当前的恢复过程，极大地提高了数据库可用性（尤其是在存在长时间运行事务的情况下）。 ADR 的主要优点是快速且一致的数据库恢复和即时事务回滚。

## <a name="azure-monitor-diagnostics-logs"></a>Azure Monitor 诊断日志
SQL 数据仓库 (SQL DW) 现可通过直接与 Azure Monitor 诊断日志集成来增强对分析工作负载的见解。 这项新功能使开发者可分析长时段内的工作负载行为，并针对查询优化或容量管理作出明智决策。 我们现在通过 [Azure Monitor 诊断日志](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json#logs)引入了外部日志记录过程，这些日志提供了有关数据仓库工作负载的其他见解。 只需单击一下按钮，即可使用 [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-queries) 为历史查询性能故障排除功能配置诊断日志。 Azure Monitor 诊断日志支持通过将日志保存到存储帐户以进行审核来自定义保留期，将日志流式传输到事件中心以获得近实时遥测见解，以及通过日志查询使用 Log Analytics 来分析日志。 诊断日志由数据仓库的遥测视图组成，等效于 SQL 数据仓库最常用的性能故障排除 DMV。 对于此初始版本，我们为以下系统动态管理视图启用了视图：

- [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql)
- [sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql)
- [sys.dm_pdw_dms_workers](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql)
- [sys.dm_pdw_waits](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql)
- [sys.dm_pdw_sql_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql)

## <a name="columnstore-memory-management"></a>列存储内存管理
随着压缩列存储行组数量的增加，管理这些行组的内部列段元数据所需的内存也会增加。  因此，针对某些列存储动态管理视图 (DMV) 执行的查询和查询性能可能会降级。  此版本中的改进旨在优化这些情况下的内部元数据大小，从而改善此类查询的体验并提高此类查询的性能。 

## <a name="azure-data-lake-storage-gen2-integration-ga"></a>Azure Data Lake Storage Gen2 集成 (GA)
Azure SQL 数据仓库 (SQL DW) 现在已与 Azure Data Lake Storage Gen2 进行了本机集成。 客户现在使用外部表将数据从 ABFS 加载到 SQL DW。 此功能使客户能够与 Data Lake Storage Gen2 中的 data lake 集成。 

## <a name="bug-fixes"></a>Bug 修复

| 标题 | 说明 |
|:---|:---|
| 针对 DW2000 及以上数据仓库小型资源类中的 CETAS 到 Parquet 故障 | 此修补程序在“Create External Table As”到“Parquet”代码路径中正确标识了空引用。 |
|在某些 CTAS 操作中，标识列值可能会丢失 | CTAS 到其他表时，标识列的值可能不会保留。 在博客中报告：[https://blog.westmonroepartners.com/azure-sql-dw-identity-column-bugs/](https://blog.westmonroepartners.com/azure-sql-dw-identity-column-bugs/)。 |
| 在某些情况下，在查询仍在运行时终止会话会产生内部错误 | 如果在查询仍在运行时终止会话，则此修补程序会触发 InvalidOperationException。 |
| （部署于 2018 年 11 月）尝试使用 Polybase 从 ADLS (Gen1) 加载多个小文件时，客户体验的性能不佳。 | 在 AAD 安全令牌验证期间，系统性能出现瓶颈。 通过启用安全令牌的缓存来缓解性能问题。 |


## <a name="next-steps"></a>后续步骤
对 SQL 数据仓库有了初步的认识后，请了解如何快速[创建 SQL 数据仓库][create a SQL Data Warehouse]。 如果不熟悉 Azure，在遇到新术语时，可以参考 [Azure 术语表][Azure glossary]。 或者，查看一下以下一些其他 SQL 数据仓库资源。  

* [客户成功案例]
* [博客]
* [功能请求]
* [视频]
* [客户顾问团队博客]
* [堆栈溢出论坛]
* [Twitter]


[博客]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[客户顾问团队博客]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[客户成功案例]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[功能请求]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[堆栈溢出论坛]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[视频]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md
