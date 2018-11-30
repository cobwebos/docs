---
title: 如何从用于 VM 的 Azure Monitor（预览版）查询日志 | Microsoft Docs
description: 用于 VM 的 Azure Monitor 解决方案会将指标和日志数据转发到 Log Analytics，本文将介绍相关的记录并提供示例查询。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/25/2018
ms.author: magoedte
ms.openlocfilehash: bfed4318d09a776f56a5a4b6218120d75a49fc80
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2018
ms.locfileid: "51713860"
---
# <a name="how-to-query-logs-from-azure-monitor-for-vms-preview"></a>如何从用于 VM 的 Azure Monitor 查询日志（预览版）
用于 VM 的 Azure Monitor 收集性能和连接指标、计算机和进程库存数据以及运行状况信息，并将其转发到 Azure Monitor 中的 Log Analytics 数据存储。  可在 Log Analytics 中[搜索](../../log-analytics/log-analytics-queries.md)此数据。 此数据可应用于包括迁移计划、容量分析、发现和按需性能故障排除在内的方案。

## <a name="map-records"></a>映射记录
除了在进程或计算机启动或载入到用于 VM 的 Azure Monitor 映射功能时生成的记录以外，还会针对每个唯一计算机和进程每小时生成一条记录。 这些记录的属性在下表中列出。 ServiceMapComputer_CL 事件中的字段和值映射到 ServiceMap Azure 资源管理器 API 中计算机资源的字段。 ServiceMapProcess_CL 事件中的字段和值映射到 ServiceMap Azure 资源管理器 API 中进程资源的字段。 ResourceName_s 字段与相应的 Azure Resource Manager 资源中的名称字段匹配。 

包含内部生成的可用于标识唯一进程和计算机的属性：

- 计算机：使用 *ResourceId* 或 *ResourceName_s* 唯一标识 Log Analytics 工作区中的计算机。
- 进程：使用 *ResourceId* 唯一标识 Log Analytics 工作区中的进程。 *ResourceName_s* 在运行该进程的计算机 (MachineResourceName_s) 的上下文中唯一 

由于在指定的时间范围内，指定的进程和计算机可能存在多条记录，因此针对同一个计算机或进程的查询可能返回多条记录。 若要仅添加最新记录，请在查询中添加“| dedup ResourceId”。

### <a name="connections"></a>连接
连接指标将写入到 Log Analytics 的新表 VMConnection 中。 此表提供有关计算机的连接（入站和出站）的信息。 还可以通过 API 公开连接指标。使用这些 API 可以获取某个时间范围内的特定指标。  在侦听套接字上执行 *accept* 命令后生成的 TCP 连接是入站连接，针对给定的 IP 和端口执行 *connect* 命令后建立的 TCP 连接是出站连接。 连接方向由 Direction 属性表示，可将其设置为 **inbound** 或 **outbound**。 

这些表中的记录是基于依赖项代理报告的数据生成的。 每条记录表示一分钟时间间隔内观测到的结果。 TimeGenerated 属性表示时间间隔的开始时间。 每条记录包含用于识别相应实体（即连接或端口）以及与该实体关联的指标的信息。 目前，只会报告使用“基于 IPv4 的 TCP”发生的网络活动。

为了控制成本和复杂性，连接记录不会显示单个物理网络连接。 多个物理网络连接分组到一个逻辑连接中，然后在相应的表中反映该逻辑连接。  这意味着，*VMConnection* 表中的记录表示逻辑分组，而不是观测到的单个物理连接。 在给定的一分钟时间间隔内对以下属性共用相同值的物理网络连接聚合到 VMConnection 中的一个逻辑记录内。 

| 属性 | Description |
|:--|:--|
|方向 |连接方向，值为 *inbound* 或 *outbound* |
|计算机 |计算机 FQDN |
|进程 |进程或进程组的标识，状态为正在启动/接受连接 |
|SourceIp |源的 IP 地址 |
|DestinationIp |目标的 IP 地址 |
|DestinationPort |目标的端口号 |
|协议 |用于连接的协议。  值为 *tcp*。 |

为了帮助你权衡分组造成的影响，以下记录属性中提供了有关分组的物理连接数的信息：

