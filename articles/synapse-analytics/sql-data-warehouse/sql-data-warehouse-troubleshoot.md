---
title: Synapse SQL 故障排除
description: Azure Synapse Analytics 中的 Synapse SQL 故障排除。
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2019
ms.author: kevin
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 44755ab13b95db1ffec8183d00a4054e291c5a50
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2020
ms.locfileid: "86039018"
---
# <a name="troubleshooting-synapse-sql-in-azure-synapse-analytics"></a>Azure Synapse Analytics 中的 Synapse SQL 故障排除

本文列出了 Synapse SQL 中的常见故障排除问题。

## <a name="connecting"></a>Connecting

| 问题                                                        | 解决方法                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| 用户 “NT AUTHORITY\ANONYMOUS LOGON” 登录失败。 （Microsoft SQL Server，错误：18456） | 当 Azure AD 用户尝试连接到 master 数据库，但 master 中没有用户时，会发生此错误。  若要纠正此问题，可以在连接时指定要连接到的 SQL 池，也可以将用户添加到 master 数据库。  有关更多详细信息，请参阅 [Security overview](sql-data-warehouse-overview-manage-security.md)（安全性概述）一文。 |
| 服务器主体“MyUserName”无法在当前的安全上下文下访问数据库“master”。 无法打开用户默认数据库。 登录失败。 用户“MyUserName”的登录失败。 （Microsoft SQL Server，错误：916） | 当 Azure AD 用户尝试连接到 master 数据库，但 master 中没有用户时，会发生此错误。  若要纠正此问题，可以在连接时指定要连接到的 SQL 池，也可以将用户添加到 master 数据库。  有关更多详细信息，请参阅 [Security overview](sql-data-warehouse-overview-manage-security.md)（安全性概述）一文。 |
| CTAIP 错误                                                  | 如果已在 SQL 数据库 master 数据库中创建登录名，但该登录名在特定 SQL 数据库中不存在，则会出现此错误。  如果遇到此错误，请参阅[安全性概述](sql-data-warehouse-overview-manage-security.md)一文。  本文介绍如何在 master 数据库中创建登录名和用户，以及如何在 SQL 数据库中创建用户。 |
| 被防火墙阻止                                          | 为了确保只有已知的 IP 地址可以访问数据库，SQL 池受到防火墙保护。 默认情况下，防火墙是安全的，这意味着，需要显式启用单个 IP 地址或地址范围才能进行连接。  若要配置防火墙的访问权限，请遵循[预配说明](create-data-warehouse-portal.md)中的[为客户端 IP 配置服务器防火墙访问权限](create-data-warehouse-portal.md)中所述的步骤。 |
| 无法使用工具或驱动程序进行连接                           | Synapse SQL 池建议使用 [SSMS](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)、[用于 Visual Studio 的 SSDT](sql-data-warehouse-install-visual-studio.md) 或 [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 来查询数据。 有关驱动程序以及如何连接到 Azure Synapse 的详细信息，请参阅 [Azure Synapse 驱动程序](sql-data-warehouse-connection-strings.md)和[连接到 Azure Synapse](sql-data-warehouse-connect-overview.md)这两篇文章。 |

## <a name="tools"></a>工具

| 问题                                                        | 解决方法                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Visual Studio 对象资源管理器缺少 Azure AD 用户           | 这是一个已知问题。  解决方法是在 [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 中查看这些用户。  若要详细了解如何将 Azure Active Directory 用于 Synapse SQL 池，请参阅[向 Azure Synapse 进行身份验证](sql-data-warehouse-authentication.md)。 |
| 使用脚本向导进行手动脚本编写或通过 SSMS 进行连接时出现缓慢、未响应或产生错误的情况 | 请确保已在 master 数据库中创建用户。 在脚本选项中，同时需确保引擎版本设置为“Microsoft Azure SQL 数据仓库版本”，且引擎类型为“Microsoft Azure SQL 数据库”。 |
| 在 SSMS 中生成脚本失败                               | 如果将“为从属对象生成脚本”选项设置为“True”，则为 Synapse SQL 池生成脚本失败。 解决方法是，用户必须手动转到“工具”->“选项”->“SQL Server 对象资源管理器”->“为从属选项生成脚本”并设置为 false |

## <a name="performance"></a>性能

| 问题                                                        | 解决方法                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| 查询性能故障排除                            | 如果要尝试对特定查询进行故障排除，请从 [Learning how to monitor your queries](sql-data-warehouse-manage-monitor.md#monitor-query-execution)（学习如何监视查询）开始。 |
| TempDB 空间问题 | [监视 TempDB](sql-data-warehouse-manage-monitor.md#monitor-tempdb) 空间使用情况。  用尽 TempDB 空间的常见原因包括：<br>- 分配给查询的资源不足，从而导致数据溢出到 TempDB。  请参阅[工作负荷管理](resource-classes-for-workload-management.md) <br>- 统计信息缺失或过期，从而导致数据移动过多。  有关如何创建统计信息的详细信息，请参阅[维护表的统计信息](sql-data-warehouse-tables-statistics.md)<br>- TempDB 空间按服务级别进行分配。  [将 SQL 池扩展](sql-data-warehouse-manage-compute-overview.md#scaling-compute)到更高 DWU 设置会分配更多 TempDB 空间。|
| 查询性能和计划不佳通常是由于缺少统计信息 | 性能不佳的最常见原因是缺少数据表的统计信息。  有关如何创建统计信息以及统计信息为何对性能至关重要的详细信息，请参阅[维护表的统计信息](sql-data-warehouse-tables-statistics.md)。 |
| 低并发性/查询排队                             | 若要了解如何利用并发性平衡内存分配，了解[工作负荷管理](resource-classes-for-workload-management.md)很重要。 |
| 如何实施最佳做法                              | 开始了解如何提高查询性能的最好地方是 [Synapse SQL 池最佳做法](sql-data-warehouse-best-practices.md)一文。 |
| 如何通过缩放提高性能                      | 有时，改进性能的解决方案是只需通过[缩放 SQL 池](sql-data-warehouse-manage-compute-overview.md)来提升查询的计算能力。 |
| 由于索引质量不佳导致查询性能不佳     | 有时，由于[列存储索引质量不佳](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality)，查询速度可能会减慢。  有关详细信息以及如何[重建索引以提高段质量](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality)，请参阅本文。 |

## <a name="system-management"></a>系统管理

| 问题                                                        | 解决方法                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| 消息 40847：无法执行操作，因为服务器将超过 45000 这一允许的数据库事务单元配额。 | 请减少要尝试创建的数据库的 [DWU](what-is-a-data-warehouse-unit-dwu-cdwu.md)，或者[请求增加配额](sql-data-warehouse-get-started-create-support-ticket.md)。 |
| 调查空间使用率                              | 请参阅[表大小](sql-data-warehouse-tables-overview.md#table-size-queries)，了解系统的空间使用率。 |
| 管理表的帮助                                    | 有关管理表的帮助，请参阅[表概述](sql-data-warehouse-tables-overview.md)一文。  本文还包含指向更详细主题的链接，如[表数据类型](sql-data-warehouse-tables-data-types.md)、[分布表](sql-data-warehouse-tables-distribute.md)、[为表编制索引](sql-data-warehouse-tables-index.md)、[将表分区](sql-data-warehouse-tables-partition.md)、[维护表统计信息](sql-data-warehouse-tables-statistics.md)和[临时表](sql-data-warehouse-tables-temporary.md)。 |
| 在 Azure 门户中，透明数据加密 (TDE) 进度栏不更新 | 可以通过[PowerShell](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)查看 TDE 的状态。 |

## <a name="differences-from-sql-database"></a>与 SQL 数据库的差异

| 问题                                 | 解决方法                                                   |
| :------------------------------------ | :----------------------------------------------------------- |
| 不支持的 SQL 数据库功能     | 请参阅[不支持的表功能](sql-data-warehouse-tables-overview.md#unsupported-table-features)。 |
| 不支持的 SQL 数据库数据类型   | 请参阅[不支持的数据类型](sql-data-warehouse-tables-data-types.md#identify-unsupported-data-types)。        |
| DELETE 和 UPDATE 限制         | 请参阅 [UPDATE 解决方法](sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements)、[DELETE 解决方法](sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements)和[使用 CTAS 解决不支持的 UPDATE 和 DELETE 语法](sql-data-warehouse-develop-ctas.md)。 |
| 不支持 MERGE 语句      | 请参阅 [MERGE 解决方法](sql-data-warehouse-develop-ctas.md#replace-merge-statements)。                  |
| 存储过程限制          | 请参阅[存储过程限制](sql-data-warehouse-develop-stored-procedures.md#limitations)，了解存储过程的一些限制。 |
| UDF 不支持 SELECT 语句 | 这是 UDF 的当前一项限制。  有关我们支持的语法，请参阅 [CREATE FUNCTION](/sql/t-sql/statements/create-function-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。 |

## <a name="next-steps"></a>后续步骤

如需查找问题的解决方案的更多帮助，下面是可以尝试的一些其他资源。

* [博客](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
* [功能请求](https://feedback.azure.com/forums/307516-sql-data-warehouse)
* [视频](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
* [创建支持票证](sql-data-warehouse-get-started-create-support-ticket.md)
* [Microsoft Q&A 问题页面](https://docs.microsoft.com/answers/topics/azure-synapse-analytics.html)
* [Stackoverflow 论坛](https://stackoverflow.com/questions/tagged/azure-sqldw)
* [Twitter](https://twitter.com/hashtag/SQLDW)
