---
title: 监视 Azure 文件同步 | Microsoft Docs
description: 查看如何使用 Azure Monitor、存储同步服务和 Windows Server 来监视你的 Azure 文件同步部署。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 08/05/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 9db8a0397c836e8cbc45404d9c4f149255fc76fa
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2020
ms.locfileid: "88271050"
---
# <a name="monitor-azure-file-sync"></a>监视 Azure 文件同步

使用 Azure 文件同步，即可将组织的文件共享集中在 Azure 文件中，同时又不失本地文件服务器的灵活性、性能和兼容性。 Azure 文件同步可将 Windows Server 转换为 Azure 文件共享的快速缓存。 可以使用 Windows Server 上可用的任意协议本地访问数据，包括 SMB、NFS 和 FTPS。 并且可以根据需要在世界各地具有多个缓存。

本文介绍如何使用 Azure Monitor、存储同步服务和 Windows Server 来监视 Azure 文件同步部署。

本指南介绍了以下方案： 
- 查看 Azure Monitor 中 Azure 文件同步度量值。
- 在 Azure Monitor 中创建警报，以主动通知关键情况。
- 使用 Azure 门户查看 Azure 文件同步部署的运行状况。
- 如何使用 Windows Server 上的事件日志和性能计数器来监视 Azure 文件同步部署的运行状况。 

## <a name="azure-monitor"></a>Azure Monitor

使用 [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) 查看指标，并为同步、云分层和服务器连接配置警报。  

### <a name="metrics"></a>指标

默认情况下，启用 Azure 文件同步指标，并每隔 15 分钟将指标发送到 Azure Monitor。

**如何在 Azure Monitor 中查看 Azure 文件同步度量值**
- 在**Azure 门户**中转到**存储同步服务**，然后单击 "**指标**"。
- 单击 " **指标** " 下拉箭头，然后选择要查看的度量值。

Azure Monitor 提供了下列 Azure 文件同步指标：

| 指标名称 | 描述 |
|-|-|
| 同步的字节数 | 传输数据大小（上传和下载）。<br><br>单元：字节<br>聚合类型： Sum<br>适用的维度：服务器终结点名称，同步方向，同步组名称 |
| 云分层回调 | 回调的数据大小。<br><br>**注意**：未来将删除此指标。 使用云分层撤回大小指标监视重新调用的数据的大小。<br><br>单元：字节<br>聚合类型： Sum<br>适用的维度：服务器名称 |
| 云分层重调大小 | 回调的数据大小。<br><br>单元：字节<br>聚合类型： Sum<br>适用的维度：服务器名称、同步组名称 |
| 应用程序的云分层重调大小 | 应用程序撤回的数据的大小。<br><br>单元：字节<br>聚合类型： Sum<br>适用的维度：应用程序名称、服务器名称、同步组名称 |
| 云分层重调吞吐量 | 数据回调吞吐量的大小。<br><br>单元：字节<br>聚合类型： Sum<br>适用的维度：服务器名称、同步组名称 |
| 未同步的文件 | 未能同步的文件数。<br><br>单元：计数<br>聚合类型： Sum<br>适用的维度：服务器终结点名称，同步方向，同步组名称 |
| 同步的文件 | 已传输的文件数（上传和下载）。<br><br>单元：计数<br>聚合类型： Sum<br>适用的维度：服务器终结点名称，同步方向，同步组名称 |
| 服务器联机状态 | 从服务器接收的检测信号数。<br><br>单元：计数<br>聚合类型：最大值<br>适用的维度：服务器名称 |
| 同步会话结果 | 同步会话结果（1=成功同步会话；0=失败同步会话）<br><br>单元：计数<br>聚合类型：最大值<br>适用的维度：服务器终结点名称，同步方向，同步组名称 |

### <a name="alerts"></a>警报

在监视数据中发现重要情况时，警报会以主动的方式通知你。 若要了解有关在 Azure Monitor 中配置警报的详细信息，请参阅 [Microsoft Azure 中的警报概述](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)。

**如何创建 Azure 文件同步的警报**

