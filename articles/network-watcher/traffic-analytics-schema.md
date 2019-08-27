---
title: Azure 流量分析架构 |Microsoft Docs
description: 了解流量分析的架构以分析 Azure 网络安全组流日志。
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
ms.openlocfilehash: bd83d915b51ab44d4287987e3da7113722910262
ms.sourcegitcommit: 80dff35a6ded18fa15bba633bf5b768aa2284fa8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2019
ms.locfileid: "70020240"
---
# <a name="schema-and-data-aggregation-in-traffic-analytics"></a>流量分析中的架构和数据聚合

流量分析是一种基于云的解决方案，可用于洞察云网络中的用户和应用程序活动。 流量分析可以分析网络观察程序网络安全组 (NSG) 流日志，帮助洞察 Azure 云中的流量流。 使用流量分析可以：

- 直观查看各个 Azure 订阅中的网络活动，以及识别热点。
- 参考有关开放的端口、尝试访问 Internet 的应用程序以及连接到恶意网络的虚拟机 (VM) 的信息，来识别网络安全威胁和保护网络。
- 了解 Azure 区域与 Internet 之间的流量流模式，优化网络部署以提高性能和容量。
- 查明导致网络连接失败的不当网络配置。
- 了解网络使用情况 (字节、数据包或流)。

### <a name="data-aggregation"></a>数据聚合

