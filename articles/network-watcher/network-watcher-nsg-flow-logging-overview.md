---
title: Nsg 的流日志记录简介
titleSuffix: Azure Network Watcher
description: 本文说明如何使用 Azure 网络观察程序的 NSG 流日志功能
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: f231a5339b9c696b2a427b0713118d4b46e30277
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76840989"
---
# <a name="introduction-to-flow-logging-for-network-security-groups"></a>针对网络安全组进行流日志记录简介

网络安全组 (NSG) 流日志是网络观察程序的一项功能，可用于查看有关通过 NSG 的入口和出口 IP 流量的信息。 流日志以 JSON 格式编写，并基于每个规则显示出站和入站流、流所适用的网络接口 (NIC)、有关流的 5 元组信息（源/目标 IP 地址、源/目标端口和协议）、是允许还是拒绝流量，版本 2 中还会显示吞吐量信息（字节和数据包）。


![流日志概述](./media/network-watcher-nsg-flow-logging-overview/figure1.png)

流日志针对的是 NSG，但其显示方式不同于其他日志。 流日志仅存储在一个存储帐户中，其采用的日志记录路径如以下示例所示：

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```
可以使用[流量分析](traffic-analytics.md)来分析流日志并获取网络流量的见解。

适用于其他日志的保留策略也适用于流日志。 可以将日志保留策略从1天设置为365天。 如果未设置保留策略，日志将一直保留。

> [!NOTE] 
> 将 NSG 流日志记录与保留策略功能结合使用可能会导致存储操作量和相关成本增加。 如果不需要使用保留策略功能，我们建议将此值设置为 0。


## <a name="log-file"></a>日志文件

流日志包含以下属性：

* **time** - 记录事件的时间
* **systemId** - 网络安全组资源 ID
* 类别 - 事件的类别。 类别始终是 NetworkSecurityGroupFlowEvent
* **resourceid** - NSG 的资源 ID
* **operationName** - 始终为 NetworkSecurityGroupFlowEvents
* **properties** - 流属性的集合
    * **Version** - 流日志事件架构的版本号
    * **flows** - 流的集合。 此属性有多个针对不同规则的条目
        * **rule** - 列出流时所依据的规则
            * **flows** - 流的集合
                * **mac** - VM 的 NIC 的 MAC 地址，用于收集流
                * **flowTuples** - 一个字符串，包含逗号分隔格式的流元组的多个属性
                    * **时间戳**-此值是在 UNIX epoch 格式中出现流时的时间戳
                    * **Source IP** - 源 IP
                    * **Destination IP** - 目标 IP
                    * **Source Port** - 源端口
                    * **Destination Port** - 目标端口
                    * **Protocol** - 流的协议。 有效值为 **T**（表示 TCP）和 **U**（表示 UDP）
                    * **Traffic Flow** - 流的方向。 有效值为 **I**（表示入站）和 **O**（表示出站）。
                    * **Traffic Decision** - 是允许了还是拒绝了流。 有效值为 **A**（表示已允许）和 **D**（表示已拒绝）。
                    * **Flow State - 仅限版本 2** - 捕获流的状态。 可能的状态为 **B**：开始（创建流时）。 未提供统计信息。 C：继续执行正在进行的流。 以 5 分钟的时间间隔提供统计信息。 E：终止流时结束。 已提供统计信息。
                    * **Packets - 源到目标 - 仅限版本 2** 自上次更新以来，从源发送到目标的 TCP 或 UDP 数据包的总数。
                    * **Bytes sent - 源到目标 - 仅限版本 2** 自上次更新以来，从源发送到目标的 TCP 或 UDP 数据包字节的总数。 数据包字节包括数据包标头和有效负载。
                    * **Packets - 目标到源 - 仅限版本 2** 自上次更新以来，从目标发送到源的 TCP 或 UDP 数据包的总数。
                    * **Bytes sent - 目标到源 - 仅限版本 2** 自上次更新以来，从目标发送到源的 TCP 和 UDP 数据包字节的总数。 数据包字节包括数据包标头和有效负载。

## <a name="nsg-flow-logs-version-2"></a>NSG 流日志版本 2

版本 2 的日志引入了流状态。 可以配置要接收的流日志的版本。 要了解如何启用流日志，请参阅[启用 NSG 流日志记录](network-watcher-nsg-flow-logging-portal.md)。

启动流时记录流状态 B。 流状态 C 和流状态 E 是分别标记流的延续和终止的状态。 状态 C 和 E 都包含流量带宽信息。

示例：介于 185.170.185.105:35370 和 10.2.0.4:23 之间的 TCP 对话中的流元组：

"1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,A,B,,,," "1493695838,185.170.185.105,10.2.0.4,35370,23,T,I,A,C,1021,588096,8005,4610880" "1493696138,185.170.185.105,10.2.0.4,35370,23,T,I,A,E,52,29952,47,27072"

对于延续 C 和结束 E 流状态，字节和数据包计数是从上一次流元祖记录时集合的计数。 引用上一示例会话，传输的数据包的总数是 1021+52+8005+47 = 9125。 传输的字节总数是 588096+29952+4610880+27072 = 5256000。

以下文本是流日志的示例。 可以看到，有多个记录遵循前一部分描述的属性列表。

## <a name="nsg-flow-logging-considerations"></a>NSG 流日志记录注意事项

**存储帐户注意事项**： 

- 位置：使用的存储帐户必须与 NSG 位于同一区域。
- 自我管理密钥轮替：如果更改/轮换存储帐户的访问密钥，NSG 流日志将停止工作。 若要解决此问题，必须禁用并重新启用 NSG 流日志。

**对附加到资源的所有 Nsg 启用 NSG 流日志记录**： Azure 中的流日志记录是在 NSG 资源上配置的。 一个流只与一个 NSG 规则相关联。 如果利用多个 NSG，则我们建议在应用资源子网或网络接口的所有 NSG 上启用 NSG 流日志记录，以确保记录所有流量。 有关详细信息，请参阅网络安全组中的[流量评估方式](../virtual-network/security-overview.md#how-traffic-is-evaluated)。

**流日志记录成本**： NSG 流日志记录在生成的日志量上计费。 流量较高时，流日志的量和相关成本可能会增大。 NSG 流日志定价不包括基本的存储成本。 将 NSG 流日志记录与保留策略功能结合使用可能会导致存储操作量和相关成本增加。 如果不需要使用保留策略功能，我们建议将此值设置为 0。 有关详细信息，请参阅[网络观察程序定价](https://azure.microsoft.com/pricing/details/network-watcher/)和[Azure 存储定价](https://azure.microsoft.com/pricing/details/storage/)了解更多详细信息。

**从 Internet ip 记录到无公共 ip 的 vm 的入站流**：没有通过公共 ip 地址分配公共 ip 地址的 vm 作为实例级公共 ip，或者作为基本的负载均衡器后端池的一部分，请使用[默认的 SNAT](../load-balancer/load-balancer-outbound-connections.md#defaultsnat) ，并使用 Azure 分配的 IP 地址来促进出站连接。 因此，如果流发送到分配给 SNAT 的端口范围内的端口，则可能会看到流的流日志条目。 虽然 Azure 不允许将这些流传输到 VM，但会记录尝试，并按设计在网络观察程序的 NSG 流日志中显示。 建议通过 NSG 显式阻止不需要的入站 internet 流量。

**无状态流的字节和数据包计数不正确**：[网络安全组（nsg）](https://docs.microsoft.com/azure/virtual-network/security-overview)作为有[状态防火墙](https://en.wikipedia.org/wiki/Stateful_firewall?oldformat=true)实现。 但是，许多用于控制流量的默认/内部规则都是以无状态方式实现的。 由于平台限制，不会记录无状态流的字节和数据包计数（也就是说，流量流经无状态规则），只记录有状态流。 因此，NSG Flow 日志中报告的字节数和数据包数（和流量分析）可能与实际流不同。 此限制计划于6月2020。

## <a name="sample-log-records"></a>示例日志记录

以下文本是流日志的示例。 可以看到，有多个记录遵循前一部分描述的属性列表。


> [!NOTE]
> **flowTuples* 属性中的值为逗号分隔列表。
 
### <a name="version-1-nsg-flow-log-format-sample"></a>版本 1 NSG 流日志格式示例
```json
{
    "records": [
        {
            "time": "2017-02-16T22:00:32.8950000Z",
            "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 1,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282421,42.119.146.95,10.1.0.4,51529,5358,T,I,D"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "UserRule_default-allow-rdp",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282370,163.28.66.17,10.1.0.4,61771,3389,T,I,A",
                                    "1487282393,5.39.218.34,10.1.0.4,58596,3389,T,I,A",
                                    "1487282393,91.224.160.154,10.1.0.4,61540,3389,T,I,A",
                                    "1487282423,13.76.89.229,10.1.0.4,53163,3389,T,I,A"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
        {
            "time": "2017-02-16T22:01:32.8960000Z",
            "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 1,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282481,195.78.210.194,10.1.0.4,53,1732,U,I,D"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "UserRule_default-allow-rdp",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282435,61.129.251.68,10.1.0.4,57776,3389,T,I,A",
                                    "1487282454,84.25.174.170,10.1.0.4,59085,3389,T,I,A",
                                    "1487282477,77.68.9.50,10.1.0.4,65078,3389,T,I,A"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
    "records":
    [
        
        {
             "time": "2017-02-16T22:00:32.8950000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282421,42.119.146.95,10.1.0.4,51529,5358,T,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282370,163.28.66.17,10.1.0.4,61771,3389,T,I,A","1487282393,5.39.218.34,10.1.0.4,58596,3389,T,I,A","1487282393,91.224.160.154,10.1.0.4,61540,3389,T,I,A","1487282423,13.76.89.229,10.1.0.4,53163,3389,T,I,A"]}]}]}
        }
        ,
        {
             "time": "2017-02-16T22:01:32.8960000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282481,195.78.210.194,10.1.0.4,53,1732,U,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282435,61.129.251.68,10.1.0.4,57776,3389,T,I,A","1487282454,84.25.174.170,10.1.0.4,59085,3389,T,I,A","1487282477,77.68.9.50,10.1.0.4,65078,3389,T,I,A"]}]}]}
        }
        ,
        {
             "time": "2017-02-16T22:02:32.9040000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282492,175.182.69.29,10.1.0.4,28918,5358,T,I,D","1487282505,71.6.216.55,10.1.0.4,8080,8080,T,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282512,91.224.160.154,10.1.0.4,59046,3389,T,I,A"]}]}]}
        }
        ,
        ...
