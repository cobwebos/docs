---
title: "浏览 Azure SQL 数据库教程 | Microsoft 文档"
description: "了解 SQL 数据库的特性和功能"
keywords: 
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 04c0fd7f-d260-4e43-a4f0-41cdcd5e3786
ms.service: sql-database
ms.custom: overview
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 12/08/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 0b200aff6a38a76abd1abaa742006f3d75e7121e
ms.openlocfilehash: 0cbde2e33517e46e49824d9f66f75e4e2f384d97


---
# <a name="explore-azure-sql-database-tutorials"></a>浏览 Azure SQL 数据库教程
以下链接可以转到每个列出的功能区域的概述以及每个区域的简单的分步入门教程。 有关在基于实际情况的完整解决方案中演示 SQL 数据库的使用的解决方案范围的快速入门，请参阅 [Azure SQL 数据库解决方案快速入门](sql-database-solution-quick-starts.md)。

## <a name="using-sql-server-management-studio"></a>使用 SQL Server Management Studio
在以下教程中，你将学习如何使用 SQL Server Management Studio 管理和查询 Azure SQL 数据库。

> [!IMPORTANT]
> 建议始终使用最新版本的 Management Studio 以保持与 Microsoft Azure 和 SQL 数据库的更新同步。 [更新 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)。
>

