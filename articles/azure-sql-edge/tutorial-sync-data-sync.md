---
title: 使用 SQL 数据同步从 Azure SQL Edge（预览）同步数据
description: 了解如何使用 Azure SQL 数据同步从 Azure SQL Edge（预览）同步数据
keywords: SQL Edge,从 SQL Edge 同步数据,SQL Edge 数据同步
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 6c0518e12a2c654b8f971db3a6733b0e64f80a1f
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2020
ms.locfileid: "85254044"
---
# <a name="tutorial-sync-data-from-sql-edge-to-azure-sql-database-by-using-sql-data-sync"></a>教程：使用 SQL 数据同步将数据从 SQL Edge 同步到 Azure SQL 数据库

本教程介绍了如何使用 Azure SQL 数据同步的同步组，以增量方式将数据从 Azure SQL Edge 同步到 Azure SQL 数据库。 使用 SQL 数据同步这项基于 Azure SQL 数据库的服务，可以跨 Azure SQL 数据库和 SQL Server 实例中的多个数据库双向同步选定数据。 有关 SQL 数据同步的详细信息，请参阅 [Azure SQL 数据同步](../azure-sql/database/sql-data-sync-data-sql-server-sql-database.md)。

由于 SQL Edge 是在最新版 [SQL Server 数据库引擎](/sql/sql-server/sql-server-technical-documentation/)的基础之上构建而成，因此任何适用于 SQL Server 实例的数据同步机制也可以用于将数据同步到边缘设备上运行的 SQL Edge 实例，或用于同步其中的数据。

## <a name="prerequisites"></a>先决条件

本教程需要使用 [Azure SQL 数据同步的 Data Sync Agent](../azure-sql/database/sql-data-sync-agent-overview.md) 配置的 Windows 计算机。

## <a name="before-you-begin"></a>开始之前

* 在 Azure SQL 数据库中创建数据库。 有关如何使用 Azure 门户创建数据库的信息，请参阅[在 Azure SQL 数据库中创建单一数据库](../azure-sql/database/single-database-create-quickstart.md?tabs=azure-portal)。

* 在 Azure SQL 数据库部署中创建表和其他必需的对象。

* 在 Azure SQL Edge 部署中创建必需的表和对象。 有关详细信息，请参阅[结合使用 SQL 数据库 DAC 包和 SQL Edge](deploy-dacpac.md)。

* 向用于 Azure SQL 数据同步的 Data Sync Agent 注册 Azure SQL Edge 实例。有关详细信息，请参阅[添加 SQL Server 数据库](../azure-sql/database/sql-data-sync-sql-server-configure.md#add-on-prem)。

## <a name="sync-data-between-a-database-in-azure-sql-database-and-sql-edge"></a>同步 Azure SQL 数据库中的数据库与 SQL Edge 之间的数据

使用 SQL 数据同步功能在 Azure SQL 数据库中的数据库与 SQL Edge 实例之间设置同步，包括以下三个关键步骤：  


1. 使用 Azure 门户创建同步组。 有关详细信息，请参阅[创建同步组](../azure-sql/database/sql-data-sync-sql-server-configure.md#create-sync-group)。 可以使用单个中心数据库创建多个同步组，以将不同 SQL Edge 实例中的数据同步到 Azure SQL 数据库中的一个或多个数据库。 

2. 将同步成员添加到同步组。 有关详细信息，请参阅[添加同步成员](../azure-sql/database/sql-data-sync-sql-server-configure.md#add-sync-members)。

3. 设置同步组以选择将作为同步的一部分的表。 有关详细信息，请参阅[配置同步组](../azure-sql/database/sql-data-sync-sql-server-configure.md#add-sync-members)。

完成上述步骤后，将拥有一个包含 Azure SQL 数据库中的数据库和 SQL Edge 实例的同步组。

有关 SQL 数据同步的详细信息，请参阅这些文章：

* [Azure SQL 数据同步的 Data Sync Agent](../azure-sql/database/sql-data-sync-agent-overview.md)

* [最佳做法](../azure-sql/database/sql-data-sync-best-practices.md)和[如何解决 Azure SQL 数据同步问题](../azure-sql/database/sql-data-sync-troubleshoot.md)

* [使用 Azure Monitor 日志监视 SQL 数据同步](../azure-sql/database/sql-data-sync-monitor-sync.md)

* [使用 Transact-SQL 更新同步架构](../azure-sql/database/sql-data-sync-update-sync-schema.md) 或使用 [PowerShell](../azure-sql/database/scripts/update-sync-schema-in-sync-group.md) 更新同步架构

## <a name="next-steps"></a>后续步骤


* [使用 PowerShell 在 Azure SQL 数据库和 Azure SQL Edge 之间进行同步](../azure-sql/database/scripts/sql-data-sync-sync-data-between-azure-onprem.md)。 在本教程中，将 `OnPremiseServer` 数据库详细信息替换为 Azure SQL Edge 详细信息。
