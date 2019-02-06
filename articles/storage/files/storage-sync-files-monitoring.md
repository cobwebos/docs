---
title: 监视 Azure 文件同步 | Microsoft Docs
description: 如何监视 Azure 文件同步。
services: storage
author: jeffpatt24
ms.service: storage
ms.topic: article
ms.date: 01/28/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 032b39846d19e34f2eb87c1311feeb4bb890cb24
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2019
ms.locfileid: "55467452"
---
# <a name="monitor-azure-file-sync"></a>监视 Azure 文件同步

使用 Azure 文件同步，即可将组织的文件共享集中在 Azure 文件中，同时又不失本地文件服务器的灵活性、性能和兼容性。 Azure 文件同步可将 Windows Server 转换为 Azure 文件共享的快速缓存。 可以使用 Windows Server 上可用的任意协议本地访问数据，包括 SMB、NFS 和 FTPS。 并且可以根据需要在世界各地具有多个缓存。

本文介绍如何使用 Azure 门户和 Windows Server 监视 Azure 文件同步部署。

目前可以使用以下监视选项：

## <a name="azure-portal"></a>Azure 门户

在 Azure 门户中，可以查看已注册的服务器运行状况、服务器终结点运行状况（同步运行状况）和指标。

### <a name="storage-sync-service"></a>存储同步服务

若要查看已注册的服务器和服务器终结点运行状况，请转到 Azure 门户中的“存储同步服务”。 可在“已注册的服务器”边栏选项卡中查看已注册的服务器运行状况。 可在“同步组”边栏选项卡中查看服务器终结点运行状况。

已注册的服务器运行状况
- 如果已注册的服务器状态为“联机”，则服务器已成功与服务进行通信。
- 如果已注册的服务器状态为“显示脱机”，则验证服务器上的存储同步监视器 (AzureStorageSyncMonitor.exe) 进程是否正在进行。 如果服务器位于防火墙或代理之后，请根据[文档](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy)配置防火墙和代理。

服务器终结点运行状况
- 门户中的服务器终结点运行状况取决于服务器上遥测事件日志中记录的同步事件（ID 9102 和 9302）。 如果同步会话因暂时性错误失败（例如，已取消的错误），只要当前同步会话在进行中，同步在门户中仍可能显示为正常运行（事件 ID 9302 用于确定是否正在应用文件）。 有关详细信息，请参阅以下文档：[同步运行状况](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) & [同步进度](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session)。
- 如果门户因未进行同步而显示同步错误，请查看[疑难解答文档](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors)以获得指导。

### <a name="azure-monitor"></a>Azure Monitor

使用 [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) 监视同步、云分层和服务器连接。 默认情况下，启用 Azure 文件同步指标，并每隔 15 分钟将指标发送到 Azure Monitor。

若要查看 Azure Monitor 中的 Azure 文件同步指标，请选择“存储同步服务”资源类型。

Azure Monitor 提供了下列 Azure 文件同步指标：

| 指标名称 | 说明 |
|-|-|
| 同步的字节数 | 传输数据大小（上传和下载）。<br><br>单位：字节<br>聚合类型：Sum<br>适用维度：服务器终结点名称、同步方向、同步组名称 |
| 云分层回调 | 回调的数据大小。<br><br>单位：字节<br>聚合类型：Sum<br>适用的维度：服务器名称 |
| 未同步的文件 | 未能同步的文件数。<br><br>单位：Count<br>聚合类型：Sum<br>适用维度：服务器终结点名称、同步方向、同步组名称 |
| 同步的文件 | 上传和下载的文件数。<br><br>单位：Count<br>聚合类型：Sum<br>适用维度：服务器终结点名称、同步方向、同步组名称 |
| 服务器检测信号 | 从服务器接收的检测信号数。<br><br>单位：Count<br>聚合类型：最大值<br>适用的维度：服务器名称 |
| 同步会话结果 | 同步会话结果（1=成功同步会话；0=失败同步会话）<br><br>单位：Count<br>聚合类型：最大值<br>适用维度：服务器终结点名称、同步方向、同步组名称 |

