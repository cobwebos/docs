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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "68699288"
---
# <a name="monitor-azure-file-sync"></a>监视 Azure 文件同步

使用 Azure 文件同步，即可将组织的文件共享集中在 Azure 文件中，同时又不失本地文件服务器的灵活性、性能和兼容性。 Azure 文件同步可将 Windows Server 转换为 Azure 文件共享的快速缓存。 可以使用 Windows Server 上可用的任意协议本地访问数据，包括 SMB、NFS 和 FTPS。 并且可以根据需要在世界各地具有多个缓存。

本文介绍如何使用 Azure 监视器、存储同步服务和 Windows 服务器监视 Azure 文件同步部署。

以下监视选项当前可用。

## <a name="azure-monitor"></a>Azure Monitor

使用[Azure 监视器](https://docs.microsoft.com/azure/azure-monitor/overview)查看指标并配置同步、云分层和服务器连接的警报。  

### <a name="metrics"></a>指标

默认情况下，启用 Azure 文件同步指标，并每隔 15 分钟将指标发送到 Azure Monitor。

要查看 Azure 监视器中的 Azure 文件同步指标，请选择**存储同步服务**资源类型。

Azure Monitor 提供了下列 Azure 文件同步指标：

| 指标名称 | 描述 |
|-|-|
| 同步的字节数 | 传输数据大小（上传和下载）。<br><br>单元：字节<br>聚合类型：总和<br>适用维度：服务器终结点名称、同步方向、同步组名称 |
| 云分层回调 | 回调的数据大小。<br><br>**注意**：此指标将在将来删除。 使用云分层召回大小指标监视召回的数据大小。<br><br>单元：字节<br>聚合类型：总和<br>适用维度：服务器名称 |
| 云分层重调大小 | 回调的数据大小。<br><br>单元：字节<br>聚合类型：总和<br>适用维度：服务器名称、同步组名称 |
| 应用程序的云分层重调大小 | 应用程序调用的数据的大小。<br><br>单元：字节<br>聚合类型：总和<br>适用维度：应用程序名称、服务器名称、同步组名称 |
| 云分层重调吞吐量 | 数据撤回吞吐量的大小。<br><br>单元：字节<br>聚合类型：总和<br>适用维度：服务器名称、同步组名称 |
| 未同步的文件 | 未能同步的文件数。<br><br>单位：计数<br>聚合类型：总和<br>适用维度：服务器终结点名称、同步方向、同步组名称 |
| 同步的文件 | 已传输的文件数（上传和下载）。<br><br>单位：计数<br>聚合类型：总和<br>适用维度：服务器终结点名称、同步方向、同步组名称 |
| 服务器联机状态 | 从服务器接收的检测信号数。<br><br>单位：计数<br>聚合类型：最大值<br>适用维度：服务器名称 |
| 同步会话结果 | 同步会话结果（1=成功同步会话；0=失败同步会话）<br><br>单位：计数<br>聚合类型：最大值<br>适用维度：服务器终结点名称、同步方向、同步组名称 |

### <a name="alerts"></a>警报

要在 Azure 监视器中配置警报，请选择存储同步服务，然后选择用于警报的[Azure 文件同步指标](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#metrics)。  

下表列出了要监视的一些示例方案和用于警报的正确指标：

| 方案 | 用于警报的指标 |
|-|-|
| 门户中的服务器终结点运行状况 = 错误 | 同步会话结果 |
| 文件无法同步到服务器或云终结点 | 未同步的文件 |
| 已注册服务器无法与存储同步服务通信 | 服务器联机状态 |
| 云分层召回规模在一天内已超过 500GiB  | 云分层重调大小 |

要了解有关在 Azure 监视器中配置警报的详细信息，请参阅[Microsoft Azure 中的警报概述]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)。

## <a name="storage-sync-service"></a>存储同步服务

要查看已注册的服务器运行状况、服务器终结点运行状况和指标，请访问 Azure 门户中的存储同步服务。 您可以在 **"已注册服务器"** 边栏选项卡和"**同步组"** 边栏选项卡中查看已注册的服务器运行状况和服务器终结点运行状况。

### <a name="registered-server-health"></a>已注册服务器运行状况

- 如果 **"已注册服务器**"状态**为"联机**"，则服务器将成功与服务通信。
- 如果 **"已注册服务器**"状态**显示为脱机**，请验证服务器上的存储同步监视器 （AzureStorageSyncMonitor.exe） 进程是否正在运行。 如果服务器位于防火墙或代理后面，请参阅[本文](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy)以配置防火墙和代理。

### <a name="server-endpoint-health"></a>服务器终结点运行状况

- 门户中的服务器终结点运行状况取决于服务器上遥测事件日志中记录的同步事件（ID 9102 和 9302）。 如果同步会话由于暂时性错误（如已取消错误）而失败，只要当前同步会话正在取得进展，同步在门户中可能仍显示为正常。 事件 ID 9302 用于确定是否应用文件。 有关详细信息，请参阅[同步运行状况](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync)和[同步进度](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session)。
- 如果门户显示同步错误，因为同步未取得进展，请参阅[故障排除文档](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors)以获得指导。

### <a name="metric-charts"></a>指标图表

- 以下指标图表可查看存储同步服务门户：

  | 指标名称 | 描述 | 刀片名称 |
  |-|-|-|
  | 同步的字节数 | 已传输数据的大小（上传和下载） | 同步组，服务器终结点 |
  | 云分层回调 | 回调的数据的大小 | 已注册的服务器 |
  | 未同步的文件 | 未能同步的文件数 | 服务器终结点 |
  | 同步的文件 | 已传输的文件数（上传和下载） | 同步组，服务器终结点 |
  | 服务器联机状态 | 从服务器接收的检测信号数 | 已注册的服务器 |

- 要了解更多信息，请参阅[Azure 监视器](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#azure-monitor)。

  > [!Note]  
  > “存储同步服务”门户中的图表的时间范围为 24 小时。 若要查看不同时间范围或维度，请使用 Azure Monitor。

## <a name="windows-server"></a>Windows Server

在 Windows 服务器上，您可以查看云分层、已注册的服务器和同步运行状况。

### <a name="event-logs"></a>事件日志

使用服务器上的遥测事件日志来监视已注册的服务器、同步和云分层运行状况。 遥测事件日志位于*应用程序和服务下的事件查看器中\Microsoft_FileSync_代理*。

同步运行状况：

- 事件 ID 9102 在同步会话完成后记录。 使用此事件可确定同步会话是否成功 **（HResult = 0**），以及是否存在每个项目同步错误。 有关详细信息，请参阅[同步运行状况](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync)和[每个项目错误](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing)文档。

  > [!Note]  
  > 有时，同步会话整体失败或每个项目错误计数为非零。 但是，它们仍向前推进，并且某些文件成功同步。 您可以在应用文件计数、应用十分计数、应用墓碑计数和应用大小字节等应用字段中看到这一点。 这些字段告诉您会话成功了多少。 如果您看到一行中的多个同步会话失败，并且它们的应用计数不断增加，请给同步时间在打开支持票证之前重试。

- 如果存在活动同步会话，则每 5 到 10 分钟记录一次事件 ID 9302。 使用此事件可确定当前同步会话是否正在取得进展（**应用项目计数> 0**）。 如果同步未取得进展，同步会话最终将失败，并且事件 ID 9102 将记录错误。 有关详细信息，请参阅[同步进度文档](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session)。

已注册服务器运行状况：

- 当服务器查询作业服务时，每 30 秒记录一次事件 ID 9301。 如果 GetNextJob 以**状态 = 0**结束，则服务器能够与服务通信。 如果 GetNextJob 出现错误，请查看[故障排除文档](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors)以获得指导。

云分层运行状况：

- 要监视服务器上的分层活动，请使用遥测事件日志中的事件 ID 9003、9016 和 9029，该日志位于*应用程序和服务下的事件查看器中。微软_FileSync_Agent*。

  - 事件 ID 9003 提供服务器终结点的错误分布情况。 例如：总错误计数和错误代码。 每个错误代码记录一个事件。
  - 事件 ID 9016 提供卷的副本创建结果。 例如：可用空间百分比是，会话中重影的文件数和文件数未能重影。
  - 事件 ID 9029 提供服务器终结点的副本创建会话信息。 例如：会话中尝试的文件数、会话中分层的文件数和已分层的文件数。
  
- 要监视服务器上的召回活动，请使用遥测事件日志中的事件 ID 9005、9006、9009 和 9059，该日志位于*应用程序和服务下的事件查看器中[Microsoft_FileSync_代理*]

  - 事件 ID 9005 提供服务器终结点的重新调用可靠性。 例如：访问的唯一文件总数，以及访问失败的总唯一文件。
  - 事件 ID 9006 提供服务器终结点的重新调用错误分布情况。 例如：失败请求总数和错误代码。 每个错误代码记录一个事件。
  - 事件 ID 9009 提供服务器终结点的回调会话信息。 例如：持续时间秒、计数文件撤回成功和计数文件撤回失败。
  - 事件 ID 9059 提供服务器终结点的应用程序回调分布情况。 例如：共享 Id、应用程序名称和总计 Gress 网络字节。

### <a name="performance-counters"></a>性能计数器

使用服务器上的 Azure 文件同步性能计数器来监视同步活动。

要查看服务器上的 Azure 文件同步性能计数器，打开性能监视器 （Perfmon.exe）。 您可以在**AFS 字节传输**和**AFS 同步操作**对象下找到计数器。

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
