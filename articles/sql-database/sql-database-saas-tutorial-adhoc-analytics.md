---
title: "针对多个 Azure SQL 数据库运行即席分析查询 | Microsoft Docs"
description: "在多租户应用程序中针对多个数据库运行即席分析查询"
keywords: "sql 数据库教程"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: billgib; sstein
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: dd41e7f1f131f6c18e03d2434982c3d681342b8b
ms.contentlocale: zh-cn
ms.lasthandoff: 05/12/2017


---
# <a name="run-ad-hoc-analytics-queries-across-all-wtp-tenants"></a>对所有 WTP 租户运行即席分析查询

在本教程中，将创建即席分析数据库并对所有租户运行多个查询。 这些查询可以提取隐藏在 WTP 应用的日常操作数据中的信息。

为了运行即席分析查询（对多个租户），WTP 应用使用[弹性查询](sql-database-elastic-query-overview.md)以及分析数据库。


本教程介绍如何：

> [!div class="checklist"]

> * 部署即席分析数据库
> * 对所有租户数据库运行分布式查询



若要完成本教程，请确保已完成以下先决条件：

* 已部署了 WTP 应用。 若要在五分钟内部署，请参阅[部署和浏览 WTP SaaS 应用程序](sql-database-saas-tutorial.md)
* Azure PowerShell 已安装。 有关详细信息，请参阅 [Azure PowerShell 入门](https://docs.microsoft.com/powershell/azure/get-started-azureps)


## <a name="ad-hoc-analytics-pattern"></a>即席分析模式

SaaS 应用程序提供的大好机会之一是使用集中存储在云中的大量租户数据。 使用这些数据来深入了解应用程序的操作和使用情况、你的租户、其用户及其首选项和行为。 你找到的见解可指导应用和服务的功能开发、可用性改进和其他投资。

在单个多租户数据库中访问此数据很简单，但当数据大规模分布在可能数千个数据库中时便不那么容易了。 一种方法是使用弹性查询，这样可以对具有常见架构的一组分布式数据库进行即席查询。 弹性查询使用单个*头*数据库，其中定义的外部表镜像分布式（租户）数据库中的表。 提交到此头数据库的查询将进行编译以生成分布式查询计划，其中的部分查询将根据需要向下推送到租户数据库。 弹性查询在目录数据库中使用分片映射提供租户数据库的位置。 设置和查询直接使用标准 T-SQL 进行，并支持从 Power BI 和 Excel 等工具进行即席查询。

## <a name="get-the-wingtip-application-scripts"></a>获取 Wingtip 应用程序脚本

Wingtip 票证脚本和应用程序源代码可在 [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS) GitHub 存储库中找到。 脚本文件位于 [Learning Modules 文件夹](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules)中。 将 **Learning Modules** 文件夹下载到本地计算机，保持其文件夹结构不变。

## <a name="deploy-the-database-used-for-ad-hoc-analytics-queries"></a>部署用于即席分析查询的数据库

本练习部署即席分析数据库，该数据库包含的架构用于发出跨所有租户数据库的即席查询。

1. 在 *PowerShell ISE* 中打开 .\\Learning Modules\\Operational Analytics\\Adhoc Analytics\\*Demo-AdhocAnalytics.ps1* 并设置以下值：
   * **$DemoScenario** = 2，**部署即席分析数据库**。

1. 按 **F5** 运行该脚本，为即席分析创建新的 SQL 数据库并将其添加到 WTP 目录。 将 TicketPurchases、Tickets 和 Venues 表添加为可以查询的外部表。
   如果在此处遇到有关“RPC 服务器不可用”的警告，是正常的。


现在已有 *adhocanalytics* 数据库，可以使用它运行分布式查询，并对所有租户收集信息！

## <a name="run-ad-hoc-analytics-queries"></a>运行即席分析查询

本练习将运行即席分析查询，从 WTP 应用程序发现租户见解。

1. 在 SSMS 中打开 ...\\Learning Modules\\Operational Analytics\\Adhoc Analytics\\*Demo-AdhocAnalyticsQueries.sql*。
1. 确保已连接到 **adhocanalytics** 数据库
1. 选择要运行的单个查询，并按 **F5**：

    ![query](media/sql-database-saas-tutorial-adhoc-analytics/query.png)




## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]

> * 部署即席分析数据库
> * 对所有租户数据库运行分布式查询

[Log Analytics (OMS) 教程](sql-database-saas-tutorial-log-analytics.md)

## <a name="additional-resources"></a>其他资源

* [基于初始 Wingtip 票证平台 (WTP) 应用程序部署编写的其他教程](sql-database-wtp-overview.md#sql-database-wtp-saas-tutorials)
* [弹性查询](sql-database-elastic-query-overview.md)

