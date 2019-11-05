---
title: Azure SQL 数据库-超大规模服务层中的性能诊断 |Microsoft Docs
description: 本文介绍如何排查 Azure SQL 数据库中的超大规模性能问题。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: troubleshooting
author: denzilribeiro
ms.author: denzilr
ms.reviewer: sstein
ms.date: 10/18/2019
ms.openlocfilehash: fa8b02cf5ee6f24bcc77aae057f41842da721981
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73521140"
---
# <a name="sql-hyperscale-performance-troubleshooting-diagnostics"></a>SQL 超大规模性能疑难解答诊断


为了解决超大规模数据库中的性能问题，Azure SQL database 计算节点上的[常规性能优化方法](sql-database-monitor-tune-overview.md)是性能调查的起点。 但是，鉴于超大规模的[分布式体系结构](sql-database-service-tier-hyperscale.md#distributed-functions-architecture)，增加了附加的诊断帮助。 本文介绍了超大规模特定的诊断数据。


## <a name="log-rate-throttling-waits"></a>日志速率限制等待


每个 Azure SQL Database 服务级别都具有通过[日志速率管理](sql-database-resource-limits-database-server.md#transaction-log-rate-governance)强制实施的日志生成速率限制。 在超大规模中，日志生成限制目前设置为 100 MB/秒，而不考虑服务级别。 但是，在某些情况下，必须限制主计算副本上的日志生成速率以保持可恢复性 Sla。 当[页面服务器或其他计算副本](sql-database-service-tier-hyperscale.md#distributed-functions-architecture)明显落后于从日志服务应用新的日志记录时，会发生此限制。

以下等待类型（在[_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql/)中）描述了在主要计算副本上可限制日志速率的原因：

|Wait 类型    |说明                         |
|-------------          |------------------------------------|
|RBIO_RG_STORAGE        | 由于页面服务器上延迟的日志消耗导致超大规模数据库主计算节点日志生成速率被阻止时发生。         |
|RBIO_RG_DESTAGE        | 当超大规模数据库计算节点日志生成速率由于长期日志存储延迟的日志消耗而受到限制时发生。         |
|RBIO_RG_REPLICA        | 由于可读辅助副本的延迟日志消耗导致超大规模数据库计算节点日志生成速率被阻止时发生。         |
|RBIO_RG_LOCALDESTAGE   | 由于日志服务延迟日志消耗而正在限制超大规模数据库计算节点日志生成速率时出现。         |


## <a name="page-server-reads"></a>页服务器读取

计算副本不在本地缓存数据库的完整副本。 计算副本本地的数据存储在缓冲池（内存）和本地弹性缓冲池扩展（RBPEX）缓存中，这是数据页的部分（非覆盖）缓存。 此本地 RBPEX 缓存按比例调整大小以计算大小，并为计算层的内存的3倍。 RBPEX 类似于缓冲池，因为它具有最常访问的数据。 另一方面，每个页面服务器都有一个涵盖 RBPEX 缓存，用于维护的数据库部分。
 
在计算副本上发出读取时，如果数据不存在于缓冲池或本地 RBPEX 缓存中，则会发出 getPage （pageId，LSN）函数调用，并从相应的页面服务器获取页面。 页面服务器上的读取是远程读取，因此比从本地 RBPEX 读取更慢。 排查与 IO 相关的性能问题时，我们需要能够通过相对较慢的远程页面服务器读取来了解已完成的 Io 数。

多个 Dmv 和扩展事件都有列和字段，它们指定了可以与总读取数进行比较的页服务器远程读取数。 

- 用于报表页服务器读取的列可用于执行 Dmv，如：
    - [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql/)
    - [sys.dm_exec_query_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql/)
    - [sys.dm_exec_procedure_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-procedure-stats-transact-sql/)
    - [sys.databases _exec_trigger_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-trigger-stats-transact-sql/)
- 页面服务器读取操作将添加到以下扩展事件中：
    - sql_statement_completed
    - sp_statement_completed
    - sql_batch_completed
    - rpc_completed
    - scan_stopped
    - query_store_begin_persist_runtime_stat
    - 查询-store_execution_runtime_info
- 对于实际计划，ActualPageServerReads/ActualPageServerReadAheads 将添加到查询计划 XML 中。 例如：

`<RunTimeCountersPerThread Thread="8" ActualRows="90466461" ActualRowsRead="90466461" Batches="0" ActualEndOfScans="1" ActualExecutions="1" ActualExecutionMode="Row" ActualElapsedms="133645" ActualCPUms="85105" ActualScans="1" ActualLogicalReads="6032256" ActualPhysicalReads="0" ActualPageServerReads="0" ActualReadAheads="6027814" ActualPageServerReadAheads="5687297" ActualLobLogicalReads="0" ActualLobPhysicalReads="0" ActualLobPageServerReads="0" ActualLobReadAheads="0" ActualLobPageServerReadAheads="0" />`

> [!NOTE]
> 若要在 SSMS 中的 "查询计划属性" 窗口中查看这些属性，将需要 SSMS 18.3 或更高版本。


## <a name="virtual-file-stats-and-io-accounting"></a>虚拟文件统计信息和 IO 记帐

在 Azure SQL 数据库中， [_io_virtual_file_stats （）](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) DMF 是监视 SQL Server io 的主要方式。 超大规模上的 IO 特征因其[分布式体系结构](sql-database-service-tier-hyperscale.md#distributed-functions-architecture)而异。 在本部分中，我们将重点介绍 IO （读取和写入）到数据文件中，如此 DMF 中所示。 在超大规模中，此 DMF 中显示的每个数据文件都对应于一个远程页面服务器。 此处提到的 RBPEX 缓存是基于 SSD 的本地缓存，它是计算副本上的非覆盖缓存。


### <a name="local-rbpex-cache-usage"></a>本地 RBPEX 缓存使用情况

本地 RBPEX 缓存位于本地 SSD 存储上的计算节点上。 因此，此 RBPEX 缓存上的 IO 比远程页面服务器上的 IO 更快。 目前，超大规模数据库中的[_io_virtual_file_stats （）](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/)有一个特殊的行，它报告在计算副本上本地 RBPEX 缓存上完成的 io。 对于 `database_id` 列和 `file_id` 列，该行的值均为0。 例如，以下查询将返回自数据库启动以来的 RBPEX 使用情况统计信息。

`select * from sys.dm_io_virtual_file_stats(0,NULL);`

在对所有其他数据文件执行的聚合读取操作上，对 RBPEX 进行的读取比提供 RBPEX 缓存命中率。


### <a name="data-reads"></a>数据读取

- 当读取由计算副本上的 SQL Server 引擎颁发时，它们可以由本地 RBPEX 缓存或远程页面服务器来提供，也可以通过两种方法的组合来处理：读取多个页面。
- 当计算副本从特定文件读取某些页面（例如 file_id 1）时，如果此数据仅驻留在本地 RBPEX 缓存上，则此读取的所有 IO 都将针对 file_id 0 （RBPEX）进行计算。 如果该数据的某些部分位于本地 RBPEX 缓存中，而某个部分位于远程页面服务器上，则 IO 对于从 RBPEX 提供的部分的 file_id 为0，而从远程页面服务器提供的部分则是针对 file_id 1。 
- 当计算副本从页面服务器请求特定[LSN](/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide/)上的页面时，如果页面服务器尚未与请求的 lsn 建立，则计算副本上的读取将等待，直到页面服务器在页面返回到计算副本之前为止。 对于在计算副本上从页面服务器读取的任何读取，如果在该 IO 上等待，则会看到 PAGEIOLATCH_ * wait 类型。 此等待时间包括将页面服务器上请求的页面捕获到所需 LSN 所需的时间，以及将页面从页面服务器传输到计算副本所需的时间。
- 较大的读取（如预读）通常是使用["散播-聚集" 读取](/sql/relational-databases/reading-pages/)来完成的。 这允许一次读取最多 4 MB 的页面，被视为 SQL Server 引擎中的单个读取。 但是，当读取的数据在 RBPEX 中时，这些读取会被视为多个单独的 8 KB 读取，因为缓冲池和 RBPEX 始终使用 8 KB 页。 因此，针对 RBPEX 检测到的读取 Io 数可能大于引擎执行的实际 Io 数。


### <a name="data-writes"></a>数据写入

- 主计算副本不会直接写入页面服务器。 相反，日志服务中的日志记录将在相应的页面服务器上重播。 
- 计算副本上发生的写入主要写入到本地 RBPEX （file_id 0）。 对于大于 8 KB 的逻辑文件的写入（即，使用[集合写入](/sql/relational-databases/writing-pages/)完成的写入操作），每个写入操作都将转换为多个 8 kb 的单个写入操作，因为缓冲池和 RBPEX 始终使用 8 kb 页。 因此，针对 RBPEX 检测到的写入 Io 数可能大于引擎执行的实际 Io 数。
- 非 RBPEX 文件或除 file_id 0 以外的其他数据文件也会显示写入。 在超大规模服务层中，将模拟这些写入，因为计算副本从不直接写入页面服务器。 写入 IOPS 和吞吐量在计算副本上发生，但 file_id 0 以外的数据文件的延迟不反映页面服务器写入的实际延迟。

### <a name="log-writes"></a>日志写入

- 在主计算上，在 file_id 2 的 _io_virtual_file_stats 中对日志写入进行了考虑。 主计算的日志写入是日志登陆区域的写入。
- 提交时，辅助副本上的日志记录不会受到强制。 在超大规模中，日志由 Xlog 服务应用到远程副本。 因为日志写入并不实际发生在辅助副本上，所以，辅助副本上的日志 IO 的任何记帐仅用于跟踪目的。

## <a name="additional-resources"></a>其他资源

- 对于超大规模单一数据库的 vCore 资源限制，请参阅[超大规模服务层 VCore 限制](sql-database-vcore-resource-limits-single-databases.md#hyperscale---provisioned-compute---gen5)
- 对于 Azure SQL 数据库性能优化，请参阅[AZURE Sql 数据库中的查询性能](sql-database-performance-guidance.md)
- 有关使用查询存储进行性能优化的详细情况，请参阅[使用查询存储进行性能监视](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store/)
- 有关 DMV 监视脚本，请参阅[使用动态管理视图监视性能 AZURE SQL 数据库](sql-database-monitoring-with-dmvs.md)
