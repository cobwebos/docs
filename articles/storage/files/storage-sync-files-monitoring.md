---
title: 监视 Azure 文件同步 | Microsoft Docs
description: 如何监视 Azure 文件同步。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: ac09f9b59bc6f47adc9311cc910352c1a0d73b5d
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699288"
---
# <a name="monitor-azure-file-sync"></a>监视 Azure 文件同步

使用 Azure 文件同步，即可将组织的文件共享集中在 Azure 文件中，同时又不失本地文件服务器的灵活性、性能和兼容性。 Azure 文件同步可将 Windows Server 转换为 Azure 文件共享的快速缓存。 可以使用 Windows Server 上可用的任意协议本地访问数据，包括 SMB、NFS 和 FTPS。 并且可以根据需要在世界各地具有多个缓存。

本文介绍如何使用 Azure Monitor、存储同步服务和 Windows Server 来监视 Azure 文件同步部署。

以下监视选项当前可用。

## <a name="azure-monitor"></a>Azure Monitor

使用[Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview)查看指标, 并为同步、云分层和服务器连接配置警报。  

### <a name="metrics"></a>指标

默认情况下，启用 Azure 文件同步指标，并每隔 15 分钟将指标发送到 Azure Monitor。

若要在 Azure Monitor 中查看 Azure 文件同步度量值, 请选择 "**存储同步服务**" 资源类型。

Azure Monitor 提供了下列 Azure 文件同步指标：

| 指标名称 | 描述 |
|-|-|
| 同步的字节数 | 传输数据大小（上传和下载）。<br><br>单位：字节<br>聚合类型：Sum<br>适用维度：服务器终结点名称、同步方向、同步组名称 |
| 云分层回调 | 回调的数据大小。<br><br>**注意**：此指标将在将来删除。 使用云分层撤回大小指标监视重新调用的数据的大小。<br><br>单位：字节<br>聚合类型：Sum<br>适用的维度：服务器名 |
| 云分层撤回大小 | 回调的数据大小。<br><br>单位：字节<br>聚合类型：Sum<br>适用的维度：服务器名称, 同步组名称 |
| 云分层通过应用程序撤回大小 | 应用程序撤回的数据的大小。<br><br>单位：字节<br>聚合类型：Sum<br>适用的维度：应用程序名称、服务器名称、同步组名称 |
| 云分层召回吞吐量 | 数据回调吞吐量的大小。<br><br>单位：字节<br>聚合类型：Sum<br>适用的维度：服务器名称, 同步组名称 |
| 未同步的文件 | 未能同步的文件数。<br><br>单位：Count<br>聚合类型：Sum<br>适用维度：服务器终结点名称、同步方向、同步组名称 |
| 同步的文件 | 已传输的文件数（上传和下载）。<br><br>单位：Count<br>聚合类型：Sum<br>适用维度：服务器终结点名称、同步方向、同步组名称 |
| 服务器联机状态 | 从服务器接收的检测信号数。<br><br>单位：Count<br>聚合类型：最大值<br>适用的维度：服务器名 |
| 同步会话结果 | 同步会话结果（1=成功同步会话；0=失败同步会话）<br><br>单位：Count<br>聚合类型：最大值<br>适用维度：服务器终结点名称、同步方向、同步组名称 |

### <a name="alerts"></a>警报

若要在 Azure Monitor 中配置警报, 请选择存储同步服务, 然后选择要用于警报的[Azure 文件同步指标](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#metrics)。  

下表列出了一些要监视的示例方案和用于警报的适当指标:

| 应用场景 | 用于警报的指标 |
|-|-|
| 门户中的服务器终结点运行状况 = 错误 | 同步会话结果 |
| 文件未能同步到服务器或云终结点 | 未同步的文件 |
| 已注册的服务器未能与存储同步服务进行通信 | 服务器联机状态 |
| 云分层撤回大小在一天内超过500GiB  | 云分层撤回大小 |

若要了解有关在 Azure Monitor 中配置警报的详细信息, 请参阅[Microsoft Azure 中的警报概述]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)。

## <a name="storage-sync-service"></a>存储同步服务

若要查看已注册的服务器运行状况、服务器终结点运行状况和指标, 请在 Azure 门户中转到存储同步服务。 可以在 "**同步组**" 边栏选项卡中的 "**已注册的服务器**" 边栏选项卡和 "服务器终结点运行状况"

### <a name="registered-server-health"></a>已注册的服务器运行状况

- 如果**已注册的服务器**状态为 "**联机**", 则服务器将成功与服务进行通信。
- 如果**已注册的服务器**状态**显示**为 "脱机", 请验证服务器上的存储同步监视器 (AzureStorageSyncMonitor) 进程是否正在运行。 如果服务器位于防火墙或代理之后, 请参阅[此文章](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy)配置防火墙和代理。

### <a name="server-endpoint-health"></a>服务器终结点运行状况

- 门户中的服务器终结点运行状况取决于服务器上遥测事件日志中记录的同步事件（ID 9102 和 9302）。 如果由于暂时性错误 (如 "错误已取消") 而导致同步会话失败, 则只要当前同步会话正在进行, 同步可能仍会在门户中显示正常。 事件 ID 9302 用于确定是否正在应用文件。 有关详细信息, 请参阅[同步运行状况](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync)和[同步进度](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session)。
- 如果由于同步未取得进展而导致门户出现同步错误, 请参阅[疑难解答文档](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors)以获得指导。

### <a name="metric-charts"></a>指标图表