## <a name="windows-server"></a>Windows Server

可以在 Windows Server 上查看云分层、已注册的服务器和同步运行状况。

### <a name="event-logs"></a>事件日志

使用服务器上的遥测事件日志来监视已注册的服务器、同步和云分层运行状况。 遥测事件日志位于事件查看器中的“应用程序和服务\Microsoft\FileSync\代理”下。

同步运行状况
- 同步会话完成后，将记录事件 ID 9102。 应使用此事件来确定同步会话是否已成功完成 (HResult = 0)，以及是否存在每项同步错误。 有关详细信息，请参阅以下文档：[同步运行状况](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) & [每项错误](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing)。

  > [!Note]  
  > 有时，同步会话虽然总体失败或者出现非零值的 PerItemErrorCount，但仍能继续进行，并且某些文件能够成功同步。 可以在 Applied 字段（AppliedFileCount、AppliedDirCount、AppliedTombstoneCount 和 AppliedSizeBytes）中看到这种状态，其中告知了有多少个会话成功。 如果在某一行中看到多个同步会话失败，但 Applied 计数增加，则应该提供一定的同步时间以便重试，然后再开具支持票证。

- 如果存在活动同步会话，则每 5 到 10 分钟记录一次事件 ID 9302。 应使用此事件来确定当前同步会话是否正在进行中 (AppliedItemCount > 0)。 如果没有进行同步，同步会话最终应失败并记录包含错误的事件 ID 9102。 有关详细信息，请参阅以下文档：[同步进度](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session)

已注册的服务器运行状况
- 当服务器查询作业服务时，每 30 秒记录一次事件 ID 9301。 如果 GetNextJob 完成且状态 = 0，服务器就可以与服务进行通信。 如果 GetNextJob 完成但出现错误，请查看[疑难解答文档](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors)以获得指导。

云分层运行状况
- 若要监视服务器上的分层活动，请使用遥测事件日志（位于“事件查看器”中的“应用程序和服务\Microsoft\FileSync\代理”下）中的事件 ID 9003、9016 和 9029。

  - 事件 ID 9003 提供服务器终结点的错误分布情况。 例如错误总数、错误代码，等等。请注意，将为每个错误代码记录一个事件。
  - 事件 ID 9016 提供卷的副本创建结果。 例如，可用空间百分比、会话中创建的文件副本数、无法创建副本的文件数，等等。
  - 事件 ID 9029 提供服务器终结点的副本创建会话信息。 例如，会话中尝试的文件数、会话分层的文件数、已分层的文件数，等等。
  
- 若要监视服务器上的回调活动，请使用遥测事件日志（位于“事件查看器”中的“应用程序和服务\Microsoft\FileSync\代理”下）中的事件 ID 9005、9006、9009 和 9059。

  - 事件 ID 9005 提供服务器终结点的重新调用可靠性。 例如，访问的唯一文件总数、访问失败的唯一文件总数，等等。
  - 事件 ID 9006 提供服务器终结点的重新调用错误分布情况。 例如，失败的请求总数、错误代码，等等。请注意，将为每个错误代码记录一个事件。
  - 事件 ID 9009 提供服务器终结点的回调会话信息。 例如，DurationSeconds、CountFilesRecallSucceeded、CountFilesRecallFailed 等。
  - 事件 ID 9059 提供服务器终结点的应用程序回调分布情况。 例如，ShareId、应用程序名称和 TotalEgressNetworkBytes。

### <a name="performance-counters"></a>性能计数器

使用服务器上的 Azure 文件同步性能计数器来监视同步活动。

若要查看服务器上的 Azure 文件同步性能计数器，启动性能监视器 (Perfmon.exe)，可以在 AFS 传输的字节数和 AFS 同步操作对象下找到计数器。

性能监视器提供了下列 Azure 文件同步性能计数器：

| 性能对象\计数器名称 | 说明 |
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
