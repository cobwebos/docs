---
title: Azure 流量分析架构 |Microsoft Docs
description: 了解流量分析来分析 Azure 网络安全组流日志的架构。
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
ms.openlocfilehash: 922e01c26a2cfe24c8b8a32bb8037d9b3b3384c6
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "58109114"
---
# <a name="schema-and-data-aggregation-in-traffic-analytics"></a>流量分析中的架构和数据聚合

流量分析是一种基于云的解决方案，可用于洞察云网络中的用户和应用程序活动。 流量分析可以分析网络观察程序网络安全组 (NSG) 流日志，帮助洞察 Azure 云中的流量流。 使用流量分析可以：

- 直观查看各个 Azure 订阅中的网络活动，以及识别热点。
- 参考有关开放的端口、尝试访问 Internet 的应用程序以及连接到恶意网络的虚拟机 (VM) 的信息，来识别网络安全威胁和保护网络。
- 了解 Azure 区域与 Internet 之间的流量流模式，优化网络部署以提高性能和容量。
- 查明导致网络连接失败的不当网络配置。
- 知道字节、 数据包或流中的网络使用情况。

### <a name="data-aggregation"></a>数据聚合

1. 在"FlowIntervalStartTime_t"和"FlowIntervalEndTime_t"之间的 NSG 的所有流日志都捕获每隔一分钟的存储帐户中作为 blob 处理的流量分析之前。 
2. 流量分析的默认处理时间间隔为 60 分钟。 这意味着，流量分析会从用于聚合的存储 blob 选取每隔 60 分钟。
3. 流，将同一源 IP、 目标 IP、 目标端口、 NSG 名称、 NSG 规则、 流方向和传输层协议 （TCP 或 UDP） (注意：源端口排除的聚合） 都组合到单个流使用流量分析
4. 此单个记录的修饰名 （在下面的部分中的详细信息） 和流量分析由 Log Analytics 数据引入量。
5. FlowStartTime_t 字段中的流日志处理间隔"FlowIntervalStartTime_t"和"FlowIntervalEndTime_t"指示此类的聚合流 （相同四元组） 的第一个匹配项。 
6. TA 中任何资源，指示在 UI 中的流的流总计看到的 NSG，但在日志 Anlaytics 用户将看到只有单个，约记录。 若要查看所有流，请使用 blob_id 字段中，可以从存储中引用。 总流计数的记录将匹配在 blob 中看到的各个流。


### <a name="fields-used-in-traffic-analytics-schema"></a>流量分析架构中使用的字段

流量分析是基于 Log Analytics，以便您可以通过流量分析修饰和设置警报，在同一个数据运行自定义查询。

下面列出了中的架构和它们所表示的字段

| 字段 | 格式 | 注释 | 
|:---   |:---    |:---  |
| TableName | AzureNetworkAnalytics_CL | 流量 Anlaytics 数据的表
| SubType_s | FlowLog | 流日志的子类型 |
| FASchemaVersion_s |   第   | 列名版本。 不会反映 NSG 流日志版本 |
| TimeProcessed_t   | 日期和时间 UTC  | 流量分析处理从存储帐户的原始流日志时间 |
| FlowIntervalStartTime_t | 日期和时间 UTC |  流日志处理间隔的开始时间。 这是从其测量流间隔时间 |
| FlowIntervalEndTime_t | 日期和时间 UTC | 流日志处理间隔的结束时间 |
| FlowStartTime_t | 日期和时间 UTC |  流 （这将获取聚合） 的"FlowIntervalStartTime_t"和"FlowIntervalEndTime_t"之间的流日志处理间隔中的第一次。 此流获取聚合基于聚合逻辑 |
| FlowEndTime_t | 日期和时间 UTC | 最后一次的流 （这将获取聚合） 在"FlowIntervalStartTime_t"和"FlowIntervalEndTime_t"之间的流日志处理间隔。 在流日志 v2 方面此字段包含相同的四个元组的最后一个流开始 （标记为"B"中的原始流记录） 时的时间 |
| FlowType_s |  * IntraVNet <br> * InterVNet <br> * S2S <br> * P2S <br> * AzurePublic <br> * ExternalPublic <br> * MaliciousFlow <br> * 未知私有 <br> * 未知 | 下表说明中定义 |
| SrcIP_s | 源 IP 地址 | 将为空时 AzurePublic 以及 ExternalPublic 流 |
| DestIP_s | 目标 IP 地址 | 将为空时 AzurePublic 以及 ExternalPublic 流 |
| VMIP_s | VM 的 IP | 用于 AzurePublic 和 ExternalPublic 流 |
| PublicIP_S | 公共 IP 地址 | 用于 AzurePublic 和 ExternalPublic 流 |
| DestPort_d | Destination Port | 流量是传入的端口 | 
| L4Protocol_s  | * T <br> * U  | 传输协议。 T = TCP <br> U = UDP | 
| L7Protocol_s  | 协议名称 | 派生自目标端口 |
| FlowDirection_s | * 我 = 入站<br> * O = 出站 | 流日志根据/超出 NSG 中流的方向 | 
| FlowStatus_s  | * NSG 规则允许 a = <br> * D = 拒绝的 NSG 规则  | 流允许/nblocked 通过 NSG 流日志根据状态 |
| NSGList_s | \<SUBSCRIPTIONID>\/<RESOURCEGROUP_NAME>\/<NSG_NAME> | 与流关联的网络安全组 (NSG) |
| NSGRules_s | \<索引值为 0） >< NSG_RULENAME >\<数据流方向 >\<流状态 >\<FlowCount ProcessedByRule > |  NSG 规则，允许或拒绝此流 |
| NSGRuleType_s | * 用户定义 * 默认值 |   使用流的 NSG 规则的类型 |
| MACAddress_s | MAC 地址 | 在其中捕获流的 NIC 的 MAC 地址 |
| Subscription_s | 订阅的 Azure 虚拟网络 / 网络接口 / 在此字段填充虚拟机 | 仅适用于 FlowType = S2S、 P2S、 AzurePublic、 ExternalPublic、 MaliciousFlow 和 UnknownPrivate 流类型 （其中只有一方是 azure 的流类型） |
| Subscription1_s | 订阅 ID | 订阅 ID 的虚拟网络 / 网络接口 / 在流中的源 IP 所属的虚拟机 |
| Subscription2_s | 订阅 ID | 订阅 ID 的虚拟网络 / 网络接口 / 目标 IP 的流中所属的虚拟机 |
| Region_s | Azure 区域的虚拟网络 / 网络接口 / 在流中的 IP 所属的虚拟机 | 仅适用于 FlowType = S2S、 P2S、 AzurePublic、 ExternalPublic、 MaliciousFlow 和 UnknownPrivate 流类型 （其中只有一方是 azure 的流类型） |
| Region1_s | Azure 区域 | Azure 区域的虚拟网络 / 网络接口 / 在流中的源 IP 所属的虚拟机 |
| Region2_s | Azure 区域 | 在流中的目标 IP 所属的虚拟网络的 azure 区域 |
| NIC_s | \<resourcegroup_Name>\/\<NetworkInterfaceName> |  与发送或接收流量的 VM 关联的 NIC |
| NIC1_s | <resourcegroup_Name>/\<NetworkInterfaceName> | 与在流中的源 IP 关联的 NIC |
| NIC2_s | <resourcegroup_Name>/\<NetworkInterfaceName> | 在流中的目标 IP 与关联的 NIC |
| VM_s | <resourcegroup_Name>\/\<NetworkInterfaceName> | 与网络接口 NIC_s 关联的虚拟机 |
| VM1_s | <resourcegroup_Name>/\<VirtualMachineName> | 与在流中的源 IP 关联的虚拟机 |
| VM2_s | <resourcegroup_Name>/\<VirtualMachineName> | 在流中的目标 IP 与关联的虚拟机 |
| Subnet_s | <ResourceGroup_Name>/<VNET_Name>/\<SubnetName> | 与 NIC_s 相关联的子网 |
| Subnet1_s | <ResourceGroup_Name>/<VNET_Name>/\<SubnetName> | 在流中的源 ip 关联的子网 |
| Subnet2_s | <ResourceGroup_Name>/<VNET_Name>/\<SubnetName>    | 与目标 IP 的流中关联的子网 |
| ApplicationGateway1_s | \<SubscriptionID>/\<ResourceGroupName>/\<ApplicationGatewayName> | 在流中的源 ip 关联的应用程序网关 | 
| ApplicationGateway2_s | \<SubscriptionID>/\<ResourceGroupName>/\<ApplicationGatewayName> | 与目标 IP 的流中关联的应用程序网关 |
| LoadBalancer1_s | \<SubscriptionID>/\<ResourceGroupName>/\<LoadBalancerName> | 在流中的源 ip 关联的负载均衡器 |
| LoadBalancer2_s | \<SubscriptionID>/\<ResourceGroupName>/\<LoadBalancerName> | 与目标 IP 的流中关联的负载均衡器 |
| LocalNetworkGateway1_s | \<SubscriptionID>/\<ResourceGroupName>/\<LocalNetworkGatewayName> | 在流中的源 ip 关联的本地网络网关 |
| LocalNetworkGateway2_s | \<SubscriptionID>/\<ResourceGroupName>/\<LocalNetworkGatewayName> | 与目标 IP 的流中关联的本地网络网关 |
| ConnectionType_s | 可能的值为 VNetPeering、 vpn 网关和 ExpressRoute |    连接类型 |
| ConnectionName_s | \<SubscriptionID>/\<ResourceGroupName>/\<ConnectionName> | 连接名称 |
| ConnectingVNets_s | 虚拟网络名称的以空格分隔列表 | 在中心辐射型拓扑，中心虚拟网络将填充在此处 |
| Country_s | 双字母国家/地区代码 (ISO 3166-1 alpha-2) | 为流类型 ExternalPublic 填充。 PublicIPs_s 字段中的所有 IP 地址将都共享相同的国家/地区代码 |
| AzureRegion_s | Azure 区域位置 | 为流类型 AzurePublic 填充。 PublicIPs_s 字段中的所有 IP 地址将都共享的 Azure 区域 |
| AllowedInFlows_d | | 允许的入站流的计数。 这表示流共享相同的四元数的捕获流 netweork 接口的入站 | 
| DeniedInFlows_d |  | 已拒绝的入站流的计数。 （入站到流中捕获的网络接口） |
| AllowedOutFlows_d | | 允许 （出站流捕获的网络接口） 的出站流的计数 |
| DeniedOutFlows_d  | | 已拒绝 （出站流捕获的网络接口） 的出站流的计数 |
| FlowCount_d | 已弃用。 总匹配该相同的四个元组的流。 在流类型 ExternalPublic 和 AzurePublic，计数将包括来自各种 PublicIP 地址的流。
| InboundPackets_d | 接收应用 NSG 规则的网络接口在捕获的数据包 | 这是仅为版本 2 的 NSG 流日志架构填充 |
| OutboundPackets_d  | 发送应用 NSG 规则的网络接口在捕获的数据包数 | 这是仅为版本 2 的 NSG 流日志架构填充 |
| InboundBytes_d |  捕获在其中应用 NSG 规则的网络接口接收的字节数 | 这是仅为版本 2 的 NSG 流日志架构填充 |
| OutboundBytes_d | 发送应用 NSG 规则的网络接口在捕获的字节数 | 这是仅为版本 2 的 NSG 流日志架构填充 |
| CompletedFlows_d  |  | 这被填充的非零值仅为版本 2 的 NSG 流日志架构 |
| PublicIPs_s | <PUBLIC_IP>\|\<FLOW_STARTED_COUNT>\|\<FLOW_ENDED_COUNT>\|\<OUTBOUND_PACKETS>\|\<INBOUND_PACKETS>\|\<OUTBOUND_BYTES>\|\<INBOUND_BYTES> | 条目分隔条 |
    
### <a name="notes"></a>说明
    
1. AzurePublic 和 ExternalPublic 流，如果客户拥有的 Azure VM IP 填充在 VMIP_s 字段中，而 PublicIPs_s 字段中填充的公共 IP 地址。 对于这些两个流类型，我们应使用 VMIP_s 和 PublicIPs_s 而非 SrcIP_s 和 DestIP_s 字段。 对于 AzurePublic 和 ExternalPublicIP 地址，我们汇总进一步，以便引入到客户 log analytics 工作区的记录数很小。（此字段将在不久后弃用和我们应使用 SrcIP_ 和 DestIP_s 具体取决于 azure VM 时的源或目标流中） 
1. 流类型的详细信息：根据流中涉及的 IP 地址，我们将分类到以下流类型中的流： 
1. IntraVNet – 在流中的这两个 IP 地址位于同一 Azure 虚拟网络中。 
1. InterVNet-在流中的 IP 地址位于两个不同 Azure 虚拟网络中。 
1. S2S – （站点到站点） 的一个 IP 地址所属的 Azure 虚拟网络时的其他 IP 地址属于客户网络 （站点） 连接到 Azure 虚拟网络通过 VPN 网关或 Express Route。 
1. P2S 的 （点到站点） 的一个 IP 地址属于 Azure 虚拟网络的其他 IP 地址属于客户网络 （站点） 时连接到 Azure 虚拟网络通过 VPN 网关。
1. AzurePublic-的一个 IP 地址属于 Azure 虚拟网络时的其他 IP 地址属于由 Microsoft 拥有的 Azure 内部的公共 IP 地址。 客户拥有的公共 IP 地址不会出现在此流类型。 例如，任何客户拥有的 VM 将流量发送到 Azure 服务 （存储终结点） 将此流类型下进行分类。 
1. 到 Azure 虚拟网络的 IP 地址之一所属 ExternalPublic-公共 IP 不在 Azure 中的其他 IP 地址时，不会报告为恶意流量分析处理间隔之间使用 ASC 源中"FlowIntervalStartTime_t"和"FlowIntervalEndTime_t"。 
1. MaliciousFlow-的一个 IP 地址属于 azure 虚拟网络的其他 IP 地址时，不是在 Azure 中并被报告为恶意流量分析处理间隔之间使用 ASC 源中的公共 IP"FlowIntervalStartTime_t"和"FlowIntervalEndTime_t"。 
1. UnknownPrivate-的一个 IP 地址属于 Azure 虚拟网络时的其他 IP 地址属于专用 IP 范围，如在 RFC 1918 中定义和不可以映射到客户拥有的站点或 Azure 虚拟网络流量分析。
1. 未知-无法映射 Azure 中的客户拓扑与在流中的 IP 地址之一，以及在本地 （站点）。

### <a name="next-steps"></a>后续步骤
若要获取常见问题的解答，请参阅[流量分析常见问题解答](traffic-analytics-faq.md)若要查看有关功能的详细信息，请参阅[流量分析文档](traffic-analytics.md)
    


    


