---
title: 还原 Azure 数据仓库 - 本地和异地冗余 | Microsoft Docs
description: 在 Azure SQL 数据仓库中恢复数据库时的数据库还原选项概述。
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jenniehubbard
editor: ''
ms.assetid: 3e01c65c-6708-4fd7-82f5-4e1b5f61d304
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: backup-restore
ms.date: 12/06/2017
ms.author: barbkess
ms.openlocfilehash: abf8f0b1005aec71812dc8ebfd12fe65250d7a0e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="sql-data-warehouse-restore"></a>SQL 数据仓库还原
> [!div class="op_single_selector"]
> * [概述][Overview]
> * [门户][Portal]
> * [PowerShell][PowerShell]
> * [REST][REST]
> 
> 

SQL 数据仓库提供本地和异地还原功能，这是其数据仓库灾难恢复功能的一部分。 使用数据仓库备份可以将数据仓库还原到主要区域的某个还原点，而使用异地冗余备份则可还原到另一地理区域。 本文介绍了还原数据仓库的细节。

## <a name="what-is-a-data-warehouse-restore"></a>什么是数据仓库还原？
数据仓库还原是指通过现有数据仓库或已删除数据仓库的备份创建的新数据仓库。 还原的数据仓库重新创建了在特定时间备份的数据仓库。 由于 SQL 数据仓库属于分布式系统，因此数据仓库还原是从存储在 Azure blob 中的许多备份文件创建的。 

数据库还原是任何业务连续性和灾难恢复策略的基本组成部分，因为数据库还原可以在意外损坏或删除数据后重新创建数据。

有关详细信息，请参阅：

* [SQL 数据仓库备份](sql-data-warehouse-backups.md)
* [业务连续性概述](../sql-database/sql-database-business-continuity.md)

## <a name="data-warehouse-restore-points"></a>数据仓库还原点
使用 Azure 高级存储的好处是，SQL 数据仓库可以使用 Azure 存储 Blob 快照备份主数据仓库。 每个快照都有一个还原点，代表启动快照的时间。 要还原数据仓库，请选择一个还原点，并发出还原命令。  

SQL 数据仓库始终将备份还原到新的数据仓库。 可以保留还原的数据仓库和当前的数据仓库，也可以删除其中一个。 要将当前的数据仓库替换为还原的数据仓库，将其重命名即可。

如果需要还原已删除或已暂停的数据仓库，则可以[创建支持票证](sql-data-warehouse-get-started-create-support-ticket.md)。 

<!-- 
### Can I restore a deleted data warehouse?

Yes, you can restore the last available restore point.

Yes, for the next seven calendar days. When you delete a data warehouse, SQL Data Warehouse actually keeps the data warehouse and its snapshots for seven days just in case you need the data. After seven days, you won't be able to restore to any of the restore points. -->

## <a name="geo-redundant-restore"></a>异地冗余还原
可以将数据仓库还原到支持所选性能级别的 Azure SQL 数据仓库的任何区域。 请注意，在预览期间，所有区域都不支持 9000 和 18000 DWU。

> [!NOTE]
> 若要执行异地冗余还原，不能选择退出此功能。
> 
> 

## <a name="restore-timeline"></a>还原时间线
可以将数据库还原到过去 7 天的任何可用还原点。 快照 4 到 8 小时启动一次，可供使用 7 天。 快照超过 7 天将过期，其还原点不再可用。

## <a name="restore-costs"></a>还原费用
已还原的数据仓库的存储费用按 Azure 高级存储费率计算。 

如果暂停还原的数据仓库，则存储费用按 Azure 高级存储费率计算。 暂停的好处是不会对 DWU 计算资源收费。

有关 SQL 数据仓库定价的详细信息，请参阅 [SQL 数据仓库定价](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)。

## <a name="uses-for-restore"></a>还原的用途
数据仓库还原的主要用途是在意外丢失或损坏数据后恢复数据。

数据仓库还原还可用于保留那些时间超过 7 天的备份。 还原备份以后，数据仓库处于联机状态，可以无限次将其暂停以节省计算费用。 暂停的数据库按 Azure 高级存储费率收取存储费用。 

## <a name="related-topics"></a>相关主题
### <a name="scenarios"></a>方案
* 有关业务连续性概述，请参阅[业务连续性概述](../sql-database/sql-database-business-continuity.md)

<!-- ### Tasks -->

若要执行数据仓库还原，请使用以下还原方式：

* Azure 门户，请参阅[使用 Azure 门户还原数据仓库](sql-data-warehouse-restore-database-portal.md)
* PowerShell cmdlets，请参阅 [Restore a data warehouse using PowerShell cmdlets](sql-data-warehouse-restore-database-powershell.md)（使用 PowerShell cmdlet 还原数据仓库）
* REST API，请参阅 [Restore a data warehouse using the REST APIs](sql-data-warehouse-restore-database-rest-api.md)（使用 REST API 还原数据仓库）

<!-- ### Tutorials -->

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md

<!--MSDN references-->


<!--Other Web references-->