| 属性 | Description |
|:--|:--|
|LinksEstablished |在报告时间范围内建立的物理网络连接数 |
|LinksTerminated |在报告时间范围内终止的物理网络连接数 |
|LinksFailed |在报告时间范围内失败的物理网络连接数 此信息目前仅适用于出站连接。 |
|LinksLive |在报告时间范围结束时打开的物理网络连接数|

#### <a name="metrics"></a>度量值

除了连接计数指标以外，以下记录属性中还包含了有关在给定逻辑连接或网络端口上发送和接收的数据量的信息：

| 属性 | Description |
|:--|:--|
|BytesSent |在报告时间范围内发送的字节总数 |
|BytesReceived |在报告时间范围内接收的字节总数 |
|响应 |在报告时间范围内观测到的响应数。 
|ResponseTimeMax |在报告时间范围内观测到的最大响应时间（毫秒）。 如果无值，则该属性为空。|
|ResponseTimeMin |在报告时间范围内观测到的最小响应时间（毫秒）。 如果无值，则该属性为空。|
|ResponseTimeSum |在报告时间范围内观测到的所有响应时间的和（毫秒）。 如果无值，则该属性为空。|

报告的第三种数据类型是响应时间 - 调用方花费了多长时间来等待通过连接发送请求进行处理，并收到远程终结点的响应。 报告的响应时间是底层应用程序协议的真实响应时间的估算值。 它是基于物理网络连接的源与目标端之间的数据流观测结果，使用试探法计算出来的。 从概念上讲，它是请求的最后一个字节离开发送方的时间，与发送方收到响应的最后一个字节的时间的差。 这两个时间戳用于描述给定物理连接上的请求和响应事件。 两者的差表示单个请求的响应时间。 

在此功能的第一个版本中，我们的算法是求近似值，根据给定网络连接所用的实际应用程序协议，其成功度各不相同。 例如，当前做法非常适合基于请求-响应的协议（例如 HTTP (S)），但不适合单向协议或基于消息队列的协议。

考虑的几个要点：

1. 如果进程在相同的 IP 地址上接受连接，但通过多个网络接口接受连接，则为每个接口单独报告一条记录。 
2. 带通配符 IP 的记录不包含任何活动。 包含此类记录的目的是表示在计算机上为入站流量开放了某个端口这一事实。
3. 为了降低详细程度和数据量，存在带有特定 IP 地址的匹配记录（适用于相同的进程、端口和协议）时，将省略带通配符 IP 的记录。 省略了通配符 IP 记录后，具有特定 IP 地址的 IsWildcardBind 记录属性将设置为“True”，表示已通过报告计算机的每个接口公开了该端口。
4. 仅在特定接口上绑定的端口的 IsWildcardBind 设置为“False”。

#### <a name="naming-and-classification"></a>命名和分类
为提供方便，RemoteIp 属性中包含了连接的远程端的 IP 地址。 对于入站连接，RemoteIp 与 SourceIp 相同；对于出站连接，RemoteIp 与 DestinationIp 相同。 RemoteDnsCanonicalNames 属性表示计算机针对 RemoteIp 报告的 DNS 规范名称。 RemoteDnsQuestions 和 RemoteClassification 属性保留供将来使用。 

#### <a name="geolocation"></a>地理位置
*VMConnection* 还包含以下记录属性中每个连接记录的远程端的地理位置信息： 

| 属性 | Description |
|:--|:--|
|RemoteCountry |RemoteIp 所在的国家/地区的名称。  例如 *United States* |
|RemoteLatitude |地理位置的纬度。 例如 *47.68* |
|RemoteLongitude |地理位置的经度。 例如 *-122.12* |

#### <a name="malicious-ip"></a>恶意 IP
将会根据一组 IP 检查 *VMConnection* 表中的每个 RemoteIp 属性，以识别已知的恶意活动。 如果 RemoteIp 识别为恶意，则会在以下记录属性中填充以下属性（如果未将该 IP 视为恶意，则这些属性为空）：