1. 位于 "FlowIntervalStartTime_t" 与 "FlowIntervalEndTime_t" 之间的 NSG 之间的所有流日志在存储帐户中以一分钟的时间间隔捕获为 blob, 然后流量分析处理。
2. 流量分析的默认处理间隔为60分钟。 这意味着每60分钟流量分析从存储中选取用于聚合的 blob。 如果选择的处理间隔是10分钟, 流量分析将在每10分钟后从存储帐户中选取 blob。
3. 具有相同源 IP、目标 IP、目标端口、NSG 名称、NSG 规则、流方向和传输层协议 (TCP 或 UDP) 的流 (注意:排除源端口以进行聚合, 流量分析
4. 此单个记录经过修饰 (以下部分中的详细信息), 流量分析 Log Analytics 中的引入。此过程可能需要最多1小时。
5. FlowStartTime_t 字段指示在 "FlowIntervalStartTime_t" 和 "FlowIntervalEndTime_t" 之间的流日志处理间隔内第一次出现此类聚合流 (相同的四个元组)。
6. 对于 TA 中的任何资源, UI 中指示的流是 NSG 看到的总流, 但在 Log Analytics 用户中, 只会看到一条精简记录。 若要查看所有流, 请使用 blob_id 字段, 该字段可从存储中引用。 该记录的总流计数将与 blob 中出现的单个流匹配。

下面的查询可帮助您查看过去30天内来自本地的所有流日志。
```
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FlowStartTime_t >= ago(30d) and FlowType_s == "ExternalPublic"
| project Subnet_s  
```
若要查看上述查询中的流的 blob 路径, 请使用以下查询:

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

上面的查询将构造用于直接访问 blob 的 URL。 带有占位符的 URL 如下所示:

```
https://{saName}@insights-logs-networksecuritygroupflowevent/resoureId=/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroup}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json

```

### <a name="fields-used-in-traffic-analytics-schema"></a>流量分析架构中使用的字段
  > [!IMPORTANT]
  > 流量分析架构已于2019年8月更新 ()。 新架构提供源和目标 Ip 分别删除需要分析 System.windows.flowdirection> 字段, 使查询更简单。 </br>
  > FASchemaVersion_s 更新了从1到2的。 </br>
  > 弃用的字段:VMIP_s, Subscription_s, Region_s, NSGRules_s, Subnet_s, VM_s, NIC_s, PublicIPs_s, FlowCount_d </br>
  > 新字段:SrcPublicIPs_s, DestPublicIPs_s, NSGRule_s </br>
  > 2019年11月之前, 将提供已弃用的字段22日。

流量分析在 Log Analytics 之上构建而成, 因此可以对通过流量分析修饰的数据运行自定义查询, 并在同一上设置警报。

下面列出了架构中的字段及其含义

| 字段 | 格式 | 注释 |
|:---   |:---    |:---  |
| TableName | AzureNetworkAnalytics_CL | 用于流量分析数据的表
| SubType_s | FlowLog | 流日志的子类型。 仅使用 "FlowLog", SubType_s 的其他值用于产品的内部工作 |
| FASchemaVersion_s |   2   | 架构版本。 不反映 NSG 流日志版本 |
| TimeProcessed_t   | UTC 格式的日期和时间  | 流量分析处理来自存储帐户的原始流日志的时间 |
| FlowIntervalStartTime_t | UTC 格式的日期和时间 |  流日志处理间隔的开始时间。 这是从其测量流间隔的时间 |
| FlowIntervalEndTime_t | UTC 格式的日期和时间 | 流日志处理间隔的结束时间 |
| FlowStartTime_t | UTC 格式的日期和时间 |  流的第一个匹配项 (将在 "FlowIntervalStartTime_t" 和 "FlowIntervalEndTime_t" 之间的流日志处理间隔内进行聚合)。 此流基于聚合逻辑进行聚合 |
| FlowEndTime_t | UTC 格式的日期和时间 | 流日志处理间隔 "FlowIntervalStartTime_t" 和 "FlowIntervalEndTime_t" 之间的最后一个匹配项 (将被聚合)。 就流日志 v2 而言, 此字段包含开始具有相同的四元组的最后一个流 (在原始流记录中标记为 "B") 的时间。 |
| FlowType_s |  * IntraVNet <br> * InterVNet <br> * S2S <br> * P2S <br> * AzurePublic <br> * ExternalPublic <br> * MaliciousFlow <br> * 未知专用 <br> * 未知 | 表下方说明中的定义 |
| SrcIP_s | 源 IP 地址 | 对于 AzurePublic 和 ExternalPublic 流, 将为空 |
| DestIP_s | 目标 IP 地址 | 对于 AzurePublic 和 ExternalPublic 流, 将为空 |
| VMIP_s | VM 的 IP | 用于 AzurePublic 和 ExternalPublic 流 |
| PublicIP_s | 公共 IP 地址 | 用于 AzurePublic 和 ExternalPublic 流 |
| DestPort_d | 目标端口 | 传入流量的端口 |
| L4Protocol_s  | * T <br> * U  | 传输协议。 T = TCP <br> U = UDP |
| L7Protocol_s  | 协议名称 | 派生自目标端口 |
| FlowDirection_s | * I = 入站<br> * O = 出站 | 流入/流出 NSG 的流向, 按 flow 日志 |
| FlowStatus_s  | * A = NSG 规则允许 <br> * D = 被 NSG 规则拒绝  | 按流日志的 NSG 允许/nblocked 的 flow 状态 |
| NSGList_s | \<SUBSCRIPTIONID>\/<RESOURCEGROUP_NAME>\/<NSG_NAME> | 与流关联的网络安全组 (NSG) |
| NSGRules_s | \<索引值 0) >\|\<NSG_RULENAME >\|\<流方向 >\|流状态\|>\<FlowCountProcessedByRule>\< |  允许或拒绝此流的 NSG 规则 |
| NSGRule_s | NSG_RULENAME |  允许或拒绝此流的 NSG 规则 |
| NSGRuleType_s | * 用户定义 * 默认值 |   流使用的 NSG 规则的类型 |
| MACAddress_s | MAC 地址 | 捕获流的 NIC 的 MAC 地址 |
| Subscription_s | 此字段中填充了 Azure 虚拟网络/网络接口/虚拟机的订阅 | 仅适用于 FlowType = S2S、P2S、AzurePublic、ExternalPublic、MaliciousFlow 和 UnknownPrivate 流类型 (其中只有一方是 azure 的流类型) |
| Subscription1_s | 订阅 ID | 流中的源 IP 所属的虚拟网络/网络接口/虚拟机的订阅 ID |
| Subscription2_s | 订阅 ID | 流中的目标 IP 所属的虚拟网络/网络接口/虚拟机的订阅 ID |
| Region_s | 流中的 IP 所属的虚拟网络/网络接口/虚拟机的 Azure 区域 | 仅适用于 FlowType = S2S、P2S、AzurePublic、ExternalPublic、MaliciousFlow 和 UnknownPrivate 流类型 (其中只有一方是 azure 的流类型) |
| Region1_s | Azure 区域 | 流中的源 IP 所属的虚拟网络/网络接口/虚拟机的 Azure 区域 |
| Region2_s | Azure 区域 | 流中目标 IP 所属的虚拟网络的 Azure 区域 |
| NIC_s | \<resourcegroup_Name>\/\<NetworkInterfaceName> |  与发送或接收流量的 VM 关联的 NIC |
| NIC1_s | <resourcegroup_Name>/\<NetworkInterfaceName> | 与流中的源 IP 关联的 NIC |
| NIC2_s | <resourcegroup_Name>/\<NetworkInterfaceName> | 与流中的目标 IP 关联的 NIC |
| VM_s | <resourcegroup_Name>\/\<NetworkInterfaceName> | 与网络接口 NIC_s 关联的虚拟机 |
| VM1_s | <resourcegroup_Name>/\<VirtualMachineName> | 与流中的源 IP 关联的虚拟机 |
| VM2_s | <resourcegroup_Name>/\<VirtualMachineName> | 与流中的目标 IP 关联的虚拟机 |
| Subnet_s | <ResourceGroup_Name>/<VNET_Name>/\<SubnetName> | 与 NIC_s 关联的子网 |
| Subnet1_s | <ResourceGroup_Name>/<VNET_Name>/\<SubnetName> | 与流中的源 IP 关联的子网 |
| Subnet2_s | <ResourceGroup_Name>/<VNET_Name>/\<SubnetName>    | 与流中的目标 IP 关联的子网 |
| ApplicationGateway1_s | \<SubscriptionID>/\<ResourceGroupName>/\<ApplicationGatewayName> | 与流中的源 IP 关联的应用程序网关 |
| ApplicationGateway2_s | \<SubscriptionID>/\<ResourceGroupName>/\<ApplicationGatewayName> | 与流中的目标 IP 关联的应用程序网关 |
| LoadBalancer1_s | \<SubscriptionID>/\<ResourceGroupName>/\<LoadBalancerName> | 与流中的源 IP 关联的负载均衡器 |
| LoadBalancer2_s | \<SubscriptionID>/\<ResourceGroupName>/\<LoadBalancerName> | 与流中的目标 IP 关联的负载均衡器 |
| LocalNetworkGateway1_s | \<SubscriptionID>/\<ResourceGroupName>/\<LocalNetworkGatewayName> | 与流中的源 IP 关联的本地网关 |
| LocalNetworkGateway2_s | \<SubscriptionID>/\<ResourceGroupName>/\<LocalNetworkGatewayName> | 与流中的目标 IP 关联的本地网关 |
| ConnectionType_s | 可能的值包括 VNetPeering、VpnGateway 和 ExpressRoute |    连接类型 |
| ConnectionName_s | \<SubscriptionID>/\<ResourceGroupName>/\<ConnectionName> | 连接名称 |
| ConnectingVNets_s | 虚拟网络名称的空格分隔列表 | 对于中心和辐射拓扑, 将在此处填充中心虚拟网络 |
| Country_s | 双字母国家/地区代码 (ISO 3166-1 alpha-2) | 为流类型 ExternalPublic 填充。 PublicIPs_s 字段中的所有 IP 地址都将共享相同的国家/地区代码 |
| AzureRegion_s | Azure 区域位置 | 为流类型 AzurePublic 填充。 PublicIPs_s 字段中的所有 IP 地址都将共享 Azure 区域 |
| AllowedInFlows_d | | 允许的入站流的计数。 这表示在捕获流的网络接口上共享同一个四元组的流的数目 |
| DeniedInFlows_d |  | 已拒绝的入站流的计数。 (入站到捕获流的网络接口) |
| AllowedOutFlows_d | | 允许的出站流的计数 (到捕获流的网络接口的出站流量) |
| DeniedOutFlows_d  | | 已拒绝的出站流计数 (出站到捕获流的网络接口) |
| FlowCount_d | 已弃用。 匹配相同的四个元组的流总数。 如果 flow 类型为 ExternalPublic 和 AzurePublic, 则 count 还将包含来自不同 PublicIP 地址的流。
| InboundPackets_d | 在应用 NSG 规则的网络接口上按捕获接收的数据包 | 仅为 NSG 流日志架构版本2填充此版本 |
| OutboundPackets_d  | 在应用 NSG 规则的网络接口上以捕获方式发送的数据包 | 仅为 NSG 流日志架构版本2填充此版本 |
| InboundBytes_d |  在应用 NSG 规则的网络接口上按捕获接收的字节数 | 仅为 NSG 流日志架构版本2填充此版本 |
| OutboundBytes_d | 在应用 NSG 规则的网络接口上按捕获发送的字节数 | 仅为 NSG 流日志架构版本2填充此版本 |
| CompletedFlows_d  |  | 仅为 NSG 流日志架构版本2的非零值填充此值 |
| PublicIPs_s | < PUBLIC_IP >\|\<FLOW_STARTED_COUNT >\|FLOW_ENDED_COUNT>\|OUTBOUND_PACKETS>\<INBOUND_PACKETS >\|\<\<\|\<OUTBOUND_BYTES>\|INBOUND_BYTES>\< | 按栏分隔的条目 |
| SrcPublicIPs_s | < SOURCE_PUBLIC_IP >\|\<FLOW_STARTED_COUNT >\|FLOW_ENDED_COUNT>\|OUTBOUND_PACKETS>\<INBOUND_PACKETS\|\<\<>\|OUTBOUND_BYTES\<>\|INBOUND_BYTES>\< | 按栏分隔的条目 |
| DestPublicIPs_s | < DESTINATION_PUBLIC_IP >\|\<FLOW_STARTED_COUNT >\|FLOW_ENDED_COUNT>\|OUTBOUND_PACKETS>\<INBOUND_\|\<\<数据包 >\|\<OUTBOUND_BYTES >\|INBOUND_BYTES\<> | 按栏分隔的条目 |

