---
title: 超大规模服务层级的性能诊断
description: 本文介绍如何排查 Azure SQL 数据库中的超大规模服务层级性能问题。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: seo-lt-2019 sqldbrb=1
ms.topic: troubleshooting
author: denzilribeiro
ms.author: denzilr
ms.reviewer: sstein
ms.date: 10/18/2019
ms.openlocfilehash: 7bd2b404627e21a80fc41a4561300d7252d1519c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "84324380"
---
# <a name="sql-hyperscale-performance-troubleshooting-diagnostics"></a>SQL 超大规模服务层级性能故障排除诊断
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

若要排查超大规模数据库中的性能问题，请根据 Azure SQL 数据库计算节点上的[常规性能优化方法](monitor-tune-overview.md)着手调查性能问题。 但是，由于超大规模数据库采用[分布式体系结构](service-tier-hyperscale.md#distributed-functions-architecture)，我们添加了附加的诊断方法用于帮助进行故障排除。 本文将描述超大规模数据库特定的诊断数据。

## <a name="log-rate-throttling-waits"></a>日志速率限制等待

每个 Azure SQL 数据库服务级别通过[日志速率调控](resource-limits-logical-server.md#transaction-log-rate-governance)来强制实施日志生成速率限制。 在超大规模数据库中，无论服务级别是什么，日志生成限制目前都设置为 100 MB/秒。 但有时，必须限制主计算副本上的日志生成速率，以保持符合可恢复性 SLA。 当 [页面服务器或其他计算副本](service-tier-hyperscale.md#distributed-functions-architecture) 明显落后于从日志服务应用新的日志记录时，会发生此限制。

以下等待类型（在 [sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql/) 中）描述了在主计算副本上限制日志速率的原因：

|Wait 类型    |说明                         |
|-------------          |------------------------------------|
|RBIO_RG_STORAGE        | 如果由于页面服务器上的日志使用延迟而导致超大规模数据库主计算节点日志生成速率受到限制，则会发生此等待。         |
|RBIO_RG_DESTAGE        | 如果由于长期日志存储的日志使用延迟而导致超大规模数据库计算节点日志生成速率受到限制，则会发生此等待。         |
|RBIO_RG_REPLICA        | 如果由于可读辅助副本的日志使用延迟而导致超大规模数据库计算节点日志生成速率受到限制，则会发生此等待。         |
|RBIO_RG_LOCALDESTAGE   | 如果由于日志服务的日志使用延迟而导致超大规模数据库计算节点日志生成速率受到限制，则会发生此等待。         |

## <a name="page-server-reads"></a>页面服务器读取

计算副本不会在本地缓存数据库的完整副本。 计算副本本地的数据存储在缓冲池（内存）和本地弹性缓冲池扩展 (RBPEX) 缓存中，该缓存是数据页面的部分（非涵盖性）缓存。 此本地 RBPEX 缓存的大小与计算大小成比例，是计算层内存的 3 倍。 RBPEX 类似于缓冲池，因为它包含最常访问的数据。 另一方面，每个页面服务器为它维护的数据库部分提供一个涵盖性的 RBPEX 缓存。

在计算副本上发出读取请求时，如果数据不在缓冲池或本地 RBPEX 缓存中，则会发出 getPage(pageId, LSN) 函数调用，并从相应的页面服务器提取页面。 从页面服务器执行的读取属于远程读取，因此，比从本地 RBPEX 执行的读取更慢。 排查 IO 相关的性能问题时，我们需要能够通过相对较慢的远程页面服务器读取来判断已完成的 IO 数。

多个动态管理视图 (DMV) 和扩展事件包含的列与字段指定了从页面服务器执行的远程读取数，可将其与读取总数进行比较。 查询存储还会捕获远程读取作为查询运行时统计信息的一部分。

- 执行 DMV 和目录视图中提供了用于报告页面服务器读取操作的列，例如：

  - [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql/)
  - [sys.dm_exec_query_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql/)
  - [sys.dm_exec_procedure_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-procedure-stats-transact-sql/)
  - [sys.dm_exec_trigger_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-trigger-stats-transact-sql/)
  - [sys.query_store_runtime_stats](/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql/)
- 页面服务器读取操作将添加到以下扩展事件：
  - sql_statement_completed
  - sp_statement_completed
  - sql_batch_completed
  - rpc_completed
  - scan_stopped
  - query_store_begin_persist_runtime_stat
  - query-store_execution_runtime_info
- ActualPageServerReads/ActualPageServerReadAheads 将添加到实际计划的查询计划 XML 中。 例如：

`<RunTimeCountersPerThread Thread="8" ActualRows="90466461" ActualRowsRead="90466461" Batches="0" ActualEndOfScans="1" ActualExecutions="1" ActualExecutionMode="Row" ActualElapsedms="133645" ActualCPUms="85105" ActualScans="1" ActualLogicalReads="6032256" ActualPhysicalReads="0" ActualPageServerReads="0" ActualReadAheads="6027814" ActualPageServerReadAheads="5687297" ActualLobLogicalReads="0" ActualLobPhysicalReads="0" ActualLobPageServerReads="0" ActualLobReadAheads="0" ActualLobPageServerReadAheads="0" />`

> [!NOTE]
> 若要在查询计划属性窗口中查看这些属性，需要安装 SSMS 18.3 或更高版本。

## <a name="virtual-file-stats-and-io-accounting"></a>虚拟文件统计信息和 IO 记帐

在 Azure SQL 数据库中，[sys.dm_io_virtual_file_stats()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) DMF 是监视 SQL 数据库 IO 的主要方式。 “超大规模”采用[分布式体系结构](service-tier-hyperscale.md#distributed-functions-architecture)，因此其 IO 特征有所不同。 本部分重点介绍如何对此 DMF 中所示的数据文件执行 IO（读取和写入）。 在超大规模数据库中，此 DMF 中显示的每个数据文件对应于一个远程页面服务器。 此处提到的 RBPEX 缓存是基于 SSD 的本地缓存，它是计算副本上的非覆盖缓存。

### <a name="local-rbpex-cache-usage"></a>本地 RBPEX 缓存使用情况

本地 RBPEX 缓存位于本地 SSD 存储中的计算副本上。 因此，针对此缓存运行的 IO 比针对远程页面服务器运行的 IO 速度更快。 目前，超大规模数据库中的 [sys.dm_io_virtual_file_stats()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) 包含一个特殊行用于报告计算副本的本地 RBPEX 缓存中完成的 IO。 对于 `database_id` 和 `file_id` 列，此行的值均为 0。 例如，以下查询返回自数据库启动以来的 RBPEX 使用情况统计信息。

`select * from sys.dm_io_virtual_file_stats(0,NULL);`

在 RBPEX 中完成的读取数与在所有其他数据文件中完成的聚合读取数之比提供了 RBPEX 缓存命中率。

### <a name="data-reads"></a>数据读取

- 当计算副本上的 SQL Server 数据库引擎发出读取请求时，这些请求可由本地 RBPEX 缓存或远程页面服务器提供服务，如果读取多个页面，则还可以通过两者的组合来提供服务。
- 当计算副本读取特定文件（例如 file_id 1）中的某些页面时，如果此数据仅驻留在本地 RBPEX 缓存中，则此读取操作的所有 IO 将计入 file_id 0 (RBPEX)。 如果该数据的某个部分位于本地 RBPEX 缓存中，而某个部分位于远程页面服务器上，则对于从 RBPEX 提供服务的部分，IO 将计入 file_id 0；对于从远程页面服务器提供服务的部分，IO 将计入 file_id 1。
- 当计算副本从页面服务器请求位于特定 [LSN](/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide/) 的页面时，如果页面服务器未捕获到请求的 LSN，则计算副本上的读取操作将会等待，直到页面服务器捕获到该 LSN，然后会将页面返回到计算副本。 对于从计算副本上的页面服务器执行的任何读取，如果该操作正在等待该 IO，则会出现 PAGEIOLATCH_* 等待类型。 在“超大规模”中，此等待时间包括捕获到页面服务器上位于所需 LSN 处的请求页面的时间，以及将该页面从页面服务器传输到计算副本所需的时间。
- 大型读取（例如预读）通常是使用[“分散/聚合”读取](/sql/relational-databases/reading-pages/)完成的。 这样就可以一次性（在 SQL Server 数据库引擎中被视为单次读取）读取最多 4 MB 的页面。 但是，当读取的数据在 RBPEX 中时，这些读取将被视为多个单独的 8 KB 读取，因为缓冲池和 RBPEX 始终使用 8 KB 页。 因此，针对 RBPEX 检测到的读取 IO 数可能大于引擎执行的实际 IO 数。

### <a name="data-writes"></a>数据写入

- 主计算副本不会直接写入页面服务器， 而是在相应的页面服务器上重播日志服务中的日志记录。
- 在计算副本上发生的写入将优先写入本地 RBPEX (file_id 0)。 对于大于 8 KB 的逻辑文件的写入（换言之，使用 " [集合写入](/sql/relational-databases/writing-pages/)" 执行的操作），每个写入操作都转换为多个 8 kb 的单个写入操作，因为缓冲池和 RBPEX 始终使用 8 kb 页。 因此，针对 RBPEX 检测到的写入 IO 数可能大于引擎执行的实际 IO 数。
- 非 RBPEX 文件，或者除 file_id 0 以外的对应于页面服务器的数据文件，也会显示写入。 在超大规模服务层级中，这些写入是模拟的，因为计算副本永远不会直接写入页面服务器。 写入 IOPS 和吞吐量被视为在计算副本上发生，但除 file_id 0 以外的数据文件的延迟不反映页面服务器写入操作的实际延迟。

### <a name="log-writes"></a>日志写入

- 在主计算副本上，日志写入计入 sys.dm_io_virtual_file_stats 的 file_id 2。 主计算副本上的日志写入将写入到日志登陆区域。
- 提交时，辅助副本上的日志记录不会强化。 在“超大规模”中，日志服务以异步方式将日志应用到次要副本。 由于日志写入实际上不是在次要副本上发生的，因此，次要副本上的日志 IO 的任何记帐仅用于跟踪目的。

## <a name="data-io-in-resource-utilization-statistics"></a>资源利用率统计信息中的数据 IO

在非超大规模数据库中，将在列中的[sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)和[sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)视图中报告针对数据文件（相对于[资源调控](/azure/sql-database/sql-database-resource-limits-database-server#resource-governance)数据 IOPS 限制）的读取和写入 IOPS `avg_data_io_percent` 。 在 Azure 门户中将同一值报告为 _数据 IO 百分比_。

在超大规模数据库中，此列将报告相对于仅限计算副本上本地存储的限制的数据 IOPS 利用率，特别是针对 RBPEX 和的 IO `tempdb` 。 此列中的100% 值表示资源调控限制了本地存储 IOPS。 如果这与性能问题相关，请优化工作负荷以生成较少的 IO，或提高数据库服务目标以提高资源调控 _最大数据 IOPS_ [限制](resource-limits-vcore-single-databases.md)。 对于 RBPEX 读取和写入的资源管理，系统会对单个 8 KB Io 进行计数，而不是由 SQL Server 数据库引擎颁发的较大 Io 计数。

不会在资源使用视图或门户中报告针对远程页面服务器的数据 IO，但会在 [ ( sys.dm_io_virtual_file_stats ](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) 中报告，如前文所述。

## <a name="additional-resources"></a>其他资源

- 有关“超大规模”单一数据库的 vCore 资源限制，请参阅[超大规模服务层级的 vCore 限制](resource-limits-vcore-single-databases.md#hyperscale---provisioned-compute---gen5)
- 有关如何优化 Azure SQL 数据库性能，请参阅 [Azure SQL 数据库中的查询性能](performance-guidance.md)
- 有关如何使用查询存储优化性能，请参阅[使用查询存储进行性能监视](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store/)
- 有关 DMV 监视脚本，请参阅[使用动态管理视图监视 Azure SQL 数据库的性能](monitoring-with-dmvs.md)
