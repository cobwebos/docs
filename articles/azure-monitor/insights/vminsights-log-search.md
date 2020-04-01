---
title: 如何从 Azure 监视器查询 VM 日志
description: 用于 VM 解决方案的 Azure Monitor 收集指标和日志数据，本文介绍了这些记录并包含了示例查询。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 61a71539dc034a216689eafd8991df60db96d2a4
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2020
ms.locfileid: "80396928"
---
# <a name="how-to-query-logs-from-azure-monitor-for-vms"></a>如何从用于 VM 的 Azure Monitor 查询日志

用于 VM 的 Azure Monitor 收集性能和连接指标、计算机和进程库存数据以及运行状况信息，并将其转发到 Azure Monitor 中的 Log Analytics 工作区。  此数据可用于 Azure Monitor 中的[查询](../../azure-monitor/log-query/log-query-overview.md)。 此数据可应用于包括迁移计划、容量分析、发现和按需性能故障排除在内的方案。

## <a name="map-records"></a>映射记录

除了在进程或计算机启动或载入到用于 VM 的 Azure Monitor 映射功能时生成的记录以外，还会针对每个唯一计算机和进程每小时生成一条记录。 这些记录的属性在下表中列出。 ServiceMapComputer_CL 事件中的字段和值映射到 ServiceMap Azure 资源管理器 API 中计算机资源的字段。 ServiceMapProcess_CL 事件中的字段和值映射到 ServiceMap Azure 资源管理器 API 中进程资源的字段。 ResourceName_s 字段与相应的 Azure Resource Manager 资源中的名称字段匹配。 

包含内部生成的可用于标识唯一进程和计算机的属性：

- 计算机：使用 *ResourceId* 或 *ResourceName_s* 唯一标识 Log Analytics 工作区中的计算机。
- 进程：使用 *ResourceId* 唯一标识 Log Analytics 工作区中的进程。 *ResourceName_s*在运行进程的机器的上下文中是唯一的（MachineResourceName_s） 

由于在指定的时间范围内，指定的进程和计算机可能存在多条记录，因此针对同一个计算机或进程的查询可能返回多条记录。 若要仅添加最新记录，请在查询中添加 `| summarize arg_max(TimeGenerated, *) by ResourceId`。

### <a name="connections-and-ports"></a>连接和端口

“连接指标”功能在 Azure Monitor 日志中引入两个新表 - VMConnection 和 VMBoundPort。 这两个表提供有关计算机的连接（入站和出站）的信息，以及在其上处于打开/活动状态的服务器端口的信息。 还可以通过 API 公开连接指标。使用这些 API 可以获取某个时间范围内的特定指标。 侦听套接字上*接受*产生的 TCP 连接是入站连接，而通过*连接到*给定 IP 和端口创建的 TCP 连接是出站连接。 连接方向由 Direction 属性表示，可将其设置为 **inbound** 或 **outbound**。 

这些表中的记录是基于依赖项代理报告的数据生成的。 每条记录表示 1 分钟时间间隔内观测到的结果。 TimeGenerated 属性表示时间间隔的开始时间。 每条记录包含用于识别相应实体（即连接或端口）以及与该实体关联的指标的信息。 目前，只会报告使用“基于 IPv4 的 TCP”发生的网络活动。 

#### <a name="common-fields-and-conventions"></a>公共字段和约定 

以下字段和约定仅适用于 VMConnection 和 VMBoundPort： 

- 计算机：报告机完全限定的域名 
- 代理 Id：具有日志分析代理的计算机的唯一标识符  
- 计算机：服务映射公开的计算机的 Azure 资源管理器资源的名称。 它是*m-{GUID}* 窗体，其中*GUID*与 AgentId 相同  
- 进程：服务映射公开的进程的 Azure 资源管理器资源的名称。 它采用 *p-{十六进制字符串}* 格式。 流程在计算机作用域内是独一无二的，用于生成在多个计算机中唯一的进程 ID，以及组合计算机和进程字段。 
- 流程名称：报告流程的可执行名称。
- 所有 IP 地址都是 IPv4 规范格式的字符串，例如 *13.107.3.160* 

