---
title: "在 Azure 网络观察程序中进行资源故障排除简介 | Microsoft 文档"
description: "此页概述了网络观察程序资源故障排除功能"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: c1145cd6-d1cf-4770-b1cc-eaf0464cc315
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: dcf8a88ebd66f5e4a5a06538532fbfbae7ce852e
ms.lasthandoff: 03/04/2017

---

# <a name="introduction-to-resource-troubleshooting-in-azure-network-watcher"></a>在 Azure 网络观察程序中进行资源故障排除简介

虚拟网关在 Azure 中的本地资源和其他虚拟网络之间提供连接。 监视这些网关及其连接对于确保通信不中断至关重要。 网络观察程序提供对虚拟网关和连接进行故障排除的功能。 可以通过 PowerShell、CLI 或 REST API 调用网络观察程序。 调用后，网络观察程序将对虚拟网关或连接的运行状况进行诊断，并返回相应的结果。 该请求是一个长时间运行的事务。一旦诊断完毕，就会返回结果。

## <a name="results"></a>结果

可以通过返回的初步结果大致了解资源的运行状况。 可以为资源提供更深层的信息，如以下部分所示：

以下列表是通过故障排除 API 返回的值：

* **startTime** - 此值是故障排除 API 调用的开始时间。
* **endTime** - 此值是故障排除的结束时间。
* **code** - 如果存在单个诊断故障，则此值为 UnHealthy。
* **results** - 返回的关于连接或虚拟网关的结果的集合。
    * **id** - 此值为错误类型。
    * **summary** - 此值为错误的摘要。
    * **detailed** - 此值提供对错误的详细说明。
    * **recommendedActions** - 此属性是要采取的建议操作的集合。
      * **actionText** - 此值包含的文本描述了要采取的具体操作。
      * **actionUri** - 此值提供操作说明文档的 URI。
      * **actionUriText** - 此值对操作文本进行了简短的说明。

下表显示了提供的不同错误类型（即前面的列表中结果下的 id）以及该错误是否创建日志。

### <a name="gateway"></a>网关

| 错误类型 | 原因 | 日志|
|---|---|---|
| NoFault | 未检测到任何错误。 |是|
| GatewayNotFound | 找不到网关，或网关未预配。 |否|
| PlannedMaintenance |  网关实例处于维护状态。  |否|
| UserDrivenUpdate | 用户更新正在进行。 可能是正在执行大小调整操作。 | 否 |
| VipUnResponsive | 无法访问网关的主实例。 运行状况探测失败时会发生这种情况。 | 否 |
| PlatformInActive | 平台出现问题。 | 否|
| ServiceNotRunning | 底层服务未运行。 | 否|
| NoConnectionsFoundForGateway | 网关未建立连接。 这只是一条警告。| 否|
| ConnectionsNotConnected | 未连接任何连接。 这只是一条警告。| 是|
| GatewayCPUUsageExceeded | 当前网关 CPU 使用率超过 95%。 | 是 |

### <a name="connection"></a>连接

| 错误类型 | 原因 | 日志|
|---|---|---|
| NoFault | 未检测到任何错误。 |是|
| GatewayNotFound | 找不到网关，或网关未预配。 |否|
| PlannedMaintenance | 网关实例处于维护状态。  |否|
| UserDrivenUpdate | 用户更新正在进行。 可能是正在执行大小调整操作。  | 否 |
| VipUnResponsive | 无法访问网关的主实例。 运行状况探测失败时会发生这种情况。 | 否 |
| ConnectionEntityNotFound | 缺少连接配置。 | 否 |
| ConnectionIsMarkedDisconnected | 连接标记为“断开连接”。 |否|
| ConnectionNotConfiguredOnGateway | 未在底层服务上配置连接。 | 是 |
| ConnectionMarkedStandy | 底层服务标记为备用。| 是|
| 身份验证 | 预共享密钥不匹配。 | 是|
| PeerReachability | 无法访问对等网关。 | 是|
| IkePolicyMismatch | 对等网关中的 IKE 策略不受 Azure 支持。 | 是|
| WfpParse 错误 | 分析 WFP 日志时出错。 |是|


## <a name="log-files"></a>日志文件

资源故障诊断完成后，其日志文件存储在存储帐户中。 下图显示了导致错误的调用的示例内容。

![zip 文件][1]

> [!NOTE]
> 在某些情况下，仅部分日志文件写入到存储中。

