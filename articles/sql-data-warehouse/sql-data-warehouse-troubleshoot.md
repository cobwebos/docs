---
title: 排查 Azure SQL 数据仓库问题 | Microsoft Docs
description: 排查 Azure SQL 数据仓库问题。
services: sql-data-warehouse
documentationcenter: NA
author: kevinvngo
manager: jhubbard
editor: ''
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 03/15/2018
ms.author: kevin;barbkess
ms.openlocfilehash: 0829d448e8b925d0dcc032ed143d8fff42ab1b69
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/17/2018
---
# <a name="troubleshooting-azure-sql-data-warehouse"></a>排查 Azure SQL 数据仓库问题
本主题列出了客户较常见的一些故障排除问题。

## <a name="connecting"></a>连接
| 问题 | 解决方法 |
|:--- |:--- |
| 用户 “NT AUTHORITY\ANONYMOUS LOGON” 登录失败。 (Microsoft SQL Server，错误: 18456) |当 AAD 用户尝试连接到 master 数据库，但 master 中没有用户时，会发生此错误。  若要纠正此问题，可以在连接时指定要连接到的 SQL 数据仓库，也可以将用户添加到 master 数据库。  有关详细信息，请参阅[安全性概述][Security overview]一文。 |
| 服务器主体“MyUserName”无法在当前的安全上下文下访问数据库“master”。 无法打开用户默认数据库。 登录失败。 用户“MyUserName”的登录失败。 (Microsoft SQL Server，错误: 916) |当 AAD 用户尝试连接到 master 数据库，但 master 中没有用户时，会发生此错误。  若要纠正此问题，可以在连接时指定要连接到的 SQL 数据仓库，也可以将用户添加到 master 数据库。  有关详细信息，请参阅[安全性概述][Security overview]一文。 |
| CTAIP 错误 |当登录名已在 SQL Server master 数据库中创建，但未在 SQL 数据仓库数据库中时，可能会出现此错误。  如果遇到此错误，请参阅[安全性概述][Security overview]一文。  本文介绍如何在 master 中创建登录名和用户，以及如何在 SQL 数据仓库数据库中创建用户。 |
| 被防火墙阻止 |为了确保只有已知的 IP 地址可以访问数据库，Azure SQL 数据库受到服务器和数据库级别的防火墙保护。 默认情况下，防火墙是安全的，这意味着，需要显式启用单个 IP 地址或地址范围才能进行连接。  若要配置用于访问的防火墙，请遵循[设置说明][Provisioning instructions]中的[为客户端 IP 配置服务器防火墙访问][Configure server firewall access for your client IP]中的步骤。 |
| 无法使用工具或驱动程序进行连接 |SQL 数据仓库建议使用 [SSMS][SSMS]、[用于 Visual Studio 的 SSDT][SSDT for Visual Studio] 或 [sqlcmd][sqlcmd] 来查询数据。 如需详细了解驱动程序以及如何连接到 SQL 数据仓库，请参阅 [Azure SQL 数据仓库驱动程序][Drivers for Azure SQL Data Warehouse]和[连接到 Azure SQL 数据仓库][Connect to Azure SQL Data Warehouse]这两篇文章。 |

## <a name="tools"></a>工具
| 问题 | 解决方法 |
|:--- |:--- |
| Visual Studio 对象资源管理器缺少 AAD 用户 |这是已知问题。  解决方法是在 [sys.database_principals][sys.database_principals] 中查看这些用户。  要了解有关将 Azure Active Directory 用于 SQL 数据仓库的详细信息，请参阅[向 Azure SQL 数据仓库进行身份验证][Authentication to Azure SQL Data Warehouse]。 |
|使用脚本向导进行手动脚本编写或通过 SSMS 进行连接时出现缓慢、挂起或产生错误的情况| 请确保已在 master 数据库中创建用户。 在脚本选项中，同时需确保引擎版本设置为“Microsoft Azure SQL 数据仓库版本”，且引擎类型为“Microsoft Azure SQL 数据库”。|

## <a name="performance"></a>性能
| 问题 | 解决方法 |
|:--- |:--- |
| 查询性能故障排除 |如果要尝试对特定查询进行故障排除，请从[学习如何监视查询][Learning how to monitor your queries]开始。 |
| 查询性能和计划不佳通常是由于缺少统计信息 |性能不佳的最常见原因是缺少数据表的统计信息。  有关如何创建统计信息以及统计信息为何对性能至关重要的详细信息，请参阅[维护表的统计信息][Statistics]。 |
| 低并发性/查询排队 |若要了解如何利用并发性平衡内存分配，了解[工作负荷管理][Workload management]很重要。 |
| 如何实施最佳做法 |开始了解如何提高查询性能的最好地方是 [SQL 数据仓库最佳实践][SQL Data Warehouse best practices]一文。 |
| 如何通过缩放提高性能 |有时，改进性能的解决方案只需通过[缩放 SQL 数据仓库][Scaling your SQL Data Warehouse]来提升查询的计算能力。 |
| 由于索引质量不佳导致查询性能不佳 |有时，由于[列存储索引质量不佳][Poor columnstore index quality]，查询速度可能会减慢。  有关详细信息以及如何[重建索引以提高段质量][Rebuild indexes to improve segment quality]，请参阅本文。 |