| 教程 | 说明 |
| --- | --- | --- |
| [使用服务器级别主体登录名连接到 Azure SQL 数据库](sql-database-get-started-security.md#connect-to-azure-sql-database-using-sql-server-authentication) |本教程将介绍如何使用 SQL 服务器级主体登录名连接到 Azure SQL 数据库。 |
| [以用户身份连接到 Azure SQL 数据库](sql-database-get-started-security.md#connect-to-azure-sql-database-as-a-user) |在本教程中，你将学习如何使用数据库级用户帐户连接到 Azure SQL 数据库。 |
|  | |

## <a name="create-servers-databases-and-server-level-firewall-rules"></a>创建服务器、数据库和服务器级防火墙规则
在以下教程中，将创建服务器、数据库和服务器级防火墙规则，并了解如何连接和查询服务器及数据库。

| 教程 | 说明 |
| --- | --- | --- |
| [通过 Azure 门户和 SQL Server Management Studio 实现 Azure SQL 数据库服务器、数据库和防火墙规则入门](sql-database-get-started.md) | 在本教程中，将使用 Azure 门户和 SQL Server Management Studio 创建 Azure SQL 数据库逻辑服务器、查看其属性、创建服务器级防火墙、连接到主数据库、创建示例数据库、查看其属性、连接到示例数据库并创建一个空白数据库。 |
| [通过 Azure PowerShell 实现 Azure SQL 数据库服务器、数据库和防火墙规则入门](sql-database-get-started-powershell.md) | 在本教程中，将使用 PowerShell 创建 Azure SQL 数据库逻辑服务器、查看其属性、创建服务器级防火墙、连接到主数据库、创建示例数据库、查看其属性、连接到示例数据库并创建一个空白数据库。 |
| [使用 C# 和面向 .NET 的 SQL 数据库库创建 SQL 数据库](sql-database-get-started-csharp.md)| 在本教程中，将使用 C# 创建 SQL 数据库服务器、防火墙规则和 SQL 数据库。 还会创建一个 Active Directory (AD) 应用程序和对 C# 应用进行身份验证时所需的服务主体。 |

## <a name="backup-and-recovery"></a>备份和恢复
在以下教程中，将数据库还原到某个时间点，配置长期备份保留，并在 Azure 恢复服务保管库中通过保留内容还原数据库。 

| 教程 | 说明 |
| --- | --- | --- |
| [数据保护和恢复的备份和还原入门](sql-database-get-started-backup-recovery.md) | 在此教程中，使用 Azure 门户将数据库还原到某个时间点，配置长期备份保留，并在 Azure 恢复服务保管库中通过保留内容还原数据库。 |


## <a name="elastic-pools"></a>弹性池
以下教程介绍如何使用[弹性池](sql-database-elastic-pool.md)管理使用模式变化很大且不可预测的多个数据库的性能目标。

| 教程 | 说明 |
| --- | --- | --- |
| [创建弹性池](sql-database-elastic-pool-create-portal.md) |在本教程中，你将学习如何创建 Azure SQL 数据库的可缩放的池。 |
| [监视弹性数据库](sql-database-elastic-pool-manage-portal.md#elastic-database-monitoring) |在本教程中，你将学习如何监视单个弹性数据库的潜在问题。 |
| [向池资源添加警报](sql-database-elastic-pool-manage-portal.md#add-an-alert-to-a-pool-resource) |在本教程中，你将学习如何向资源添加规则，以便在资源达到设置的利用率阈值时，向人员发送电子邮件或是向 URL 终结点发送警报字符串。 |
| [将数据库移入弹性池](sql-database-elastic-pool-manage-portal.md#move-a-database-into-an-elastic-pool) |在本教程中，你将学习如何将数据库移入弹性池。 |
| [将数据库移出弹性池](sql-database-elastic-pool-manage-portal.md#move-a-database-out-of-an-elastic-pool) |在本教程中，你将学习如何将数据库移出弹性池。 |
| [更改池的性能设置](sql-database-elastic-pool-manage-portal.md#change-performance-settings-of-a-pool) |在本教程中，你将学习如何调整池的性能和存储限制。 |
|  | |

## <a name="elastic-database-jobs"></a>弹性数据库作业
以下教程介绍如何使用[弹性数据库作业](sql-database-elastic-jobs-overview.md)。

| 教程 | 说明 |
| --- | --- | --- |
| [弹性数据库工具入门](sql-database-elastic-scale-get-started.md) |在本教程中，你将学习如何使用简单的分片应用程序来使用弹性数据库工具的功能。 |
| [Azure SQL 数据库弹性作业入门](sql-database-elastic-jobs-getting-started.md) |本教程将介绍如何创建和管理用于管理一组相关数据库的作业。 |
|  | |

## <a name="elastic-queries"></a>弹性查询
以下教程介绍如何运行[弹性查询](sql-database-elastic-query-overview.md)。

| 教程 | 说明 |
| --- | --- | --- |
| [跨横向分区（分片）数据库](sql-database-elastic-query-getting-started.md)查询） |此教程介绍如何使用[弹性查询](sql-database-elastic-query-overview.md)从位于横向分区（分片）数据库中的所有数据库创建报表 |
| [跨垂直分区数据库查询](sql-database-elastic-query-getting-started-vertical.md#create-database-objects) |此教程介绍如何使用[弹性查询](sql-database-elastic-query-overview.md)从位于纵向分区数据库中的所有数据库创建报表 |
| [迁移要横向扩展的现有数据库](sql-database-elastic-convert-to-use-elastic-tools.md) |在本教程中，你将学习如何横向扩展（分片）Azure SQL 数据库。 |
|  | |

## <a name="performance-optimization"></a>性能优化
以下教程介绍如何优化[独立数据库的性能](sql-database-performance-guidance.md)。 有关多个数据库的性能优化，请参阅[弹性池](#elastic-pools)。

| 教程 | 说明 |
| --- | --- | --- |
| [更改数据库的服务层和性能级别](sql-database-scale-up.md#change-the-service-tier-and-performance-level-of-your-database) |在本教程中，你将学习如何使用服务层扩展或缩减 Azure SQL 数据库的性能。 |
| [SQL 数据库顾问 Query Performance Insight](sql-database-performance.md#performance-overview) |在本教程中，你将学习如何打开和使用 SQL 数据库顾问 Query Performance Insight。 |
| [SQL 数据库顾问性能建议](sql-database-advisor.md) |在本教程中，你将学习如何查看和应用 SQL 数据库顾问性能建议。 |
| [查看 DTU 消耗量靠前的查询](sql-database-query-performance.md#review-top-cpu-consuming-queries) |在本教程中，你将学习如何使用 SQL 数据库顾问 Query Performance Insight 来查看 CPU 消耗量靠前的查询。 |
| [查看单个查询的详细信息](sql-database-query-performance.md#viewing-individual-query-details) |在本教程中，你将学习如何使用 SQL 数据库顾问 Query Performance Insight 来查看单个查询性能的详细信息。 |
|  | |

## <a name="sql-database-migration-and-archive"></a>SQL 数据库迁移和存档
以下教程介绍如何[将现有的 SQL Server 数据库迁移到 Azure SQL 数据库](sql-database-cloud-migrate.md)。

| 教程 | 说明 |
| --- | --- | --- |
| [使用 SQL Server Data Tools for Visual Studio 检测兼容性问题](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md#detecting-compatibility-issues-using-sql-server-data-tools-for-visual-studio) |在本教程中，你将学习如何使用 SQL Server Data Tools for Visual Studio 确定 Azure SQL 数据库的兼容性。 |
| [使用 SQL Server Data Tools for Visual Studio 解决兼容性问题](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md#fixing-compatibility-issues-using-sql-server-data-tools-for-visual-studio) |在本教程中，你将学习如何使用 SQL Server Data Tools for Visual Studio 修复 Azure SQL 数据库的兼容性问题。 |
| [使用 SqlPackage.exe 确定 SQL 数据库兼容性](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md#using-sqlpackageexe) |在本教程中，你将学习如何使用 SQLPackage.exe 命令行实用工具确定 Azure SQL 数据库的兼容性。 |
| [使用 SSMS 确定 SQL 数据库兼容性](sql-database-cloud-migrate-determine-compatibility-ssms.md#using-sql-server-management-studio) |在本教程中，你将学习如何使用 SQL Server Management Studio 确定 Azure SQL 数据库的兼容性。 |
| [使用“将数据库部署到 Microsoft Azure 数据库”向导将 SQL Server 数据库迁移到 SQL 数据库](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md#use-the-deploy-database-to-microsoft-azure-database-wizard) |本教程将介绍如何在 SQL Server Management Studio 中使用“将数据库部署到 Microsoft Azure 数据库”向导将兼容的 SQL Server 数据库迁移到 Azure SQL 数据库。 |
| [使用 SSMS 将 SQL Server 数据库导出到 BACPAC 文件](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md) |在本教程中，你将学习如何在 SQL Server Management Studio 中使用“导出数据层应用程序”向导将兼容的 SQL Server 数据库导出到 BACPAC 文件。 |
| [使用 SqlPackage 将 SQL Server 数据库导出到 BACPAC 文件](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md) |在本教程中，你将学习如何使用 SQLPackage.exe 命令行实用工具将兼容的 SQL Server 数据库导出到 BACPAC 文件。 |
| [使用 SSMS 将 BACPAC 文件导入 Azure SQL 数据库](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md) |在本教程中，你将学习如何在 SQL Server Management Studio 中使用“导出数据层应用程序”向导将数据库从 BACPAC 文件导入 Azure SQL 数据库。 |
| [使用 SqlPackage 将 BACPAC 文件导入 Azure SQL 数据库](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md#import-from-a-bacpac-file-into-azure-sql-database-using-sqlpackage) |在本教程中，你将学习如何使用 SQLPackage 命令行实用工具将数据库从 BACPAC 文件导入 Azure SQL 数据库。 |
| [使用 Azure 门户将 BACPAC 文件导入 Azure SQL 数据库](sql-database-import.md) |在本教程中，你将学习如何使用 Azure 门户将数据库从存储在 Azure blob 中的 BACPAC 文件导入 Azure SQL 数据库。 |
| [使用 PowerShell 将 BACPAC 文件导入 Azure SQL 数据库](sql-database-import-powershell.md) |在本教程中，你将学习如何使用 PowerShell 将数据库从 BACPAC 文件导入 Azure SQL 数据库。 |
| [使用 Azure 门户存档 Azure SQL 数据库](sql-database-export.md#export-your-database) |在本教程中，你将学习如何使用 Azure 门户将 Azure SQL 数据库存档到 BACPAC 文件。 |
| [使用 PowerShell 存档 Azure SQL 数据库](sql-database-export-powershell.md) |在本教程中，你将学习如何使用 PowerShell 将 Azure SQL 数据库存档到 BACPAC 文件。 |
| [使用 Azure 门户复制 Azure SQL 数据库](sql-database-copy.md) |在本教程中，你将学习如何使用 Azure 门户复制 Azure SQL 数据库。 |
| [使用 PowerShell 复制 Azure SQL 数据库](sql-database-copy-powershell.md) |在本教程中，你将学习如何使用 PowerShell 复制 Azure SQL 数据库。 |
| [使用 Transact-SQL 复制 Azure SQL 数据库](sql-database-copy-transact-sql.md#copy-your-sql-database) |在本教程中，你将学习如何使用 Transact-SQL 复制 Azure SQL 数据库。 |
|  | |

## <a name="develop"></a>开发
以下教程介绍 [SQL](sql-database-develop-overview.md) 数据库开发以及如何使用[连接库](sql-database-libraries.md)。

| 教程 | 说明 |
| --- | --- | --- |
| [使用 .NET (C#) 连接到 SQL 数据库](sql-database-develop-dotnet-simple.md) |在本教程中，你将学习如何使用 C# 连接到 Azure SQL 数据库。 |
| [使用 Java 连接到 SQL 数据库](sql-database-develop-java-simple.md) |在本教程中，你将学习如何使用 Java 连接到 Azure SQL 数据库。 |
| [使用 Node.js 连接到 SQL 数据库](sql-database-develop-nodejs-simple.md) |在本教程中，你将学习如何使用 Node.js 连接到 Azure SQL 数据库。 |
| [使用 PHP 连接到 SQL 数据库](sql-database-develop-php-simple.md) |在本教程中，你将学习如何使用 PHP 连接到 Azure SQL 数据库。 |
| [使用 Python 连接到 SQL 数据库](sql-database-develop-python-simple.md) |在本教程中，你将学习如何使用 Python 连接到 Azure SQL 数据库。 |
| [使用 Ruby 连接到 SQL 数据库](sql-database-develop-ruby-simple.md) |在本教程中，你将学习如何使用 Ruby 连接到 Azure SQL 数据库。 |
|  | |

## <a name="database-access"></a>数据库访问
以下教程介绍如何[创建和管理登录名和用户](sql-database-manage-logins.md)。

| 教程 | 说明 |
| --- | --- | --- |
| [使用 Azure 门户创建 Azure SQL 数据库服务器级防火墙规则](sql-database-configure-firewall-settings.md) |在本教程中，你将学习如何使用 Azure 门户配置 SQL 数据库服务器级防火墙。 |
| [使用 Transact-SQL 创建数据库级防火墙规则](sql-database-configure-firewall-settings-tsql.md#database-level-firewall-rules) |在本教程中，你将学习如何使用 Transact-SQL 创建数据库级防火墙规则。 |
| [使用 Transact-SQL 管理服务器级防火墙规则](sql-database-configure-firewall-settings-tsql.md#server-level-firewall-rules) |在本教程中，你将学习如何使用 Transact-SQL 管理 Azure SQL 数据库服务器级防火墙。 |
| [使用 PowerShell 管理服务器级防火墙规则](sql-database-configure-firewall-settings-powershell.md#manage-firewall-rules-by-using-powershell) |在本教程中，你将学习如何使用 PowerShell 管理 Azure SQL 数据库服务器级防火墙。 |
| [使用 REST API 管理服务器级防火墙规则](sql-database-configure-firewall-settings-rest.md#manage-firewall-rules-using-the-rest-api) |在本教程中，你将学习如何使用 RESET API 管理 Azure SQL 数据库服务器级防火墙。 |
| [使用服务器级别主体登录名连接到 Azure SQL 数据库](sql-database-get-started-security.md#connect-to-azure-sql-database-using-sql-server-authentication) |在本教程中，你将学习如何使用服务器级主体登录名连接到 Azure SQL 数据库。 |
| [授予对登录名的数据库访问权限](sql-database-manage-logins.md#granting-database-access-to-a-login() |在本教程中，你将学习如何授予对服务器级登录名的数据库访问权限。 |
| [使用 SSMS 创建新数据库用户](sql-database-get-started-security.md#create-new-database-user-using-ssms) |在本教程中，你将学习如何使用 SSMS 在现有数据库中创建新的数据库用户。 |
| [授予新的数据库用户 db_owner 权限](sql-database-get-started-security.md#grant-new-database-user-dbowner-permissions) |在本教程中，你将学习如何授予现有数据库用户 db_owner 权限。 |
| [以用户身份连接到 Azure SQL 数据库](sql-database-get-started-security.md#connect-to-azure-sql-database-as-a-user) |在本教程中，你将学习如何使用数据库级用户帐户连接到 Azure SQL 数据库。 |
|  | |

## <a name="data-security"></a>数据安全
以下教程介绍如何[保护 Azure SQL 数据库数据](sql-database-security.md)。

| 教程 | 说明 |
| --- | --- | --- |
| [使用 Azure 门户为数据库启用威胁检测](sql-database-threat-detection-get-started.md#set-up-threat-detection-for-your-database) |在本教程中，你将学习在 Azure 门户中如何为数据库设置威胁检测。 |
| [使用“始终加密”保护敏感数据](sql-database-always-encrypted-azure-key-vault.md) |在本教程中，你将使用“始终加密”向导保护 Azure SQL 数据库中的敏感数据。 |
| [使用透明数据加密保护敏感数据](https://msdn.microsoft.com/library/dn948096.aspx) |在本教程中，你将学习如何使用透明数据加密保护敏感数据。 |
| [加密一列数据](https://msdn.microsoft.com/library/ms179331.aspx) |在本教程中，你将学习如何使用 Transact-SQL 加密一列数据。 |
| [设置动态数据掩码](sql-database-dynamic-data-masking-get-started.md#set-up-dynamic-data-masking-for-your-database-using-the-azure-portal) |在本教程中，你将学习如何为 Azure SQL 数据库设置动态数据掩码。 |
|  | |

## <a name="business-continuity-and-query-scale-out"></a>业务连续性和查询横向扩展
以下教程介绍如何使用[异地还原和活动异地复制](sql-database-business-continuity.md)从错误中恢复过来，以保持业务连续性和实现查询扩展。

| 教程 | 说明 |
| --- | --- | --- |
| [使用 Azure 门户将 Azure SQL 数据库还原到之前的时间点](sql-database-point-in-time-restore.md) |在本教程中，你将学习如何使用 Azure 门户将数据库还原到较早的时间点。 |
| [使用 PowerShell 将 Azure SQL 数据库还原到之前的时间点](sql-database-point-in-time-restore.md) |在本教程中，你将学习如何使用 PowerShell 将数据库还原到较早的时间点 |
| [使用 Azure 门户还原已删除的 Azure SQL 数据库](sql-database-restore-deleted-database-portal.md) |在本教程中，你将学习如何使用 Azure 门户还原已删除的数据库。 |
| [使用 PowerShell 还原已删除的 Azure SQL 数据库](sql-database-restore-deleted-database-powershell.md) |在本教程中，你将学习如何使用 PowerShell 还原已删除的数据库。 |
| [使用 Azure 门户为 Azure SQL 数据库配置异地复制](sql-database-geo-replication-portal.md) |本教程将介绍如何使用 Azure 门户配置活动异地复制。 |
| [使用 PowerShell 为 Azure SQL 数据库配置异地复制](sql-database-geo-replication-powershell.md) |本教程将介绍如何使用 PowerShell 配置活动异地复制。 |
| [使用 Transact-SQL 为 Azure SQL 数据库配置异地复制](sql-database-geo-replication-transact-sql.md) |本教程将介绍如何使用 Transact-SQL 配置活动异地复制。 |
| [使用 Azure 门户为 Azure SQL 数据库启动计划内或计划外故障转移](sql-database-geo-replication-failover-portal.md) |在本教程中，你将学习如何使用 Azure 门户故障转移到异地复制的次要副本。 |
| [使用 PowerShell 为 Azure SQL 数据库启动计划内或计划外故障转移](sql-database-geo-replication-failover-powershell.md) |在本教程中，你将学习如何使用 PowerShell 故障转移到异地复制的次要副本。 |
| [使用 Transact-SQL 为 Azure SQL 数据库启动计划内或计划外故障转移](sql-database-geo-replication-failover-transact-sql.md) |在本教程中，你将学习如何使用 Transact-SQL 故障转移到异地复制的次要副本。 |
|  | |

## <a name="data-sync"></a>数据同步
此教程介绍如何进行[数据同步](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf)。

| 教程 | 说明 |
| --- | --- | --- |
| [Azure SQL 数据同步入门（预览版）](sql-database-get-started-sql-data-sync.md) |在本教程中，你将了解使用 Azure 经典门户的 Azure SQL 数据同步的基础知识。 |
|  | |

## <a name="next-steps"></a>后续步骤
[浏览 Azure SQL 数据库解决方案快速入门](sql-database-solution-quick-starts.md)



<!--HONumber=Dec16_HO2-->


