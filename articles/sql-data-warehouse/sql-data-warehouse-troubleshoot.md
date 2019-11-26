---
title: 故障排除
description: 排查 Azure SQL 数据仓库问题。
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 11/25/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: b2a9a7b0b759f5853d83a4b1999887414fd5f430
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483213"
---
# <a name="troubleshooting-azure-sql-data-warehouse"></a>排查 Azure SQL 数据仓库问题
本文列出了常见的故障排除问题。

## <a name="connecting"></a>连接
| 问题                                                        | 分辨率                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| 用户 “NT AUTHORITY\ANONYMOUS LOGON” 登录失败。 (Microsoft SQL Server，错误: 18456) | 当 AAD 用户尝试连接到 master 数据库，但 master 中没有用户时，会发生此错误。  若要纠正此问题，可以在连接时指定要连接到的 SQL 数据仓库，也可以将用户添加到 master 数据库。  有关更多详细信息，请参阅 [Security overview][Security overview]（安全性概述）一文。 |
| 服务器主体“MyUserName”无法在当前的安全上下文下访问数据库“master”。 无法打开用户默认数据库。 登录失败。 用户“MyUserName”的登录失败。 (Microsoft SQL Server，错误: 916) | 当 AAD 用户尝试连接到 master 数据库，但 master 中没有用户时，会发生此错误。  若要纠正此问题，可以在连接时指定要连接到的 SQL 数据仓库，也可以将用户添加到 master 数据库。  有关更多详细信息，请参阅 [Security overview][Security overview]（安全性概述）一文。 |
| CTAIP 错误                                                  | 当登录名已在 SQL Server master 数据库中创建，但未在 SQL 数据仓库数据库中时，可能会出现此错误。  如果遇到此错误，请参阅[安全性概述][Security overview]一文。  本文介绍如何在 master 中创建登录名和用户，以及如何在 SQL 数据仓库数据库中创建用户。 |
| 被防火墙阻止                                          | 为了确保只有已知的 IP 地址可以访问数据库，Azure SQL 数据库受到服务器和数据库级别的防火墙保护。 默认情况下，防火墙是安全的，这意味着，需要显式启用单个 IP 地址或地址范围才能进行连接。  若要配置防火墙的访问权限，请遵循[预配说明][Provisioning instructions]中的[为客户端 IP 配置服务器防火墙访问权限][Configure server firewall access for your client IP]中所述的步骤。 |
| 无法使用工具或驱动程序进行连接                           | SQL Data Warehouse recommends using [SSMS][SSMS], [SSDT for Visual Studio][SSDT for Visual Studio], or [sqlcmd][sqlcmd] to query your data. For more information on drivers and connecting to SQL Data Warehouse, see [Drivers for Azure SQL Data Warehouse][Drivers for Azure SQL Data Warehouse] and [Connect to Azure SQL Data Warehouse][Connect to Azure SQL Data Warehouse] articles. |

## <a name="tools"></a>工具
| 问题                                                        | 分辨率                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Visual Studio 对象资源管理器缺少 AAD 用户           | 这是已知问题。  解决方法是在 [sys.database_principals][sys.database_principals] 中查看这些用户。  要详细了解如何将 Azure Active Directory 用于 SQL 数据仓库，请参阅[向 Azure SQL 数据仓库进行身份验证][Authentication to Azure SQL Data Warehouse]。 |
| Manual scripting, using the scripting wizard, or connecting via SSMS is slow, not responding, or producing errors | 请确保已在 master 数据库中创建用户。 在脚本选项中，同时需确保引擎版本设置为“Microsoft Azure SQL 数据仓库版本”，且引擎类型为“Microsoft Azure SQL 数据库”。 |
| 在 SSMS 中生成脚本失败                               | Generating a script for SQL Data Warehouse fails if the option "Generate script for dependent objects" option is set to "True." 解决方法是，用户必须手动转到“工具”->“选项”->“SQL Server 对象资源管理器”->“为从属选项生成脚本”并设置为 false |

