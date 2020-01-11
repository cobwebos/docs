---
title: 如何调试 Udf-Azure 数字孪生 |Microsoft Docs
description: 了解调试 Azure 数字孪生中的用户定义函数的建议方法。
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/22/2019
ms.custom: seodec18
ms.openlocfilehash: 94038d743bf0aafaadb4693ffc48108e5351bca4
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/10/2020
ms.locfileid: "75863540"
---
# <a name="how-to-debug-user-defined-functions-in-azure-digital-twins"></a>如何在 Azure 数字孪生中调试用户定义的函数

本文总结了如何诊断和调试 Azure 数字孪生中的用户定义函数。 然后，它介绍了调试这些函数时遇到的一些最常见情况。

>[!TIP]
> 若要详细了解如何使用活动日志、诊断日志和 Azure Monitor 在 Azure 数字孪生中设置调试工具，请阅读[如何配置监视和日志记录](./how-to-configure-monitoring.md)。

## <a name="debug-issues"></a>调试问题

知道如何诊断 Azure 数字孪生中的问题，您可以有效地分析问题，确定问题的原因，并为他们提供合适的解决方案。

提供各种日志记录、分析和诊断工具。

### <a name="enable-logging-for-your-instance"></a>为实例启用日志记录

Azure 数字孪生支持可靠的日志记录、监视和分析。 解决方案开发人员可以使用 Azure Monitor 日志、诊断日志、活动日志和其他服务来支持 IoT 应用的复杂监视需求。 可以将日志记录选项组合在一起，用于查询或显示多个服务的记录，并为许多服务提供精细的日志记录范围。

* 有关特定于 Azure 数字孪生的日志记录配置，请参阅[如何配置监视和日志记录](./how-to-configure-monitoring.md)。
* 请参阅[Azure Monitor](../azure-monitor/overview.md)概述，了解通过 Azure Monitor 启用了强大的日志设置。
* 查看文章[从 azure 资源收集和使用日志数据](../azure-monitor/platform/platform-logs-overview.md)，以便通过 Azure 门户、Azure CLI 或 PowerShell 配置 Azure 数字孪生中的诊断日志设置。

配置后，你将能够选择所有日志类别和指标，并使用强大的 Azure Monitor log analytics 工作区来支持调试工作。

### <a name="trace-sensor-telemetry"></a>跟踪传感器遥测数据

若要跟踪传感器遥测数据，请验证为 Azure 数字孪生实例启用了诊断设置。 然后，确保选择所需的全部日志类别。 最后，确认所需的日志将发送到 Azure Monitor 日志。

要将传感器遥测数据消息与其各自的日志进行匹配，可以在要发送的事件数据上指定相关 ID。 为此，请将 `x-ms-client-request-id` 属性设置为 GUID。

发送遥测数据后，请打开 Azure Monitor log analytics，以使用设置的相关 ID 查询日志：

```Kusto
AzureDiagnostics
| where CorrelationId == 'YOUR_CORRELATION_IDENTIFIER'
```

| 查询值 | 替换为 |
| --- | --- |
| YOUR_CORRELATION_IDENTIFIER | 已在事件数据中指定的相关 ID |

阅读所有最近的遥测日志查询：

```Kusto
AzureDiagnostics
| order by CorrelationId desc
```

如果为用户定义函数启用日志记录，则这些日志将显示在 log analytics 实例中，其类别 `UserDefinedFunction`。 若要检索它们，请在 log analytics 中输入以下查询条件：

```Kusto
AzureDiagnostics
| where Category == 'UserDefinedFunction'
```

有关功能强大的查询操作的详细信息，请阅读[开始使用查询](../azure-monitor/log-query/get-started-queries.md)。

## <a name="identify-common-issues"></a>识别常见问题

在对解决方案进行故障排查时，诊断和识别常见问题都很重要。 下面的各个子部分汇总了开发用户定义的函数时经常遇到的几个问题。

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

### <a name="check-if-a-role-assignment-was-created"></a>检查是否创建了角色分配

如果没有在管理 API 中创建角色分配，用户定义的函数将无权执行任何操作，例如发送通知、检索元数据以及在拓扑中设置计算值。

通过管理 API 检查用户定义的函数是否存在角色分配：

```URL
GET YOUR_MANAGEMENT_API_URL/roleassignments?path=/&traverse=Down&objectId=YOUR_USER_DEFINED_FUNCTION_ID
```