为了控制成本和复杂性，连接记录不会显示单个物理网络连接。 多个物理网络连接分组到一个逻辑连接中，然后在相应的表中反映该逻辑连接。  这意味着，*VMConnection* 表中的记录表示逻辑分组，而不是观测到的单个物理连接。 在给定的一分钟时间间隔内对以下属性共用相同值的物理网络连接聚合到 VMConnection** 中的一个逻辑记录内。 

| properties | 说明 |
|:--|:--|
|方向 |连接方向，值为 *inbound* 或 *outbound* |
|计算机 |计算机 FQDN |
|进程 |进程或进程组的标识，状态为正在启动/接受连接 |
|SourceIp |源的 IP 地址 |
|DestinationIp |目标的 IP 地址 |
|DestinationPort |目标的端口号 |
|协议 |用于连接的协议。  值为 *tcp*。 |

为了帮助你权衡分组造成的影响，以下记录属性中提供了有关分组的物理连接数的信息：

| properties | 说明 |
|:--|:--|
|LinksEstablished |在报告时间范围内建立的物理网络连接数 |
|LinksTerminated |在报告时间范围内终止的物理网络连接数 |
|LinksFailed |在报告时间范围内失败的物理网络连接数 此信息目前仅适用于出站连接。 |
|LinksLive |在报告时间范围结束时打开的物理网络连接数|

#### <a name="metrics"></a>指标

除了连接计数指标以外，以下记录属性中还包含了有关在给定逻辑连接或网络端口上发送和接收的数据量的信息：

| properties | 说明 |
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
4. 仅在特定接口上绑定的端口将 IsWildcardBind 设置为*False*。

#### <a name="naming-and-classification"></a>命名和分类

为提供方便，RemoteIp 属性中包含了连接的远程端的 IP 地址。 对于入站连接，RemoteIp 与 SourceIp 相同；对于出站连接，RemoteIp 与 DestinationIp 相同。 RemoteDnsCanonicalNames 属性表示计算机针对 RemoteIp 报告的 DNS 规范名称。 RemoteDnsQuestions 和 RemoteClassification 属性保留供将来使用。 

#### <a name="geolocation"></a>地理位置

*VMConnection* 还包含以下记录属性中每个连接记录的远程端的地理位置信息： 

| properties | 说明 |
|:--|:--|
|RemoteCountry |托管 RemoteIp 的国家/地区的名称。  例如，*美国* |
|RemoteLatitude |地理位置的纬度。 例如 *47.68* |
|RemoteLongitude |地理位置的经度。 例如 *-122.12* |

#### <a name="malicious-ip"></a>恶意 IP

将会根据一组 IP 检查 *VMConnection* 表中的每个 RemoteIp 属性，以识别已知的恶意活动。 如果 RemoteIp 识别为恶意，则会在以下记录属性中填充以下属性（如果未将该 IP 视为恶意，则这些属性为空）：

| properties | 说明 |
|:--|:--|
|MaliciousIp |RemoteIp 地址 |
|IndicatorThreadType |检测到的威胁标志是以下值之一：Botnet**、C2**、CryptoMining**、Darknet**、DDos**、MaliciousUrl**、Malware**、Phishing**、Proxy**、PUA** 和 Watchlist**。   |
|说明 |观察到的威胁说明。 |
|TLPLevel |交通信号灯协议 (TLP) 级别是以下定义值之一：White**、Green**、Amber** 和 Red**。 |
|置信度 |值介于 0 和 100** 之间。 |
|严重性 |值介于 0 和 5** 之间，其中 5** 表示最严重，0** 表示毫不严重。 默认值为 3**。  |
|FirstReportedDateTime |提供程序第一次报告指标。 |
|LastReportedDateTime |Interflow 最后一次看到指标。 |
|IsActive |使用值 True** 或 False** 指明是否停用标志。 |
|ReportReferenceLink |与给定可观测结果相关的报告的链接。 |
|AdditionalInformation |提供观测到的威胁的其他信息（若有）。 |

### <a name="ports"></a>端口 

计算机上那些会积极接受传入流量，或者可能会接受流量，但在报告时间窗口期间处于空闲状态的端口会写入到 VMBoundPort 表。  

VMBoundPort 中的每个记录按以下字段标识： 

