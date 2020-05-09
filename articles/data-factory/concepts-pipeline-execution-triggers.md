---
title: Azure 数据工厂中的管道执行和触发器
description: 本文介绍了如何在 Azure 数据工厂中按需执行管道，或者通过创建触发器来执行管道。
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/05/2018
ms.openlocfilehash: a31f800ad157e22f3d35abae3d3b714fa29178ef
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82562196"
---
# <a name="pipeline-execution-and-triggers-in-azure-data-factory"></a>Azure 数据工厂中的管道执行和触发器

> [!div class="op_single_selector" title1="选择要使用的数据工厂服务的版本："]
> * [版本 1](v1/data-factory-scheduling-and-execution.md)
> * [当前版本](concepts-pipeline-execution-triggers.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

 Azure 数据工厂中的“管道运行”用于定义管道执行实例。 例如，假设你有一个管道，分别在上午 8:00、9:00 和 10:00 点执行。 在这种情况下，将分三次单独运行管道，也即有三次管道运行。 每次管道运行都有唯一的管道运行 ID。 运行 ID 是一个 GUID，用于对该特定的管道运行进行唯一定义。

管道运行通常通过将自变量传递给管道中定义的参数进行实例化。 执行管道时，可以手动，也可以  使用触发器。 本文提供了有关执行管道的两种方式的详细信息。

## <a name="manual-execution-on-demand"></a>手动执行（按需）

管道的手动执行也称为按需执行。 

例如，假设有一个需要执行的名为 **copyPipeline** 的基本管道。 该管道有一项活动，可以将数据从 Azure Blob 存储源文件夹复制到同一存储中的目标文件夹。 下面的 JSON 定义显示此示例管道：

```json
{
    "name": "copyPipeline",
    "properties": {
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "name": "CopyBlobtoBlob",
                "inputs": [
                    {
                        "referenceName": "sourceBlobDataset",
                        "type": "DatasetReference"
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "sinkBlobDataset",
                        "type": "DatasetReference"
                    }
                ]
            }
        ],
        "parameters": {
            "sourceBlobContainer": {
                "type": "String"
            },
            "sinkBlobContainer": {
                "type": "String"
            }
        }
    }
}
```

在 JSON 定义中，管道采用两个参数：**sourceBlobContainer** 和 **sinkBlobContainer**。 你在运行时将值传递给这些参数。

可通过以下方法之一手动运行管道：
- .NET SDK
- Azure PowerShell 模块
- REST API
- Python SDK

### <a name="rest-api"></a>REST API

以下示例命令演示了如何通过手动方式使用 REST API 来运行管道：

```
POST
https://management.azure.com/subscriptions/mySubId/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory/pipelines/copyPipeline/createRun?api-version=2017-03-01-preview
```

有关完整示例，请参阅[快速入门：使用 REST API 创建数据工厂](quickstart-create-data-factory-rest-api.md)。

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

以下示例命令演示了如何使用 Azure PowerShell 手动运行管道：

```powershell
Invoke-AzDataFactoryV2Pipeline -DataFactory $df -PipelineName "Adfv2QuickStartPipeline" -ParameterFile .\PipelineParameters.json
```

请在请求有效负载的正文中传递参数。 在 .NET SDK、Azure PowerShell 和 Python SDK 中，请在作为参数传递给调用的字典中传递值：

```json
{
  "sourceBlobContainer": "MySourceFolder",
  "sinkBlobContainer": "MySinkFolder"
}
```

响应有效负载是管道运行的唯一 ID：

```json
{
  "runId": "0448d45a-a0bd-23f3-90a5-bfeea9264aed"
}
```

有关完整示例，请参阅[快速入门：使用 Azure PowerShell 创建数据工厂](quickstart-create-data-factory-powershell.md)。

### <a name="net-sdk"></a>.NET SDK

以下示例调用演示了如何通过手动方式使用 .NET SDK 来运行管道：

```csharp
client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, parameters)
```

有关完整示例，请参阅[快速入门：使用 .NET SDK 创建数据工厂](quickstart-create-data-factory-dot-net.md)。

> [!NOTE]
> 可以使用 .NET SDK 从 Azure Functions、你的 Web 服务等位置调用数据工厂管道。

## <a name="trigger-execution"></a>触发器执行

触发器是可以执行管道运行的另一种方法。 触发器表示一个处理单元，用于确定何时需要启动管道执行。 目前，数据工厂支持三种类型的触发器：

- 计划触发器：按时钟计划调用管道的触发器。

- 翻转窗口触发器：一种触发器，可以定期运行，同时还能保留状态。

- 基于事件的触发器：响应某个事件的触发器。

管道和触发器具有多对多关系（翻转窗口触发器除外）。多个触发器可以启动单个管道，或者单个触发器可以启动多个管道。 在以下触发器定义中，pipelines  属性是指一系列由特定的触发器触发的管道。 属性定义包括管道参数的值。
### <a name="basic-trigger-definition"></a>基本的触发器定义

```json
{
    "properties": {
        "name": "MyTrigger",
        "type": "<type of trigger>",
        "typeProperties": {...},
        "pipelines": [
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "<Name of your pipeline>"
                },
                "parameters": {
                    "<parameter 1 Name>": {
                        "type": "Expression",
                        "value": "<parameter 1 Value>"
                    },
                    "<parameter 2 Name>": "<parameter 2 Value>"
                }
            }
        ]
    }
}
```

## <a name="schedule-trigger"></a>计划触发器
计划触发器按时钟计划运行管道。 此触发器支持定期和高级日历选项。 例如，此触发器支持“每周”或“星期一下午 5:00 和星期四晚上 9:00”之类的时间间隔。 计划触发器很灵活，因为数据集模式与数据类型无关，该触发器不区分时序数据和非时序数据。

如需计划触发器的详细信息和示例，请参阅[创建计划触发器](how-to-create-schedule-trigger.md)。

## <a name="schedule-trigger-definition"></a>计划触发器定义
创建计划触发器时，请使用 JSON 定义指定计划和定期触发。

若要使用计划触发器启动管道运行，请在触发器定义中包括特定管道的管道引用。 管道和触发器具有“多对多”关系。 多个触发器可以启动单个管道。 单个触发器可以启动多个管道。

```json
{
  "properties": {
    "type": "ScheduleTrigger",
    "typeProperties": {
      "recurrence": {
        "frequency": <<Minute, Hour, Day, Week, Year>>,
        "interval": <<int>>, // How often to fire
        "startTime": <<datetime>>,
        "endTime": <<datetime>>,
        "timeZone": "UTC",
        "schedule": { // Optional (advanced scheduling specifics)
          "hours": [<<0-24>>],
          "weekDays": [<<Monday-Sunday>>],
          "minutes": [<<0-60>>],
          "monthDays": [<<1-31>>],
          "monthlyOccurrences": [
            {
              "day": <<Monday-Sunday>>,
              "occurrence": <<1-5>>
            }
          ]
        }
      }
    },
  "pipelines": [
    {
      "pipelineReference": {
        "type": "PipelineReference",
        "referenceName": "<Name of your pipeline>"
      },
      "parameters": {
        "<parameter 1 Name>": {
          "type": "Expression",
          "value": "<parameter 1 Value>"
        },
        "<parameter 2 Name>": "<parameter 2 Value>"
      }
    }
  ]}
}
```

> [!IMPORTANT]
> **parameters** 属性是 **pipelines** 元素的必需属性。 如果管道不使用任何参数，则必须包括一个空的针对 **parameters** 属性的 JSON 定义。

### <a name="schema-overview"></a>架构概述
下表概述了与触发器的定期触发和计划相关的主要架构元素：

| JSON 属性 | 说明 |
|:--- |:--- |
| **startTime** | 一个日期时间值。 对于基本的计划，**startTime** 属性的值适用于第一个匹配项。 对于复杂的计划，触发器的启动时间不早于指定的 **startTime** 值。 |
| **endTime** | 触发器的结束日期和时间。 该触发器不在指定的结束日期和时间之后执行。 属性的值不能是过去的时间。 <!-- This property is optional. --> |
| **timeZone** | 时区。 目前仅支持 UTC 时区。 |
| **recurrence** | 一个 recurrence 对象，指定触发器的定期触发规则。 recurrence 对象支持 **frequency**、**interval**、**endTime**、**count** 和 **schedule** 元素。 定义 recurrence 对象时，**frequency** 元素是必需的。 recurrence 对象的其他元素为可选元素。 |
| **frequency** | 触发器定期触发的频率单位。 支持的值包括 "minute"、"hour"、"day"、"week" 和 "month"。 |
| **interval** | 一个正整数，表示 **frequency** 值对应的时间间隔。 **frequency** 值决定了触发器的运行频率。 例如，如果 **interval** 为 3，**frequency** 为 "week"，则触发器每三周定期触发一次。 |
| **schedule** | 触发器的定期触发计划。 指定了 **frequency** 值的触发器会根据定期触发计划更改其定期触发。 **schedule** 属性包含对定期触发的修改，这些修改是按分钟、小时、工作日、月份日期和周次进行的。

### <a name="schedule-trigger-example"></a>计划触发器示例

```json
{
  "properties": {
    "name": "MyTrigger",
    "type": "ScheduleTrigger",
    "typeProperties": {
      "recurrence": {
        "frequency": "Hour",
        "interval": 1,
        "startTime": "2017-11-01T09:00:00-08:00",
        "endTime": "2017-11-02T22:00:00-08:00"
      }
    },
    "pipelines": [{
        "pipelineReference": {
          "type": "PipelineReference",
          "referenceName": "SQLServerToBlobPipeline"
        },
        "parameters": {}
      },
      {
        "pipelineReference": {
          "type": "PipelineReference",
          "referenceName": "SQLServerToAzureSQLPipeline"
        },
        "parameters": {}
      }
    ]
  }
}
```

### <a name="schema-defaults-limits-and-examples"></a>架构默认值、限制和示例

| JSON 属性 | 类型 | 必须 | 默认值 | 有效值 | 示例 |
|:--- |:--- |:--- |:--- |:--- |:--- |
| **startTime** | string | 是 | 无 | ISO 8601 日期时间 | `"startTime" : "2013-01-09T09:30:00-08:00"` |
| **recurrence** | object | 是 | 无 | recurrence 对象 | `"recurrence" : { "frequency" : "monthly", "interval" : 1 }` |
| **interval** | number | 否 | 1 | 1 到 1000 | `"interval":10` |
| **endTime** | string | 是 | 无 | 代表将来某个时间的日期/时间值 | `"endTime" : "2013-02-09T09:30:00-08:00"` |
| **schedule** | object | 否 | 无 | schedule 对象 | `"schedule" : { "minute" : [30], "hour" : [8,17] }` |

### <a name="starttime-property"></a>startTime 属性
下表说明了 **startTime** 属性如何控制触发器运行：

| startTime 值 | 不按计划循环 | 按计划循环 |
|:--- |:--- |:--- |
| **开始时间在过去** | 计算开始时间后的第一个将来执行时间，并在该时间运行。<br /><br />根据上次执行时间进行计算后，运行后续执行。<br /><br />请参阅此表后面的示例。 | 触发器不会早于指定的开始时间  启动。 第一次循环基于从开始时间计算的计划。<br /><br />后续执行的运行取决于定期触发计划。 |
| **开始时间在将来或当前** | 在指定的开始时间运行一次。<br /><br />根据上次执行时间进行计算后，运行后续执行。 | 触发器不会早于指定的开始时间  启动。 第一次循环基于从开始时间计算的计划。<br /><br />后续执行的运行取决于定期触发计划。 |

让我们查看一个示例，了解在开始时间是过去的时间，并且指定了定期触发但未指定计划的情况下，会发生什么事情。 假设当前时间为 2017-04-08 13:00，开始时间为 2017-04-07 14:00，定期触发为每两天触发一次。 （定义 **recurrence** 值时，可将 **frequency** 属性设置为 "day"，**interval** 属性设置为 2。）请注意，**startTime** 值为过去的时间，发生在当前时间之前。

在上述情况下，首次执行时间为 2017-04-09 14:00。 计划程序引擎从开始时间计算执行循环。 过去的所有实例会被丢弃。 引擎将使用将来发生的下一个实例。 在这种情况下，开始时间为 2017-04-07 的下午 2:00。 下一个实例为从该时间算起的 2 天，即 2017-04-09 的下午 2:00。

首次执行时间是相同的，不管 **startTime** 是 2017-04-05 14:00 还是 2017-04-01 14:00。 在首次执行后，使用该计划计算后续执行。 因此，后续的执行时间是 2017-04-11 下午 2:00，然后是 2017-04-13 下午 2:00，再然后是 2017-04-15 下午 2:00，依此类推。

最后，如果没有在触发器的计划中设置小时或分钟，则会将第一次执行时对应的小时或分钟值用作默认值。

### <a name="schedule-property"></a>schedule 属性
可以使用 **schedule** 来限制触发器执行的次数。  例如，如果触发器的频率为按月，根据计划仅在第 31 天运行，则该触发器仅在有 31 日的月份运行。

还可以使用 **schedule** 来扩展触发器执行的次数。  例如，如果触发器的频率为按月，根据计划在每月的第 1 天和第 2 天运行，则该触发器会在当月的第 1 天和第 2 天运行，而不是每月运行一次。

如果指定了多个 schedule  元素，则求值顺序为从大到小的计划设置：周次、月份日期、星期、小时、分钟。

下表详细描述了 **schedule** 元素：

| JSON 元素 | 说明 | 有效值 |
|:--- |:--- |:--- |
| **minutes** | 运行触发器的小时中的分钟。 |- 整数<br />- 整数数组|
| **小时数** | 运行触发器的日期中的小时。 |- 整数<br />- 整数数组|
| **工作日** | 运行触发器的星期日期。 此值只能使用与星期相关的频率来指定。|<br />- Monday<br />- Tuesday<br />- Wednesday<br />- Thursday<br />- Friday<br />- Saturday<br />- Sunday<br />- 星期值的数组（最大数组值为 7）<br /><br />星期值不区分大小写|
| **monthlyOccurrences** | 运行触发器的月份日期。 此值只能使用与月份相关的频率来指定。 |- **monthlyOccurrence** 对象的数组：`{ "day": day, "occurrence": occurrence }`<br />- **day** 属性表示运行触发器那天为星期几。 例如，如果 **monthlyOccurrences** 属性的 **day** 值为 `{Sunday}`，则表示在当月的每个星期日运行触发器。 **day** 属性是必需的。<br />- **occurrence** 属性是指定的 **day** 在当月的匹配项。 例如，如果 **monthlyOccurrences** 属性的 **day** 和 **occurrence** 值为 `{Sunday, -1}`，则表示在当月的最后一个星期日运行触发器。 **occurrence** 属性是可选的。|
| **monthDays** | 运行触发器的月份日期。 此值只能使用与月份相关的频率来指定。 |<= -1 且 >= -31 的任意值<br />>= 1 且 <= 31 的任意值<br />- 值组成的数组|

## <a name="tumbling-window-trigger"></a>翻转窗口触发器
翻转窗口触发器是一类可以在保留状态的同时按周期性的时间间隔（从指定的开始时间算起）触发的触发器。 翻转窗口是一系列固定大小、非重叠且连续的时间间隔。

如需翻转窗口触发器的详细信息和示例，请参阅[创建翻转窗口触发器](how-to-create-tumbling-window-trigger.md)。

> [!NOTE]
> 翻转窗口触发器运行*等待触发的管道运行*完成。 其运行状态反映触发的管道运行的状态。 例如，如果已取消触发管道运行，则相应的翻转窗口触发器运行将被标记为已取消。 这不同于计划触发器的 "火灾和遗忘" 行为，只要启动管道运行，就会将其标记为成功。

## <a name="event-based-trigger"></a>基于事件的触发器

基于事件的触发器在 Azure Blob 存储中通过运行管道来响应某个事件，例如某个文件已到达，或者某个文件已删除。

若要详细了解基于事件的触发器，请参阅[创建可以通过运行管道来响应事件的触发器](how-to-create-event-trigger.md)。

## <a name="examples-of-trigger-recurrence-schedules"></a>触发器定期触发计划示例
此部分提供定期触发计划的示例， 重点介绍 **schedule** 对象及其元素。

这些示例假定 interval  值为 1，且根据计划定义，frequency  值是正确的。 例如，不能在 **frequency** 值为 "day" 的同时，在 **schedule** 对象中有一个 **monthDays** 修改项。 这些类型的限制在上一部分的表中已说明过。

| 示例 | 说明 |
|:--- |:--- |
| `{"hours":[5]}` | 在每天早晨 5:00 运行。 |
| `{"minutes":[15], "hours":[5]}` | 在每天早晨 5:15 运行。 |
| `{"minutes":[15], "hours":[5,17]}` | 在每天早晨 5:15 和下午 5:15 运行。 |
| `{"minutes":[15,45], "hours":[5,17]}` | 在每天早晨 5:15、5:45 和下午 5:15、5:45 运行。 |
| `{"minutes":[0,15,30,45]}` | 每 15 分钟运行一次。 |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` | 每小时运行一次。<br /><br />此触发器每小时运行一次。 如果指定了值，则分钟由 **startTime** 值控制。 如果未指定值，则分钟由创建时间控制。 例如，如果开始时间或创建时间（以适用的为准）为中午 12:25，则触发器会在 00:25、01:25、02:25 ... 23:25 运行。<br /><br />该计划相当于为触发器指定了 **frequency** 值 "hour"、**interval** 值 1，无 **schedule**。 可以使用 **frequency** 和 **interval** 值不同于此的该计划来创建其他触发器。 例如，如果 **frequency** 值为 "month"，则此计划每月仅运行一次，而不是每天运行一次（如果 **frequency** 值为 "day"）。 |
| `{"minutes":[0]}` | 每小时整点运行。<br /><br />此触发器每小时整点运行，开始时间为晚上 12:00、凌晨 1:00、凌晨 2:00，依此类推。<br /><br />此计划相当于 **frequency** 值为 "hour"，**startTime** 值为零分钟，没有 **schedule** 但 **frequency** 值为 "day" 的触发器。 如果 **frequency** 值为 "week" 或 "month"，则此计划会相应地在每周的某一天或每月的某一天执行。 |
| `{"minutes":[15]}` | 在整点的 15 分钟过后运行。<br /><br />此触发器每小时在整点过后 15 分钟运行，开始时间为凌晨 00:15、凌晨 1:15、凌晨 2:15，依此类推，结束时间为晚上 11:15。 |
| `{"hours":[17], "weekDays":["saturday"]}` | 在每周星期六的下午 5:00 运行。 |
| `{"hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | 在每周星期一、星期三和星期五的下午 5:00 运行。 |
| `{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | 在每周星期一、星期三和星期五的下午 5:15 和下午 5:45 运行。 |
| `{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | 在工作日每 15 分钟运行一次。 |
| `{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | 在工作日的早晨 9:00 到下午 4:45 之间每 15 分钟运行一次。 |
| `{"weekDays":["tuesday", "thursday"]}` | 在星期二和星期四的指定开始时间运行。 |
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` | 在每月 28 日的早晨 6:00 运行（假设 **frequency** 值为 "month"）。 |
| `{"minutes":[0], "hours":[6], "monthDays":[-1]}` | 在当月最后一天的早晨 6:00 运行。<br /><br />若要在某个月的最后一天运行触发器，请使用 -1 而不是 28 日、29 日、30 日或 31 日。 |
| `{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` | 在每月第一天和最后一天的早晨 6:00 运行。 |
| `{monthDays":[1,14]}` | 在每月第一天和第 14 天的指定开始时间运行。 |
| `{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | 在每月第一个星期五的早晨 5:00 运行。 |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | 在每月第一个星期五的指定开始时间运行。 |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` | 在每月从月末算起的第三个星期五的指定开始时间运行。 |
| `{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | 在每月第一个和最后一个星期五的早晨 5:15 运行。 |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | 在每月第一个和最后一个星期五的指定开始时间运行。 |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` | 在每月第五个星期五的指定开始时间运行。<br /><br />当月份中没有第五个星期五时，不运行管道。 若要在月份的最后一个星期五运行触发器，可以考虑使用 -1 而不是 5 作为 **occurrence** 值。 |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` | 在月份的最后一个星期五每 15 分钟运行一次。 |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` | 在每月第三个星期三的早晨 5:15、早晨 5:45、下午 5:15、下午 5:45 运行。 |

## <a name="trigger-type-comparison"></a>触发器类型比较
翻转窗口触发器和计划触发器都按时间检测信号运行， 那么其不同之处在哪里？

下表对翻转窗口触发器和计划触发器进行了比较：

|  | 翻转窗口触发器 | 计划触发器 |
|:--- |:--- |:--- |
| **回填方案** | 。 可以针对过去的窗口来计划管道运行。 | 不支持。 只能在从现在的时间到未来的时间这个时段内执行管道运行。 |
| **可靠性** | 100% 的可靠性。 可以针对所有窗口（从指定的开始日期算起，没有间隔）来计划管道运行。 | 可靠性较低。 |
| **重试功能** | 。 失败的管道运行的重试策略默认设置为 0，也可由用户在触发器定义中指定。 在管道运行因并发/服务器/限制值（即状态代码 400：用户错误；429：请求过多；500：内部服务器错误）而失败时自动重试。 | 不支持。 |
| **并发** | 。 用户可以为触发器显式设置并发限制。 允许 1 到 50 个并发的触发管道运行。 | 不支持。 |
| **系统变量** | 支持使用 **WindowStart** 和 **WindowEnd** 系统变量。 用户可以访问在触发器定义中充当触发器系统变量的 `triggerOutputs().windowStartTime` 和 `triggerOutputs().windowEndTime`。 这两个值分别用作窗口开始时间和窗口结束时间。 例如，如果翻转窗口触发器每小时运行一次，则对于凌晨 1:00 到凌晨 2:00 这个窗口，相应的定义为 `triggerOutputs().WindowStartTime = 2017-09-01T01:00:00Z` 和 `triggerOutputs().WindowEndTime = 2017-09-01T02:00:00Z`。 | 不支持。 |
| **管道-触发器关系** | 支持一对一关系。 只能触发一个管道。 | 支持多对多关系。 多个触发器可以启动单个管道。 单个触发器可以启动多个管道。 |

## <a name="next-steps"></a>后续步骤
参阅以下教程：

- [快速入门：使用 .NET SDK 创建数据工厂](quickstart-create-data-factory-dot-net.md)
- [创建计划触发器](how-to-create-schedule-trigger.md)
- [创建翻转窗口触发器](how-to-create-tumbling-window-trigger.md)
