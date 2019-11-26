---
title: Azure 流量分析架构 | Microsoft Docs
description: 了解用于分析 Azure 网络安全组流日志的流量分析的架构。
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2019
ms.author: vinigam
ms.openlocfilehash: a678039b3386c3df290327238d3bf968a803d2c1
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229438"
---
# <a name="schema-and-data-aggregation-in-traffic-analytics"></a>流量分析中的架构和数据聚合

流量分析是一种基于云的解决方案，可用于洞察云网络中的用户和应用程序活动。 流量分析可以分析网络观察程序网络安全组 (NSG) 流日志，帮助洞察 Azure 云中的流量流。 使用流量分析可以：

- 直观查看各个 Azure 订阅中的网络活动，以及识别热点。
- 参考有关开放的端口、尝试访问 Internet 的应用程序以及连接到恶意网络的虚拟机 (VM) 的信息，来识别网络安全威胁和保护网络。
- 了解 Azure 区域与 Internet 之间的流量流模式，优化网络部署以提高性能和容量。
- 查明导致网络连接失败的不当网络配置。
- 了解网络用量（字节、数据包或流）。

### <a name="data-aggregation"></a>数据聚合

1. 位于“FlowIntervalStartTime_t”与“FlowIntervalEndTime_t”之间的 NSG 中的所有流日志将按一分钟间隔捕获为存储帐户中的 Blob，然后由流量分析处理。
2. 流量分析的默认处理间隔为 60 分钟。 即，流量分析每隔 60 分钟从存储中选取要聚合的 Blob。 如果所选的处理间隔为 10 分钟，则流量分析将每隔 10 分钟从存储帐户中选取 Blob。
3. 具有相同源 IP、目标 IP、目标端口、NSG 名称、NSG 规则、流方向和传输层协议（TCP 或 UDP）的流（注意：排除源端口以进行聚合）通过流量分析
4. 此单条记录在经过修饰后（以下部分将提供详细信息）由流量分析引入到 Log Analytics 中。此过程最长可能需要花费 1 小时。
5. FlowStartTime_t 字段指示流日志处理间隔中出现在“FlowIntervalStartTime_t”与“FlowIntervalEndTime_t”之间的第一个此类聚合流（相同的四元组）。
6. 对于 TA 中的任何资源，UI 中指示的流是 NSG 看到的总流数，但在 Log Analytics 中，用户只会看到一条简化的记录。 若要查看所有流，请使用可从存储引用的 blob_id 字段。 该记录的总流数将与 Blob 中出现的各个流相匹配。

下面的查询可帮助您查看过去30天内本地的所有流日志。
```
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FlowStartTime_t >= ago(30d) and FlowType_s == "ExternalPublic"
| project Subnet_s  
```
若要查看上述查询中的流的 Blob 路径，请使用以下查询：

```
let TableWithBlobId =
(AzureNetworkAnalytics_CL
   | where SubType_s == "Topology" and ResourceType == "NetworkSecurityGroup" and DiscoveryRegion_s == Region_s and IsFlowEnabled_b
   | extend binTime = bin(TimeProcessed_t, 6h),
            nsgId = strcat(Subscription_g, "/", Name_s),
            saNameSplit = split(FlowLogStorageAccount_s, "/")
   | extend saName = iif(arraylength(saNameSplit) == 3, saNameSplit[2], '')
   | distinct nsgId, saName, binTime)
| join kind = rightouter (
   AzureNetworkAnalytics_CL
   | where SubType_s == "FlowLog"  
   | extend binTime = bin(FlowEndTime_t, 6h)
) on binTime, $left.nsgId == $right.NSGList_s  
| extend blobTime = format_datetime(todatetime(FlowIntervalStartTime_t), "yyyy MM dd hh")
| extend nsgComponents = split(toupper(NSGList_s), "/"), dateTimeComponents = split(blobTime, " ")
| extend BlobPath = strcat("https://", saName,
                        "@insights-logs-networksecuritygroupflowevent/resoureId=/SUBSCRIPTIONS/", nsgComponents[0],
                        "/RESOURCEGROUPS/", nsgComponents[1],
                        "/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/", nsgComponents[2],
                        "/y=", dateTimeComponents[0], "/m=", dateTimeComponents[1], "/d=", dateTimeComponents[2], "/h=", dateTimeComponents[3],
                        "/m=00/macAddress=", replace(@"-", "", MACAddress_s),
                        "/PT1H.json")
| project-away nsgId, saName, binTime, blobTime, nsgComponents, dateTimeComponents;

TableWithBlobId
| where SubType_s == "FlowLog" and FlowStartTime_t >= ago(30d) and FlowType_s == "ExternalPublic"
| project Subnet_s , BlobPath
```

