---
title: 设置诊断
titleSuffix: Azure Digital Twins
description: 请参阅如何使用诊断设置启用日志记录。
author: baanders
ms.author: baanders
ms.date: 7/28/2020
ms.topic: troubleshooting
ms.service: digital-twins
ms.openlocfilehash: fc397b6d6beb719e11dc3959bbcf4d75c08a8dda
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2020
ms.locfileid: "88723922"
---
# <a name="troubleshooting-azure-digital-twins-diagnostics-logging"></a>Azure 数字孪生故障排除：诊断日志记录

Azure 数字孪生收集提供有关资源状态的信息的服务实例的 [指标](troubleshoot-metrics.md) 。 你可以使用这些度量值来评估 Azure 数字孪生服务及其连接到的资源的整体运行状况。 这些面向用户的统计信息可帮助你查看 Azure 数字孪生所发生的情况，并帮助对问题执行根本原因分析，而无需联系 Azure 支持部门。

本文介绍如何从 Azure 数字孪生实例为指标数据启用 **诊断日志记录** 。 您可以使用这些日志来帮助您解决服务问题，并将诊断设置配置为将 Azure 数字孪生指标发送到不同的目标。 有关这些设置的详细信息，请参阅 [*创建诊断设置以将平台日志和指标发送到不同的目标*](../azure-monitor/platform/diagnostic-settings.md)。

## <a name="turn-on-diagnostic-settings-with-the-azure-portal"></a>启用 Azure 门户的诊断设置

下面介绍如何为 Azure 数字孪生实例启用诊断设置：

