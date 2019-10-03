---
title: 监视 Azure Service Fabric 中的 Linux 群集事件 | Microsoft Docs
description: 了解如何监视 Syslog 中的 Linux 群集事件
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/23/2018
ms.author: srrengar
ms.openlocfilehash: 402e3dfe018c94ef068caf918b38aaad00064a49
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "62118366"
---
# <a name="service-fabric-linux-cluster-events-in-syslog"></a>Syslog 中的 Service Fabric Linux 群集事件

Service Fabric 公开一组平台事件，告知你群集中的重要活动。 点击[此处](service-fabric-diagnostics-event-generation-operational.md)查看公开的完整事件列表。 可通过多种方式使用这些事件。 在本文中，我们将讨论如何配置 Service Fabric，以便将这些事件写入 Syslog。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="introduction"></a>简介

在 6.4 版中就已引入了 SyslogConsumer，用于将 Service Fabric 平台事件发送到 Linux 群集的 Syslog。 开启后，事件将自动传递到可通过 Log Analytics 代理收集和发送的 Syslog。

每个 Syslog 事件均有 4 个组件
* 设施
* 标识
* Message
* Severity

SyslogConsumer 使用 `Local0` 设施写入所有平台事件。 更改 config 配置可更新为任何有效的设施。使用的标识是 `ServiceFabric`。 消息字段包含在 JSON 中序列化的整个事件，以便可通过各种工具查询和使用。 

## <a name="enable-syslogconsumer"></a>启用 SyslogConsumer

要启用 SyslogConsumer，你需要将自己的群集升级。 需要使用以下代码更新 `fabricSettings` 部分。 请注意，此代码仅包含与 SyslogConsumer 相关的部分

```json
    "fabricSettings": [
        {
            "name": "Diagnostics",
            "parameters": [
            {
                "name": "ConsumerInstances",
                "value": "AzureWinFabCsv, AzureWinFabCrashDump, AzureTableWinFabEtwQueryable, SyslogConsumer"
            }
            ]
        },
        {
            "name": "SyslogConsumer",
            "parameters": [
            {
                "name": "ProducerInstance",
                "value": "WinFabLttProducer"
            },
            {
            "name": "ConsumerType",
            "value": "SyslogConsumer"
            },
            {
                "name": "IsEnabled",
                "value": "true"
            }
            ]
        },
        {
            "name": "Common",
            "parameters": [
            {
                "name": "LinuxStructuredTracesEnabled",
                "value": "true"
            }
            ]
        }
    ],
```

下面是需要说明的更改
1. 在 Common 部分中，有一个名为 `LinuxStructuredTracesEnabled` 的新参数。 这就要求在将 Linux 事件发送到 Syslog 时，将它们组织起来并串行化。 
2. 在 Diagnostics 部分中，添加了一个新的 ConsumerInstance：SyslogConsumer。 这就告诉平台还有另一个事件使用者。 
3. 新部分 SyslogConsumer 需要将 `IsEnabled` 设为 `true`。 它已配置为自动使用 Local0 设施。 你可以添加另一个参数来替代该设置。

```json
    {
        "name": "New LogFacility",
        "value": "<Valid Syslog Facility>"
    }
```

## <a name="azure-monitor-logs-integration"></a>Azure 监视器日志集成
你可以读取 Azure Monitor 日志之类的监视工具中的这些 Syslog 事件。 你可以按照[说明].(../azure-monitor/learn/quick-create-workspace.md)，使用 Azure 市场创建一个 Log Analytics 工作区。还需向群集添加 Log Analytics 代理，以向该工作区收集和发送该数据。 这正是用于收集性能计数器的同一个代理。 

1. 导航到`Advanced Settings`边栏选项卡

    ![工作区设置](media/service-fabric-diagnostics-oms-syslog/workspace-settings.png)

2. 单击 `Data`
3. 单击 `Syslog`
4. 将 Local0 配置为要跟踪的设施。如果在 fabricSettings 中进行了更改，可以再添加一个设施

    ![配置 Syslog](media/service-fabric-diagnostics-oms-syslog/syslog-configure.png)
5. 单击工作区资源菜单中的`Logs`可前往查询浏览器启动查询

    ![工作区日志](media/service-fabric-diagnostics-oms-syslog/workspace-logs.png)
6. 可以对 `Syslog` 表进行查询，查找作为 ProcessName 的 `ServiceFabric`。 下面的示例查询说明如何分析事件中的 JSON 并显示其内容

```kusto
    Syslog | where ProcessName == "ServiceFabric" | extend $payload = parse_json(SyslogMessage) | project $payload
```

![Syslog 查询](media/service-fabric-diagnostics-oms-syslog/syslog-query.png)

上面的示例为 NodeDown 事件。 可在[此处](service-fabric-diagnostics-event-generation-operational.md)查看事件的完整列表。

## <a name="next-steps"></a>后续步骤
* [将 Log Analytics 代理部署到节点上](service-fabric-diagnostics-oms-agent.md)，以收集性能计数器、docker 统计信息和容器日志
* 掌握[日志搜索和查询](../log-analytics/log-analytics-log-searches.md)作为 Azure Monitor 日志的一部分提供的功能
* [使用视图设计器在 Azure Monitor 日志中创建自定义视图](../log-analytics/log-analytics-view-designer.md)
* 有关如何为引用[Azure Monitor 记录与 Syslog 集成](../log-analytics/log-analytics-data-sources-syslog.md)。