以上查询构造一个用于直接访问 Blob 的 URL。 下面是包含占位符的 URL：

```
https://{saName}@insights-logs-networksecuritygroupflowevent/resoureId=/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroup}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json

```

### <a name="fields-used-in-traffic-analytics-schema"></a>在流量分析架构中使用的字段
  > [!IMPORTANT]
  > 流量分析架构已在 2019 年 8 月 22 日更新。 新架构单独提供源和目标 IP，无需用户分析 FlowDirection 字段，因此可以简化查询。 </br>
  > FASchemaVersion_s 已从 1 更新为 2。 </br>
  > 弃用的字段： VMIP_s、Subscription_s、Region_s、NSGRules_s、Subnet_s、VM_s、NIC_s、PublicIPs_s、FlowCount_d </br>
  > 新字段： SrcPublicIPs_s、DestPublicIPs_s NSGRule_s </br>
  > 已弃用的字段在 2019 年 11 月 22 日之前仍然可用。

流量分析构建在 Log Analytics 的基础之上，因此你可以针对流量分析修饰的数据运行自定义查询，并针对这些数据设置警报。

下面列出了架构中的字段及其含义

| 字段 | 格式 | 注释 |
|:---   |:---    |:---  |
| TableName | AzureNetworkAnalytics_CL | 流量分析数据表
| SubType_s | FlowLog | 流日志的子类型。 仅使用“FlowLog”，SubType_s 的其他值用于产品的内部工作 |
| FASchemaVersion_s |   2   | 架构版本。 不反映 NSG 流日志版本 |
| TimeProcessed_t   | UTC 日期和时间  | 流量分析处理存储帐户中的原始流日志的时间 |
| FlowIntervalStartTime_t | UTC 日期和时间 |  流日志处理间隔的开始时间。 这是开始计量流间隔的时间 |
| FlowIntervalEndTime_t | UTC 日期和时间 | 流日志处理间隔的结束时间 |
| FlowStartTime_t | UTC 日期和时间 |  流日志处理间隔中出现在“FlowIntervalStartTime_t”与“FlowIntervalEndTime_t”之间的第一个流（将会聚合）。 此流将会基于聚合逻辑进行聚合 |
| FlowEndTime_t | UTC 日期和时间 | 流日志处理间隔中出现在“FlowIntervalStartTime_t”与“FlowIntervalEndTime_t”之间的最后一个流（将会聚合）。 在流日志 v2 中，此字段包含启动具有相同四元组的最后一个流（在原始流记录中标记为“B”）的时间。 |
| FlowType_s |  * IntraVNet <br> * InterVNet <br> * S2S <br> * P2S <br> * AzurePublic <br> * ExternalPublic <br> * MaliciousFlow <br> * Unknown Private <br> * Unknown | 表格下方的注释中提供了定义 |
| SrcIP_s | 源 IP 地址 | 使用 AzurePublic 和 ExternalPublic 流时是空白的 |
| DestIP_s | 目标 IP 地址 | 使用 AzurePublic 和 ExternalPublic 流时是空白的 |
| VMIP_s | VM 的 IP | 用于 AzurePublic 和 ExternalPublic 流 |
| PublicIP_s | 公共 IP 地址 | 用于 AzurePublic 和 ExternalPublic 流 |
| DestPort_d | Destination Port | 传入流量的端口 |
| L4Protocol_s  | * T <br> * U  | 传输协议。 T = TCP <br> U = UDP |
| L7Protocol_s  | 协议名称 | 派生自目标端口 |
| FlowDirection_s | * I = 出站<br> * O = 出站 | 根据流日志流入/流出 NSG 的方向 |
| FlowStatus_s  | * A = 由 NSG 规则允许 <br> * D = 由 NSG 规则拒绝  | 根据流日志由 NSG 允许/阻止的流的状态 |
| NSGList_s | \<订阅 ID>\/<资源组名称>\/<NSG 名称> | 与流关联的网络安全组 (NSG) |
| NSGRules_s | \<索引值 0)>\|\<NSG 规则名称>\|\<流方向>\|\<流状态>\|\<规则处理的流数> |  允许或拒绝此流的 NSG 规则 |
| NSGRule_s | NSG_RULENAME |  允许或拒绝此流的 NSG 规则 |
| NSGRuleType_s | * 用户定义 * 默认值 |   流使用的 NSG 规则类型 |
| MACAddress_s | MAC 地址 | 捕获流的 NIC 的 MAC 地址 |
| Subscription_s | 此字段中填充了 Azure 虚拟网络/网络接口/虚拟机的订阅 | 仅适用于 FlowType = S2S、P2S、AzurePublic、ExternalPublic、MaliciousFlow 和 UnknownPrivate 流类型（只有一端的流类型是 Azure） |
| Subscription1_s | 订阅 ID | 流中的源 IP 所属的虚拟网络/网络接口/虚拟机的订阅 ID |
| Subscription2_s | 订阅 ID | 流中的目标 IP 所属的虚拟网络/网络接口/虚拟机的订阅 ID |
| Region_s | 流中的 IP 所属的虚拟网络/网络接口/虚拟机的 Azure 区域 | 仅适用于 FlowType = S2S、P2S、AzurePublic、ExternalPublic、MaliciousFlow 和 UnknownPrivate 流类型（只有一端的流类型是 Azure） |
| Region1_s | Azure 区域 | 流中的源 IP 所属的虚拟网络/网络接口/虚拟机的 Azure 区域 |
| Region2_s | Azure 区域 | 流中的目标 IP 所属的虚拟网络的 Azure 区域 |
| NIC_s | \<资源组名称>\/\<网络接口名称> |  与发送或接收流量的 VM 关联的 NIC |
| NIC1_s | <资源组名称>/\<网络接口名称> | 与流中的源 IP 关联的 NIC |
| NIC2_s | <资源组名称>/\<网络接口名称> | 与流中的目标 IP 关联的 NIC |
| VM_s | <资源组名称>\/\<网络接口名称> | 与网络接口 NIC_s 关联的虚拟机 |
| VM1_s | <资源组名称>/\<虚拟机名称> | 与流中的源 IP 关联的虚拟机 |
| VM2_s | <资源组名称>/\<虚拟机名称> | 与流中的目标 IP 关联的虚拟机 |
| Subnet_s | <资源组名称>/<VNET 名称>/\<子网名称> | 与 NIC_s 关联的子网 |
| Subnet1_s | <资源组名称>/<VNET 名称>/\<子网名称> | 与流中的源 IP 关联的子网 |
| Subnet2_s | <资源组名称>/<VNET 名称>/\<子网名称>    | 与流中的目标 IP 关联的子网 |
| ApplicationGateway1_s | \<订阅 ID>/\<资源组名称>/\<应用程序网关名称> | 与流中的源 IP 关联的应用程序网关 |
| ApplicationGateway2_s | \<订阅 ID>/\<资源组名称>/\<应用程序网关名称> | 与流中的目标 IP 关联的应用程序网关 |
| LoadBalancer1_s | \<订阅 ID>/\<资源组名称>/\<负载均衡器名称> | 与流中的源 IP 关联的负载均衡器 |
| LoadBalancer2_s | \<订阅 ID>/\<资源组名称>/\<负载均衡器名称> | 与流中的目标 IP 关联的负载均衡器 |
| LocalNetworkGateway1_s | \<订阅 ID>/\<资源组名称>/\<本地网络网关名称> | 与流中的源 IP 关联的本地网络网关 |
| LocalNetworkGateway2_s | \<订阅 ID>/\<资源组名称>/\<本地网络网关名称> | 与流中的目标 IP 关联的本地网络网关 |
| ConnectionType_s | 可能的值为 VNetPeering、VpnGateway 和 ExpressRoute |    连接类型 |
| ConnectionName_s | \<订阅 ID>/\<资源组名称>/\<连接名称> | 连接名称 |
| ConnectingVNets_s | 虚拟网络名称的空格分隔列表 | 对于中心辐射型拓扑，此处将会填充中心虚拟网络 |
| Country_s | 双字母国家/地区代码 (ISO 3166-1 alpha-2) | 为流类型 ExternalPublic 填充此字段。 PublicIPs_s 字段中的所有 IP 地址将共享同一个国家/地区代码 |
| AzureRegion_s | Azure 区域位置 | 为流类型 AzurePublic 填充此字段。 PublicIPs_s 字段中的所有 IP 地址将共享该 Azure 区域 |
| AllowedInFlows_d | | 允许的入站流数。 这表示共享入站到捕获流的网络接口的同一个四元组的流数 |
| DeniedInFlows_d |  | 拒绝的入站流数。 （入站到捕获流的网络接口） |
| AllowedOutFlows_d | | 允许的出站流数（出站到捕获流的网络接口） |
| DeniedOutFlows_d  | | 拒绝的出站流数（出站到捕获流的网络接口） |
| FlowCount_d | 已弃用。 匹配同一个四元组的总流数。 如果流类型为 ExternalPublic 和 AzurePublic，则计数还包括来自各个 PublicIP 地址的流。
| InboundPackets_d | 在应用 NSG 规则的网络接口上捕获的已接收数据包数 | 仅为 NSG 流日志架构版本 2 填充此字段 |
| OutboundPackets_d  | 在应用 NSG 规则的网络接口上捕获的已发送数据包数 | 仅为 NSG 流日志架构版本 2 填充此字段 |
| InboundBytes_d |  在应用 NSG 规则的网络接口上捕获的已接收字节数 | 仅为 NSG 流日志架构版本 2 填充此字段 |
| OutboundBytes_d | 在应用 NSG 规则的网络接口上捕获的已发送字节数 | 仅为 NSG 流日志架构版本 2 填充此字段 |
| CompletedFlows_d  |  | 仅为 NSG 流日志架构版本 2 在此字段中填充非零值 |
| PublicIPs_s | <公共 IP>\|\<启动的流计数>\|\<结束的流计数>\|\<出站数据包数>\|\<入站数据包数>\|\<出站字节数>\|\<入站字节数> | 条形分隔的条目 |
| SrcPublicIPs_s | <源公共 IP>\|\<启动的流计数>\|\<结束的流计数>\|\<出站数据包数>\|\<入站数据包数>\|\<出站字节数>\|\<入站字节数> | 条形分隔的条目 |
| DestPublicIPs_s | <目标公共 IP>\|\<启动的流计数>\|\<结束的流计数>\|\<出站数据包数>\|\<入站数据包数>\|\<出站字节数>\|\<入站字节数> | 条形分隔的条目 |

