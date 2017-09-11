---
title: "使用 Azure 网络观察程序针对网络安全组进行流日志记录简介 | Microsoft 文档"
description: "此页说明如何使用 Azure 网络观察程序的 NSG 流日志功能"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 47d91341-16f1-45ac-85a5-e5a640f5d59e
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: c24c2c4e297497e106691d62be76ad0591bb33de
ms.contentlocale: zh-cn
ms.lasthandoff: 03/04/2017

---

# <a name="introduction-to-flow-logging-for-network-security-groups"></a>针对网络安全组进行流日志记录简介

网络安全组流日志是网络观察程序的一项功能，用于查看通过网络安全组的入口和出口 IP 流量的信息。 这些流日志以 json 格式编写，并根据规则显示出站和入站流、流所适用的 NIC、有关流的 5 元组信息（源/目标 IP、源/目标端口、协议），以及是允许还是拒绝流量。

![流日志概览][1]

流日志针对的是网络安全组，但其显示方式不同于其他日志。 流日志仅存储在一个存储帐户中，其采用的日志记录路径如以下示例所示：

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId%3D/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/microsoft.network/networksecuritygroups/{nsgName}/{year}/{month}/{day}/PT1H.json
```

适用于其他日志的保留策略也适用于流日志。 日志的保留策略可以设置为 1 到 365 天。 如果未设置保留策略，则会永久保留日志。

## <a name="log-file"></a>日志文件

流日志具有多个属性。 以下列表列出了在 NSG 流日志中返回的属性：

* **time** - 记录事件的时间
* **systemId** - 网络安全组资源 ID
* **category** - 事件的类别，始终为 NetworkSecurityGroupFlowEvent
* **resourceid** - NSG 的资源 ID
* **operationName** - 始终为 NetworkSecurityGroupFlowEvents
* **properties** - 流属性的集合
    * **Version** - 流日志事件架构的版本号
    * **flows** - 流的集合。 此属性有多个针对不同规则的条目
        * **rule** - 列出流时所依据的规则
            * **flows** - 流的集合
                * **mac** - VM 的 NIC 的 MAC 地址，用于收集流
                * **flowTuples** - 一个字符串，包含逗号分隔格式的流元组的多个属性
                    * **Time Stamp** - 此值为时间戳，表示流的发生时间，采用 UNIX EPOCH 格式
                    * **Source IP** - 源 IP
                    * **Destination IP** - 目标 IP
                    * **Source Port** - 源端口
                    * **Destination Port** - 目标端口
                    * **Protocol** - 流的协议。 有效值为 **T**（表示 TCP）和 **U**（表示 UDP）
                    * **Traffic Flow** - 流的方向。 有效值为 **I**（表示入站）和 **O**（表示出站）。
                    * **Traffic** - 是允许了还是拒绝了流。 有效值为 **A**（表示已允许）和 **D**（表示已拒绝）。


下面是流日志的示例。 可以看到，有多个记录遵循前一部分描述的属性列表。 

> [!NOTE]
> flowTuples 属性中的值为逗号分隔列表。
 
```json
{
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

## <a name="next-steps"></a>后续步骤

访问[启用流日志记录](network-watcher-nsg-flow-logging-portal.md)，了解如何启用流日志。

访问[网络安全组 (NSG) 的日志分析](../virtual-network/virtual-network-nsg-manage-log.md)，了解 NSG 日志记录。

访问[使用“IP 流验证”验证流量](network-watcher-check-ip-flow-verify-portal.md)，了解是允许还是拒绝流

<!-- Image references -->
[1]: ./media/network-watcher-nsg-flow-logging-overview/figure1.png


