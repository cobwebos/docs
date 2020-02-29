---
title: 管理 SQL 池的计算资源
description: 了解 Azure Synapse Analytics SQL 池中的性能横向扩展功能。 调整 DWU 可以实现横向扩展，暂停数据仓库可以降低成本。
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 11/12/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: ef860fb607a4f241e6428b714b022058a4697228
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78197277"
---
# <a name="manage-compute-in-azure-synapse-analytics-data-warehouse"></a>管理 Azure Synapse Analytics 数据仓库中的计算

了解如何管理 Azure Synapse Analytics SQL 池中的计算资源。 通过暂停 SQL 池降低成本，或根据性能需求缩放数据仓库。 

## <a name="what-is-compute-management"></a>计算管理是什么？

数据仓库的体系结构将存储和计算分离，使每个体系结构都可以独立缩放。 因此，可以独立于数据存储，根据性能需求缩放计算资源。 还可以暂停和恢复计算资源。 此体系结构的自然结果是，计算和存储的[计费](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)是独立的。 如果有一段时间不需要使用数据仓库，可以通过暂停计算来节省计算成本。 

## <a name="scaling-compute"></a>缩放计算资源

可以通过调整 SQL 池的[数据仓库单位](what-is-a-data-warehouse-unit-dwu-cdwu.md)设置来横向扩展或缩减计算。 添加更多的数据仓库单位后，加载和查询性能可线性提高。 