### <a name="notes"></a>说明

1. 对于 AzurePublic 和 ExternalPublic 流，客户拥有的 Azure VM IP 将填充在 VMIP_s 字段中，而公共 IP 地址将填充在 PublicIPs_s 字段中。 对于这两种流类型，我们应使用 VMIP_s 和 PublicIPs_s 字段，而不是 SrcIP_s 和 DestIP_s 字段。 对于 AzurePublic 和 ExternalPublicIP 地址，我们将进一步聚合，以尽量减少引入到客户 Log Analytics 工作区的记录数。（此字段即将弃用，我们应该根据 Azure VM 是流中的源还是目标，使用 SrcIP_ 或 DestIP_s）
1. 流类型的详细信息：根据流中涉及的 IP 地址将流分类为以下流类型：
1. IntraVNet - 流中的两个 IP 地址位于同一个 Azure 虚拟网络中。
1. InterVNet - 流中的 IP 地址位于两个不同的 Azure 虚拟网络中。
1. S2S（站点到站点）- 一个 IP 地址属于 Azure 虚拟网络，而另一个 IP 地址属于通过 VPN 网关或 Express Route 连接到 Azure 虚拟网络的客户网络（站点）。
1. P2S（点到站点）- 一个 IP 地址属于 Azure 虚拟网络，而另一个 IP 地址属于通过 VPN 网关连接到 Azure 虚拟网络的客户网络（站点）。
1. AzurePublic - 一个 IP 地址属于 Azure 虚拟网络，而另一个 IP 地址属于 Microsoft 拥有的 Azure 内部公共 IP 地址。 客户拥有的公共 IP 地址不属于此流类型。 例如，客户拥有的、将流量发送到 Azure 服务（存储终结点）的任何 VM 将划分到此流类型。
1. ExternalPublic - 一个 IP 地址属于 Azure 虚拟网络，而另一个 IP 地址是不在 Azure 中的公共 IP 地址，流量分析在处理间隔期间在“FlowIntervalStartTime_t”与“FlowIntervalEndTime_t”之间使用的 ASC 源中不会将此地址报告为恶意地址。
1. MaliciousFlow - 一个 IP 地址属于 Azure 虚拟网络，而另一个 IP 地址是不在 Azure 中的公共 IP 地址，流量分析在处理间隔期间在“FlowIntervalStartTime_t”与“FlowIntervalEndTime_t”之间使用的 ASC 源中会将此地址报告为恶意地址。
1. UnknownPrivate - 一个 IP 地址属于 Azure 虚拟网络，而另一个 IP 地址属于 RFC 1918 中定义的专用 IP 范围，无法由流量分析映射到客户拥有的站点或 Azure 虚拟网络。
1. Unknown - 无法将流中的任一 IP 地址映射到 Azure 中的客户拓扑以及本地（站点）。
1. 某些字段名称的后面追加了 \_s 或 \_d。 这些后缀并不表示源 (source) 和目标 (destination)，而是表示数据类型字符串 (string) 和十进制 (decimal)。

### <a name="next-steps"></a>后续步骤
若要获取常见问题的解答，请参阅[流量分析常见问题解答](traffic-analytics-faq.md)。若要查看有关功能的详细信息，请参阅[流量分析文档](traffic-analytics.md)
