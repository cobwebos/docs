---
title: Azure 网络安全组流日志更改 | Microsoft Docs
description: 了解 Azure 网络安全组流日志更改。
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2018
ms.author: jdial
ms.openlocfilehash: b3a5ca929c83f70c31d667c2d9c811623691cff8
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2018
ms.locfileid: "40180418"
---
# <a name="network-security-group-flow-logs--version-2--upcoming-changes"></a>网络安全组流日志 - 版本 2 – 即将进行的更改

从 2018 年 9 月 15 日开始，网络安全组流日志格式将更改。 新增字段介绍了流的状态、每个方向传输的字节和数据包的增量计数。 分析流日志的应用程序将受此更改影响，并且需要相应地更新。 本文概述更改的详细信息。

## <a name="what-is-changing"></a>有什么变化？

网络安全组流日志的版本将从“版本”：1 更改为“版本”：2，且日志中的 flowTuples 属性添加了其他字段。 有关格式的详细信息，请参阅[如何在版本 2 中对流进行建模](#how-is-a-flow-modeled-in-version-2)。

### <a name="version-1-flow-tuple-format"></a>版本 1 流元组格式

```
"1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,D"
```

### <a name="version-2-flow-tuple-format"></a>版本 2 流元组格式

```
"1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,A,C,1,103,1,186"
```

## <a name="when-will-this-change-take-place"></a>此更改何时生效？

此更改将于 2018 年 9 月 15 日在美国中西部区域生效。 2018 年 11 月 31 将完成在公共云区域中推广更改，此后仅提供版本 2 日志。

## <a name="am-i-affected-by-the-change"></a>我是否会受此更改影响？

如果生成或使用处理网络安全组流日志数据的应用程序，可能受此更改影响。

### <a name="if-i-use-traffic-analytics"></a>如果使用流量分析呢？

不是。 从版本 1 过渡到版本 2 流日志记录的过程中，流量分析不受影响。 即将推出的增强功能将利用版本 2 流日志中提供的其他会话和带宽信息。

### <a name="if-im-using-third-party-tooling"></a>如果我正在使用第三方工具呢？

日志格式更改已预先与所有[网络观察程序合作伙伴](https://azure.microsoft.com/services/network-watcher)沟通。 有关详细信息，请联系供应商。

### <a name="if-i-have-built-a-custom-application-or-integration"></a>如果我已生成自定义应用程序或集成呢？

是的，你需要修改应用程序，才能处理采用新格式的 NSG 流日志。

## <a name="what-is-the-new-flow-logging-format"></a>新的流日志记录格式是什么？

流日志版本 2 包含采用以下格式的流元组：

```
"1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,A,C,1,103,1,186".
```

下表提供了网络安全组版本 2 流元组的属性名称和说明。 在[版本 2 示例事件](#version2sampleevent)中可以找到完整的事例记录。

| 属性名称                        | 值           | Description                                                                                                                                                 |
| ------------------------------------ | --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 时间戳                           | 1493763938      | 对应流条目的时间戳。 使用 UNIX EPOCH 格式。                                                                                       |
| 源 IP 地址                    | 185.170.185.105 |                                                                                                                                                             |
| 目标 IP 地址               | 10.2.0.4        |                                                                                                                                                             |
| Source Port                          | 35370           |                                                                                                                                                             |
| Destination Port                     | 23              |                                                                                                                                                             |
| 流量协议                     | T               | T：TCP 和 U：UDP。                                                                                                                                  |
| 流量流方向               | I               | I：进站流量和 O：出站流量。                                                                                                         |
| 流量决策                     | A               | A：允许流和 D：拒绝流。                                                                                                         |
| 流状态                           | C               | B：创建流时开始。 未提供统计信息。 C：继续执行正在进行的流。 以 5 分钟的时间间隔提供统计信息。 E：终止流时结束。 已提供统计信息。                                                                                                                                                        |
| 数据包 - 源到目标      | 1               | 自上次更新以来，从源发送到目标的 TCP 和 UDP 数据包的总数。                                                                  |
| 发送的字节数 - 源到目标   | 103             | 自上次更新以来，从源发送到目标的 TCP 和 UDP 数据包字节的总数。 数据包字节包括数据包标头和有效负载。         |
| 发送的数据包数 - 目标到源 | 1               | 自上次更新以来，从目标发送到源的 TCP 和 UDP 数据包的总数。                                                                  |
| 发送的字节数 - 目标到源   | 186             | 自上次更新以来，从目标发送到源的 TCP 和 UDP 数据包字节的总数。 数据包字节包括数据包标头和有效负载。             |

## <a name="how-is-a-flow-modeled-in-version-2"></a>如何在版本 2 中为流建模？

版本 2 的日志引入了流状态。 启动流时记录流状态 B。 流状态 C 和流状态 E 是分别标记流的延续和终止的状态。 状态 C 和 E 都包含流量带宽信息。

示例：介于 185.170.185.105:35370 和 10.2.0.4:23 之间的 TCP 对话中的流元组：

"1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,A,B,,,," "1493695838,185.170.185.105,10.2.0.4,35370,23,T,I,A,C,1021,588096,8005,4610880" "1493696138,185.170.185.105,10.2.0.4,35370,23,T,I,A,E,52,29952,47,27072"

### <a name="how-does-the-format-differ-from-version-1"></a>格式与第 1 版有何区别？

在版本 1 中，每次建立流或尝试建立流时（拒绝用例）都要创建流元组 ["1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,D"]。

### <a name="how-are-bytes-and-packets-accounted-for"></a>如何解释字节和数据包？

对于延续 C 和结束 E 流状态，字节和数据包计数是从上一次流元祖记录时集合的计数。 引用上一示例会话，传输的数据包的总数是 1021+52+8005+47 = 9125。 传输的字节总数是 588096+29952+4610880+27072 = 5256000。

### <a name="if-my-application-doesnt-understand-the-traffic-bandwidth-fields-how-does-version-2-affect-the-application"></a>如果我的应用程序不了解流量带宽字段，版本 2 如何影响应用程序？

使用版本 1 网络安全组流日志记录的应用程序通常计算唯一流的数目。 如果未对流状态解释进行任何分析中的更改，可能会发现报告的流数目增加不准确。 通过忽略 C 和 E 流量状态中的流元祖可实现延续唯一流。

## <a name="can-i-control-the-version-format-i-receive"></a>能否控制收到的版本格式？

不是。 启用和禁用流日志都不会影响日志的输出格式。 从版本 1 到版本 2 日志的更改将逐区域进行。 当某区域从版本 1 升级到版本 2 时，可能会看到采用两种格式的 NSG 流日志。 推广完成后，仅版本 2 日志可用。

## <a name="while-the-change-occurs-can-i-see-both-formats-for-the-same-network-security-group"></a>发生更改时，能否看到同时采用两种格式的同一网络安全组？

是的。 在某区域内，将逐 mac 地址发生转换。 由于一个网络安全组可应用于多台计算机，因此可以看到同时采用两种格式写入存储的日志。 日志将为版本 1 或版本 2。

## <a name="will-i-see-duplicate-data"></a>是否会看到重复数据？

格式之间不会出现重复的流日志记录数据。 更改发生时，将采用版本 1 或版本 2 格式记录流。

## <a name="how-can-i-test-the-new-format-ahead-of-time"></a>如何提前测试新格式？

是的。 可以[下载](https://aka.ms/nsgflowlogsv2blobsample)示例版本 2 流日志文件，用于测试解决方案。

## <a name="are-there-changes-to-configuration-and-management-of-network-security-group-flow-logging"></a>网络安全组流日志记录的配置和管理是否发生更改？

不是。 今年初，[已发布](https://azure.microsoft.com/blog/new-azure-network-watcher-integrations-and-network-security-group-flow-logging-updates/)支持跨订阅的 Azure 存储帐户共享相同 Azure Active Directory 租户。 此更改有助于减少管理网络安全组流日志所需的存储帐户的数目。

## <a name="questions-or-feedback"></a>还有任何疑问或反馈？

我们期待收到你使用网络安全组流日志和网络观察程序的体验。 你可以在线提供反馈或建议，或发送电子邮件至 AzureNetworkWatcher@microsoft.com。

## <a name="version-2-sample"></a>版本 2 示例

```json
{

"records": [

{

"time": "2018-08-03T17:00:54.5657764Z",

"systemId": "bbd48473-8ce0-4834-99bb-2e13b9b23ff8",

"category": "NetworkSecurityGroupFlowEvent",

"resourceId":
"/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FLOWLOGSV2DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/MULTITIERAPP2-NSG",

"operationName": "NetworkSecurityGroupFlowEvents",

"properties": {

"Version": 2,

"flows": [

{

"rule": "DefaultRule_AllowInternetOutBound",

"flows": [

{

"mac": "002248034CC3",

"flowTuples": [

"1533315592,10.1.1.6,204.79.197.200,62375,80,T,O,A,B,,,,",

"1533315595,10.1.1.6,204.79.197.200,62373,80,T,O,A,E,30,1784,92,123631",

"1533315597,10.1.1.6,204.79.197.200,62376,80,T,O,A,B,,,,",

"1533315601,10.1.1.6,204.79.197.200,62374,80,T,O,A,E,13,866,87,123079",

"1533315603,10.1.1.6,204.79.197.200,62377,80,T,O,A,B,,,,",

"1533315604,10.1.1.6,204.79.197.200,62375,80,T,O,A,E,33,1946,90,123247",

"1533315608,10.1.1.6,204.79.197.200,62378,80,T,O,A,B,,,,",

"1533315610,10.1.1.6,204.79.197.200,62376,80,T,O,A,E,20,1244,92,123355",

"1533315613,10.1.1.6,204.79.197.200,62379,80,T,O,A,B,,,,",

"1533315616,10.1.1.6,204.79.197.200,62377,80,T,O,A,E,24,1460,92,123355",

"1533315619,10.1.1.6,204.79.197.200,62380,80,T,O,A,B,,,,",

"1533315622,10.1.1.6,204.79.197.200,62378,80,T,O,A,E,23,1406,93,123409",

"1533315624,10.1.1.6,204.79.197.200,62381,80,T,O,A,B,,,,",

"1533315628,10.1.1.6,204.79.197.200,62379,80,T,O,A,E,16,1028,88,123133",

"1533315630,10.1.1.6,204.79.197.200,62382,80,T,O,A,B,,,,",

"1533315631,10.1.1.6,204.79.197.200,62380,80,T,O,A,E,13,866,87,123079",

"1533315635,10.1.1.6,204.79.197.200,62384,80,T,O,A,B,,,,",

"1533315637,10.1.1.6,204.79.197.200,62381,80,T,O,A,E,15,974,86,123025",

"1533315640,10.1.1.6,204.79.197.200,62385,80,T,O,A,B,,,,",

"1533315643,10.1.1.6,204.79.197.200,62382,80,T,O,A,E,16,1028,88,123139",

"1533315646,10.1.1.6,204.79.197.200,62386,80,T,O,A,B,,,,",

"1533315649,10.1.1.6,204.79.197.200,62384,80,T,O,A,E,21,1298,92,123355",

"1533315651,10.1.1.6,204.79.197.200,62387,80,T,O,A,B,,,,"

]

}

]

}

]

}

},

{

"time": "2018-08-03T17:01:54.5668880Z",

"systemId": "bbd48473-8ce0-4834-99bb-2e13b9b23ff8",

"category": "NetworkSecurityGroupFlowEvent",

"resourceId":
"/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FLOWLOGSV2DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/MULTITIERAPP2-NSG",

"operationName": "NetworkSecurityGroupFlowEvents",

"properties": {

"Version": 2,

"flows": [

{

"rule": "DefaultRule_DenyAllInBound",

"flows": [

{

"mac": "002248034CC3",

"flowTuples": [

"1533315685,80.211.83.37,10.1.1.6,45321,81,T,I,D,B,,,,"

]

}

]

},

{

"rule": "DefaultRule_AllowInternetOutBound",

"flows": [

{

"mac": "002248034CC3",

"flowTuples": [

"1533315655,10.1.1.6,204.79.197.200,62385,80,T,O,A,E,20,1244,87,123079",

"1533315657,10.1.1.6,204.79.197.200,62388,80,T,O,A,B,,,,",

"1533315658,10.1.1.6,204.79.197.200,62386,80,T,O,A,E,26,1568,92,123355",

"1533315662,10.1.1.6,204.79.197.200,62389,80,T,O,A,B,,,,",

"1533315664,10.1.1.6,204.79.197.200,62387,80,T,O,A,E,15,974,88,123133",

"1533315667,10.1.1.6,204.79.197.200,62390,80,T,O,A,B,,,,",

"1533315670,10.1.1.6,204.79.197.200,62388,80,T,O,A,E,18,1136,88,123139",

"1533315673,10.1.1.6,204.79.197.200,62391,80,T,O,A,B,,,,",

"1533315676,10.1.1.6,204.79.197.200,62389,80,T,O,A,E,14,920,87,123079",

"1533315678,10.1.1.6,204.79.197.200,62392,80,T,O,A,B,,,,",

"1533315679,10.1.1.6,204.79.197.200,62390,80,T,O,A,E,31,1838,94,123739",

"1533315684,10.1.1.6,204.79.197.200,62393,80,T,O,A,B,,,,",

"1533315685,10.1.1.6,204.79.197.200,62391,80,T,O,A,E,28,1676,101,141199",

"1533315689,10.1.1.6,204.79.197.200,62394,80,T,O,A,B,,,,",

"1533315691,10.1.1.6,204.79.197.200,62392,80,T,O,A,E,21,1298,93,123409",

"1533315694,10.1.1.6,204.79.197.200,62395,80,T,O,A,B,,,,",

"1533315697,10.1.1.6,204.79.197.200,62393,80,T,O,A,E,26,1568,91,123393",

"1533315700,10.1.1.6,204.79.197.200,62396,80,T,O,A,B,,,,",

"1533315703,10.1.1.6,204.79.197.200,62394,80,T,O,A,E,14,920,89,123187",

"1533315705,10.1.1.6,204.79.197.200,62397,80,T,O,A,B,,,,",

"1533315706,10.1.1.6,204.79.197.200,62395,80,T,O,A,E,13,866,87,123079",

"1533315711,10.1.1.6,204.79.197.200,62398,80,T,O,A,B,,,,"

]

}

]

}

]

}

}

]

}
```

## <a name="version-1-sample"></a>版本 1 示例

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

        } 

    ] 
}
```