## <a name="system-management"></a>系统管理
| 问题 | 解决方法 |
|:--- |:--- |
| 消息 40847：无法执行操作，因为服务器将超过 45000 这一允许的数据库事务单元配额。 |请减少要尝试创建的数据库的 [DWU][DWU]，或者[请求增加配额][request a quota increase]。 |
| 调查空间使用率 |请参阅[表大小][Table sizes]，了解系统的空间使用率。 |
| 管理表的帮助 |有关管理表的帮助，请参阅[表概述][Overview]一文。  本文还包含指向更详细主题的链接，如[表数据类型][Data types]、[分布表][Distribute]、[为表编制索引][Index]、[将表分区][Partition]、[维护表统计信息][Statistics]和[临时表][Temporary]。 |
|在 Azure 门户中，透明数据加密 (TDE) 进度栏不更新|可以通过 [powershell](/powershell/module/azurerm.sql/get-azurermsqldatabasetransparentdataencryption) 查看 TDE 的状态。|

## <a name="polybase"></a>Polybase
| 问题 | 解决方法 |
|:--- |:--- |
| 由于行大加载失败 |目前 Polybase 不提供大型行支持。  这意味着，如果表包含 VARCHAR(MAX)、NVARCHAR(MAX) 或 VARBINARY(MAX)，将无法使用外部表加载数据。  目前仅支持通过 Azure 数据工厂（带 BCP）、Azure 流分析、SSIS、BCP 或 .NET SQLBulkCopy 类加载大型行。 在未来版本中将添加对大型行的 PolyBase 支持。 |
| 使用 bcp 加载包含 MAX 数据类型的表失败 |有一个已知问题，它要求在某些情况下将 VARCHAR(MAX)、NVARCHAR(MAX) 或 VARBINARY(MAX) 放置在表的末尾。  请尝试将 MAX 列移到表的末尾。 |

## <a name="differences-from-sql-database"></a>与 SQL 数据库的差异
| 问题 | 解决方法 |
|:--- |:--- |
| 不支持的 SQL 数据库功能 |请参阅[不受支持的表功能][Unsupported table features]。 |
| 不支持的 SQL 数据库数据类型 |请参阅[不受支持的数据类型][Unsupported data types]。 |
| DELETE 和 UPDATE 限制 |请参阅 [UPDATE 解决方法][UPDATE workarounds]、[DELETE 解决方法][DELETE workarounds]和[使用 CTAS 解决不支持的 UPDATE 和 DELETE 语法][Using CTAS to work around unsupported UPDATE and DELETE syntax]。 |
| 不支持 MERGE 语句 |请参阅 [MERGE 解决方法][MERGE workarounds]。 |
| 存储过程限制 |请参阅[存储过程限制][Stored procedure limitations]，了解存储过程的一些限制。 |
| UDF 不支持 SELECT 语句 |这是 UDF 的当前一项限制。  有关我们支持的语法，请参阅 [CREATE 函数][CREATE FUNCTION]。 |

## <a name="next-steps"></a>后续步骤
如需查找问题的解决方案的更多帮助，下面是可以尝试的一些其他资源。

* [博客]
* [功能请求]
* [视频]
* [CAT 团队博客]
* [创建支持票证]
* [MSDN 论坛]
* [堆栈溢出论坛]
* [Twitter]

<!--Image references-->

<!--Article references-->
[Security overview]: ./sql-data-warehouse-overview-manage-security.md
[SSMS]: https://msdn.microsoft.com/library/mt238290.aspx
[SSDT for Visual Studio]: ./sql-data-warehouse-install-visual-studio.md
[Drivers for Azure SQL Data Warehouse]: ./sql-data-warehouse-connection-strings.md
[Connect to Azure SQL Data Warehouse]: ./sql-data-warehouse-connect-overview.md
[创建支持票证]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Scaling your SQL Data Warehouse]: ./sql-data-warehouse-manage-compute-overview.md
[DWU]: ./sql-data-warehouse-overview-what-is.md
[request a quota increase]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Learning how to monitor your queries]: ./sql-data-warehouse-manage-monitor.md
[Provisioning instructions]: ./sql-data-warehouse-get-started-provision.md
[Configure server firewall access for your client IP]: ./sql-data-warehouse-get-started-provision.md
[SQL Data Warehouse best practices]: ./sql-data-warehouse-best-practices.md
[Table sizes]: ./sql-data-warehouse-tables-overview.md#table-size-queries
[Unsupported table features]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[Unsupported data types]: ./sql-data-warehouse-tables-data-types.md#unsupported-data-types
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[Poor columnstore index quality]: ./sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[Rebuild indexes to improve segment quality]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Workload management]: ./resource-classes-for-workload-management.md
[Using CTAS to work around unsupported UPDATE and DELETE syntax]: ./sql-data-warehouse-develop-ctas.md#using-ctas-to-work-around-unsupported-features
[UPDATE workarounds]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements
[DELETE workarounds]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements
[MERGE workarounds]: ./sql-data-warehouse-develop-ctas.md#replace-merge-statements
[Stored procedure limitations]: ./sql-data-warehouse-develop-stored-procedures.md#limitations
[Authentication to Azure SQL Data Warehouse]: ./sql-data-warehouse-authentication.md
[Working around the PolyBase UTF-8 requirement]: ./sql-data-warehouse-load-polybase-guide.md#working-around-the-polybase-utf-8-requirement

<!--MSDN references-->
[sys.database_principals]: https://msdn.microsoft.com/library/ms187328.aspx
[CREATE FUNCTION]: https://msdn.microsoft.com/library/mt203952.aspx
[sqlcmd]: https://azure.microsoft.com/en-us/documentation/articles/sql-data-warehouse-get-started-connect-sqlcmd/

<!--Other Web references-->
[博客]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[CAT 团队博客]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[功能请求]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[MSDN 论坛]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse
[堆栈溢出论坛]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[视频]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
