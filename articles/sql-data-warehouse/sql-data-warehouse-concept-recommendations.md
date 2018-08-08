---
title: SQL 数据仓库的建议 - 概念 | Microsoft Docs
description: 了解 SQL 数据仓库的建议及其生成方式
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 07/27/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 2cbd691c29039c65b98d8b0191e9e278d2440f09
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2018
ms.locfileid: "39347660"
---
# <a name="sql-data-warehouse-recommendations"></a>SQL 数据仓库的建议

本文介绍 SQL 数据仓库通过 Azure 顾问提供的建议。  

SQL 数据仓库提供建议，确保以一致的方式优化数据仓库的性能。 数据仓库建议与 [Azure 顾问](https://docs.microsoft.com/azure/advisor/advisor-performance-recommendations)紧密集成，在直接在 [Azure 门户](https://aka.ms/Azureadvisor)中提供最佳做法。 SQL 数据仓库每日分析数据仓库的当前状态，收集遥测数据，并针对活动工作负荷提供建议。 下面概述了支持的数据仓库建议方案，以及如何应用建议的操作。

如果在 SQL 数据仓库顾问方面有任何反馈或遇到任何问题，请通过 [sqldwadvisor@service.microsoft.com](mailto:sqldwadvisor@service.microsoft.com) 联系我们。   

单击[此处](https://aka.ms/Azureadvisor)立即查看建议！ 目前，此功能仅适用于第 2 代数据仓库。 

## <a name="data-skew"></a>数据倾斜

运行工作负荷时，数据倾斜可能会造成额外的数据移动或资源瓶颈。 以下文档介绍了如何识别数据倾斜，以及如何通过选择最佳的分布键来防止发生数据倾斜。

- [识别和消除倾斜](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice) 

## <a name="no-or-outdated-statistics"></a>无统计信息或统计信息过时

欠佳的统计信息可能严重影响查询性能，因为它可能导致 SQL 数据仓库查询优化器生成欠佳的查询计划。 以下文档介绍了有关创建和更新统计信息的最佳做法：

- [创建和更新表统计信息](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistic)

针对这两条建议，顾问会持续运行以下 [T-SQL 脚本](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/samples/sqlops/MonitoringScripts/ImpactedTables)来识别受倾斜和统计信息建议影响的表。
