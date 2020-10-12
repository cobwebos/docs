---
title: NSG 的流日志记录简介
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
ms.openlocfilehash: c0001add9ddbafb67dc7ac305c5fc171a8e24a51
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89070575"
---
# <a name="introduction-to-flow-logging-for-network-security-groups"></a>针对网络安全组进行流日志记录简介

## <a name="introduction"></a>简介

[网络安全组](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) (NSG) 流日志是 Azure 网络观察程序的一项功能，可用于记录流过 NSG 的 IP 流量的信息。 流数据将发送到 Azure 存储帐户，在其中可以访问这些数据，并将其导出到所选的任何可视化工具、SIEM 或 IDS。

![流日志概述](./media/network-watcher-nsg-flow-logging-overview/homepage.jpg)

## <a name="why-use-flow-logs"></a>为何使用流日志？

在监视、管理自己的网络，以及识别网络中是否存在安全性、合规性和性能问题时，流量分析非常关键。 在保护和优化自己的环境之前，了解该环境至关重要。 通常需要知道网络的当前状态、谁正在连接、他们从哪里进行连接、向 Internet 开放了哪些端口、预期网络行为、异常网络行为，以及流量的突发性增长。

流日志是云环境中所有网络活动的事实来源。 无论你是一家想要优化资源的创业公司，还是想要检测入侵的大型企业，流日志都是最值得依赖的功能。 可以使用此功能来优化网络流、监视吞吐量、验证合规性、检测入侵，等等。

## <a name="common-use-cases"></a>常见用例

**网络监视**：识别未知或意外的流量。 监视流量水平和带宽消耗。 按 IP 和端口筛选流日志，以了解应用程序的行为。 将流日志导出到所选的分析和可视化工具，以设置监视仪表板。

**使用情况监视和优化**：识别网络中最活跃的通信方。 结合 GeoIP 数据来识别跨区域流量。 了解流量增长情况以进行容量预测。 基于数据删除过度严格的流量规则。

**合规性**：使用流数据验证网络是否隔离以及是否符合企业访问规则

**网络取证和安全分析**：分析来自已遭入侵的 IP 和网络接口的网络流。 将流日志导出到所选的任何 SIEM 或 IDS 工具。

## <a name="how-logging-works"></a>日志记录的工作原理

**关键属性**

