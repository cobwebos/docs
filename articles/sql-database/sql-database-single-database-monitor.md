---
title: 在 Azure SQL 数据库中监视数据库性能 | Microsoft Docs
description: 了解使用 Azure 工具和动态管理视图监视数据库时可用的选项。
keywords: 数据库监视,云数据库性能
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: article
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: c9fa74304e8672bc18f403aae138a3c1dbea3d4e
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="monitoring-database-performance-in-azure-sql-database"></a>在 Azure SQL 数据库中监视数据库性能
若要监视 Azure 中的 SQL 数据库的性能，首先需要监视所选数据库性能级别相关的资源利用率。 监视功能可帮助确定数据库是否超出容量，或者因资源超限而遇到问题，并确定是否有必要调整数据库的性能级别和 [服务层](sql-database-service-tiers.md)。 可以使用 [Azure 门户](https://portal.azure.com)中的图形工具或使用 SQL [动态管理视图](https://msdn.microsoft.com/library/ms188754.aspx)来监视数据库。

> [!TIP]
> 使用 [Azure SQL Intelligent Insights](sql-database-intelligent-insights.md) 自动监视数据库性能。 检测到性能问题后，将生成一个包含问题的详细信息和根本原因分析 (RCA) 的诊断日志。 在可能的情况下提供性能改善建议。
>

## <a name="monitor-databases-using-the-azure-portal"></a>使用 Azure 门户监视数据库
在 [Azure 门户](https://portal.azure.com/)中，可以通过选择数据库并单击“监视”图表来监视单一数据库的利用率。 这会显示“指标”窗口，可通过单击“编辑图表”按钮来对其进行更改。 添加以下指标：

* CPU 百分比
* DTU 百分比
* 数据 IO 百分比
* 数据库大小百分比

添加这些指标后，可以继续在“监视”图表上查看它们，并可在“指标”窗口上查看更多详细信息。 **DTU** 的平均利用率百分比。 参阅 [服务层](sql-database-service-tiers.md) 文章，了解有关 DTU 的详细信息。

![监视数据库服务层的性能。](./media/sql-database-single-database-monitoring/sqldb_service_tier_monitoring.png)

还可以针对性能指标配置警报。 在“指标”窗口中单击“添加警报”按钮。 按照向导说明来配置警报。 可以选择在指标超出特定阈值或指标低于特定阈值时显示警报。

例如，如果预期数据库的工作负荷会增长，则可以选择配置在数据库的任何性能指标达到 80% 时发出电子邮件警报。 可以将此警报用作预警，以确定你何时需要切换到下一个更高的性能级别。

性能指标还可以帮助你确定是否能够降级到更低的性能级别。 假定你正在使用一个标准 S2 数据库并且所有性能指标均显示该数据库在任何给定时间平均的使用率不超过 10%。 很可能该数据库在标准 S1 中会很好地正常工作。 但是，在决定转换到更低的性能级别之前，请注意出现峰值或波动情况的工作负荷。

## <a name="monitor-databases-using-dmvs"></a>使用 DMV 监视数据库
在门户中公开的相同指标也可以通过系统视图查看：服务器的逻辑 [master](https://msdn.microsoft.com/library/dn269979.aspx) 数据库中的 **sys.resource_stats**，以及用户数据库中的 [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx)。 如果需要在更长时间段内监视更粗粒度的数据，请使用 **sys.resource_stats**。 如果需要在较小的时间范围内监视更细粒度的数据，请使用 **sys.dm_db_resource_stats**。 有关详细信息，请参阅 [Azure SQL 数据库性能指南](sql-database-single-database-monitor.md#monitor-resource-use)。

> [!NOTE]
> 如果在已停用的 Web 和 Business Edition 数据库中使用，**sys.dm_db_resource_stats** 将返回空结果集。
>
>

### <a name="monitor-resource-use"></a>监视资源使用情况

可以使用 [SQL 数据库 Query Performance Insight](sql-database-query-performance.md) 和 [Query Store](https://msdn.microsoft.com/library/dn817826.aspx) 监视资源使用情况。

也可以使用以下两个视图来监视使用情况：

* [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx)
* [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx)

#### <a name="sysdmdbresourcestats"></a>sys.dm_db_resource_stats
可以在每个 SQL 数据库中使用 [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) 视图。 **Sys.dm_db_resource_stats** 视图显示相对于服务层的最新资源使用数据。 CPU、数据 IO、日志写入以及内存的平均百分比每 15 秒记录一次，并保留 1 小时。

由于此视图提供了更精细的资源使用情况，因此首先将 **sys.dm_db_resource_stats** 用于任何当前状态分析或故障排除。 例如，此查询显示过去一小时的当前数据库平均和最大资源使用情况：

    SELECT  
        AVG(avg_cpu_percent) AS 'Average CPU use in percent',
        MAX(avg_cpu_percent) AS 'Maximum CPU use in percent',
        AVG(avg_data_io_percent) AS 'Average data IO in percent',
        MAX(avg_data_io_percent) AS 'Maximum data IO in percent',
        AVG(avg_log_write_percent) AS 'Average log write use in percent',
        MAX(avg_log_write_percent) AS 'Maximum log write use in percent',
        AVG(avg_memory_usage_percent) AS 'Average memory use in percent',
        MAX(avg_memory_usage_percent) AS 'Maximum memory use in percent'
    FROM sys.dm_db_resource_stats;  

有关其他查询，请参阅 [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) 中的示例。

#### <a name="sysresourcestats"></a>sys.resource_stats
**master** 数据库中的 [Sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) 视图包含可帮助监视 SQL 数据库在特定服务层和性能级别的性能。 每 5 分钟收集一次数据，并且会保留大约 14 天。 此视图可用于 SQL 数据库使用资源的方式的长期历史分析。

下图显示一周内每小时的 P2 性能级别高级数据库的 CPU 资源使用情况。 此图形从周一开始，显示 5 个工作日，并显示周末（应用程序上很少发生的情况）。

![SQL 数据库资源使用情况](./media/sql-database-performance-guidance/sql_db_resource_utilization.png)

从数据而言，此数据库当前有一个峰值 CPU 负载刚好超过相对于 P2 性能级别的 50% CPU 使用率（星期二中午）。 如果 CPU 是应用程序资源配置文件的决定因素，可以决定 P2 是适当的性能级别以保证工作负荷始终适合。 如果希望应用程序可以随时间增长，最好具有额外的资源缓冲，以便应用程序不会达到性能级别的限制。 如果增加性能级别，则有助于避免当数据库没有足够能力有效处理请求（尤其是在易受延迟影响的环境中）时向客户显示错误。 一个示例是支持应用程序根据数据库调用结果绘制网页的数据库。

其他应用程序类型可能以不同的方式解释同一图形。 例如，如果某个应用程序尝试每天处理工资数据并使用相同的图表，则在 P1 性能级别也许就能让此类“批处理作业”模型正常工作。 P1 性能级别有 100 个 DTU，P2 性能级别有 200 个 DTU。 P1 性能级别提供的性能是 P2 性能级别的一半。 因此，P2 中 50% 的 CPU 使用率相当于 P1 中 100 % 的 CPU 使用率。 如果应用程序没有超时，则作业耗时 2 小时或 2.5 小时完成并不重要（如果今天完成）。 此类别中的应用程序可能使用 P1 性能级别。 可以充分利用一天之中资源使用率较低的时间段，以便“大峰值”可以溢出到当天稍后的某个低谷。 只要作业可以每天按时完成，P1 性能级别就适用于该类型的应用程序（且节省费用）。

Azure SQL 数据库在每个服务器的 **master** 数据库的 **sys.resource_stats** 视图中，公开每个活动数据库的资源耗用信息。 表中的数据以 5 分钟为间隔收集而得。 对于基本、标准和高级服务层，数据可能需要再耗费 5 分钟才会出现在表中，以使此数据更有利于历史分析而非接近实时的分析。 查询 **sys.resource_stats** 视图，以查看数据库的最近历史记录和验证你选择的保留是否提供了所需的性能。

> [!NOTE]
> 必须连接到逻辑 SQL 数据库服务器的 **master** 数据库，才能查询下面示例中的 **sys.resource_stats**。
> 
> 

此示例演示如何公开此视图中的数据：

    SELECT TOP 10 *
    FROM sys.resource_stats
    WHERE database_name = 'resource1'
    ORDER BY start_time DESC

![sys.resource_stats 目录视图](./media/sql-database-performance-guidance/sys_resource_stats.png)

下面的示例演示可以用不同方式使用 **sys.resource_stats** 目录视图以获取有关 SQL 数据库使用资源的方式的信息：

1. 若要查看数据库 userdb1 过去一周的资源使用情况，可以运行此查询：
   
        SELECT *
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND
              start_time > DATEADD(day, -7, GETDATE())
        ORDER BY start_time DESC;
2. 要评估工作负荷与性能级别的适合程度，需要向下钻取资源指标的每个方面：CPU、读取数、写入数、辅助进程数和会话数。 下面是使用 **sys.resource_stats** 的修订查询，用于报告这些资源度量值的平均值和最大值：
   
        SELECT
            avg(avg_cpu_percent) AS 'Average CPU use in percent',
            max(avg_cpu_percent) AS 'Maximum CPU use in percent',
            avg(avg_data_io_percent) AS 'Average physical data IO use in percent',
            max(avg_data_io_percent) AS 'Maximum physical data IO use in percent',
            avg(avg_log_write_percent) AS 'Average log write use in percent',
            max(avg_log_write_percent) AS 'Maximum log write use in percent',
            avg(max_session_percent) AS 'Average % of sessions',
            max(max_session_percent) AS 'Maximum % of sessions',
            avg(max_worker_percent) AS 'Average % of workers',
            max(max_worker_percent) AS 'Maximum % of workers'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
3. 使用每个资源指标的平均值和最大值信息，可以评估工作负荷与所选性能级别的适合程度。 通常情况下，**sys.resource_stats** 中的平均值提供了一个用于目标大小的良好基准。 它应该是主要测量标杆。 例如，你可能正在使用 S2 性能级别的标准服务层。 CPU 和 IO 读写的平均使用百分比低于 40%，平均辅助角色数低于 50，平均会话数低于 200。 工作负荷可能适合 S1 性能级别。 很容易查看数据库是否在辅助角色和会话限制内。 要查看数据库是否适合 CPU 和读写数等更低性能级别，请将更低性能级别的 DTU 数除以当前性能级别的 DTU 数，并将结果乘以 100：
   
    **S1 DTU / S2 DTU * 100 = 20 / 50 * 100 = 40**
   
    结果是以百分比表示的两个性能级别之间的相对性能差异。 如果资源率使用不超出此量，则工作负荷可能适合更低的性能级别。 但是，需要查看资源用量值的所有范围，并按百分比确定数据库工作负荷适合更低性能级别的频率。 以下查询会根据此示例中计算得出的 40% 阈值，输出每个资源维度的适合百分比：
   
        SELECT
            (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent'
            ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent'
            ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data IO Fit Percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
   
    根据数据库服务级别目标 (SLO)，可以决定工作负荷是否适合更低的性能级别。 如果数据库工作负荷 SLO 为 99.9%，而上述查询针对所有三个资源维度返回的值大于 99.9%，则工作负荷可能适合更低的性能级别。
   
    查看适合性百分比还可以深入分析是否应转到下一个更高的性能级别以满足 SLO。 例如，userdb1 显示过去一周的如下 CPU 使用率：
   
   | 平均 CPU 百分比 | 最大 CPU 百分比 |
   | --- | --- |
   | 24.5 |100.00 |
   
    平均 CPU 大约是性能级别限制的四分之一，这意味着它很适合数据库的性能级别限制。 但是，最大值显示该数据库达到了性能级别的限制。 在这种情况下，是否需要转到下一个更高的性能级别？ 查看工作负荷达到 100% 的次数，并将这种情况与数据库工作负荷 SLO 进行比较。
   
        SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log write fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical data IO fit percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
   
    如果对于三个资源维度中的任何一个维度，此查询返回的值小于 99.9%，请考虑转到下一个更高的性能级别，或使用应用程序优化技术来减少 SQL 数据库上的负载。
4. 本练习还应将未来预计的工作负荷增加考虑在内。

对于弹性池，可以使用本节中所述的方法来监视池中的单个数据库。 但你还可以在总体上监视池。 有关信息，请参阅[监视和管理弹性池](sql-database-elastic-pool-manage-portal.md)。


### <a name="maximum-concurrent-requests"></a>最大并发请求数
若要查看并发请求数，请在 SQL 数据库中运行以下 Transact-SQL 查询：

    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R

若要分析本地 SQL Server 数据库的工作负荷，请修改此查询以筛选要分析的特定数据库。 例如，如果有一个名为 MyDatabase 的本地数据库，此 TRANSACT-SQL 查询返回该数据库中的并发请求计数：

    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R
    INNER JOIN sys.databases D ON D.database_id = R.database_id
    AND D.name = 'MyDatabase'

这只是某一时刻的快照。 要更好地了解工作负荷和并发请求要求，需要在一段时间内收集多个样本。

### <a name="maximum-concurrent-logins"></a>最大并发登录数
可以通过分析用户和应用程序模式来了解登录频率。 还可以在测试环境中运行实际负荷，确保不会超过本文所讨论的这样或那样的限制。 没有单个查询或动态管理视图 (DMV) 可以显示并发登录计数或历史记录。

如果多个客户端使用相同的连接字符串，该服务也会对每个登录名进行身份验证。 如果 10 个用户使用相同的用户名和密码同时连接到数据库，将有 10 个并发登录。 此限制仅适用于登录和身份验证期间。 如果相同的 10 个用户按顺序连接到数据库，则并发登录数将不会大于 1。

> [!NOTE]
> 此限制目前不适用于弹性池中的数据库。
> 
> 

### <a name="maximum-sessions"></a>最大会话数
若要查看当前活动会话数，请在 SQL 数据库中运行以下 Transact-SQL 查询：

    SELECT COUNT(*) AS [Sessions]
    FROM sys.dm_exec_connections

如果要分析本地 SQL Server 工作负荷，可以对查询进行修改，使之专注于特定的数据库。 如果考虑将数据库移至 Azure SQL 数据库，此查询可帮助你确定该数据库可能的会话需求。

    SELECT COUNT(*)  AS [Sessions]
    FROM sys.dm_exec_connections C
    INNER JOIN sys.dm_exec_sessions S ON (S.session_id = C.session_id)
    INNER JOIN sys.databases D ON (D.database_id = S.database_id)
    WHERE D.name = 'MyDatabase'

同样，这些查询将返回时间点计数。 如果在一段内收集多个样本，则会对会话的使用情况有最佳了解。

对于 SQL 数据库分析，也可以通过查询 [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) 视图并查看 **active_session_count** 列获取会话的历史统计信息。 

## <a name="next-steps"></a>后续步骤

- 使用 [Azure SQL 数据库自动优化](sql-database-automatic-tuning.md)自动优化数据库索引和查询执行计划。
- 使用 [Azure SQL Intelligent Insights](sql-database-intelligent-insights.md) 自动监视数据库性能。 此功能提供性能问题的诊断信息和根本原因分析。