| properties | 说明 |
|:--|:--|
|进程 | 标识与端口关联的进程（或进程组）。|
|Ip | 端口 IP 地址（可以是通配符 IP *0.0.0.0*） |
|端口 |端口号 |
|协议 | 协议。  例如 *tcp* 或 *udp*（目前仅支持 *tcp*）。|
 
端口派生自上述五个字段时使用的标识，存储在 PortId 属性中。 可以使用此属性快速查找一段时间内特定端口的记录。 

#### <a name="metrics"></a>指标 

端口记录包含的指标代表与之关联的连接。 目前会报告以下指标（每个指标的详细信息已在上一部分介绍）： 

- BytesSent 和 BytesReceived 
- LinksEstablished、LinksTerminated、LinksLive 
- ResposeTime、ResponseTimeMin、ResponseTimeMax、ResponseTimeSum 

考虑的几个要点：

- 如果进程在相同的 IP 地址上接受连接，但通过多个网络接口接受连接，则为每个接口单独报告一条记录。  
- 带通配符 IP 的记录不包含任何活动。 包含此类记录的目的是表示在计算机上为入站流量开放了某个端口这一事实。 
- 为了降低详细程度和数据量，存在带有特定 IP 地址的匹配记录（适用于相同的进程、端口和协议）时，将省略带通配符 IP 的记录。 省略了通配符 IP 记录后，具有特定 IP 地址的记录的 *IsWildcardBind* 属性将设置为 *True*。  这表示已通过报告计算机的每个接口公开了该端口。 
- 仅在特定接口上绑定的端口将 IsWildcardBind 设置为*False*。 

### <a name="vmcomputer-records"></a>虚拟机记录

具有*VMComputer*类型的记录具有具有依赖项代理的服务器的清单数据。 这些记录的属性在下表中列出：

| properties | 说明 |
|:--|:--|
|TenantId | 工作区的唯一标识符 |
|SourceSystem | *洞察力* | 
|TimeGenerated | 记录的时间戳 （UTC） |
|Computer | 计算机 FQDN | 
|AgentId | 日志分析代理的唯一 ID |
|计算机 | ServiceMap 公开的计算机的 Azure 资源管理器资源的名称。 它是*m-{GUID}* 窗体，其中*GUID*与 AgentId 相同。 | 
|DisplayName | 显示名称 | 
|全显示器名称 | 完整显示名称 | 
|HostName | 没有域名的计算机名称 |
|启动时间 | 机器启动时间 （UTC） |
|TimeZone | 规范化时区 |
|虚拟化状态 | *虚拟*，*虚拟机管理程序*，*物理* |
|Ipv4 地址 | IPv4 地址数组 | 
|Ipv4 子网掩码 | IPv4 子网掩码数组（顺序与 Ipv4 地址相同）。 |
|Ipv4 默认网关 | IPv4 网关阵列 | 
|Ipv6 地址 | IPv6 地址的数组 | 
|Mac 地址 | MAC 地址数组 | 
|Dns名称 | 与计算机关联的 DNS 名称数组。 |
|依赖项代理版本 | 在计算机上运行的依赖项代理的版本。 | 
|操作系统系列 | *Linux*，*窗口* |
|操作系统全名 | 操作系统的全名 | 
|物理内存MB | 以兆字节为单位的物理内存 | 
|Cpu | 处理器的数目 | 
|Cpu速度 | CPU 速度（以 MHz 为单位） | 
|虚拟机类型 | *hyperv*， *vmware*， *xen* |
|虚拟机本机 Id | 由虚拟机监控程序分配的 VM ID | 
|虚拟机本机名称 | VM 的名称 |
|虚拟机虚拟机虚拟机管理Id | 托管 VM 的虚拟机管理程序的唯一标识符 |
|虚拟机管理程序类型 | *超v* |
|虚拟机管理 | 虚拟机管理程序的唯一 ID | 
|托管提供商 | *蔚蓝* |
|_ResourceId | Azure 资源的唯一标识符 |
|AzureSubscriptionId | 标识订阅的全球唯一标识符 | 
|Azure 资源组 | 计算机是 其成员的 Azure 资源组的名称。 |
|Azure 资源名称 | Azure 资源的名称 |
|Azure 定位 | Azure 资源的位置 |
|Azure 更新域 | Azure 更新域的名称 |
|AzureFaultDomain | Azure 容错域的名称 |
|AzureVmId | Azure 虚拟机的唯一标识符 |
|Azure 大小 | Azure 虚拟机的大小 |
|Azure 图像发布者 | Azure VM 发布者的名称 |
|Azure 图像提供 | Azure VM 产品/服务类型的名称 | 
|Azure 图像库 | Azure VM 映像的 SKU | 
|Azure 图像版本 | Azure VM 映像的版本 | 
|Azure 云服务名称 | Azure 云服务的名称 |
|Azure 云服务部署 | 云服务的部署 ID |
|Azure 云服务角色名称 | 云服务角色名称 |
|Azure 云服务角色类型 | 云服务角色类型：*辅助角色*或*Web* |
|Azure 云服务实例 Id | 云服务角色实例 ID |
|AzureVmScaleSet 名称 | 虚拟机缩放集的名称 |
|AzureVmScaleSet部署 | 虚拟机规模集部署 ID |
|AzureVmScaleSet 资源 Id | 虚拟机缩放集资源的唯一标识符。|
|AzureVmScaleSetinstanceId | 虚拟机规模集的唯一标识符 |
|Azure 服务Fabric集群 ID | Azure 服务结构群集的唯一标识器 | 
|Azure 服务 Fabric 群集名称 | Azure 服务结构群集的名称 |