- 在**Azure 门户**中转到**存储同步服务**。 
- 单击 "监视" 部分中的 " **警报** "，然后单击 " **+ 新建警报规则**"。
- 单击 " **选择条件** " 并为警报提供以下信息： 
    - **指标**
    - **维度名称**
    - **警报逻辑**
- 单击 " **选择操作组** "，并将操作组 (电子邮件、短信等 ) 添加到警报，方法是选择现有操作组或创建新的操作组。
- 填写警报 **详细信息** ，如 **警报规则名称**、 **描述** 和 **严重性**。
- 单击 " **创建警报规则** " 以创建警报。  

下表列出了一些要监视的示例方案和用于警报的适当指标：

| 方案 | 用于警报的指标 |
|-|-|
| 服务器终结点运行状况在门户中显示错误 | 同步会话结果 |
| 文件未能同步到服务器或云终结点 | 未同步的文件 |
| 已注册的服务器未能与存储同步服务进行通信 | 服务器联机状态 |
| 云分层撤回大小在一天内超过500GiB  | 云分层重调大小 |

有关如何为这些方案创建警报的说明，请参阅 " [警报示例](#alert-examples) " 部分。

## <a name="storage-sync-service"></a>存储同步服务

若要查看 **Azure 门户**中 Azure 文件同步部署的运行状况，请导航到 **存储同步服务** ，并提供以下信息：

- 已注册的服务器运行状况
- 服务器终结点运行状况
    - 未同步的文件
    - 同步活动
    - 云分层效率
    - 不分层文件
    - 撤回错误
- 指标

### <a name="registered-server-health"></a>已注册的服务器运行状况

若要在门户中查看**已注册的服务器运行状况**，请导航到**存储同步服务**的 "**已注册服务器**" 部分。

