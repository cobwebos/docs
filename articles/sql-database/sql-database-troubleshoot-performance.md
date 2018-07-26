---
title: 监视和性能优化 - Azure SQL 数据库 | Microsoft Docs
description: 有关通过评估和改进来调整 Azure SQL 数据库性能的提示。
services: sql-database
author: v-shysun
manager: craigg
editor: ''
keywords: sql 性能优化, 数据库性能优化, sql 性能优化提示, sql 数据库性能优化
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: v-shysun
ms.openlocfilehash: 79f41ab133cba539e5f855b3ab8fa21723694acb
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/17/2018
ms.locfileid: "39092556"
---
# <a name="monitoring-and-performance-tuning"></a>监视和性能优化

Azure SQL 数据库由系统自动管理，它是一个灵活的数据服务，可在其中轻松监视使用情况、添加或删除资源（CPU、内存、IO）、查找可以提高数据库性能的建议，或者让数据库适应你的工作负荷并自动优化性能。

本文概述 Azure SQL 数据库中提供的监视和性能优化选项。

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="monitoring-and-troubleshooting-database-performance"></a>监视数据库性能以及对其进行故障排除

在 Azure SQL 数据库中，可以轻松监视数据库的使用情况，以及识别可能导致性能问题的查询。 可以使用 Azure 门户或系统视图监视数据库性能。 可使用以下选项来监视数据库性能以及对其进行故障排除：

1. 在 [Azure 门户](https://portal.azure.com)中单击“SQL 数据库”，选择数据库，然后使用“监视”图表查找接近其上限的资源。 默认会显示 DTU 消耗量。 单击“**编辑**”更改显示的时间范围和值。
2. 使用 [Query Performance Insight](sql-database-query-performance.md) 来识别资源消耗量最大的查询。
3. 可以使用动态管理视图 (DMV)、扩展事件 (`XEvents`) 和 SSMS 中的查询存储实时获取性能参数。

若要使用这些报告或视图识别某个问题，请参阅[性能指南主题](sql-database-performance-guidance.md)，了解可用于提高 Azure SQL 数据库性能的方法。

> [!IMPORTANT] 
> 建议始终使用最新版本的 Management Studio 以保持与 Microsoft Azure 和 SQL 数据库的更新同步。 [更新 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)。
>

## <a name="optimize-database-to-improve-performance"></a>优化数据库以提高性能

在 Azure SQL 数据库中，可以通过查看[性能优化建议](sql-database-advisor.md)来找到提高和优化查询性能的机会，而无需更改资源。 缺少索引与查询优化不足是数据库性能不佳的常见原因。 可以应用这些优化建议来提高工作负荷的性能。
此外，可以通过应用所有已确定的建议并验证它们是否可以提高数据库的性能，让 Azure SQL 数据库[自动优化查询的性能](sql-database-automatic-tuning.md)。 可使用以下选项来提高数据库的性能：

1. 使用 [Azure SQL 数据库顾问](sql-database-advisor-portal.md)查看有关创建和删除索引、参数化查询，以及解决架构问题的建议。
2. [启用自动优化](sql-database-automatic-tuning-enable.md)，并让 Azure SQL 数据库自动修复已识别到的性能问题。

## <a name="improving-database-performance-with-more-resources"></a>使用更多的资源提高数据库的性能

最后，如果没有可行的措施可以提高数据库的性能，可以更改 Azure SQL 数据库中可用的资源量。 随时可以通过更改独立数据库的 [DTU 服务层](sql-database-service-tiers-dtu.md)或者增加弹性池的 eDTU 数目，来分配更多的资源。 或者，如果使用[基于 vCore 的购买模型](sql-database-service-tiers-vcore.md)，则可更改服务层或增加分配给数据库的资源。 
1. 对于独立数据库，可以根据需要[更改服务层](sql-database-service-tiers-dtu.md)或[计算资源](sql-database-service-tiers-vcore.md)以提高数据库性能。
2. 对于多个数据库，请考虑使用[弹性池](sql-database-elastic-pool-guidance.md)自动缩放资源。

## <a name="tune-and-refactor-application-or-database-code"></a>优化和重构应用程序或数据库代码

可以更改应用程序代码来最好地利用数据库、更改索引、强制计划，或使用提示来手动让数据库适应工作负荷。 [性能指南主题](sql-database-performance-guidance.md)文章中提供了一些有关手动优化和重写代码的指导与提示。


## <a name="next-steps"></a>后续步骤

- 若要在 Azure SQL 数据库中启用自动优化并使自动优化功能完全管理工作负荷，请参阅[启用自动优化](sql-database-automatic-tuning-enable.md)。
- 若要使用手动优化，请参阅 [Azure 门户中的优化建议](sql-database-advisor-portal.md)，然后手动应用一些可提高查询性能的建议。
- 通过更改 [Azure SQL 数据库服务层](sql-database-performance-guidance.md)来更改数据库中可用的资源