1. 登录到 [Azure 门户](https://portal.azure.com) 并导航到 Azure 数字孪生实例。 可以通过在门户搜索栏中键入其名称来找到它。 

2. 从菜单中选择 " **诊断设置** "，然后单击 " **添加诊断设置**"。

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings.png" alt-text="显示 诊断设置 页和要添加的按钮的屏幕截图":::

3. 在下面的页面上，填写以下值：
     * **诊断设置名称**：为诊断设置指定一个名称。
     * **类别详细信息**：选择要监视的操作，并选中相应的复选框以对这些操作启用诊断。 诊断设置可以报告的操作如下：
        - DigitalTwinsOperation
        - EventRoutesOperation
        - ModelsOperation
        - QueryOperation
        - AllMetrics
        
        有关这些选项的更多详细信息，请参阅下面的 [*类别详细信息*](#category-details) 部分。
     * **目标详细信息**：选择要将日志发送到的位置。 可选择以下三个选项的任意组合：
        - 发送到 Log Analytics
        - 存档到存储帐户
        - 流式传输到事件中心

        如果目标选择需要，系统可能会要求你填写其他详细信息。  
    
4. 保存新设置。 

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings-details.png" alt-text="显示 诊断设置 页的屏幕截图，其中用户填写了诊断设置名称，并对类别详细信息和目标详细信息进行了一些复选框选择。突出显示 保存 按钮。":::

新设置在大约 10 分钟后生效。 之后，日志会在实例的 " **诊断设置** " 页上显示在配置的目标中。 

## <a name="category-details"></a>类别详细信息

下面更详细地介绍了在设置诊断设置时，可在 " **类别详细信息** " 下选择的日志类别。

| 日志类别 | 说明 |
| --- | --- |
| ADTModelsOperation | 记录与模型有关的所有 API 调用 |
| ADTQueryOperation | 记录与查询相关的所有 API 调用 |
| ADTEventRoutesOperation | 记录与事件路由相关的所有 API 调用，以及从 Azure 数字孪生传出事件到终结点服务（如事件网格、事件中心和服务总线）的事件 |
| ADTDigitalTwinsOperation | 记录与 Azure 数字孪生有关的所有 API 调用 |

每个日志类别由写入、读取、删除和操作操作组成。  它们映射到 REST API 调用，如下所示：

| 事件类型 | REST API 操作 |
| --- | --- |
| 写入 | PUT 和 PATCH |
| 读取 | GET |
| 删除 | DELETE |
| 操作 | POST |

下面是在每个类别中记录的操作和相应的 [Azure 数字孪生 REST API 调用](https://docs.microsoft.com/rest/api/azure-digitaltwins/) 的完整列表。 

>[!NOTE]
> 每个日志类别包含若干个操作/REST API 调用。 在下表中，每个日志类别映射到其下的所有操作/REST API 调用，直到列出下一个日志类别。 

| 日志类别 | 操作 | REST API 调用和其他事件 |
| --- | --- | --- |
| ADTModelsOperation | DigitalTwins/模型/写入 | 数字克隆模型更新 API |
|  | DigitalTwins/模型/读取 | 数字克隆模型按 ID 和列表 Api 获取 |
|  | DigitalTwins/模型/删除 | 数字克隆模型删除 API |
|  | DigitalTwins/模型/操作 | 数字克隆模型添加 API |
| ADTQueryOperation | DigitalTwins/查询/操作 | 查询孪生 API |
| ADTEventRoutesOperation | DigitalTwins/eventroutes/write | 事件路由添加 API |
|  | DigitalTwins/eventroutes/read | 事件路由由 ID 和列表 Api 获取 |
|  | DigitalTwins/eventroutes/delete | 事件路由删除 API |
|  | DigitalTwins/eventroutes/action | 尝试将事件发布到终结点服务时失败， (不是 API 调用)  |
| ADTDigitalTwinsOperation | DigitalTwins/DigitalTwins/write | 数字孪生添加、添加关系、更新、更新组件 |
|  | DigitalTwins/DigitalTwins/read | 数字孪生按 ID、获取组件、按 ID 获取关系、列出传入关系、列表关系 |
|  | DigitalTwins/DigitalTwins/delete | 数字孪生删除，删除关系 |
|  | DigitalTwins/DigitalTwins/action | 数字孪生发送组件遥测数据，发送遥测数据 |

## <a name="log-schemas"></a>日志架构 

每个日志类别都有一个架构，该架构定义如何报告该类别中的事件。 每个单独的日志条目都作为文本存储，并格式化为 JSON blob。 下面为每种日志类型提供了日志中的字段和示例 JSON 正文。 

`ADTDigitalTwinsOperation`、 `ADTModelsOperation` 和 `ADTQueryOperation` 使用一致的 API 日志架构; `ADTEventRoutesOperation` 具有其自己的单独架构。

### <a name="api-log-schemas"></a>API 日志架构

此日志架构对于、和是一致的 `ADTDigitalTwinsOperation` `ADTModelsOperation` `ADTQueryOperation` 。 它包含与 Azure 数字孪生实例的 API 调用相关的信息。

下面是 API 日志的字段和属性描述。

| 字段名称 | 数据类型 | 说明 |
|-----|------|-------------|
| `Time` | DateTime | 此事件发生的日期和时间（UTC） |
| `ResourceID` | 字符串 | 发生事件的资源的 Azure 资源管理器资源 ID |
| `OperationName` | 字符串  | 事件期间执行的操作类型 |
| `OperationVersion` | 字符串 | 事件期间使用的 API 版本 |
| `Category` | 字符串 | 正在发出的资源的类型 |
| `ResultType` | 字符串 | 事件的结果 |
| `ResultSignature` | 字符串 | 事件的 Http 状态代码 |
| `ResultDescription` | 字符串 | 有关事件的其他详细信息 |
| `DurationMs` | 字符串 | 执行事件所花的时间（以毫秒为单位） |
| `CallerIpAddress` | 字符串 | 事件的掩码源 IP 地址 |
| `CorrelationId` | Guid | 客户提供的事件的唯一标识符 |
| `Level` | 字符串 | 事件的日志记录严重性 |
| `Location` | 字符串 | 发生事件的区域 |
| `RequestUri` | Uri | 事件发生时使用的终结点 |

下面是这些日志类型的示例 JSON 正文。

#### <a name="adtdigitaltwinsoperation"></a>ADTDigitalTwinsOperation

```json
{
  "time": "2020-03-14T21:11:14.9918922Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/digitaltwins/write",
  "operationVersion": "2020-05-31-preview",
  "category": "DigitalTwinOperation",
  "resultType": "Success",
  "resultSignature": "200",
  "resultDescription": "",
  "durationMs": "314",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "2f6a8e64-94aa-492a-bc31-16b9f0b16ab3",
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/digitaltwins/factory-58d81613-2e54-4faa-a930-d980e6e2a884?api-version=2020-05-31-preview"
}
```

#### <a name="adtmodelsoperation"></a>ADTModelsOperation

```json
{
  "time": "2020-10-29T21:12:24.2337302Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/models/write",
  "operationVersion": "2020-05-31-preview",
  "category": "ModelsOperation",
  "resultType": "Success",
  "resultSignature": "201",
  "resultDescription": "",
  "durationMs": "935",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "9dcb71ea-bb6f-46f2-ab70-78b80db76882",
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/Models?api-version=2020-05-31-preview",
}
```

#### <a name="adtqueryoperation"></a>ADTQueryOperation

```json
{
  "time": "2020-12-04T21:11:44.1690031Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/query/action",
  "operationVersion": "2020-05-31-preview",
  "category": "QueryOperation",
  "resultType": "Success",
  "resultSignature": "200",
  "resultDescription": "",
  "durationMs": "255",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "1ee2b6e9-3af4-4873-8c7c-1a698b9ac334",
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/query?api-version=2020-05-31-preview",
}
```

### <a name="egress-log-schemas"></a>出口日志架构

这是日志的架构 `ADTEventRoutesOperation` 。 其中包含有关异常的详细信息，以及有关连接到 Azure 数字孪生实例的出口终结点的 API 操作的详细信息。

|字段名称 | 数据类型 | 说明 |
|-----|------|-------------|
| `Time` | DateTime | 此事件发生的日期和时间（UTC） |
| `ResourceId` | 字符串 | 发生事件的资源的 Azure 资源管理器资源 ID |
| `OperationName` | 字符串  | 事件期间执行的操作类型 |
| `Category` | 字符串 | 正在发出的资源的类型 |
| `ResultDescription` | 字符串 | 有关事件的其他详细信息 |
| `Level` | 字符串 | 事件的日志记录严重性 |
| `Location` | 字符串 | 发生事件的区域 |
| `EndpointName` | 字符串 | 在 Azure 数字孪生中创建的出口终结点的名称 |

下面是这些日志类型的示例 JSON 正文。

#### <a name="adteventroutesoperation"></a>ADTEventRoutesOperation

```json
{
  "time": "2020-11-05T22:18:38.0708705Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/eventroutes/action",
  "category": "EventRoutesOperation",
  "resultDescription": "Unable to send EventGrid message to [my-event-grid.westus-1.eventgrid.azure.net] for event Id [f6f45831-55d0-408b-8366-058e81ca6089].",
  "correlationId": "7f73ab45-14c0-491f-a834-0827dbbf7f8e",
  "level": "3",
  "location": "southcentralus",
  "properties": {
    "endpointName": "endpointEventGridInvalidKey"
  }
}
```

## <a name="next-steps"></a>后续步骤

* 有关配置诊断的详细信息，请参阅 [*从 Azure 资源收集和使用日志数据*](../azure-monitor/platform/platform-logs-overview.md)。
* 有关 Azure 数字孪生指标的信息，请参阅 [*故障排除：查看包含 Azure Monitor 的指标*](troubleshoot-metrics.md)。
* 若要了解如何启用指标警报，请参阅 [*故障排除：设置警报*](troubleshoot-alerts.md)。
