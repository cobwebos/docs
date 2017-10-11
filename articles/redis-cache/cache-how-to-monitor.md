---
title: "如何监视 Azure Redis 缓存 | Microsoft Docs"
description: "了解如何监视 Azure Redis 缓存实例的运行状况和性能"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 7e70b153-9c87-4290-85af-2228f31df118
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: sdanie
ms.openlocfilehash: 8996f5ce03e39557d9cc9c3de1ec214f5cd664b4
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2017
---
# <a name="how-to-monitor-azure-redis-cache"></a>如何监视 Azure Redis 缓存
Azure Redis 缓存使用 [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) 提供用于监视缓存实例的几个选项。 可以查看度量值、将度量值图表固定到启动面板、自定义监视图表的日期和时间范围、在图表中添加和删除度量值，以及设置符合特定条件时发出的警报。 借助这些工具，可以监视 Azure Redis 缓存实例的运行状况，以及管理缓存应用程序。

每分钟使用 Redis [INFO](http://redis.io/commands/info) 命令收集约两次 Azure Redis 缓存实例的指标，然后将其自动存储 30 天（请参阅[导出缓存指标](#export-cache-metrics)以配置不同保留期策略），以便这些指标可以显示在指标图表中并由预警规则进行评估。 深入了解用于每个缓存指标的不同 INFO 值，请参阅[可用指标和报告间隔](#available-metrics-and-reporting-intervals)。

<a name="view-cache-metrics"></a>

若要查看缓存指标，请[浏览](cache-configure.md#configure-redis-cache-settings)到 [Azure 门户](https://portal.azure.com)中的缓存实例。  Azure Redis 缓存在“概述”边栏选项卡和“Redis 指标”边栏选项卡上提供一些内置图表。 通过添加或删除度量值以及更改报告间隔可以自定义每个图表。

![Redis 指标](./media/cache-how-to-monitor/redis-cache-redis-metrics-blade.png)

## <a name="view-pre-configured-metrics-charts"></a>查看预配置的指标图表

“概述”边栏选项卡具有以下预配置的监视图表。

* [监视图表](#monitoring-charts)
* [使用情况图表](#usage-charts)

### <a name="monitoring-charts"></a>监视图表
“概述”边栏选项卡中的“监视”部分有“命中数和未命中数”、“获取数和设置数”、“连接数”以及“总命令数”图表。

![监视图表](./media/cache-how-to-monitor/redis-cache-monitoring-part.png)

### <a name="usage-charts"></a>图表使用情况
“概述”边栏选项卡中的“使用情况”部分有“Redis 服务器负载”、“内存使用情况”、“网络带宽”和“CPU 使用率”图表，并且还显示缓存实例的“定价层”。

![图表使用情况](./media/cache-how-to-monitor/redis-cache-usage-part.png)

“定价层”显示缓存定价层，并可用于将缓存[缩放](cache-how-to-scale.md)到不同的定价层。

## <a name="view-metrics-with-azure-monitor"></a>通过 Azure Monitor 查看指标
若要使用 Azure Monitor 查看 Redis 指标和创建自定义图表，从“资源菜单”单击“指标”，并使用所需的指标、报告间隔、图表类型等对图表进行自定义。

![Redis 指标](./media/cache-how-to-monitor/redis-cache-monitor.png)

有关使用 Azure Monitor 处理指标的详细信息，请参阅 [Microsoft Azure 中的指标概述](../monitoring-and-diagnostics/monitoring-overview-metrics.md)。

<a name="how-to-view-metrics-and-customize-chart"></a>
<a name="enable-cache-diagnostics"></a>
## <a name="export-cache-metrics"></a>导出缓存指标
默认情况下，Azure Monitor 中的缓存指标将被[存储 30 天](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md#store-and-archive)，然后将被删除。 若要延长缓存指标的保存时间，使其超过 30 天，可以为缓存指标[指定存储帐户](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md)，并指定“保留期（天）”策略。 

若要配置缓存指标的存储帐户：

1. 从“Redis 缓存”边栏选项卡中的“资源菜单”单击“诊断”。
2. 单击“打开”。
3. 选中“存档到存储帐户”。
4. 选择要在其中存储缓存指标的存储帐户。
5. 选中“1 分钟”复选框，并指定“保留期（天）”策略。 如果不想应用任何保留期策略也不想永久保留数据，可将“保留期（天）”设置为“0”。
6. 单击“保存” 。

![Redis 诊断](./media/cache-how-to-monitor/redis-cache-diagnostics.png)

>[!NOTE]
>除了将缓存指标存档至存储区外，还可以[将其流式传输到事件中心或将其发送到 Log Analytics](../monitoring-and-diagnostics/monitoring-overview-metrics.md#export-metrics)。
>
>

若要访问指标，可按前文所述在 Azure 门户中查看它们，还可以使用 [Azure Monitor 指标 REST API](../monitoring-and-diagnostics/monitoring-overview-metrics.md#access-metrics-via-the-rest-api) 对其进行访问。

> [!NOTE]
> 如果更改存储帐户，以前配置的存储帐户中的数据仍可供下载，但并不会显示在 Azure 门户中。  
> 
> 

## <a name="available-metrics-and-reporting-intervals"></a>可用度量值和报告间隔
将使用多个报告间隔报告缓存指标，其中包括“前一小时”、“今天”、“前一周”和“自定义”。 每个指标图表的“指标”边栏选项卡会在图表中显示每个指标的平均值、最小值和最大值，并且一些指标会显示总报告间隔。 

每个度量值均包含两个版本。 一个指标测量整个缓存的性能以及使用[群集](cache-how-to-premium-clustering.md)的缓存的性能，名称中包含 `(Shard 0-9)` 的另一指标版本则测量缓存中单个分片的性能。 例如，如果缓存有 4 个分片，`Cache Hits` 就是整个缓存的命中总数，而 `Cache Hits (Shard 3)` 就只是该缓存分片的命中数。

> [!NOTE]
> 即使在缓存处于空闲状态，且没有连接的活动客户端应用程序时，也可能会看到一些缓存活动，例如，连接的客户端、内存使用率以及正在执行的操作。 Azure Redis 缓存实例的操作期间，此活动是正常情况。
> 
> 

| 度量值 | 说明 |
| --- | --- |
| 缓存命中数 |在指定的报告间隔期间，成功的键查找次数。 此值映射到 Redis [INFO](http://redis.io/commands/info) 命令输出中的 `keyspace_hits`。 |
| 缓存未命中数 |在指定的报告间隔期间，失败的键查找次数。 此值映射到 Redis INFO 命令输出中的 `keyspace_misses`。 缓存未命中并不一定意味着缓存出现了问题。 例如，在使用缓存端编程模式时，应用程序会首先查找缓存中的项。 如果该项不存在（缓存未命中），则将从数据库中检索该项并将其添加到下一次缓存中。 对于缓存端编程模式，缓存未命中是正常行为。 如果缓存未命中数大于预期值，请检查从缓存中填充并读取的应用程序逻辑。 如果由于内存压力而导致项目从缓存中逐出，则可能存在一些缓存未命中的情况，但度量值 `Used Memory` 或 `Evicted Keys` 可以更好的监视内存压力。 |
| 连接的客户端数 |指定的报告间隔期间，客间户端与缓存的连接数。 此值映射到 Redis INFO 命令输出中的 `connected_clients`。 一旦达到了[连接限制](cache-configure.md#default-redis-server-configuration)，则对缓存的后续连接尝试将失败。 注意，即使没有任何活动的客户端应用程序，由于内部进程和连接，仍可能存在一些连接的客户端的实例。 |
| 逐出的密钥数 |由于 `maxmemory` 限制，指定的报告间隔期间从缓存中逐出的项目数。 此值映射到 Redis INFO 命令输出中的 `evicted_keys`。 |
| 过期的密钥数 |指定的报告间隔期间，缓存中过期的项目数。 此值映射到 Redis INFO 命令输出中的 `expired_keys`。 |
| 总密钥数  | 上一个报表时期中，缓存中的最大密钥数。 此值映射到 Redis INFO 命令输出中的 `keyspace`。 由于基本指标系统的限制，对于启用了群集的缓存，“总密钥数”返回报告间隔期间具有最大密钥数的分片的最大密钥数。  |
| 获取数 |指定的报告间隔期间，缓存中的获取操作数。 此值是以下 Redis INFO 所有命令中的值的总和：`cmdstat_get`、`cmdstat_hget`、`cmdstat_hgetall`、`cmdstat_hmget`、`cmdstat_mget`、`cmdstat_getbit` 和 `cmdstat_getrange`，并且等效于报告间隔期间缓存命中和未命中数的总和。 |
| Redis 服务器负载 |Redis 服务器忙于处理消息并且非空闲等待消息的周期百分比。 如果此计数器达到 100，则意味着 Redis 服务器已达到性能上限并且 CPU 无法更快地工作。 如果看到高 Redis 服务器负载，则会在客户端看到超时异常。 在这种情况下，应该考虑将数据扩大或分区到多个缓存。 |
| 设置数 |指定的报告间隔期间，对缓存的设置操作数。 此值是以下 Redis INFO 所有命令中的值的总和：`cmdstat_set`、`cmdstat_hset`、`cmdstat_hmset`、`cmdstat_hsetnx`、`cmdstat_lset`、`cmdstat_mset`、`cmdstat_msetnx`、`cmdstat_setbit`、`cmdstat_setex`、`cmdstat_setrange` 和 `cmdstat_setnx`。 |
| 总操作数 |指定的报告间隔期间，由缓存服务器处理的命令总数。 此值映射到 Redis INFO 命令输出中的 `total_commands_processed`。 注意，当 Azure Redis 缓存纯粹用于发布/订阅时，将不存在 `Cache Hits`、`Cache Misses`、`Gets` 或 `Sets` 的度量值，但存在 `Total Operations` 度量值，该度量值反映发布/订阅操作的缓存使用情况。 |
| 已用内存 |在指定报告间隔期间，缓存中的键/值对所用的缓存内存量（以 MB 为单位）。 此值映射到 Redis INFO 命令输出中的 `used_memory`。 这不包括元数据或碎片。 |
| 已用内存 RSS |指定报告间隔期间所用的缓存内存量（以 MB 为单位），包括碎片和元数据。 此值映射到 Redis INFO 命令输出中的 `used_memory_rss`。 |
| CPU |指定报告间隔期间，Azure Redis 缓存服务器的 CPU 使用率（以百分比表示）。 此值映射到操作系统 `\Processor(_Total)\% Processor Time` 性能计数器。 |
| 缓存读取量 |指定报告间隔期间，从缓存中读取的数据量，以每秒兆字节数（MB/秒）为单位。 此值来源于支持虚拟机的网络接口卡，该虚拟机托管缓存，但并不特定于 Redis。 **此值对应于该缓存使用的网络带宽。如果要针对服务器端网络带宽限制设置警报，则可使用此 `Cache Read` 计数器来创建警报。请参阅[此表](cache-faq.md#cache-performance)，了解各种缓存定价层和大小所遵循的带宽限制。** |
| 缓存写入量 |指定报告间隔期间，写入缓存中的数据量，以每秒兆字节数（MB/秒）为单位。 此值来源于支持虚拟机的网络接口卡，该虚拟机托管缓存，但并不特定于 Redis。 此值对应于从客户端发送到缓存的数据的网络带宽。 |

<a name="operations-and-alerts"></a>
## <a name="alerts"></a>警报
可配置为基于指标和活动日志接收警报。 通过 Azure Monitor 可配置警报，使警报触发时执行以下操作：

* 发送电子邮件通知
* 调用 Webhook
* 调用 Azure 逻辑应用

若要配置缓存的预警规则，从“资源菜单”单击“预警规则”。

![监视](./media/cache-how-to-monitor/redis-cache-monitoring.png)

有关配置和使用警报的详细信息，请参阅[警报概述](../monitoring-and-diagnostics/insights-alerts-portal.md)。

## <a name="activity-logs"></a>活动日志
活动日志提供针对 Azure Redis 缓存实例执行的操作的详细信息。 活动日志以前称为“审核日志”或“操作日志”。 通过活动日志，可确定对 Azure Redis 缓存实例执行的任何写入操作 (PUT、POST、DELETE) 的“操作内容、操作人员和操作时间”。 

> [!NOTE]
> 活动日志不包括读取 (GET) 操作。
>
>

若要查看缓存的活动日志，从“资源菜单”单击“活动日志”。

有关活动日志的详细信息，请参阅 [Azure 活动日志概述](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)。