有关横向扩展的步骤，请参阅适用于 [Azure 门户](quickstart-scale-compute-portal.md)、[PowerShell](quickstart-scale-compute-powershell.md) 或 [T-SQL](quickstart-scale-compute-tsql.md) 的快速入门。 也可以使用 [REST API](sql-data-warehouse-manage-compute-rest-api.md#scale-compute) 执行横向扩展操作。

若要执行缩放操作，SQL 池首先会中止所有传入的查询，然后回滚事务以确保状态一致。 缩放只会在事务回滚完成后发生。 对于缩放操作，系统会将存储层从计算节点分离，添加计算节点，然后将存储层重新附加到计算层。 每个 SQL 池都存储为60分发，它们均匀地分布到计算节点。 添加更多计算节点会增加计算能力。 随着计算节点数量的增加，每个计算节点的分布数会减少，从而为查询提供更多的计算能力。 同样，减少数据仓库单位会减少计算节点的数量，从而减少查询的计算资源。

下表显示了当数据仓库单位数发生变化时，每个计算节点的分布区数目如何变化。  DW30000c 提供60个计算节点，实现比 DW100c 更高的查询性能。 

| 数据仓库单位数  | 计算节点 \# | 每个节点的分布区 \# |
| -------- | ---------------- | -------------------------- |
| DW100c   | 1                | 60                         |
| DW200c   | 1                | 60                         |
| DW300c   | 1                | 60                         |
| DW400c   | 1                | 60                         |
| DW500c   | 1                | 60                         |
| DW1000c  | 2                | 30                         |
| DW1500c  | 3                | 20                         |
| DW2000c  | 4                | 15                         |
| DW2500c  | 5                | 12                         |
| DW3000c  | 6                | 10                         |
| DW5000c  | 10               | 6                          |
| DW6000c  | 12               | 5                          |
| DW7500c  | 15               | 4                          |
| DW10000c | 20               | 3                          |
| DW15000c | 30               | 2                          |
| DW30000c | 60               | 1                          |


## <a name="finding-the-right-size-of-data-warehouse-units"></a>找到数据仓库单位的适当大小

若要体验横向扩展带来的性能优势（尤其是对于较大的数据仓库单位），可以至少使用 1 TB 的数据集。 若要查找 SQL 池的最佳数据仓库单位数，请尝试增加和减少。 加载数据后，使用不同数量的数据仓库单位运行几个查询。 由于缩放很快就能完成，可以在一个小时或更少时间内尝试一些不同级别的性能。 

有关找到最佳数据仓库单位数目的建议：

- 对于正在开发的 SQL 池，请先选择较小的数据仓库单位数。  很好的起点是 DW400c 或 DW200c。
- 监视应用程序性能，将所选数据仓库单位数目与观测到的性能变化进行比较。
- 采用线性缩放，确定需要以多大的增量来增加或减少数据仓库单位。 
- 继续进行调整，直到达到业务要求的最佳性能级别。

## <a name="when-to-scale-out"></a>何时横向扩展

横向扩展数据仓库单位会影响性能的以下方面：

- 以线性方式改善系统的扫描、聚合和 CTAS 语句性能。
- 增加用于加载数据的读取器和编写器数量。
- 并发查询和并发槽的最大数量。

有关何时横向扩展数据仓库单位的建议：

- 执行繁重的数据加载或转换操作时，可以横向扩展以更快速地获取数据。
- 高峰业务时段，可以横向扩展以容纳更大数量的并发查询。 

## <a name="what-if-scaling-out-does-not-improve-performance"></a>如果横向扩展无法提高性能怎么办？

添加数据仓库单位可提高并行度。 如果工作在计算节点之间均匀拆分，则更高的并行度可以提高查询性能。 有几种原因会导致横向扩展不能改变性能。 数据可能在分布区中扭曲，或者查询可能引入了大量的数据移动操作。 若要调查查询性能问题，请参阅[排查性能问题](sql-data-warehouse-troubleshoot.md#performance)。 

## <a name="pausing-and-resuming-compute"></a>暂停和恢复计算资源

暂停计算资源会导致存储层从计算节点分离。 计算资源将从你的帐户中释放。 暂停计算资源时，不会产生计算费用。 恢复计算资源会将存储重新附加到计算节点，并且恢复计算费用。 暂停 SQL 池时：

* 计算和内存资源返回到数据中心的可用资源池中
* 暂停期间，数据仓库单位的费用为零。
* 不影响数据存储，数据保持不变。 
* 所有正在运行或已排队的操作都将被取消。

恢复 SQL 池时：

* SQL 池获取数据仓库单位设置的计算资源和内存资源。
* 数据仓库单位的计算费用将会恢复。
* 数据可用。
* SQL 池联机后，需要重新启动工作负荷查询。

如果你始终希望可以访问 SQL 池，请考虑将其缩放到最小大小，而不是暂停。 

有关暂停和恢复步骤，请参阅适用于 [Azure 门户](pause-and-resume-compute-portal.md)或 [PowerShell](pause-and-resume-compute-powershell.md) 的快速入门。 也可以使用[暂停 REST API](sql-data-warehouse-manage-compute-rest-api.md#pause-compute) 或[恢复 REST API](sql-data-warehouse-manage-compute-rest-api.md#resume-compute)。

## <a name="drain-transactions-before-pausing-or-scaling"></a>暂停或缩放之前清空事务

在启动暂停或缩放操作之前，我们建议先让现有事务完成。

暂停或缩放 SQL 池时，在后台启动暂停或缩放请求时，将取消查询。 取消简单的 SELECT 查询是很快的操作，对于暂停或缩放实例所花费的时间几乎没有什么影响。  但是，事务性查询（将修改数据或结构）可能无法快速地停止。 **按定义，事务性查询必须完全完成或回退更改。** 回滚事务性查询已完成的任务可能需要很长时间，甚至比查询应用原始更改更久。 例如，如果取消的删除行查询已经运行一小时，系统可能需要一个小时重新插入已删除的行。 如果在事务运行中运行暂停或缩放，暂停或缩放操作可能需要一些时间，因为暂停和缩放必须等回滚完成才能继续。

另请参阅[了解事务](sql-data-warehouse-develop-transactions.md)和[优化事务](sql-data-warehouse-develop-best-practices-transactions.md)。

## <a name="automating-compute-management"></a>将计算管理自动化

若要将计算管理操作自动化，请参阅[使用 Azure Functions 管理计算](manage-compute-with-azure-functions.md)。

每项横向扩展、暂停和恢复操作可能需要几分钟才能完成。 如果自动执行缩放、暂停或恢复操作，我们建议实现相应的逻辑来确保先完成特定的操作，然后再继续其他操作。 通过不同的终结点检查 SQL 池状态，可正确实现此类操作的自动化。 

若要检查 SQL 池状态，请参阅[PowerShell](quickstart-scale-compute-powershell.md#check-data-warehouse-state)或[t-sql](quickstart-scale-compute-tsql.md#check-data-warehouse-state)快速入门。 你还可以使用[REST API](sql-data-warehouse-manage-compute-rest-api.md#check-database-state)检查 SQL 池状态。


## <a name="permissions"></a>权限

缩放 SQL 池需要[ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse)中所述的权限。  暂停和恢复需要 [SQL DB 参与者](../role-based-access-control/built-in-roles.md#sql-db-contributor)权限，具体而言是 Microsoft.Sql/servers/databases/action 权限。


## <a name="next-steps"></a>后续步骤
请参阅管理计算资源的操作方法指南管理计算资源的[另一个方面](manage-compute-with-azure-functions.md)是为单独的查询分配不同的计算资源。 有关详细信息，请参阅[用于工作负荷管理的资源类](resource-classes-for-workload-management.md)。