| 参数值 | 替换为 |
| --- | --- |
| YOUR_USER_DEFINED_FUNCTION_ID | 要检索其角色分配的用户定义的函数 ID|

如果不存在任何角色分配，请了解[如何为用户定义的函数创建角色分配](./how-to-user-defined-functions.md)。

### <a name="check-if-the-matcher-works-for-a-sensors-telemetry"></a>检查匹配程序是否针对传感器的遥测数据工作

通过针对 Azure 数字孪生实例的管理 API 的以下调用，能够确定给定的匹配程序是否适用于给定的传感器。

```URL
GET YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_IDENTIFIER/evaluate/YOUR_SENSOR_IDENTIFIER?enableLogging=true
```

| 参数 | 替换为 |
| --- | --- |
| *YOUR_MATCHER_IDENTIFIER* | 要评估的匹配程序的 ID |
| *YOUR_SENSOR_IDENTIFIER* | 要评估的传感器的 ID |

响应：

```JavaScript
{
    "success": true,
    "logs": [
        "$.dataType: \"Motion\" Equals \"Motion\" => True"
    ]
}
```

### <a name="check-what-a-sensor-triggers"></a>检查传感器要触发的内容

通过针对 Azure 数字孪生管理 API 的以下调用，能够确定由给定传感器的传入遥测数据触发的用户定义的函数的标识符：

```URL
GET YOUR_MANAGEMENT_API_URL/sensors/YOUR_SENSOR_IDENTIFIER/matchers?includes=UserDefinedFunctions
```

| 参数 | 替换为 |
| --- | --- |
| *YOUR_SENSOR_IDENTIFIER* | 要发送遥测数据的传感器的 ID |

响应：

```JavaScript
[
    {
        "id": "48a64768-797e-4832-86dd-de625f5f3fd9",
        "name": "MotionMatcher",
        "spaceId": "2117b3e1-b6ce-42c1-9b97-0158bef59eb7",
        "conditions": [
            {
                "id": "024a067a-414f-415b-8424-7df61392541e",
                "target": "Sensor",
                "path": "$.dataType",
                "value": "\"Motion\"",
                "comparison": "Equals"
            }
        ],
        "userDefinedFunctions": [
            {
                "id": "373d03c5-d567-4e24-a7dc-f993460120fc",
                "spaceId": "2117b3e1-b6ce-42c1-9b97-0158bef59eb7",
                "name": "Motion User-Defined Function",
                "disabled": false
            }
        ]
    }
]
```

### <a name="issue-with-receiving-notifications"></a>接收通知的问题

如果未从触发的用户定义函数收到通知，请确认拓扑对象类型参数是否与正在使用的标识符的类型匹配。

不正确示例：

```JavaScript
var customNotification = {
    Message: 'Custom notification that will not work'
};

sendNotification(telemetry.SensorId, "Space", JSON.stringify(customNotification));
```

出现这种情况是因为使用的标识符指的是传感器，而指定的拓扑对象类型是 `Space`。

正确示例：

```JavaScript
var customNotification = {
    Message: 'Custom notification that will work'
};

sendNotification(telemetry.SensorId, "Sensor", JSON.stringify(customNotification));
```

避免遇到此问题的最简单方法是在元数据对象上使用 `Notify` 方法。

示例：

```JavaScript
function process(telemetry, executionContext) {
    var sensorMetadata = getSensorMetadata(telemetry.SensorId);

    var customNotification = {
        Message: 'Custom notification'
    };

    // Short-hand for above methods where object type is known from metadata.
    sensorMetadata.Notify(JSON.stringify(customNotification));
}
```

## <a name="common-diagnostic-exceptions"></a>常见诊断异常

如果启用诊断设置，可能会遇到以下常见异常：

1. 限制：如果用户定义的函数超出[服务限制](./concepts-service-limits.md)一文中列出的执行速率限制，它将受到限制。 在限制失效之前，任何进一步的操作都不会成功执行。

1. **找不到数据：** 如果用户定义的函数尝试访问不存在的元数据，操作将失败。

1. **未经授权：** 如果用户定义的函数没有设置角色分配或缺少从拓扑中访问某些元数据的足够权限，操作将失败。

## <a name="next-steps"></a>后续步骤

- 了解如何在 Azure 数字孪生中启用[监视和日志](./how-to-configure-monitoring.md)。

- 有关 Azure 日志记录选项的详细信息，请参阅[Azure 活动日志概述一](../azure-monitor/platform/platform-logs-overview.md)文。