### <a name="vmprocess-records"></a>VMProcess 记录

具有*VMProcess*类型的记录具有具有依赖项代理的服务器上 TCP 连接进程的清单数据。 这些记录的属性在下表中列出：

| properties | 说明 |
|:--|:--|
|TenantId | 工作区的唯一标识符 |
|SourceSystem | *洞察力* | 
|TimeGenerated | 记录的时间戳 （UTC） |
|Computer | 计算机 FQDN | 
|AgentId | 日志分析代理的唯一 ID |
|计算机 | ServiceMap 公开的计算机的 Azure 资源管理器资源的名称。 它是*m-{GUID}* 窗体，其中*GUID*与 AgentId 相同。 | 
|进程 | 服务映射过程的唯一标识符。 它的形式是*p_GUID}*。 
|可执行名称 | 进程可执行文件的名称 | 
|DisplayName | 进程显示名称 |
|角色 | 过程角色： *WebServer*，*应用程序服务器*，*数据库服务器*， *ldapServer*， *smbServer* |
|组 | 进程组名称。 同一组中的进程在逻辑上相关，例如，同一产品或系统组件的一部分。 |
|StartTime | 进程池启动时间 |
|第一皮 | 进程池中的第一个 PID |
|说明 | 进程说明 |
|CompanyName | 公司名称 |
|InternalName | 内部名称 |
|ProductName | 产品名称 |
|ProductVersion | 产品版本 |
|FileVersion | 文件的版本 |
|可执行路径 |可执行文件的路径 |
|CommandLine | 命令行 |
|WorkingDirectory | 工作目录 |
|Services | 进程执行的服务数组 |
|UserName | 执行进程所用的帐户 |
|UserDomain | 执行进程所在的域 |
|_ResourceId | 工作区中进程的唯一标识符 |


## <a name="sample-map-queries"></a>示例地图查询

### <a name="list-all-known-machines"></a>列出所有已知计算机

```kusto
VMComputer | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="when-was-the-vm-last-rebooted"></a>VM 上次重启的时间

```kusto
let Today = now(); VMComputer | extend DaysSinceBoot = Today - BootTime | summarize by Computer, DaysSinceBoot, BootTime | sort by BootTime asc
```

### <a name="summary-of-azure-vms-by-image-location-and-sku"></a>按映像、位置和 SKU 分类的 Azure VM 摘要

```kusto
VMComputer | where AzureLocation != "" | summarize by Computer, AzureImageOffering, AzureLocation, AzureImageSku
```

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>列出所有托管计算机的物理内存容量

```kusto
VMComputer | summarize arg_max(TimeGenerated, *) by _ResourceId | project PhysicalMemoryMB, Computer
```

### <a name="list-computer-name-dns-ip-and-os"></a>列出计算机名称、DNS、IP 和操作系统

```kusto
VMComputer | summarize arg_max(TimeGenerated, *) by _ResourceId | project Computer, OperatingSystemFullName, DnsNames, Ipv4Addresses
```

### <a name="find-all-processes-with-sql-in-the-command-line"></a>在命令行中查找带有“sql”的所有进程

```kusto
VMProcess | where CommandLine contains_cs "sql" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>按资源名称查找计算机（最新记录）

