---
title: 管理 Azure SQL 数据仓库中的计算资源 | Microsoft Docs
description: 了解 Azure SQL 数据仓库中的性能横向扩展功能。 调整 DWU 可以实现横向扩展，暂停数据仓库可以降低成本。
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: johnmac
editor: ''
ms.assetid: e13a82b0-abfe-429f-ac3c-f2b6789a70c6
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 02/20/2018
ms.author: elbutter
ms.openlocfilehash: c34e37f0c6393c65d4b60705012769608bb7395b
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2018
---
# <a name="manage-compute-in-azure-sql-data-warehouse"></a>管理 Azure SQL 数据仓库中的计算资源
了解如何管理 Azure SQL 数据仓库中的计算资源。 可以通过暂停数据仓库来降低成本，或者根据性能需求缩放数据仓库。 

## <a name="what-is-compute-management"></a>计算管理是什么？
SQL 数据仓库的体系结构对存储和计算功能进行了分隔，允许每项功能单独进行缩放。 因此，可以独立于数据存储，根据性能需求缩放计算资源。 还可以暂停和恢复计算资源。 此体系结构的自然结果是，计算和存储的[计费](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)是独立的。 如果有一段时间不需要使用数据仓库，可以通过暂停计算来节省计算成本。 

## <a name="scaling-compute"></a>缩放计算资源
调整数据仓库的[数据仓库单位](what-is-a-data-warehouse-unit-dwu-cdwu.md)设置，可以横向扩展或还原计算资源。 添加更多的数据仓库单位后，加载和查询性能可线性提高。 SQL 数据仓库提供数据仓库单位的[服务级别](performance-tiers.md#service-levels)，确保在横向扩展或还原后性能不会有明显的变化。 

有关横向扩展的步骤，请参阅适用于 [Azure 门户](quickstart-scale-compute-portal.md)、[PowerShell](quickstart-scale-compute-powershell.md) 或 [T-SQL](quickstart-scale-compute-tsql.md) 的快速入门。 也可以使用 [REST API](sql-data-warehouse-manage-compute-rest-api.md#scale-compute) 执行横向扩展操作。

若要执行缩放操作，SQL 数据仓库首先会终止所有传入的查询，并会回滚事务以确保一致的状态。 缩放只会在事务回滚完成后发生。 对于缩放操作，系统会从计算节点分离存储层、添加计算节点，然后将存储层重新附加到计算层。 每个数据仓库存储为在计算节点之间平均分布的 60 个分布区。 添加更多计算节点可提高计算能力。 随着计算节点的增加，每个计算节点的分布区数目会减少，因此可为查询提供更高的计算能力。 同样，减少数据仓库单位会减少计算节点数目，从而减少用于查询的计算资源。

下表显示了当数据仓库单位数发生变化时，每个计算节点的分布区数目如何变化。  DWU6000 提供 60 个计算节点，实现的查询性能比 DWU100 高得多。 

| 数据仓库单位数  | 计算节点数 \# | 每个节点的分布区 \# |
| ---- | ------------------ | ---------------------------- |
| 100  | 1                  | 60                           |
| 200  | 2                  | 30                           |
| 300  | 3                  | 20                           |
| 400  | 4                  | 15                           |
| 500  | 5                  | 12                           |
| 600  | 6                  | 10                           |
| 1000 | 10                 | 6                            |
| 1200 | 12                 | 5                            |
| 1500 | 15                 | 4                            |
| 2000 | 20                 | 3                            |
| 3000 | 30                 | 2                            |
| 6000 | 60                 | 1                            |


## <a name="finding-the-right-size-of-data-warehouse-units"></a>找到数据仓库单位的适当大小

若要体验横向扩展带来的性能优势（尤其是对于较大的数据仓库单位），可以至少使用 1 TB 的数据集。 若要找到数据仓库的最佳数据仓库单位数目，请尝试纵向扩展和缩减。 加载数据后，使用不同数量的数据仓库单位运行几个查询。 由于缩放很快就能完成，可以在一个小时或更少时间内尝试一些不同级别的性能。 

有关找到最佳数据仓库单位数目的建议：

- 对于正在开发的数据仓库，可以从少量的数据仓库单位开始。  一个好的起点为 DW400 或 DW200。
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
暂停计算资源会导致存储层从计算节点分离。 将从帐户中释放计算资源。 暂停计算资源时，不会产生计算费用。 恢复计算资源会将存储重新附加到计算节点，并且恢复计算费用。 暂停数据仓库时：

* 计算和内存资源返回到数据中心的可用资源池中
* 暂停期间，数据仓库单位的费用为零。
* 不影响数据存储，数据保持不变。 
* SQL 数据仓库将取消所有正在运行或已排队的操作。

恢复数据仓库时：

* SQL 数据仓库将获取数据仓库单位设置的计算和内存资源。
* 数据仓库单位的计算费用将会恢复。
* 数据可用。
* 数据仓库联机后，需要重启工作负荷查询。

如果希望随时可访问数据仓库，请考虑将其缩减到最小大小，而不是暂停。 

有关暂停和恢复步骤，请参阅适用于 [Azure 门户](pause-and-resume-compute-portal.md)或 [PowerShell](pause-and-resume-compute-powershell.md) 的快速入门。 也可以使用[暂停 REST API](sql-data-warehouse-manage-compute-rest-api.md#pause-compute) 或[恢复 REST API](sql-data-warehouse-manage-compute-rest-api.md#resume-compute)。

## <a name="drain-transactions-before-pausing-or-scaling"></a>暂停或缩放之前清空事务
在启动暂停或缩放操作之前，我们建议先让现有事务完成。

在暂停或缩放 SQL 数据仓库时，用户一发起暂停或缩放请求，系统就会在后台取消查询。  取消简单的 SELECT 查询是很快的操作，对于暂停或缩放实例所花费的时间几乎没有什么影响。  但是，事务性查询（将修改数据或结构）可能无法快速地停止。  **按定义，事务性查询必须完全完成或回退更改。**  回滚事务性查询已完成的任务可能需要很长时间，甚至比查询应用原始更改更久。  例如，如果取消的删除行查询已经运行一小时，系统可能需要一个小时重新插入已删除的行。  如果在事务运行中运行暂停或缩放，暂停或缩放操作可能需要一些时间，因为暂停和缩放必须等回滚完成才能继续。

另请参阅[了解事务](sql-data-warehouse-develop-transactions.md)和 [优化事务][优化事务](sql-data-warehouse-develop-best-practices-transactions.md)。

## <a name="automating-compute-management"></a>将计算管理自动化
若要将计算管理操作自动化，请参阅[使用 Azure Functions 管理计算](manage-compute-with-azure-functions.md)。

每项横向扩展、暂停和恢复操作可能需要几分钟才能完成。 如果自动执行缩放、暂停或恢复操作，我们建议实现相应的逻辑来确保先完成特定的操作，然后再继续其他操作。 通过不同的终结点检查数据仓库状态可以正确实现此类操作的自动化。 

若要检查数据仓库状态，请参阅适用于 [PowerShell](quickstart-scale-compute-powershell.md#check-data-warehouse-state) 或 [T-SQL](quickstart-scale-compute-tsql.md#check-data-warehouse-state) 的快速入门。 也可以使用 [REST API](sql-data-warehouse-manage-compute-rest-api.md#check-database-state) 检查数据仓库状态。


## <a name="permissions"></a>权限

缩放数据仓库需要 [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse.md) 中所述的权限。  暂停和恢复需要 [SQL DB 参与者](../active-directory/role-based-access-built-in-roles.md#sql-db-contributor)权限，具体而言是 Microsoft.Sql/servers/databases/action 权限。


## <a name="next-steps"></a>后续步骤
计算资源管理工作的另一方面是为单个查询分配不同的计算资源。 有关详细信息，请参阅[用于工作负荷管理的资源类](resource-classes-for-workload-management.md)。