- 如果 **已注册的服务器** 状态为 " **联机**"，则服务器将成功与服务进行通信。
- 如果 **已注册的服务器** 状态 **显示**为 "脱机"，则存储同步监视器进程 ( # A0) 未运行，或者服务器无法访问 Azure 文件同步服务。 有关指南，请参阅 [故障排除文档](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#server-endpoint-noactivity) 。

### <a name="server-endpoint-health"></a>服务器终结点运行状况

若要在门户中查看**服务器终结点**的运行状况，请导航到**存储同步服务**的 "**同步组**" 部分，并选择一个**同步组**。

- 门户中的 **服务器终结点运行状况** 和 **同步活动** 基于服务器上的遥测事件日志中记录的同步事件， (ID 9102 和 9302) 。 如果同步会话由于暂时性错误（例如 "错误已取消"）而失败，则只要当前同步会话正在进行，则同步仍会在门户中显示为正常状态，前提是当前同步会话正在 (文件) 应用。 事件 ID 9302 是同步会话完成后记录的同步进度事件和事件 ID 9102。  有关详细信息，请参阅 [同步运行状况](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) 和 [同步进度](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session)。 如果由于同步未取得进展而导致门户出现错误，请参阅 [疑难解答文档](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) 以获得指导。
- 门户中的 " **文件未同步** 计数" 基于在服务器的遥测事件日志中记录的事件 ID 9121。 同步会话完成后，每个项的错误都会记录此事件。 若要解决每个项的错误，请参阅[如何实现查看是否存在不同步的特定文件或文件夹？](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing)
- 若要在门户中查看 **云分层效率** ，请转到 **服务器终结点属性** ，然后导航到 **云分层** 部分。 为云分层效率提供的数据基于事件 ID 9071，该事件记录在服务器上的遥测事件日志中。 若要了解详细信息，请参阅[云分层概述](https://docs.microsoft.com/azure/storage/files/storage-sync-cloud-tiering)。
- 若要查看未在门户中 **分层** 和 **撤回错误** 的文件，请转到 **服务器终结点属性** ，然后导航到 **云分层** 部分。 **不分层的文件** 基于事件 id 9003，该事件记录在服务器上的遥测事件日志中，并且 **撤回错误** 基于事件 id 9006。 若要调查无法进行分层或召回的文件，请参阅 [如何对无法进行层级处理的文件进行故障排除](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#how-to-troubleshoot-files-that-fail-to-tier) ，以及如何对无法 [召回的文件进行故障排除](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#how-to-troubleshoot-files-that-fail-to-be-recalled)。

### <a name="metric-charts"></a>指标图表

- 可在存储同步服务门户中查看以下指标图表：

  | 指标名称 | 描述 | 边栏选项卡名称 |
  |-|-|-|
  | 同步的字节数 | 已传输数据的大小（上传和下载） | 同步组，服务器终结点 |
  | 云分层回调 | 回调的数据的大小 | 已注册的服务器 |
  | 未同步的文件 | 未能同步的文件数 | 服务器终结点 |
  | 同步的文件 | 已传输的文件数（上传和下载） | 同步组，服务器终结点 |
  | 服务器联机状态 | 从服务器接收的检测信号数 | 已注册的服务器 |

- 若要了解详细信息，请参阅 [Azure Monitor](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#azure-monitor)。

  > [!Note]  
  > “存储同步服务”门户中的图表的时间范围为 24 小时。 若要查看不同时间范围或维度，请使用 Azure Monitor。

## <a name="windows-server"></a>Windows Server

在安装了 Azure 文件同步代理的 **Windows Server** 上，你可以使用 **事件日志** 和 **性能计数器**查看该服务器上的服务器终结点的运行状况。

### <a name="event-logs"></a>事件日志

使用服务器上的遥测事件日志来监视已注册的服务器、同步和云分层运行状况。 遥测事件日志事件查看器位于 "Services\Microsoft\FileSync\Agent" 下的 " *应用程序" 和 "*" 下。

同步运行状况

- 同步会话完成后，将记录事件 ID 9102。 使用此事件可确定同步会话是否成功 (**HResult = 0**) 以及每项同步错误 (**PerItemErrorCount**) 。 有关详细信息，请参阅 [同步运行状况](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) 和  [每项错误](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing) 文档。

  > [!Note]  
  > 有时同步会话会整体失败或具有非零 PerItemErrorCount。 但是，它们仍将继续进行，并且某些文件同步成功。 可以在应用的字段（如 AppliedFileCount、AppliedDirCount、AppliedTombstoneCount 和 AppliedSizeBytes）中查看此项。 这些字段告诉你会话成功的次数。 如果在一行中发现多个同步会话失败，并且它们的应用计数已增加，请在打开支持票证之前，提供同步时间以重试。

- 同步会话完成后，每个项的错误都会记录事件 ID 9121。 使用此事件来确定由于此错误 (**PersistentCount** 和 **TransientCount**) 而未能同步的文件数。 应调查永久性的每项错误，请参阅[如何实现查看是否存在不同步的特定文件或文件夹？](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing)

- 如果存在活动同步会话，则每 5 到 10 分钟记录一次事件 ID 9302。 使用此事件可确定当前同步会话是否正在 (**AppliedItemCount > 0**) 进行进度。 如果同步未进行进度，同步会话应最终会失败，并且会记录错误的事件 ID 9102。 有关详细信息，请参阅 [同步进度文档](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session)。

已注册的服务器运行状况

- 当服务器查询作业服务时，每 30 秒记录一次事件 ID 9301。 如果 GetNextJob 的 **状态为 0**，则服务器可以与服务进行通信。 如果 GetNextJob 完成并出现错误，请查看 [故障排除文档](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#server-endpoint-noactivity) 以获取指导。

云分层运行状况

- 若要监视服务器上的分层活动，请使用遥测事件日志中的事件 ID 9003、9016和9029，该日志事件查看器位于 "Services\Microsoft\FileSync\Agent" 下的 " *应用程序" 和 "*" 下。

  - 事件 ID 9003 提供服务器终结点的错误分布情况。 例如：错误计数和 ErrorCode 总数。 每个错误代码记录一个事件。
  - 事件 ID 9016 提供卷的副本创建结果。 例如：可用空间百分比是，会话中幻像的文件数，以及失败的文件数。
  - 事件 ID 9029 提供服务器终结点的副本创建会话信息。 例如：会话中尝试的文件数、会话中分层的文件数以及已分层的文件数。
  
- 若要监视服务器上的撤回活动，请在遥测事件日志中使用事件 ID 9005、9006、9009、9059和9071，该日志事件查看器位于 Services\Microsoft\FileSync\Agent 下的 " *应用程序" 和 "*" 下。

  - 事件 ID 9005 提供服务器终结点的重新调用可靠性。 例如：已访问的唯一文件总数和访问失败的唯一文件总数。
  - 事件 ID 9006 提供服务器终结点的重新调用错误分布情况。 例如：失败请求总数和错误代码。 每个错误代码记录一个事件。
  - 事件 ID 9009 提供服务器终结点的回调会话信息。 例如： DurationSeconds、CountFilesRecallSucceeded 和 CountFilesRecallFailed。
  - 事件 ID 9059 提供服务器终结点的应用程序回调分布情况。 例如： ShareId、Application Name 和 TotalEgressNetworkBytes。
  - 事件 ID 9071 为服务器终结点提供云分层效率。 例如： TotalDistinctFileCountCacheHit、TotalDistinctFileCountCacheMiss、TotalCacheHitBytes 和 TotalCacheMissBytes。

### <a name="performance-counters"></a>性能计数器

使用服务器上的 Azure 文件同步性能计数器来监视同步活动。

若要在服务器上查看 Azure 文件同步性能计数器，请打开 # A0)  ( 的性能监视器。 可以在 "已 **传输的 Afs 字节** " 和 " **afs 同步操作** " 对象下找到计数器。

性能监视器提供了下列 Azure 文件同步性能计数器：

| 性能对象\计数器名称 | 描述 |
|-|-|
| AFS 传输的字节数\下载的字节数/秒 | 每秒下载的字节数。 |
| AFS 传输的字节数\上传的字节数/秒 | 每秒上传的字节数。 |
| AFS 传输的字节数\总字节数/秒 | 每秒（上传和下载）的总字节数。 |
| AFS 同步操作数\下载的同步文件数/秒 | 每秒下载的文件数。 |
| AFS 同步操作数\上传的同步文件数/秒 | 每秒上传的文件数。 |
| AFS 同步操作数\同步文件总操作数/秒 | 同步的文件总数（上传和下载）。 |

## <a name="alert-examples"></a>警报示例
本部分提供了有关 Azure 文件同步的一些示例警报。

  > [!Note]  
  > 如果创建了警报，但该警报太干扰，请调整阈值和警报逻辑。
  
### <a name="how-to-create-an-alert-if-the-server-endpoint-health-shows-an-error-in-the-portal"></a>如何在服务器终结点运行状况在门户中显示错误时创建警报

1. 在 **Azure 门户**中，导航到相应的 **存储同步服务**。 
2. 中转到 " **监视** " 部分，然后单击 " **警报**"。 
3. 单击 " **+ 新建警报规则** " 以创建新的警报规则。 
4. 通过单击 " **选择条件**" 配置条件。
5. 在 **配置信号逻辑** 边栏选项卡中，单击信号名称下的 **同步会话结果** 。  
6. 选择以下维度配置： 
    - 维度名称： **服务器终结点名称**  
    - 操作员 **=** 
    - 维度值： **当前和未来值**  
7. 导航到 **警报逻辑** 并完成以下操作： 
    - 阈值设置为 **静态** 
    - 运算符： **小于** 
    - 聚合类型： **最大值**  
    - 阈值： **1** 
    - 计算依据：聚合粒度 = **24 小时** |评估频率 = **每小时** 
    - 单击 " **完成"。** 
8. 单击 " **选择操作组** "，通过选择现有操作组或创建新的操作组，将操作组 (电子邮件、SMS 等 ) 添加到警报。
9. 填写警报 **详细信息** ，如 **警报规则名称**、 **描述** 和 **严重性**。
10. 单击“创建警报规则”。 

### <a name="how-to-create-an-alert-if-files-are-failing-to-sync-to-a-server-or-cloud-endpoint"></a>如果文件未能同步到服务器或云终结点，如何创建警报

1. 在 **Azure 门户**中，导航到相应的 **存储同步服务**。 
2. 中转到 " **监视** " 部分，然后单击 " **警报**"。 
3. 单击 " **+ 新建警报规则** " 以创建新的警报规则。 
4. 通过单击 " **选择条件**" 配置条件。
5. 在 " **配置信号逻辑** " 边栏选项卡中，单击 "不在信号名称下 **同步的文件** "  
6. 选择以下维度配置： 
     - 维度名称： **服务器终结点名称**  
     - 操作员 **=** 
     - 维度值： **当前和未来值**  
7. 导航到 **警报逻辑** 并完成以下操作： 
     - 阈值设置为 **静态** 
     - 运算符： **大于** 
     - 聚合类型： **Total**  
     - 阈值： **100** 
     - 计算依据：聚合粒度 = **5 分钟** |评估频率 = **每5分钟** 
     - 单击 " **完成"。** 
8. 单击 " **选择操作组** "，通过选择现有操作组或创建新的操作组，将操作组 (电子邮件、SMS 等 ) 添加到警报。
9. 填写警报 **详细信息** ，如 **警报规则名称**、 **描述** 和 **严重性**。
10. 单击“创建警报规则”。 

### <a name="how-to-create-an-alert-if-a-registered-server-is-failing-to-communicate-with-the-storage-sync-service"></a>如果已注册的服务器未能与存储同步服务进行通信，如何创建警报

1. 在 **Azure 门户**中，导航到相应的 **存储同步服务**。 
2. 中转到 " **监视** " 部分，然后单击 " **警报**"。 
3. 单击 " **+ 新建警报规则** " 以创建新的警报规则。 
4. 通过单击 " **选择条件**" 配置条件。
5. 在 **配置信号逻辑** 边栏选项卡中，单击信号名称下的 **服务器联机状态** 。  
6. 选择以下维度配置： 
     - 维度名称： **服务器名称**  
     - 操作员 **=** 
     - 维度值： **当前和未来值**  
7. 导航到 **警报逻辑** 并完成以下操作： 
     - 阈值设置为 **静态** 
     - 运算符： **小于** 
     - 聚合类型： **最大值**  
     - 阈值 (字节) ： **1** 
     - 计算依据：聚合粒度 = **1 小时** |评估频率 = **每30分钟** 
     - 单击 " **完成"。** 
8. 单击 " **选择操作组** "，通过选择现有操作组或创建新的操作组，将操作组 (电子邮件、SMS 等 ) 添加到警报。
9. 填写警报 **详细信息** ，如 **警报规则名称**、 **描述** 和 **严重性**。
10. 单击“创建警报规则”。 

### <a name="how-to-create-an-alert-if-the-cloud-tiering-recall-size-has-exceeded-500gib-in-a-day"></a>如果云分层撤回大小在一天内超过500GiB，如何创建警报

1. 在 **Azure 门户**中，导航到相应的 **存储同步服务**。 
2. 中转到 " **监视** " 部分，然后单击 " **警报**"。 
3. 单击 " **+ 新建警报规则** " 以创建新的警报规则。 
4. 通过单击 " **选择条件**" 配置条件。
5. 在 **配置信号逻辑** 边栏选项卡中，单击信号名称下的 **云分层撤回大小** 。  
6. 选择以下维度配置： 
     - 维度名称： **服务器名称**  
     - 操作员 **=** 
     - 维度值： **当前和未来值**  
7. 导航到 **警报逻辑** 并完成以下操作： 
     - 阈值设置为 **静态** 
     - 运算符： **大于** 
     - 聚合类型： **Total**  
     - 阈值 (字节) ： **67108864000** 
     - 计算依据：聚合粒度 = **24 小时** |评估频率 = **每小时** 
    - 单击 " **完成"。** 
8. 单击 " **选择操作组** "，通过选择现有操作组或创建新的操作组，将操作组 (电子邮件、SMS 等 ) 添加到警报。
9. 填写警报 **详细信息** ，如 **警报规则名称**、 **描述** 和 **严重性**。
10. 单击“创建警报规则”。 

## <a name="next-steps"></a>后续步骤
- [规划 Azure 文件同步部署](storage-sync-files-planning.md)
- [考虑防火墙和代理设置](storage-sync-files-firewall-and-proxy.md)
- [部署 Azure 文件同步](storage-sync-files-deployment-guide.md)
- [Azure 文件同步排除故障](storage-sync-files-troubleshoot.md)
- [Azure 文件常见问题解答](storage-files-faq.md)