- 流日志在 [第4层](https://en.wikipedia.org/wiki/OSI_model#Layer_4:_Transport_Layer) 操作，记录传入和传出 NSG 的所有 IP 流
- 日志是通过 Azure 平台收集的，不会对客户资源或网络性能造成任何形式的影响。
- 日志以 JSON 格式编写，基于每个 NSG 规则显示出站和入站流。
- 每条日志记录包含流所应用到网络接口 (NIC)、5 元组信息、流量决策和（仅限版本 2）吞吐量信息。 有关完整详细信息，请参阅下面的_日志格式_。
- 流日志具有保留功能，可以自动删除在创建后已保留一年的日志。 

> [!NOTE]
> 仅当使用[常规用途 v2 存储帐户 (GPv2)](https://docs.microsoft.com/azure/storage/common/storage-account-overview#types-of-storage-accounts) 时，才可以使用保留。 

**核心概念**

- 软件定义的网络是围绕虚拟网络 (VNET) 和子网进行组织的。 可以使用 NSG 来管理这些 VNet 和子网的安全性。
- 网络安全组 (NSG) 包含安全规则列表，这些规则可允许或拒绝 VNet 和子网所连接到的资源中的网络流量。 可以将 NSG 与子网、单个 VM 或附加到 VM 的单个网络接口 (NIC)（资源管理器模型）进行关联。 有关详细信息，请参阅[网络安全组概述](https://docs.microsoft.com/azure/virtual-network/security-overview?toc=%2Fazure%2Fnetwork-watcher%2Ftoc.json)。
- 网络中的所有流量流都是使用适用 NSG 中的规则评估的。
- 这些评估的结果就是 NSG 流日志。 流日志通过 Azure 平台收集，无需对客户资源进行任何更改。
- 注意：规则分为两种类型-终止 & 不终止，每个都有不同的日志记录行为。
- - NSG 拒绝规则是终止类型。 拒绝流量的 NSG 会将其记录在流日志中，在这种情况下，处理将在任何 NSG 拒绝流量后停止。 
- - NSG 允许规则是非终止类型，这意味着即使一个 NSG 允许，处理也会继续下一 NSG。 允许流量的最后一个 NSG 会将流量记录到流日志。
- NSG 流日志将写入到存储帐户，从存储帐户中可以访问这些日志。
- 可以使用 TA、Splunk、Grafana、Stealthwatch 等工具导出、处理、分析和可视化流日志。

## <a name="log-format"></a>日志格式

流日志包含以下属性：

* **time** - 记录事件的时间
* **systemId** - 网络安全组系统 ID。
* 类别 - 事件的类别。 类别始终是 NetworkSecurityGroupFlowEvent
* **resourceid** - NSG 的资源 ID。
* **operationName** - 始终为 NetworkSecurityGroupFlowEvents
* **properties** - 流属性的集合
    * **Version** - 流日志事件架构的版本号
    * **flows** - 流的集合。 此属性有多个针对不同规则的条目
        * **rule** - 列出流时所依据的规则
            * **flows** - 流的集合
                * **mac** - VM 的 NIC 的 MAC 地址，用于收集流
                * **flowTuples** - 一个字符串，包含逗号分隔格式的流元组的多个属性
                    * **Time Stamp** - 此值为时间戳，表示流的发生时间，采用 UNIX epoch 格式
                    * **Source IP** - 源 IP
                    * **Destination IP** - 目标 IP
                    * **Source Port** - 源端口
                    * **Destination Port** - 目标端口
                    * **Protocol** - 流的协议。 有效值为 **T**（表示 TCP）和 **U**（表示 UDP）
                    * **Traffic Flow** - 流的方向。 有效值为 **I**（表示入站）和 **O**（表示出站）。
                    * **Traffic Decision** - 是允许了还是拒绝了流。 有效值为 **A**（表示已允许）和 **D**（表示已拒绝）。
                    * **Flow State - 仅限版本 2** - 捕获流的状态。 可能的状态包括 **B**：创建流时开始。 未提供统计信息。 **C**：继续执行正在进行的流。 以 5 分钟的时间间隔提供统计信息。 **E**：在流终止时结束。 已提供统计信息。
                    * **Packets - 源到目标 - 仅限版本 2** 自上次更新以来，从源发送到目标的 TCP 或 UDP 数据包的总数。
                    * **Bytes sent - 源到目标 - 仅限版本 2** 自上次更新以来，从源发送到目标的 TCP 或 UDP 数据包字节的总数。 数据包字节包括数据包标头和有效负载。
                    * **Packets - 目标到源 - 仅限版本 2** 自上次更新以来，从目标发送到源的 TCP 或 UDP 数据包的总数。
                    * **Bytes sent - 目标到源 - 仅限版本 2** 自上次更新以来，从目标发送到源的 TCP 和 UDP 数据包字节的总数。 数据包字节包括数据包标头和有效负载。


**NSG 流日志版本 2（与版本 1 的比较）** 

日志版本 2 引入了流状态的概念。 可以配置要接收的流日志的版本。

启动流时记录流状态 B。 流状态 C 和流状态 E 是分别标记流的延续和终止的状态 。 状态 C 和 E 都包含流量带宽信息 。

### <a name="sample-log-records"></a>示例日志记录

以下文本是流日志的示例。 可以看到，有多个记录遵循前一部分描述的属性列表。

> [!NOTE]
> flowTuples 属性中的值为逗号分隔列表。
 
**版本 1 NSG 流日志格式示例**
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
        
        
```
**版本 2 NSG 流日志格式示例**
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
        }
        
```
**日志元组说明**

![流日志元组](./media/network-watcher-nsg-flow-logging-overview/tuple.png)

**采样带宽计算**

介于 185.170.185.105:35370 和 10.2.0.4:23 之间的 TCP 对话中的流元组：

"1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,A,B,,,," "1493695838,185.170.185.105,10.2.0.4,35370,23,T,I,A,C,1021,588096,8005,4610880" "1493696138,185.170.185.105,10.2.0.4,35370,23,T,I,A,E,52,29952,47,27072"

对于延续 C 和结束 E 流状态，字节和数据包计数是从上一次流元祖记录时集合的计数 。 引用上一示例会话，传输的数据包的总数是 1021+52+8005+47 = 9125。 传输的字节总数是 588096+29952+4610880+27072 = 5256000。


## <a name="enabling-nsg-flow-logs"></a>启用 NSG 流日志

使用下面的相关链接获取有关启用流日志的指导。

- [Azure 门户](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)
- [PowerShell](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-powershell)
- [CLI](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-cli)
- [REST](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-rest)
- [Azure Resource Manager](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-azure-resource-manager)

## <a name="updating-parameters"></a>更新参数

**Azure 门户**

在 Azure 门户上，导航到“网络观察程序”中的“NSG 流日志”部分。 然后单击 NSG 的名称。 此时会打开流日志的“设置”窗格。 更改所需的参数，然后点击“保存”以部署更改。

**PS/CLI/REST/ARM**

若要通过命令行工具更新参数，请使用上面所述的用于启用流日志的同一命令，但要指定你想要更改的参数。

## <a name="working-with-flow-logs"></a>使用流日志

*读取和导出流日志*

- [从门户下载并查看流日志](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal#download-flow-log)
- [使用 PowerShell 函数读取流日志](https://docs.microsoft.com/azure/network-watcher/network-watcher-read-nsg-flow-logs)
- [将 NSG 流日志导出到 Splunk](https://www.splunk.com/en_us/blog/tips-and-tricks/splunking-microsoft-azure-network-watcher-data.html)

流日志针对的是 NSG，但其显示方式不同于其他日志。 流日志仅存储在一个存储帐户中，其采用的日志记录路径如以下示例所示：

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

*可视化流日志*

- [Azure 流量分析](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)是一个 Azure 原生服务，用于处理流日志、提取见解以及可视化流日志。 
- [[教程] 使用 Power BI 可视化 NSG 流日志](https://docs.microsoft.com/azure/network-watcher/network-watcher-visualize-nsg-flow-logs-power-bi)
- [[教程] 使用 Elastic Stack 可视化 NSG 流日志](https://docs.microsoft.com/azure/network-watcher/network-watcher-visualize-nsg-flow-logs-open-source-tools)
- [[教程] 使用 Grafana 管理和分析 NSG 流日志](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-grafana)
- [[教程] 使用 Graylog 管理和分析 NSG 流日志](https://docs.microsoft.com/azure/network-watcher/network-watcher-analyze-nsg-flow-logs-graylog)


## <a name="nsg-flow-logging-considerations"></a>NSG 流日志记录注意事项

**存储帐户注意事项**： 

- 位置：所用的存储帐户必须与 NSG 位于同一区域中。
- 自行管理密钥轮换：如果你更改/轮换存储帐户的访问密钥，则 NSG 流日志将停止工作。 若要解决此问题，必须禁用并重新启用 NSG 流日志。

**流日志记录成本**：NSG 流日志记录按生成的日志量计费。 流量较高时，流日志的量和相关成本可能会增大。 NSG 流日志定价不包括基本的存储成本。 将保留策略功能与 NSG 流日志记录配合使用意味着在较长时间内会产生单独的存储成本。 如果不需要使用保留策略功能，我们建议将此值设置为 0。 有关详细信息，请参阅[网络观察程序定价](https://azure.microsoft.com/pricing/details/network-watcher/)和 [Azure 存储定价](https://azure.microsoft.com/pricing/details/storage/)。

**用户定义的入站 TCP 规则的问题**： [ (nsg) 的网络安全组 ](https://docs.microsoft.com/azure/virtual-network/security-overview) 作为有 [状态防火墙](https://en.wikipedia.org/wiki/Stateful_firewall?oldformat=true)实现。 但是，由于当前平台限制，影响入站 TCP 流的用户定义的规则将以无状态方式实现。 因此，被用户定义的入站规则影响的流将变为非终止类型。 不会为这些流记录额外的字节和数据包计数。 因此，NSG 流日志（和流量分析）中报告的字节数和数据包数可能与实际数字不同。 我们计划将在 2020 年 12 月的最新版本中提供一个用于解决这些问题的选择加入标志。 在此期间，由于此行为导致的严重问题，客户可以通过支持请求选择加入，请 > NSG Flow 日志下的 "网络观察程序" 下提出支持请求。  

**入站流被从 Internet IP 记录到了没有公共 IP 的虚拟机**：对于没有通过与 NIC 关联的公共 IP 地址分配公共 IP 地址作为实例级公共 IP 的虚拟机，或者是属于基本负载均衡器后端池的一部分的虚拟机，请使用[默认SNAT](../load-balancer/load-balancer-outbound-connections.md)，并使用由 Azure 分配的 IP 地址以便于进行出站连接。 因此，如果流的目的地是分配给 SNAT 的端口范围内的端口，你可能会看到来自 Internet IP 地址的流的流日志条目。 虽然 Azure 不允许将这些流传输到 VM，但是按照设计，该尝试会被记录并显示在网络观察程序的 NSG 流日志中。 我们建议使用 NSG 来显式阻止不需要的入站 Internet 流量。

## <a name="best-practices"></a>最佳实践

**在关键的 VNET/子网上启用**：作为审核和安全方面的最佳做法，应在订阅中的所有关键 VNET/子网上启用流日志。 

**在附加到资源的所有 NSG 上启用 NSG 流日志记录**：Azure 中的流日志记录是在 NSG 资源上配置的。 一个流只与一个 NSG 规则相关联。 如果利用了多个 NSG，我们建议在应用了 NSG 的所有资源子网或网络接口中启用 NSG 流日志，以确保记录所有流量。 有关详细信息，请参阅网络安全组中的[流量评估方式](../virtual-network/network-security-group-how-it-works.md)。

**存储预配**：应该根据预期的流日志量预配存储。

## <a name="troubleshooting-common-issues"></a>排查常见问题

**无法启用 NSG 流日志**

- **Microsoft.Insights** 资源提供程序未注册

如果收到 _AuthorizationFailed_ 或 _GatewayAuthenticationFailed_ 错误，则表明你可能尚未在订阅上启用 Microsoft Insights 资源提供程序。 [按照说明](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal#register-insights-provider)启用 Microsoft Insights 提供程序。

**我已经启用 NSG 流日志，但在存储帐户中看不到数据**

- **设置时间**

NSG 流日志可能需要长达 5 分钟的时间才能显示在存储帐户中（如果配置正确）。 可以[根据此处的说明](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal#download-flow-log)访问将要显示的 PT1H.json。

- **NSG 上没有流量**

有时，由于 VM 处于不可用状态，或者应用程序网关或其他设备上的上游筛选器阻止了 NSG 的流量，因此，你将看不到日志。

**我想自动执行 NSG 流日志**

NSG 流日志当前不支持通过 ARM 模板进行自动化操作。 有关详细信息，请阅读[功能公告](https://azure.microsoft.com/updates/arm-template-support-for-nsg-flow-logs/)。

## <a name="faq"></a>常见问题

**NSG 流日志有什么作用？**

可以通过[网络安全组 (NSG)](https://docs.microsoft.com/azure/virtual-network/security-overview) 来合并和管理 Azure 网络资源。 使用 NSG 流日志可以通过 NSG 记录有关所有流量的 5 元组流信息。 原始流日志将写入 Azure 存储帐户，在存储帐户中，可以根据需要进一步处理、分析、查询或导出这些日志。

**使用流日志是否影响网络延迟或性能？**

流日志数据是在网络流量路径的外部收集的，因此不会影响网络吞吐量或延迟。 可以创建或删除流日志，而不会对网络性能产生任何影响。

**当存储帐户位于防火墙后面时，如何使用 NSG 流日志？**

若要使用防火墙后面的存储帐户，必须提供一个例外，以便受信任的 Microsoft 服务访问你的存储帐户：

- 在门户或[“存储帐户”页](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Storage%2FStorageAccounts)的全局搜索框中键入存储帐户的名称，导航到存储帐户
- 在“设置”部分下，选择“防火墙和虚拟网络” 
- 在“允许的访问来源”中，选择“所选网络”。  然后，在“例外”下，勾选“允许受信任的 Microsoft 服务访问此存储帐户”旁边的框
- 如果已选中，则不需进行更改。
- 在 [NSG 流日志概述页](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs)上找到目标 NSG，并启用选择了上述存储帐户的 NSG 流日志。

可以在数分钟后检查存储日志，应该会看到时间戳已更新，或者会看到新的 JSON 文件已创建。

**当存储帐户位于服务终结点后面时，如何使用 NSG 流日志？**

NSG 流日志与服务终结点兼容，无需任何额外的配置。 请参阅[有关在虚拟网络中启用服务终结点的教程](https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources#enable-a-service-endpoint)。

**流日志版本 1 和 2 有何区别？**

流日志版本 2 引入了“流状态”的概念，并会存储有关传输的字节和数据包的信息。 [了解详细信息](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview#log-file)

## <a name="pricing"></a>定价

NSG 流日志按收集的日志量（以 GB 为单位）收费，并为每个订阅附送 5 GB/月的免费层。 有关你所在区域的当前定价，请参阅[网络观察程序定价页](https://azure.microsoft.com/pricing/details/network-watcher/)。

日志存储费单独计收，相关价格请参阅 [Azure 存储块 Blob 定价页](https://azure.microsoft.com/pricing/details/storage/blobs/)。
 