| 属性 | Description |
|:--|:--|
|MaliciousIp |RemoteIp 地址 |
|IndicatorThreadType |检测到的威胁标志是以下值之一：Botnet、C2、CryptoMining、Darknet、DDos、MaliciousUrl、Malware、Phishing、Proxy、PUA 和 Watchlist。   |
|Description |观察到的威胁说明。 |
|TLPLevel |交通信号灯协议 (TLP) 级别是以下定义值之一：White、Green、Amber 和 Red。 |
|置信度 |值介于 0 和 100 之间。 |
|严重性 |值介于 0 和 5 之间，其中 5 表示最严重，0 表示毫不严重。 默认值为 3。  |
|FirstReportedDateTime |提供程序第一次报告指标。 |
|LastReportedDateTime |Interflow 最后一次看到指标。 |
|IsActive |使用值 True 或 False 指明是否停用标志。 |
|ReportReferenceLink |与给定可观测结果相关的报告的链接。 |
|AdditionalInformation |提供观测到的威胁的其他信息（若有）。 |

### <a name="servicemapcomputercl-records"></a>ServiceMapComputer_CL 记录
类型为 *ServiceMapComputer_CL* 的记录包含具有依赖项代理的服务器的库存数据。 这些记录的属性在下表中列出：

| 属性 | 说明 |
|:--|:--|
| 类型 | *ServiceMapComputer_CL* |
| SourceSystem | *OpsManager* |
| ResourceId | 工作区中计算机的唯一标识符 |
| ResourceName_s | 工作区中计算机的唯一标识符 |
| ComputerName_s | 计算机 FQDN |
| Ipv4Addresses_s | 服务器的 IPv4 地址列表 |
| Ipv6Addresses_s | 服务器的 IPv6 地址列表 |
| DnsNames_s | DNS 名称的数组 |
| OperatingSystemFamily_s | Windows 或 Linux |
| OperatingSystemFullName_s | 操作系统的全名  |
| Bitness_s | 计算机的位数（32 位或 64 位）  |
| PhysicalMemory_d | 物理内存（以 MB 为单位） |
| Cpus_d | CPU 数 |
| CpuSpeed_d | CPU 速度（以 MHz 为单位）|
| VirtualizationState_s | *未知*、*物理*、*虚拟*、*虚拟机监控程序* |
| VirtualMachineType_s | *hyperv*、*vmware* 等等 |
| VirtualMachineNativeMachineId_g | 由虚拟机监控程序分配的 VM ID |
| VirtualMachineName_s | VM 的名称 |
| BootTime_t | 引导时间 |

### <a name="servicemapprocesscl-type-records"></a>ServiceMapProcess_CL 类型记录
类型为 *ServiceMapProcess_CL* 的记录包含具有依赖项代理的服务器上 TCP 连接进程的库存数据。 这些记录的属性在下表中列出：

| 属性 | 说明 |
|:--|:--|
| 类型 | *ServiceMapProcess_CL* |
| SourceSystem | *OpsManager* |
| ResourceId | 工作区中进程的唯一标识符 |
| ResourceName_s | 进程在运行它的计算机中的唯一标识符|
| MachineResourceName_s | 计算机的资源名称 |
| ExecutableName_s | 进程可执行文件的名称 |
| StartTime_t | 进程池启动时间 |
| FirstPid_d | 进程池中的第一个 PID |
| Description_s | 进程说明 |
| CompanyName_s | 公司名称 |
| InternalName_s | 内部名称 |
| ProductName_s | 产品名称 |
| ProductVersion_s | 产品版本 |
| FileVersion_s | 文件版本 |
| CommandLine_s | 命令行 |
| ExecutablePath_s | 可执行文件的路径 |
| WorkingDirectory_s | 工作目录 |
| UserName | 执行进程所用的帐户 |
| UserDomain | 执行进程所在的域 |

## <a name="sample-log-searches"></a>示例日志搜索

### <a name="list-all-known-machines"></a>列出所有已知计算机
`ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId`

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>列出所有托管计算机的物理内存容量。
`ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId | project PhysicalMemory_d, ComputerName_s`

### <a name="list-computer-name-dns-ip-and-os"></a>列出计算机名称、DNS、IP 和 OS。
`ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId | project ComputerName_s, OperatingSystemFullName_s, DnsNames_s, Ipv4Addresses_s`

