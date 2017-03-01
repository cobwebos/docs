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
ms.date: 02/08/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 9b09383350284c8f1cd3e384c802c25c962b1226
ms.openlocfilehash: 1fe15e7ad3667d42995cd487c793fae496216fca
ms.lasthandoff: 02/16/2017

        
---
 
# <a name="explore-azure-sql-database-tutorials"></a>浏览 Azure SQL 数据库教程
下表中的链接可以转到每个列出的功能区域的概述以及每个区域的简单的分步入门教程。 

## <a name="create-servers-databases-and-server-level-firewall-rules"></a>创建服务器、数据库和服务器级防火墙规则
在以下教程中，将创建服务器、数据库和服务器级防火墙规则，并了解如何连接和查询服务器及数据库。

| 教程 | 说明 |
| --- | --- | 
| [第一个 Azure SQL 数据库](sql-database-get-started.md) | 完成此快速入门教程后，你会有一个示例数据库，以及一个在 Azure 资源组中运行并已附加到逻辑服务器的空数据库。 此外还会有两个服务器级防火墙规则并将其配置为启用服务器级主体，用于从两个指定的 IP 地址登录到服务器。 最后，将学习如何在 Azure 门户中查询数据库，以及如何使用 SQL Server Management Studio 进行连接和查询。 |
| [使用 PowerShell 预配和访问 Azure SQL 数据库](sql-database-get-started-powershell.md) | 完成本教程后，你会有一个示例数据库和一个空的数据库在 Azure 资源组中运行并附加到逻辑服务器。 此外，还将创建一个服务器级防火墙规则，它已配置为启用服务器级主体，用于从指定的 IP 地址（或 IP 地址范围）登录到服务器。 |
| [使用 C# 和面向 .NET 的 SQL 数据库库创建 SQL 数据库](sql-database-get-started-csharp.md)| 在本教程中，将使用 C# 创建 SQL 数据库服务器、防火墙规则和 SQL 数据库。 还会创建一个 Active Directory (AD) 应用程序和对 C# 应用进行身份验证时所需的服务主体。 |
|  | |

## <a name="backups-long-term-retention-and-database-recovery"></a>备份、长期保留和数据库恢复
在以下教程中，将了解如何使用[数据库备份](sql-database-automated-backups.md)、[长期备份保留](sql-database-long-term-retention.md)和[使用备份恢复数据库](sql-database-recovery-using-backups.md)。

| 教程 | 说明 |
| --- | --- | 
| [使用 Azure 门户备份和还原](sql-database-get-started-backup-recovery-portal.md) | 在此教程中，将了解如何使用 Azure 门户查看备份、还原到某个时间点、配置长期备份保留，并在 Azure 恢复服务保管库中从备份还原
| [使用 PowerShell 备份和还原](sql-database-get-started-backup-recovery-powershell.md) | 在此教程中，将了解如何使用 PowerShell 查看备份、还原到某个时间点、配置长期备份保留，并在 Azure 恢复服务保管库中从备份还原
|  | |

## <a name="sharded-databases"></a>分片的数据库
在以下教程中，将了解如何[使用分片映射管理器扩大数据库](sql-database-elastic-scale-shard-map-management.md)。

| 教程 | 说明 |
| --- | --- | 
| [创建分片应用程序](sql-database-elastic-scale-get-started.md) |在本教程中，你将学习如何使用简单的分片应用程序来使用弹性数据库工具的功能。 |
| [部署拆分 / 合并服务](sql-database-elastic-scale-configure-deploy-split-and-merge.md) |在此教程中，将学习如何在分片数据库之间移动数据。 |
|  | |

## <a name="elastic-database-jobs"></a>弹性数据库作业
以下教程介绍如何使用[弹性数据库作业](sql-database-elastic-jobs-overview.md)。

| 教程 | 说明 |
| --- | --- | 
| [Azure SQL 数据库弹性作业入门](sql-database-elastic-jobs-getting-started.md) |此教程将介绍如何创建和管理用于管理一组相关数据库的作业。 |
|  | |

## <a name="elastic-queries"></a>弹性查询
以下教程介绍如何运行[弹性查询](sql-database-elastic-query-overview.md)。

| 教程 | 说明 |
| --- | --- | 
| [创建弹性查询](sql-database-elastic-query-getting-started-vertical.md) | 此教程将介绍如何使用单一连接点运行跨多个数据库的 T-SQL 查询 |
| [跨已扩展的云数据库进行报告](sql-database-elastic-query-getting-started.md) |此教程介绍如何从位于横向分区（分片）数据库中的所有数据库创建报表 |
| [在具有不同架构的云数据库中进行查询](sql-database-elastic-query-vertical-partitioning.md) | 此教程将介绍如何使用不同架构运行跨多个数据库的 T-SQL 查询 |
| [跨扩展云数据库进行报告](sql-database-elastic-query-horizontal-partitioning.md) |此教程介绍如何在分片数据库中创建跨所有数据库的报表。 |
|  | |

## <a name="database-authentication-and-authorization"></a>数据库身份验证和授权
以下教程介绍如何[创建和管理登录名和用户](sql-database-manage-logins.md)。

| 教程 | 说明 |
| --- | --- | --- |
| [SQL 身份验证和授权](sql-database-control-access-sql-authentication-get-started.md) | 此教程介绍如何使用 SQL Server 身份验证创建登录名和用户、将其添加到角色，以及向他们授予权限 |
| [Azure AD 身份验证和授权](sql-database-control-access-aad-authentication-get-started.md) | 此教程介绍如何使用 Azure Active Directory 身份验证创建登录名和用户 |
|  | |

## <a name="secure-and-protect-data"></a>保护数据
以下教程介绍如何[保护 Azure SQL 数据库数据](sql-database-security-overview.md)。

| 教程 | 说明 |
| --- | --- | 
| [使用 Always Encrypted 保护敏感数据](sql-database-always-encrypted-azure-key-vault.md) |在此教程中，将使用“Always Encrypted”向导保护 Azure SQL 数据库中的敏感数据。 |
|  | |

## <a name="develop"></a>开发
以下教程介绍应用程序和数据库开发。

| 教程 | 说明 |
| --- | --- | 
| [使用 Excel 创建报表](sql-database-connect-excel.md) |此教程介绍如何将 Excel 连接到云中的 SQL 数据库，以便导入数据并根据数据库中的值来创建表和图表。 |
| [使用 SQL Server 生成应用](https://www.microsoft.com/sql-server/developer-get-started/) |此教程介绍如何使用 SQL Server 生成应用 |
| [临时表](sql-database-temporal-tables.md) | 在本教程中，你将了解有关临时表的信息。
| [使用带弹性工具的实体框架](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) |此教程介绍与弹性数据库工具集成所需的实体框架应用程序中的更改。 |
| [采用内存中 OLTP](sql-database-in-memory-oltp-migration.md) | 此教程介绍如何使用[内存中 OLTP](sql-database-in-memory.md) 提高事务处理的性能。 |
| [对新数据库使用 Code First](https://msdn.microsoft.com/data/jj193542.aspx) | 在本教程中，你将了解 Code First 开发。
| [Tailspin Surveys 示例应用程序](https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md) | 本教程使用 Tailspon Surveys 示例应用程序。 |
| [Contoso Clinic 演示应用程序](https://github.com/Microsoft/azure-sql-security-sample) | 本教程使用 Contoso Clinic 示例应用程序。 |
|  | |

## <a name="data-sync"></a>数据同步
此教程介绍如何进行[数据同步](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf)。

| 教程 | 说明 |
| --- | --- | 
| [Azure SQL 数据同步入门（预览版）](sql-database-get-started-sql-data-sync.md) |在本教程中，你将了解使用 Azure 经典门户的 Azure SQL 数据同步的基础知识。 |
|  | |

## <a name="monitor-and-tune"></a>监视和优化
以下教程介绍如何监视和优化。
| 教程 | 说明 |
| --- | --- | 
| [使用 PowerShell 收集弹性池遥测](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools)| 本教程介绍如何使用 PowerShell 收集弹性池遥测。 |
| [SaaS 的弹性池自定义仪表板](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools-custom-dashboard) | 本教程介绍如何创建用于监视弹性池的自定义仪表板。 |
| [将扩展事件捕获到事件文件目标](sql-database-xevent-code-event-file.md)| 本教程介绍如何将扩展事件捕获到事件目标文件。|
| [将扩展事件捕获到环形缓冲区目标](sql-database-xevent-code-ring-buffer.md)| 本教程介绍如何将扩展事件捕获到代码环形缓冲区。|
|  | |