有关从 Azure 存储帐户下载文件的说明，请参阅[通过 .NET 使用 Azure Blob 存储入门](../storage/storage-dotnet-how-to-use-blobs.md)。 可以使用的另一个工具是存储资源管理器。 有关存储资源管理器的详细信息可以在此链接中找到：[存储资源管理器](http://storageexplorer.com/)

### <a name="connectionstatstxt"></a>ConnectionStats.txt

**ConnectionStats.txt** 文件包含连接的综合性统计信息，包括入口和出口字节数、连接状态、建立连接的时间。

> [!NOTE]
> 如果调用故障排除 API 后返回“正常”，则只在 zip 文件中返回 **ConnectionStats.txt** 文件。

该文件的内容类似于以下示例：

```
Connectivity State : Connected
Remote Tunnel Endpoint :
Ingress Bytes (since last connected) : 288 B
Egress Bytes (Since last connected) : 288 B
Connected Since : 2/1/2017 8:22:06 PM
```

### <a name="cpustatstxt"></a>CPUStats.txt

**CPUStats.txt** 文件包含测试时的 CPU 使用率和可用内存。  该文件的内容类似于以下示例：

```
Current CPU Usage : 0 % Current Memory Available : 641 MBs
```

### <a name="ikeerrorstxt"></a>IKEErrors.txt

**IKEErrors.txt** 文件包含在监视过程中发现的任何 IKE 错误。

以下示例显示一个 IKEErrors.txt 文件的内容。 用户的错误可能因问题的不同而不同。

```
Error: Authentication failed. Check shared key. Check crypto. Check lifetimes. 
     based on log : Peer failed with Windows error 13801(ERROR_IPSEC_IKE_AUTH_FAIL)
Error: On-prem device sent invalid payload. 
     based on log : IkeFindPayloadInPacket failed with Windows error 13843(ERROR_IPSEC_IKE_INVALID_PAYLOAD)
```

### <a name="scrubbed-wfpdiagtxt"></a>Scrubbed-wfpdiag.txt

**Scrubbed-wfpdiag.txt** 日志文件包含 wfp 日志。 该日志包含对数据包丢弃操作和 IKE/AuthIP 故障的日志记录。

以下示例显示 Scrubbed-wfpdiag.txt 文件的内容。 在此示例中，连接的共享密钥不正确（倒数第 3 行）。 以下示例只是完整日志的一个片段，因为日志可能很长（具体取决于问题）。

```
...
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Deleted ICookie from the high priority thread pool list
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|IKE diagnostic event:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Event Header:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Timestamp: 1601-01-01T00:00:00.000Z
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Flags: 0x00000106
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    Local address field set
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    Remote address field set
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    IP version field set
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  IP version: IPv4
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  IP protocol: 0
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Local address: 13.78.238.92
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Remote address: 52.161.24.36
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Local Port: 0
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Remote Port: 0
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Application ID:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  User SID: <invalid>
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Failure type: IKE/Authip Main Mode Failure
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Type specific info:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Failure error code:0x000035e9
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    IKE authentication credentials are unacceptable
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Failure point: Remote
...
```

### <a name="wfpdiagtxtsum"></a>wfpdiag.txt.sum

**wfpdiag.txt.sum** 文件是一个显示已处理的缓冲区和事件的日志。

以下示例是 wfpdiag.txt.sum 文件的内容。
```
Files Processed:
    C:\Resources\directory\924336c47dd045d5a246c349b8ae57f2.GatewayTenantWorker.DiagnosticsStorage\2017-02-02T17-34-23\wfpdiag.etl
Total Buffers Processed 8
Total Events  Processed 2169
Total Events  Lost      0
Total Format  Errors    0
Total Formats Unknown   486
Elapsed Time            330 sec
+-----------------------------------------------------------------------------------+
|EventCount    EventName            EventType   TMF                                 |
+-----------------------------------------------------------------------------------+
|        36    ikeext               ike_addr_utils_c844  a0c064ca-d954-350a-8b2f-1a7464eef8b6|
|        12    ikeext               ike_addr_utils_c857  a0c064ca-d954-350a-8b2f-1a7464eef8b6|
|        96    ikeext               ike_addr_utils_c832  a0c064ca-d954-350a-8b2f-1a7464eef8b6|
|         6    ikeext               ike_bfe_callbacks_c133  1dc2d67f-8381-6303-e314-6c1452eeb529|
|         6    ikeext               ike_bfe_callbacks_c61  1dc2d67f-8381-6303-e314-6c1452eeb529|
|        12    ikeext               ike_sa_management_c5698  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|         6    ikeext               ike_sa_management_c8447  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|        12    ikeext               ike_sa_management_c494  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|        12    ikeext               ike_sa_management_c642  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|         6    ikeext               ike_sa_management_c3162  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|        12    ikeext               ike_sa_management_c3307  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
```

## <a name="next-steps"></a>后续步骤

访问[网关故障诊断 - PowerShell](network-watcher-troubleshoot-manage-powershell.md)，了解如何使用 PowerShell 对 VPN 网关和连接进行诊断。
<!--Image references-->

[1]: ./media/network-watcher-troubleshoot-overview/GatewayTenantWorkerLogs.png