- 可在存储同步服务门户中查看以下指标图表:

  | 指标名称 | 描述 | 边栏选项卡名称 |
  |-|-|-|
  | 同步的字节数 | 已传输数据的大小（上传和下载） | 同步组，服务器终结点 |
  | 云分层回调 | 回调的数据的大小 | 已注册的服务器 |
  | 未同步的文件 | 未能同步的文件数 | 服务器终结点 |
  | 同步的文件 | 已传输的文件数（上传和下载） | 同步组，服务器终结点 |
  | 服务器联机状态 | 从服务器接收的检测信号数 | 注册的服务器 |

- 若要了解详细信息, 请参阅[Azure Monitor](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#azure-monitor)。

  > [!Note]  
  > “存储同步服务”门户中的图表的时间范围为 24 小时。 若要查看不同时间范围或维度，请使用 Azure Monitor。

## <a name="windows-server"></a>Windows Server

在 Windows Server 上, 你可以查看云分层、已注册的服务器和同步运行状况。

### <a name="event-logs"></a>事件日志

使用服务器上的遥测事件日志来监视已注册的服务器、同步和云分层运行状况。 遥测事件日志事件查看器位于 "Services\Microsoft\FileSync\Agent" 下的 "*应用程序" 和 "* " 下。

同步运行状况:

- 在同步会话完成后记录事件 ID 9102。 使用此事件可确定同步会话是否成功 (**HResult = 0**) 以及每项是否有同步错误。 有关详细信息, 请参阅[同步运行状况](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync)和[每项错误](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing)文档。

  > [!Note]  
  > 有时同步会话会整体失败或具有非零 PerItemErrorCount。 但是, 它们仍将继续进行, 并且某些文件同步成功。 可以在应用的字段 (如 AppliedFileCount、AppliedDirCount、AppliedTombstoneCount 和 AppliedSizeBytes) 中查看此项。 这些字段告诉你会话成功的次数。 如果在一行中发现多个同步会话失败, 并且它们的应用计数已增加, 请在打开支持票证之前, 提供同步时间以重试。

- 如果存在活动同步会话，则每 5 到 10 分钟记录一次事件 ID 9302。 使用此事件可确定当前同步会话是否正在进行 (**AppliedItemCount > 0**)。 如果同步未进行进度, 同步会话应最终会失败, 并且会记录错误的事件 ID 9102。 有关详细信息, 请参阅[同步进度文档](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session)。

已注册的服务器运行状况:

- 当服务器查询作业服务时，每 30 秒记录一次事件 ID 9301。 如果 GetNextJob 的**状态为 0**, 则服务器可以与服务进行通信。 如果 GetNextJob 完成并出现错误, 请查看[故障排除文档](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors)以获取指导。

云分层运行状况:

- 若要监视服务器上的分层活动, 请使用遥测事件日志中的事件 ID 9003、9016和 9029, 该日志事件查看器位于 "Services\Microsoft\FileSync\Agent" 下的 "*应用程序" 和 "* " 下。

  - 事件 ID 9003 提供服务器终结点的错误分布情况。 例如：错误计数和 ErrorCode 总数。 每个错误代码记录一个事件。
  - 事件 ID 9016 提供卷的副本创建结果。 例如：可用空间百分比是, 会话中的文件数和失败的文件数。
  - 事件 ID 9029 提供服务器终结点的副本创建会话信息。 例如：会话中尝试的文件数、会话中分层的文件数以及已分层的文件数。
  
- 若要监视服务器上的撤回活动, 请在遥测事件日志中使用事件 ID 9005、9006、9009和 9059, 该日志事件查看器位于 Services\Microsoft\FileSync\Agent 下的 "*应用程序" 和 "* " 下。

  - 事件 ID 9005 提供服务器终结点的重新调用可靠性。 例如：访问失败的唯一文件总数, 以及访问失败的唯一文件总数。
  - 事件 ID 9006 提供服务器终结点的重新调用错误分布情况。 例如：失败请求总数和错误代码。 每个错误代码记录一个事件。
  - 事件 ID 9009 提供服务器终结点的回调会话信息。 例如：DurationSeconds、CountFilesRecallSucceeded 和 CountFilesRecallFailed。
  - 事件 ID 9059 提供服务器终结点的应用程序回调分布情况。 例如：ShareId、应用程序名称和 TotalEgressNetworkBytes。

### <a name="performance-counters"></a>性能计数器

使用服务器上的 Azure 文件同步性能计数器来监视同步活动。

若要在服务器上查看 Azure 文件同步性能计数器, 请打开性能监视器 (Perfmon)。 可以在 "已**传输的 Afs 字节**" 和 " **afs 同步操作**" 对象下找到计数器。

性能监视器提供了下列 Azure 文件同步性能计数器：

| 性能对象\计数器名称 | 描述 |
|-|-|
| AFS 传输的字节数\下载的字节数/秒 | 每秒下载的字节数。 |
| AFS 传输的字节数\上传的字节数/秒 | 每秒上传的字节数。 |
| AFS 传输的字节数\总字节数/秒 | 每秒（上传和下载）的总字节数。 |
| AFS 同步操作数\下载的同步文件数/秒 | 每秒下载的文件数。 |
| AFS 同步操作数\上传的同步文件数/秒 | 每秒上传的文件数。 |
| AFS 同步操作数\同步文件总操作数/秒 | 同步的文件总数（上传和下载）。 |

## <a name="next-steps"></a>后续步骤
- [规划 Azure 文件同步部署](storage-sync-files-planning.md)
- [考虑防火墙和代理设置](storage-sync-files-firewall-and-proxy.md)
- [部署 Azure 文件同步](storage-sync-files-deployment-guide.md)
- [对 Azure 文件同步进行故障排除](storage-sync-files-troubleshoot.md)
- [Azure 文件常见问题解答](storage-files-faq.md)