```
### <a name="version-2-nsg-flow-log-format-sample"></a>版本 2 NSG 流日志格式示例
```json
 {
    "records": [
        {
            "time": "2018-11-13T12:00:35.3899262Z",
            "systemId": "a0fca5ce-022c-47b1-9735-89943b42f2fa",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 2,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110402,94.102.49.190,10.5.16.4,28746,443,U,I,D,B,,,,",
                                    "1542110424,176.119.4.10,10.5.16.4,56509,59336,T,I,D,B,,,,",
                                    "1542110432,167.99.86.8,10.5.16.4,48495,8088,T,I,D,B,,,,"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "DefaultRule_AllowInternetOutBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110377,10.5.16.4,13.67.143.118,59831,443,T,O,A,B,,,,",
                                    "1542110379,10.5.16.4,13.67.143.117,59932,443,T,O,A,E,1,66,1,66",
                                    "1542110379,10.5.16.4,13.67.143.115,44931,443,T,O,A,C,30,16978,24,14008",
                                    "1542110406,10.5.16.4,40.71.12.225,59929,443,T,O,A,E,15,8489,12,7054"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
        {
            "time": "2018-11-13T12:01:35.3918317Z",
            "systemId": "a0fca5ce-022c-47b1-9735-89943b42f2fa",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 2,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110437,125.64.94.197,10.5.16.4,59752,18264,T,I,D,B,,,,",
                                    "1542110475,80.211.72.221,10.5.16.4,37433,8088,T,I,D,B,,,,",
                                    "1542110487,46.101.199.124,10.5.16.4,60577,8088,T,I,D,B,,,,",
                                    "1542110490,176.119.4.30,10.5.16.4,57067,52801,T,I,D,B,,,,"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
        ...
```

## <a name="next-steps"></a>后续步骤

- 要了解如何启用流日志，请参阅[启用 NSG 流日志记录](network-watcher-nsg-flow-logging-portal.md)。
- 若要了解如何读取流日志，请参阅[读取 NSG 流日志](network-watcher-read-nsg-flow-logs.md)。
- 若要了解有关 NSG 日志记录的详细信息，请参阅[网络安全组（nsg） Azure Monitor 日志](../virtual-network/virtual-network-nsg-manage-log.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)。
- 如需确定是允许还是拒绝流量进出 VM，请参阅[诊断 VM 网络流量筛选器问题](diagnose-vm-network-traffic-filtering-problem.md)
