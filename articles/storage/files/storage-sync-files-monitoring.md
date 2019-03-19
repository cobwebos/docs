---
title: 监视 Azure 文件同步 | Microsoft Docs
description: 如何监视 Azure 文件同步。
services: storage
author: jeffpatt24
ms.service: storage
ms.topic: article
ms.date: 01/31/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 4ae17249903f317e7a75a3e6bc7c03292021c96a
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2019
ms.locfileid: "57534627"
---
# <a name="monitor-azure-file-sync"></a>监视 Azure 文件同步

使用 Azure 文件同步，即可将组织的文件共享集中在 Azure 文件中，同时又不失本地文件服务器的灵活性、性能和兼容性。 Azure 文件同步可将 Windows Server 转换为 Azure 文件共享的快速缓存。 可以使用 Windows Server 上可用的任意协议本地访问数据，包括 SMB、NFS 和 FTPS。 并且可以根据需要在世界各地具有多个缓存。

本文介绍如何使用 Azure 门户和 Windows Server 监视你的 Azure 文件同步部署。

当前可用以下监视选项。

## <a name="azure-portal"></a>Azure 门户

在 Azure 门户中，可以查看已注册的服务器运行状况、服务器终结点运行状况（同步运行状况）和指标。

### <a name="storage-sync-service"></a>存储同步服务

若要查看已注册的服务器运行状况、 服务器终结点运行状况和指标，请转到 Azure 门户中的存储同步服务。 您可以查看已注册的服务器中的运行状况**已注册的服务器**中的边栏选项卡和服务器终结点运行状况**同步组**边栏选项卡。

已注册的服务器运行状况：

- 如果**已注册服务器**状态**联机**，服务器已成功与服务通信。
- 如果**已注册服务器**状态**脱机出现**，验证服务器上的存储同步监视器 (AzureStorageSyncMonitor.exe) 进程是否正在运行。 如果服务器位于防火墙或代理服务器后面，请参阅[这篇文章](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy)若要配置防火墙和代理。

服务器终结点运行状况：

- 门户中的服务器终结点运行状况取决于服务器上遥测事件日志中记录的同步事件（ID 9102 和 9302）。 如果同步会话由于暂时性错误而失败，如错误取消，同步可能仍显示在门户中正常运行的只要当前同步会话将取得进展。 事件 ID 9302 用于确定是否要应用的文件。 有关详细信息，请参阅[同步运行状况](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync)并[同步进度](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session)。
- 如果门户显示同步错误，因为同步未取得进展，请参阅[疑难解答文档](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors)有关的指南。

指标：

- 可以在“存储同步服务”门户中查看以下指标：

  | 指标名称 | 描述 | 边栏选项卡名称 |
  |-|-|-|
  | 同步的字节数 | 已传输数据的大小（上传和下载） | 同步组，服务器终结点 |
  | 云分层回调 | 回调的数据的大小 | 已注册的服务器 |
  | 未同步的文件 | 未能同步的文件数 | 服务器终结点 |
  | 同步的文件 | 已传输的文件数（上传和下载） | 同步组，服务器终结点 |
  | 服务器联机状态 | 从服务器接收的检测信号数 | 已注册的服务器 |