### <a name="find-all-processes-with-sql-in-the-command-line"></a>在命令行中查找带有“sql”的所有进程
`ServiceMapProcess_CL | where CommandLine_s contains_cs "sql" | summarize arg_max(TimeGenerated, *) by ResourceId`

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>按资源名称查找计算机（最新记录）
`search in (ServiceMapComputer_CL) "m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | summarize arg_max(TimeGenerated, *) by ResourceId`

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>按 IP 地址查找计算机（最新记录）
`search in (ServiceMapComputer_CL) "10.229.243.232" | summarize arg_max(TimeGenerated, *) by ResourceId`

### <a name="list-all-known-processes-on-a-specified-machine"></a>列出指定计算机上的所有已知进程
`ServiceMapProcess_CL | where MachineResourceName_s == "m-559dbcd8-3130-454d-8d1d-f624e57961bc" | summarize arg_max(TimeGenerated, *) by ResourceId`

### <a name="list-all-computers-running-sql"></a>列出所有运行 SQL 的计算机
`ServiceMapComputer_CL | where ResourceName_s in ((search in (ServiceMapProcess_CL) "\*sql\*" | distinct MachineResourceName_s)) | distinct ComputerName_s`

### <a name="list-all-unique-product-versions-of-curl-in-my-datacenter"></a>在我的数据中心列出 curl 的所有唯一产品版本
`ServiceMapProcess_CL | where ExecutableName_s == "curl" | distinct ProductVersion_s`

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>创建由运行 CentOS 的所有计算机组成的计算机组
`ServiceMapComputer_CL | where OperatingSystemFullName_s contains_cs "CentOS" | distinct ComputerName_s`

### <a name="summarize-the-outbound-connections-from-a-group-of-machines"></a>汇总一组计算机的出站连接
```
// the machines of interest
let machines = datatable(m: string) ["m-82412a7a-6a32-45a9-a8d6-538354224a25"];
// map of ip to monitored machine in the environment
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
// all connections to/from the machines of interest
let out=materialize(VMConnection
| where Machine in (machines)
| summarize arg_max(TimeGenerated, *) by ConnectionId);
// connections to localhost augmented with RemoteMachine
let local=out
| where RemoteIp startswith "127."
| project ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine=Machine;
// connections not to localhost augmented with RemoteMachine
let remote=materialize(out
| where RemoteIp !startswith "127."
| join kind=leftouter (ips) on $left.RemoteIp == $right.ips
| summarize by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine=MonitoredMachine);
// the remote machines to/from which we have connections
let remoteMachines = remote | summarize by RemoteMachine;
// all augmented connections
(local)
| union (remote)
//Take all outbound records but only inbound records that come from either //unmonitored machines or monitored machines not in the set for which we are computing dependencies.
| where Direction == 'outbound' or (Direction == 'inbound' and RemoteMachine !in (machines))
| summarize by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine
// identify the remote port
| extend RemotePort=iff(Direction == 'outbound', DestinationPort, 0)
// construct the join key we'll use to find a matching port
| extend JoinKey=strcat_delim(':', RemoteMachine, RemoteIp, RemotePort, Protocol)
// find a matching port
| join kind=leftouter (VMBoundPort 
| where Machine in (remoteMachines) 
| summarize arg_max(TimeGenerated, *) by PortId 
| extend JoinKey=strcat_delim(':', Machine, Ip, Port, Protocol)) on JoinKey
// aggregate the remote information
| summarize Remote=makeset(iff(isempty(RemoteMachine), todynamic('{}'), pack('Machine', RemoteMachine, 'Process', Process1, 'ProcessName', ProcessName1))) by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol
```

## <a name="next-steps"></a>后续步骤
* 如果你不太了解如何在 Log Analytics 中编写查询，请参考 Azure 门户中的[如何使用 Log Analytics 页](../../log-analytics/query-language/get-started-analytics-portal.md)编写 Log Analytics 查询。
* 了解如何[编写搜索查询](../../log-analytics/query-language/search-queries.md)。