```kusto
search in (VMComputer) "m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>按 IP 地址查找计算机（最新记录）

```kusto
search in (VMComputer) "10.229.243.232" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="list-all-known-processes-on-a-specified-machine"></a>列出指定计算机上的所有已知进程

```kusto
VMProcess | where Machine == "m-559dbcd8-3130-454d-8d1d-f624e57961bc" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="list-all-computers-running-sql-server"></a>列出所有运行 SQL Server 的计算机

```kusto
VMComputer | where AzureResourceName in ((search in (VMProcess) "*sql*" | distinct Machine)) | distinct Computer
```

### <a name="list-all-unique-product-versions-of-curl-in-my-datacenter"></a>在我的数据中心列出 curl 的所有唯一产品版本

```kusto
VMProcess | where ExecutableName == "curl" | distinct ProductVersion
```

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>创建由运行 CentOS 的所有计算机组成的计算机组

```kusto
VMComputer | where OperatingSystemFullName contains_cs "CentOS" | distinct Computer
```

### <a name="bytes-sent-and-received-trends"></a>发送和收到的字节数趋势

```kusto
VMConnection | summarize sum(BytesSent), sum(BytesReceived) by bin(TimeGenerated,1hr), Computer | order by Computer desc | render timechart
```

### <a name="which-azure-vms-are-transmitting-the-most-bytes"></a>哪些 Azure VM 传输的字节数最多

```kusto
VMConnection | join kind=fullouter(VMComputer) on $left.Computer == $right.Computer | summarize count(BytesSent) by Computer, AzureVMSize | sort by count_BytesSent desc
```

### <a name="link-status-trends"></a>链接状态趋势

```kusto
VMConnection | where TimeGenerated >= ago(24hr) | where Computer == "acme-demo" | summarize dcount(LinksEstablished), dcount(LinksLive), dcount(LinksFailed), dcount(LinksTerminated) by bin(TimeGenerated, 1h) | render timechart
```

### <a name="connection-failures-trend"></a>连接失败趋势

```kusto
VMConnection | where Computer == "acme-demo" | extend bythehour = datetime_part("hour", TimeGenerated) | project bythehour, LinksFailed | summarize failCount = count() by bythehour | sort by bythehour asc | render timechart
```

### <a name="bound-ports"></a>绑定端口

```kusto
VMBoundPort
| where TimeGenerated >= ago(24hr)
| where Computer == 'admdemo-appsvr'
| distinct Port, ProcessName
```

### <a name="number-of-open-ports-across-machines"></a>多个计算机中已打开端口的数目。

```kusto
VMBoundPort
| where Ip != "127.0.0.1"
| summarize by Computer, Machine, Port, Protocol
| summarize OpenPorts=count() by Computer, Machine
| order by OpenPorts desc
```

### <a name="score-processes-in-your-workspace-by-the-number-of-ports-they-have-open"></a>按已打开端口的数目对工作区中的进程评分

```kusto
VMBoundPort
| where Ip != "127.0.0.1"
| summarize by ProcessName, Port, Protocol
| summarize OpenPorts=count() by ProcessName
| order by OpenPorts desc
```

### <a name="aggregate-behavior-for-each-port"></a>聚合每个端口的行为

然后，可以使用此查询按活动对端口评分，例如，入站/出站流量最多的端口、连接数最多的端口。
```kusto
// 
VMBoundPort
| where Ip != "127.0.0.1"
| summarize BytesSent=sum(BytesSent), BytesReceived=sum(BytesReceived), LinksEstablished=sum(LinksEstablished), LinksTerminated=sum(LinksTerminated), arg_max(TimeGenerated, LinksLive) by Machine, Computer, ProcessName, Ip, Port, IsWildcardBind
| project-away TimeGenerated
| order by Machine, Computer, Port, Ip, ProcessName
```

### <a name="summarize-the-outbound-connections-from-a-group-of-machines"></a>汇总一组计算机的出站连接

```kusto
// the machines of interest
let machines = datatable(m: string) ["m-82412a7a-6a32-45a9-a8d6-538354224a25"];
// map of ip to monitored machine in the environment
let ips=materialize(VMComputer
| summarize ips=makeset(todynamic(Ipv4Addresses)) by MonitoredMachine=AzureResourceName
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

## <a name="performance-records"></a>性能记录
具有*InsightsMetrics*类型的记录具有来自虚拟机来宾操作系统的性能数据。 这些记录的属性在下表中列出：


| properties | 说明 |
|:--|:--|
|TenantId | 工作区的唯一标识符 |
|SourceSystem | *洞察力* | 
|TimeGenerated | 收集值的时间 （UTC） |
|Computer | 计算机 FQDN | 
|源 | *vm.azm.ms* |
|命名空间 | 性能计数器的类别 | 
|名称 | 性能计数器的名称 |
|Val | 收集值 | 
|Tags | 有关记录的相关详细信息。 有关与不同记录类型一起使用的标记，请参阅下表。  |
|AgentId | 每台计算机代理的唯一标识符 |
|类型 | *InsightsMetrics* |
|_资源 Id_ | 虚拟机的资源 ID |

当前收集到*的见解指标*表中的性能计数器列在下表中：

| 命名空间 | 名称 | 说明 | 单位 | Tags |
|:---|:---|:---|:---|:---|
| Computer    | 检测信号             | 计算机心跳                        | | |
| 内存      | AvailableMB           | 内存可用字节                    | 字节          | 内存大小MB - 总内存大小|
| 网络     | WriteBytesPerSecond   | 每秒网络写入字节数            | 每秒字节数 | 网络设备 Id - 设备的 ID<br>字节 - 发送的字节总数 |
| 网络     | ReadBytesPerSecond    | 每秒网络读取字节数             | 每秒字节数 | 网络设备 Id - 设备的 ID<br>字节 - 接收的字节总数 |
| 处理器   | 利用率百分比 | 处理器利用率百分比          | 百分比        | 总 CPU - 总 CPU |
| LogicalDisk | WritesPerSecond       | 每秒逻辑磁盘写入            | 每秒计数 | 安装 Id - 设备的安装 ID |
| LogicalDisk | 写入延迟M        | 逻辑磁盘写入延迟毫秒    | 毫秒   | 安装 Id - 设备的安装 ID |
| LogicalDisk | WriteBytesPerSecond   | 逻辑磁盘每秒写入字节数       | 每秒字节数 | 安装 Id - 设备的安装 ID |
| LogicalDisk | TransfersPerSecond    | 每秒逻辑磁盘传输         | 每秒计数 | 安装 Id - 设备的安装 ID |
| LogicalDisk | 传输延迟M     | 逻辑磁盘传输延迟毫秒 | 毫秒   | 安装 Id - 设备的安装 ID |
| LogicalDisk | ReadsPerSecond        | 每秒逻辑磁盘读取             | 每秒计数 | 安装 Id - 设备的安装 ID |
| LogicalDisk | 读取延迟M         | 逻辑磁盘读取延迟毫秒     | 毫秒   | 安装 Id - 设备的安装 ID |
| LogicalDisk | ReadBytesPerSecond    | 逻辑磁盘每秒读取字节数        | 每秒字节数 | 安装 Id - 设备的安装 ID |
| LogicalDisk | 可用空间百分比   | 逻辑磁盘可用空间百分比        | 百分比        | 安装 Id - 设备的安装 ID |
| LogicalDisk | 自由空间MB           | 逻辑磁盘可用空间字节             | 字节          | 安装 Id - 设备的安装 ID<br>磁盘大小 - 总磁盘大小 |
| LogicalDisk | 每秒字节数        | 每秒逻辑磁盘字节数             | 每秒字节数 | 安装 Id - 设备的安装 ID |


## <a name="next-steps"></a>后续步骤

* 如果不了解如何在 Azure Monitor 中编写日志查询，请参阅 Azure 门户中的[如何使用 Log Analytics](../../azure-monitor/log-query/get-started-portal.md) 来编写日志查询。

* 了解有关[编写搜索查询。](../../azure-monitor/log-query/search-queries.md)
