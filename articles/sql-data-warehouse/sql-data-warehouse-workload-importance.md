---
title: 工作负载重要性
description: 有关为 Azure SQL 数据仓库中的查询设置重要性的指南。
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload-management
ms.date: 05/01/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: 76a77c1833ae1827f2a6a9b577b3cca51b35a344
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75351430"
---
# <a name="azure-sql-data-warehouse-workload-importance"></a>Azure SQL 数据仓库工作负荷重要性

本文介绍工作负荷重要性如何影响 SQL 数据仓库请求的执行顺序。

## <a name="importance"></a>Importance

> [!Video https://www.youtube.com/embed/_2rLMljOjw8]

业务需求可能要求数据仓库工作负荷比其他工作负载更重要。  考虑在会计期结束前加载任务关键销售数据的方案。  其他源的数据加载（如天气数据）没有严格的 Sla。 为加载销售数据的请求设置高重要性，并为加载天气数据的请求设置低重要性，可确保销售数据负载首先获取对资源的访问权限，并快速完成。

## <a name="importance-levels"></a>重要性级别

有五个重要性级别： low、below_normal、normal、above_normal 和 high。  不设置重要性的请求将被分配默认级别 "正常"。 具有相同重要性级别的请求具有当天存在的相同计划行为。

## <a name="importance-scenarios"></a>重要性方案

除了上面介绍的销售和天气数据的基本重要性方案之外，还有其他一些方案，工作负荷重要性有助于满足数据处理和查询需求。

### <a name="locking"></a>锁定

对读取和写入活动的锁定的访问是一种自然争用区域。 [分区切换](/azure/sql-data-warehouse/sql-data-warehouse-tables-partition)或[重命名对象](/sql/t-sql/statements/rename-transact-sql?view=azure-sqldw-latest)等活动需要提升的锁定。  如果没有工作负荷重要性，SQL 数据仓库将针对吞吐量进行优化。 针对吞吐量进行优化意味着在运行和排队请求具有相同的锁定需求和资源时，排队的请求可以绕过传入请求队列的更高锁定需求的请求。 一旦将工作负荷重要性应用于具有更高锁定需求的请求。 重要性较高的请求会在具有较低重要性的请求之前运行。

请考虑以下示例：

- Q1 正在积极运行并从 Prd.salesfact 中选择数据。
- 第2季度排队等候完成 Q1。  它在上午9点提交，尝试将新数据分区到 Prd.salesfact 中。
- 3季度提交于9：01am，并希望从 Prd.salesfact 选择数据。

如果第2季度和第3季度仍在执行，则第三季度会开始执行。 第2季度将继续等待 Prd.salesfact 上的排他锁。  如果第2季度的重要性高于第3季度，则第三季度将等待第二季度完成，然后才能开始执行。

### <a name="non-uniform-requests"></a>非统一请求

如果提交具有不同资源类的请求，则重要性有助于满足查询要求的另一个方案是。  如前所述，SQL 数据仓库在相同的重要性下进行优化，以提高吞吐量。 当混合大小请求（如 smallrc 或 mediumrc）排队时，SQL 数据仓库将选择适合可用资源的最早到达请求。 如果应用工作负荷重要性，则计划下一个重要性最高的请求。
  
请在 DW500c 上考虑以下示例：

- Q1、第2季度、第3季度和第四季度正在运行 smallrc 查询。
- Q5 随 mediumrc 资源类在上午9点提交。
- Q6 是在9：01am 上的 smallrc 资源类中提交的。

因为 Q5 是 mediumrc，所以它需要两个并发槽。 Q5 需要等待两个正在运行的查询完成。  但是，当其中一个正在运行的查询（Q1-Q4）完成时，将立即计划 Q6，因为存在用于执行查询的资源。  如果 Q5 具有比 Q6 更高的重要性，则 Q6 将等待，直到运行 Q5，然后才能开始执行。

## <a name="next-steps"></a>后续步骤

- 有关创建分类器的详细信息，请参阅[创建工作负荷分类器（transact-sql）](/sql/t-sql/statements/create-workload-classifier-transact-sql)。  
- 有关 SQL 数据仓库工作负荷分类的详细信息，请参阅[工作负荷分类](sql-data-warehouse-workload-classification.md)。  
- 有关如何创建工作负荷分类器的详细说明，请参阅快速入门[创建工作负荷分类器](quickstart-create-a-workload-classifier-tsql.md)。 
- 请参阅有关[配置工作负荷重要性](sql-data-warehouse-how-to-configure-workload-importance.md)和[管理和监视工作负荷管理](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)的操作指南文章。
- 参阅 [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest) 以查看查询和分配的重要性。