### <a name="notes"></a>说明

1. 对于 AzurePublic 和 ExternalPublic 流, 客户拥有的 Azure VM IP 在 VMIP_s 字段中填充, 而公共 IP 地址在 PublicIPs_s 字段中填充。 对于这两种流类型, 应使用 VMIP_s 和 PublicIPs_s, 而不是 SrcIP_s 和 DestIP_s 字段。 对于 AzurePublic 和 ExternalPublicIP 地址, 我们将进一步聚合, 以便将引入的记录数量降至客户 log analytics 工作区。(此字段即将弃用, 应根据 azure VM 是流中的源还是目标, 使用 SrcIP_ 和 DestIP_s)
1. 流类型的详细信息:根据流中涉及的 IP 地址, 我们将流分类为以下流类型:
1. IntraVNet-流中的两个 IP 地址都位于同一个 Azure 虚拟网络中。
1. InterVNet-流中的 IP 地址驻留在两个不同的 Azure 虚拟网络中。
1. S2S – (站点到站点) 其中一个 IP 地址属于 Azure 虚拟网络, 而另一个 IP 地址属于通过 VPN 网关或 Express Route 连接到 Azure 虚拟网络的客户网络 (站点)。
1. P2S-(点到站点) 其中一个 IP 地址属于 Azure 虚拟网络, 而另一个 IP 地址属于通过 VPN 网关连接到 Azure 虚拟网络的客户网络 (站点)。
1. AzurePublic-其中一个 IP 地址属于 Azure 虚拟网络, 而另一个 IP 地址属于 Microsoft 拥有的 Azure 内部公共 IP 地址。 客户拥有的公共 IP 地址不属于此流类型。 例如, 将流量发送到 Azure 服务 (存储终结点) 的任何客户拥有的 VM 都将归入此流类型。
1. ExternalPublic-其中一个 IP 地址属于 Azure 虚拟网络, 而另一个 IP 地址是不在 Azure 中的公共 IP 地址, 因此, 在流量分析使用的FlowIntervalStartTime_t "和" FlowIntervalEndTime_t "。
1. MaliciousFlow-其中一个 IP 地址属于 azure 虚拟网络, 而另一个 IP 地址是不在 Azure 中的公共 IP 地址, 并且在 ASC 源中被报告为恶意, 流量分析将在 "FlowIntervalStartTime_t "和" FlowIntervalEndTime_t "。
1. UnknownPrivate-其中一个 IP 地址属于 Azure 虚拟网络, 而另一个 IP 地址属于 RFC 1918 中定义的专用 IP 范围, 无法通过流量分析映射到客户拥有的站点或 Azure 虚拟网络。
1. 未知–无法将流中的 IP 地址与 Azure 中的客户拓扑以及本地 (站点) 进行映射。
1. 某些字段名称附加了\_s 或\_d。 这并不表示源和目标, 但分别表示数据类型字符串和十进制。

### <a name="next-steps"></a>后续步骤
若要获取常见问题的解答, 请参阅[流量分析常见问题解答](traffic-analytics-faq.md)若要查看有关功能的详细信息, 请参阅[流量分析文档](traffic-analytics.md)