- 若要了解详细信息，请参阅[Azure Monitor](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#azure-monitor)。

  > [!Note]  
  > “存储同步服务”门户中的图表的时间范围为 24 小时。 若要查看不同时间范围或维度，请使用 Azure Monitor。

### <a name="azure-monitor"></a>Azure Monitor

使用 [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) 监视同步、云分层和服务器连接。 默认情况下，启用 Azure 文件同步指标，并每隔 15 分钟将指标发送到 Azure Monitor。

若要查看 Azure Monitor 中的 Azure 文件同步指标，请选择**存储同步服务**资源类型。

Azure Monitor 提供了下列 Azure 文件同步指标：

| 指标名称 | 描述 |
|-|-|
| 同步的字节数 | 传输数据大小（上传和下载）。<br><br>单位：字节<br>聚合类型：Sum<br>适用维度：服务器终结点名称、同步方向、同步组名称 |
| 云分层回调 | 回调的数据大小。<br><br>单位：字节<br>聚合类型：Sum<br>适用的维度：服务器名称 |
| 未同步的文件 | 未能同步的文件数。<br><br>单位：Count<br>聚合类型：Sum<br>适用维度：服务器终结点名称、同步方向、同步组名称 |
| 同步的文件 | 已传输的文件数（上传和下载）。<br><br>单位：Count<br>聚合类型：Sum<br>适用维度：服务器终结点名称、同步方向、同步组名称 |
| 服务器联机状态 | 从服务器接收的检测信号数。<br><br>单位：Count<br>聚合类型：最大值<br>适用的维度：服务器名称 |
| 同步会话结果 | 同步会话结果（1=成功同步会话；0=失败同步会话）<br><br>单位：Count<br>聚合类型：最大值<br>适用维度：服务器终结点名称、同步方向、同步组名称 |

## <a name="windows-server"></a>Windows Server

在 Windows 服务器上，可以查看云分层，已注册的服务器，并同步运行状况。

### <a name="event-logs"></a>事件日志

使用服务器上的遥测事件日志来监视已注册的服务器、同步和云分层运行状况。 遥测事件日志位于事件查看器*Applications and Services\Microsoft\FileSync\Agent*。

同步运行状况：

- 同步会话完成后，记录事件 ID 9102。 使用此事件来确定是否成功的同步会话 (**HResult = 0**) 以及是否存在每个项同步错误。 有关详细信息，请参阅[同步运行状况](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync)并[每项错误](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing)文档。

  > [!Note]  
  > 有时总体同步会话失败，或具有非零值 PerItemErrorCount。 但是，它们仍使进程向前推进，并且某些文件已成功同步。 可以在应用字段如 AppliedFileCount、 AppliedDirCount、 AppliedTombstoneCount 和 AppliedSizeBytes 中看到。 这些字段会告知你多少会话已成功。 如果您看到某行中，失败的多个同步会话，并且它们具有不断增加应用的计数，给同步时间，以打开支持票证之前，请重试。

- 如果存在活动同步会话，则每 5 到 10 分钟记录一次事件 ID 9302。 使用此事件来确定当前同步会话取得进展 (**AppliedItemCount > 0**)。 如果同步没有进展，最终会失败的同步会话，并将错误记录事件 ID 9102。 有关详细信息，请参阅[同步进度文档](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session)。

已注册的服务器运行状况：

- 当服务器查询作业服务时，每 30 秒记录一次事件 ID 9301。 如果与完成 GetNextJob**状态 = 0**，服务器就可以与服务进行通信。 如果 GetNextJob 完成并出现错误，检查[疑难解答文档](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors)有关的指南。

云分层的运行状况：

- 若要监视的服务器上的分层活动，请使用事件 ID 9003，9016 以及遥测事件日志位于事件查看器中的 9029 *Applications and Services\Microsoft\FileSync\Agent*。

  - 事件 ID 9003 提供服务器终结点的错误分布情况。 例如：总错误数和错误代码。 每个错误代码将记录一个事件。
  - 事件 ID 9016 提供卷的副本创建结果。 例如：可用空间百分比是，在会话中，幻像的文件数和失败的文件数的虚影。
  - 事件 ID 9029 提供服务器终结点的副本创建会话信息。 例如：尝试在会话中，文件的数量的文件数分层在会话中，并已分层的文件数。
  
- 若要监视的服务器上的召回活动，请使用事件 ID 9005，9006、 9009 和遥测事件日志位于事件查看器中的 9059 *Applications and Services\Microsoft\FileSync\Agent*。

  - 事件 ID 9005 提供服务器终结点的重新调用可靠性。 例如：总唯一的文件访问，并具有失败的访问权限的唯一文件总数。
  - 事件 ID 9006 提供服务器终结点的重新调用错误分布情况。 例如：请求和错误代码失败的总数。 每个错误代码将记录一个事件。
  - 事件 ID 9009 提供服务器终结点的回调会话信息。 例如：DurationSeconds、 CountFilesRecallSucceeded 和 CountFilesRecallFailed。
  - 事件 ID 9059 提供服务器终结点的应用程序回调分布情况。 例如：ShareId、 应用程序名称和 TotalEgressNetworkBytes。

### <a name="performance-counters"></a>性能计数器

使用服务器上的 Azure 文件同步性能计数器来监视同步活动。

若要查看在服务器上的 Azure 文件同步的性能计数器，请打开性能监视器 (Perfmon.exe)。 您可以找到下的计数器**AFS 传送的字节数**并**AFS 同步操作**对象。

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