## <a name="performance"></a>性能
| 问题                                                        | 分辨率                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| 查询性能故障排除                            | 如果要尝试对特定查询进行故障排除，请从 [Learning how to monitor your queries][Learning how to monitor your queries]（学习如何监视查询）开始。 |
| TempDB space issues | [Monitor TempDB](sql-data-warehouse-manage-monitor.md#monitor-tempdb) space usage.  Common causes for running out of TempDB space are:<br>- Not enough resources allocated to the query causing data to spill to TempDB.  See [Workload management](resource-classes-for-workload-management.md) <br>- Statistics are missing or out of date causing excessive data movement.  See [Maintaining table statistics][Statistics] for details on how to create statistics<br>- TempDB space is allocated per service level.  [Scaling your SQL Data Warehouse][Scaling your SQL Data Warehouse] to a higher DWU setting allocates more TempDB space.|
| 查询性能和计划不佳通常是由于缺少统计信息 | 性能不佳的最常见原因是缺少数据表的统计信息。  See [Maintaining table statistics][Statistics] for details on how to create statistics and why they are critical to your performance. |
| 低并发性/查询排队                             | 若要了解如何利用并发性平衡内存分配，了解[工作负荷管理][Workload management]很重要。 |
| 如何实施最佳做法                              | 开始了解如何提高查询性能的最好地方是 [SQL 数据仓库最佳实践][SQL Data Warehouse best practices]一文。 |
| 如何通过缩放提高性能                      | 有时，改进性能的解决方案只需通过[缩放 SQL 数据仓库][Scaling your SQL Data Warehouse]来提升查询的计算能力。 |
| 由于索引质量不佳导致查询性能不佳     | Some times queries can slow down because of [Poor columnstore index quality][Poor columnstore index quality].  有关详细信息以及如何[重建索引以提高段质量][Rebuild indexes to improve segment quality]，请参阅本文。 |

## <a name="system-management"></a>系统管理
| 问题                                                        | 分辨率                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| 消息 40847：无法执行操作，因为服务器将超过 45000 这一允许的数据库事务单元配额。 | 请减少要尝试创建的数据库的 [DWU][DWU]，或者[请求增加配额][request a quota increase]。 |
| 调查空间使用率                              | 请参阅[表大小][Table sizes]，了解系统的空间使用率。 |
| 管理表的帮助                                    | See the [Table overview][Overview] article for help with managing your tables.  This article also includes links into more detailed topics like [Table data types][Data types], [Distributing a table][Distribute], [Indexing a table][Index],  [Partitioning a table][Partition], [Maintaining table statistics][Statistics] and [Temporary tables][Temporary]. |
| Transparent data encryption (TDE) progress bar is not updating in the Azure portal | 可以通过 [powershell](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) 查看 TDE 的状态。 |


## <a name="differences-from-sql-database"></a>与 SQL 数据库的差异
| 问题                                 | 分辨率                                                   |
| :------------------------------------ | :----------------------------------------------------------- |
| 不支持的 SQL 数据库功能     | 请参阅[不支持的表功能][Unsupported table features]。 |
| 不支持的 SQL 数据库数据类型   | 请参阅[不支持的数据类型][Unsupported data types]。        |
| DELETE 和 UPDATE 限制         | 请参阅 [UPDATE 解决方法][UPDATE workarounds]、[DELETE 解决方法][DELETE workarounds]和[使用 CTAS 解决不支持的 UPDATE 和 DELETE 语法][Using CTAS to work around unsupported UPDATE and DELETE syntax]。 |
| 不支持 MERGE 语句      | 请参阅 [MERGE 解决方法][MERGE workarounds]。                  |
| 存储过程限制          | 请参阅[存储过程限制][Stored procedure limitations]，了解存储过程的一些限制。 |
| UDF 不支持 SELECT 语句 | 这是 UDF 的当前一项限制。  有关我们支持的语法，请参阅 [CREATE FUNCTION][CREATE FUNCTION]。 |

## <a name="next-steps"></a>后续步骤
如需查找问题的解决方案的更多帮助，下面是可以尝试的一些其他资源。

* [博客]
* [功能请求]
* [视频]
* [CAT 团队博客]
* [创建支持票证]
* [MSDN 论坛]
* [Stackoverflow 论坛]
* [Twitter]

<!--Image references-->

<!--Article references-->
[Security overview]: sql-data-warehouse-overview-manage-security.md
[SSMS]: /sql/ssms/download-sql-server-management-studio-ssms
[SSDT for Visual Studio]: sql-data-warehouse-install-visual-studio.md
[Drivers for Azure SQL Data Warehouse]: sql-data-warehouse-connection-strings.md
[Connect to Azure SQL Data Warehouse]: sql-data-warehouse-connect-overview.md
[创建支持票证]: sql-data-warehouse-get-started-create-support-ticket.md
[Scaling your SQL Data Warehouse]: sql-data-warehouse-manage-compute-overview.md
[DWU]: sql-data-warehouse-overview-what-is.md
[request a quota increase]: sql-data-warehouse-get-started-create-support-ticket.md
[Learning how to monitor your queries]: sql-data-warehouse-manage-monitor.md
[Provisioning instructions]: sql-data-warehouse-get-started-provision.md
[Configure server firewall access for your client IP]: sql-data-warehouse-get-started-provision.md
[SQL Data Warehouse best practices]: sql-data-warehouse-best-practices.md
[Table sizes]: sql-data-warehouse-tables-overview.md#table-size-queries
[Unsupported table features]: sql-data-warehouse-tables-overview.md#unsupported-table-features
[Unsupported data types]: sql-data-warehouse-tables-data-types.md#unsupported-data-types
[Overview]: sql-data-warehouse-tables-overview.md
[Data types]: sql-data-warehouse-tables-data-types.md
[Distribute]: sql-data-warehouse-tables-distribute.md
[Index]: sql-data-warehouse-tables-index.md
[Partition]: sql-data-warehouse-tables-partition.md
[Statistics]: sql-data-warehouse-tables-statistics.md
[Temporary]: sql-data-warehouse-tables-temporary.md
[Poor columnstore index quality]: sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[Rebuild indexes to improve segment quality]: sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Workload management]: resource-classes-for-workload-management.md
[Using CTAS to work around unsupported UPDATE and DELETE syntax]: sql-data-warehouse-develop-ctas.md
[UPDATE workarounds]: sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements
[DELETE workarounds]: sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements
[MERGE workarounds]: sql-data-warehouse-develop-ctas.md#replace-merge-statements
[Stored procedure limitations]: sql-data-warehouse-develop-stored-procedures.md#limitations
[Authentication to Azure SQL Data Warehouse]: sql-data-warehouse-authentication.md


<!--MSDN references-->
[sys.database_principals]: /sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql
[CREATE FUNCTION]: /sql/t-sql/statements/create-function-sql-data-warehouse
[sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md

<!--Other Web references-->
[博客]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[CAT 团队博客]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[功能请求]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[MSDN 论坛]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse
[Stackoverflow 论坛]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[视频]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[Databricks]: https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse#load-data-into-azure-sql-data-warehouse
