---
title: 使用 SQL 数据同步从 Azure SQL Database Edge 同步数据 | Microsoft Docs
description: 了解如何使用 Azure SQL 数据同步从 Azure SQL Database Edge 同步数据
keywords: sql database edge, 从 sql database edge 同步数据, sql database edge 数据同步
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 7c38ba6dbabef4affd8672295a93d46fd4b0e494
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74384187"
---
# <a name="tutorial-sync-data-from-sql-database-edge-to-azure-sql-database-by-using-sql-data-sync"></a>教程：使用 SQL 数据同步将数据从 SQL Database Edge 同步到 Azure SQL 数据库

在本教程中，你将了解如何使用“Azure SQL 数据同步”同步组，以增量方式将数据从 Azure SQL Database Edge 同步到 Azure SQL 数据库  。 使用 SQL 数据同步这项基于 Azure SQL 数据库的服务，可以跨多个 SQL 数据库和 SQL Server 实例双向同步选定数据。 有关 SQL 数据同步的详细信息，请参阅 [Azure SQL 数据同步](../sql-database/sql-database-sync-data.md)。

由于 SQL Database Edge 是在最新版本的 [SQL Server 数据库引擎](/sql/sql-server/sql-server-technical-documentation/)上构建的，因此，适用于本地 SQL Server 实例的任何数据同步机制还可以用于将数据同步到边缘设备上运行的 SQL Database Edge 实例或从该实例同步数据。

## <a name="prerequisites"></a>先决条件

本教程需要使用 [Azure SQL 数据同步的 Data Sync Agent](../sql-database/sql-database-data-sync-agent.md) 配置的 Windows 计算机。

## <a name="before-you-begin"></a>开始之前

* 创建 Azure SQL 数据库。 有关如何使用 Azure 门户创建 Azure SQL 数据库的信息，请参阅[在 Azure SQL 数据库中创建单一数据库](../sql-database/sql-database-single-database-get-started.md?tabs=azure-portal)。

* 在 Azure SQL 数据库部署中创建表和其他必需的对象。

* 在 Azure SQL Database Edge 部署中创建必需的表和对象。 有关详细信息，请参阅[使用 SQL 数据库 DAC 包和 SQL Database Edge](stream-analytics.md)。

* 将 Azure SQL Database Edge 实例注册到 Azure SQL 数据同步的 Data Sync Agent。有关详细信息，请参阅[添加本地 SQL Server 数据库](../sql-database/sql-database-get-started-sql-data-sync.md#add-on-prem)。

## <a name="sync-data-between-an-azure-sql-database-and-sql-database-edge"></a>在 Azure SQL 数据库与 SQL Database Edge 之间同步数据

设置使用 SQL 数据同步在 Azure SQL 数据库与 SQL Database Edge 实例之间进行同步包括三个关键步骤：  

1. 使用 Azure 门户创建同步组。 有关详细信息，请参阅[创建同步组](../sql-database/sql-database-get-started-sql-data-sync.md#create-sync-group)。 可以使用单个中心数据库创建多个同步组，以将不同 SQL Database Edge 实例中的数据同步到 Azure 中的一个或多个 SQL 数据库  。

2. 将同步成员添加到同步组。 有关详细信息，请参阅[添加同步成员](../sql-database/sql-database-get-started-sql-data-sync.md#add-sync-members)。

3. 设置同步组以选择将作为同步的一部分的表。 有关详细信息，请参阅[配置同步组](../sql-database/sql-database-get-started-sql-data-sync.md#add-sync-members)。

完成上述步骤后，你将拥有一个包含 Azure SQL 数据库和 SQL Database Edge 实例的同步组。

有关 SQL 数据同步的详细信息，请参阅这些文章：

* [Azure SQL 数据同步的 Data Sync Agent](../sql-database/sql-database-data-sync-agent.md)

* [最佳做法](../sql-database/sql-database-best-practices-data-sync.md)和[如何解决 Azure SQL 数据同步问题](../sql-database/sql-database-troubleshoot-data-sync.md)

* [使用 Azure Monitor 日志监视 SQL 数据同步](../sql-database/sql-database-sync-monitor-oms.md)

* [使用 Transact-SQL 更新同步架构](../sql-database/sql-database-update-sync-schema.md) 或使用 [PowerShell](../sql-database/scripts/sql-database-sync-update-schema.md) 更新同步架构

## <a name="next-steps"></a>后续步骤

* [使用 PowerShell 在 Azure SQL 数据库和 Azure SQL Database Edge 之间进行同步](../sql-database/scripts/sql-database-sync-data-between-azure-onprem.md)。 在本教程中，将 `OnPremiseServer` 数据库详细信息替换为 Azure SQL Database Edge 详细信息。
