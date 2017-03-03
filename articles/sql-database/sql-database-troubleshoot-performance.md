---
title: "SQL 数据库性能优化提示 | Microsoft 文档"
description: "有关通过评估和改进来调整 Azure SQL 数据库性能的提示。"
services: sql-database
documentationcenter: 
author: v-shysun
manager: felixwu
editor: 
keywords: "sql 性能优化, 数据库性能优化, sql 性能优化提示, sql 数据库性能优化"
ms.assetid: eb7b3f66-3b33-4e1b-84fb-424a928a6672
ms.service: sql-database
ms.custom: monitor and tune
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: v-shysun
translationtype: Human Translation
ms.sourcegitcommit: 984adf244596578a3301719e5ac2f68a841153bf
ms.openlocfilehash: 3bfcaf4ae29d23754a19a61f2775d1b12e3e69ba
ms.lasthandoff: 02/16/2017


---
# <a name="sql-database-performance-tuning-tips"></a>SQL 数据库性能调整提示
你可以更改独立数据库的[服务层](sql-database-service-tiers.md)，或随时增大弹性池的 eDTU 以提高性能，但需要首先确定改善和优化查询性能的机会。 缺少索引与查询优化不足是数据库性能不佳的常见原因。 本文提供有关 SQL 数据库中的性能优化的指南。

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="steps-to-evaluate-and-tune-database-performance"></a>评估和优化数据库性能的步骤
1. 在 [Azure 门户](https://portal.azure.com)中，单击 **SQL 数据库**，选择该数据库，然后使用“监视”图表查找接近其上限的资源。 默认将显示 DTU 消耗量。 单击“**编辑**”更改显示的时间范围和值。
2. 使用[查询性能见解](sql-database-query-performance.md)评估使用 DTU 的查询，然后使用 [SQL 数据库顾问](sql-database-advisor.md)查看用于创建和删除索引、参数化查询以及解决架构问题的建议。
3. 可以使用动态管理视图 (DMV)、扩展事件 (Xevent) 和 SSMS 中的查询存储实时获取性能参数。 有关详细的监视和优化提示，请参阅[性能指南主题](sql-database-performance-guidance.md)。

> [!IMPORTANT] 
> 建议始终使用最新版本的 Management Studio 以保持与 Microsoft Azure 和 SQL 数据库的更新同步。 [更新 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)。
>

## <a name="steps-to-improve-database-performance-with-more-resources"></a>使用更多资源提高数据库性能的步骤
1. 对于独立数据库，可以根据需要[更改服务层](sql-database-service-tiers.md)以提高数据库性能。
2. 对于多个数据库，请考虑使用[弹性池](sql-database-elastic-pool-guidance.md)自动缩放资源。


