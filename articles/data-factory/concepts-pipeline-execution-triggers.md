---
title: "Azure 数据工厂中的管道执行和触发器 | Microsoft Docs"
description: "本文提供了有关如何在 Azure 数据工厂中按需或通过创建触发器执行管道的信息。"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/10/2017
ms.author: shlo
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: c319979cce23da69965d4fbab037919461f67b3a
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---

# <a name="pipeline-execution-and-triggers-in-azure-data-factory"></a>Azure 数据工厂中的管道执行和触发器 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [版本 1 - GA](v1/data-factory-scheduling-and-execution.md)
> * [版本 2 - 预览版](concepts-pipeline-execution-triggers.md)

“管道运行”是 Azure 数据工厂版本 2 中的一个术语，用于定义管道执行的实例。 例如，假设你有一个管道分别在 8AM、9AM 和 10AM 执行。 在这种情况下，将单独运行管道三次（管道运行）。 每次管道运行都具有唯一的管道运行 ID，这是唯一定义特定管道运行的 GUID。 管道运行通常通过将自变量传递给管道中定义的参数进行实例化。 执行管道有两种方式：“手动”或通过“触发器”。 本文提供了有关执行管道的两种方式的详细信息。 

> [!NOTE]
> 本文适用于目前处于预览状态的版本 2 数据工厂。 如果使用数据工厂服务版本 1（即正式版 (GA)），请参阅[数据工厂 V1 中的计划和执行](v1/data-factory-scheduling-and-execution.md)。

## <a name="run-pipeline-on-demand"></a>按需运行管道
在这种方法中，手动运行管道。 这也被视为管道的按需执行。 

例如，假设你具有想要执行的名为 copyPipeline 的管道。 该管道是一个简单的管道，具有从 Azure Blob 存储中的源文件夹复制到同一存储中的目标文件夹的单个活动。 以下是示例管道定义： 

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
管道采用两个参数：sourceBlobContainer 和 sinkBlobContainer，如 JSON 定义中所示。 你在运行时将值传递给这些参数。 

若要手动运行管道，可以使用以下方式之一：.NET、PowerShell、REST 和 Python。 

### <a name="rest-api"></a>REST API
下面是示例 REST 命令：  

```
POST
https://management.azure.com/subscriptions/mySubId/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory/pipelines/copyPipeline/createRun?api-version=2017-03-01-preview
```
有关完整示例，请参阅[快速入门：使用 REST API 创建数据工厂](quickstart-create-data-factory-rest-api.md)。

### <a name="powershell"></a>PowerShell
下面是示例 PowerShell 命令： 

```powershell
Invoke-AzureRmDataFactoryV2Pipeline -DataFactory $df -PipelineName "Adfv2QuickStartPipeline" -ParameterFile .\PipelineParameters.json
```

在请求有效负载的正文中传递参数。 在 .NET、Powershell 和 Python 中，可以在作为参数传递给调用的字典中传递值。

```json
{
  “sourceBlobContainer”: “MySourceFolder”,
  “sinkBlobCountainer”: “MySinkFolder”
}
```

响应有效负载是管道运行的唯一 ID：

```json
{
  "runId": "0448d45a-a0bd-23f3-90a5-bfeea9264aed"
}
```


有关完整示例，请参阅[快速入门：使用 PowerShell 创建数据工厂](quickstart-create-data-factory-powershell.md)。

### <a name="net"></a>.NET 
下面是示例 .NET 调用： 

```csharp
client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, parameters)
```

有关完整示例，请参阅[快速入门：使用 .NET 创建数据工厂](quickstart-create-data-factory-dot-net.md)。

> [!NOTE]
> 可以使用 .NET API 从 Azure Functions、你自己的 Web 服务等来调用数据工厂管道。

## <a name="triggers"></a>触发器
触发器提供执行管道运行的第二种方式。 触发器表示一个处理单元，用于确定何时需要启动管道执行。 目前，数据工厂支持按时钟计划调用管道的触发器。 它称为“计划程序触发器”。 目前，数据工厂不支持基于事件的触发器，例如，在文件到达时管道运行的触发器。

管道和触发器具有“n-m”关系。 多个触发器可以启动单个管道，并且同一个触发器可以启动多个管道。 在触发器的以下 JSON 定义中，pipelines 属性是指由特定的触发器和管道参数的值触发的管道的列表。

### <a name="basic-trigger-definition"></a>基本触发器定义： 
```json
    "properties": {
        "name": "MyTrigger",
        "type": "<type of trigger>",
        "typeProperties": {
            …
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
                    "<parameter 2 Name>" : "<parameter 2 Value>"
                }
            }
        ]
    }
```

## <a name="scheduler-trigger"></a>计划程序触发器
计划程序触发器按时钟计划运行管道。 这种触发器支持定期和高级日历选项（每周、星期一 5PM 和星期四 9PM）。 它非常灵活，与数据集模式无关，而且不区分时间序列和非时间序列数据。

### <a name="scheduler-trigger-json-definition"></a>计划程序触发器 JSON 定义
在创建计划程序触发器时，可以使用 JSON 指定计划和循环，如本部分中的示例所示。 

若要使用计划程序触发器启动管道运行，请在触发器定义中包括特定管道的管道引用。 管道和触发器具有“n-m”关系。 多个触发器可以启动单个管道。 同一个触发器可以启动多个管道。

```json
{
  "properties": {
    "type": "ScheduleTrigger",
    "typeProperties": {
      "recurrence": {
        "frequency": <<Minute, Hour, Day, Week, Year>>,
        "interval": <<int>>,             // optional, how often to fire (default to 1)
        "startTime": <<datetime>>,
        "endTime": <<datetime>>,
        "timeZone": <<default UTC>>
        "schedule": {                    // optional (advanced scheduling specifics)
          "hours": [<<0-24>>],
          "weekDays": ": [<<Monday-Sunday>>],
          "minutes": [<<0-60>>],
          "monthDays": [<<1-31>>],
          "monthlyOccurences": [
               {
                    "day": <<Monday-Sunday>>,
                    "occurrence": <<1-5>>
               }
           ] 
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
                    "<parameter 2 Name> : "<parameter 2 Value>"
                }
           }
      ]
  }
}
```

### <a name="overview-scheduler-trigger-schema"></a>概述：计划程序触发器架构
下表提供了与触发器中的循环和计划相关的主要元素的高级概述：

JSON 属性 |     说明
------------- | -------------
startTime | startTime 采用日期时间格式。 对于简单的计划，startTime 是第一个匹配项。 对于复杂的计划，触发器的启动时间不早于 startTime。
recurrence | recurrence 对象指定触发器的循环规则。 recurrence 对象支持以下元素：frequency、interval、endTime、count 和 schedule。 如果定义了 recurrence，则必须定义 frequency；recurrence 的其他元素是可选项。
frequency | 表示触发器重复的频率的单元。 支持的值为：`minute`、`hour`、`day`、`week` 或 `month`。
interval | interval 是一个正整数。 它表示确定触发器运行频率的频率间隔。 例如，如果 interval 为 3，frequency 为“week”，则触发器每 3 周重复一次。
endTime | 指定触发器的结束日期时间。 触发器在这个时间之后不执行。 发生在过去的 endTime 是无效项。
schedule | 指定了频率的触发器会根据循环计划更改其循环。 schedule 包含基于分钟、小时、星期、月份日次和周次的修改。

### <a name="overview-scheduler-trigger-schema-defaults-limits-and-examples"></a>概述：计划程序触发器架构默认值、限制和示例

JSON 名称 | 值类型 | 必需？ | 默认值 | 有效值 | 示例
--------- | ---------- | --------- | ------------- | ------------ | -------
startTime | String | 是 | 无 | ISO-8601 日期时间 | ```"startTime" : "2013-01-09T09:30:00-08:00"```
recurrence | 对象 | 是 | 无 | Recurrence 对象 | ```"recurrence" : { "frequency" : "monthly", "interval" : 1 }```
interval | Number | 否 | 1 | 1 到 1000。 | ```"interval":10```
endTime | String | 是 | 无 | 代表将来某个时间的日期时间值 | `"endTime" : "2013-02-09T09:30:00-08:00"`
schedule | 对象 | 否 | 无 | Schedule 对象 | `"schedule" : { "minute" : [30], "hour" : [8,17] }`

### <a name="deep-dive-starttime"></a>深入探讨：startTime
下表说明了 startTime 如何控制触发器的运行方式：

startTime 值 | 不按计划循环 | 按计划循环
--------------- | --------------------------- | ------------------------
开始时间在过去 | 计算开始时间后的第一个将来执行时间，并在该时间运行。<p>在从上次执行时间进行计算后运行后续执行。</p><p>请参阅此表后面的示例。</p> | 触发器不会早于指定的开始时间启动。 第一次循环基于从开始时间计算的计划。 <p>根据循环计划运行后续执行</p>
开始时间在将来或当前 | 在指定的开始时间运行一次。 <p>在从上次执行时间进行计算后运行后续执行。</p> | 触发器不会早于指定的开始时间启动。 第一次循环基于从开始时间计算的计划。<p>根据循环计划运行后续执行。</p>

让我们查看 startTime 在过去，并且指定了 recurrence 但未指定 schedule 的情况下会发生的情况示例。 假设当前时间为 `2017-04-08 13:00`，startTime 为 `2017-04-07 14:00`，recurrence 为每 2 天（定义方式为 frequency: day，interval: 2）。请注意，startTime 在过去，即发生在当前时间以前。

在上述情况下，首次执行时间为 `2017-04-09 at 14:00`。 计划程序引擎从开始时间计算执行循环。 过去的所有实例会被丢弃。 引擎将使用将来发生的下一个实例。 在本例中，startTime 为 `2017-04-07 at 2:00pm`，因此，下一个实例为从该时间算起的 2 天，即 `2017-04-09 at 2:00pm`。

首次执行时间是相同的，即使 startTime 为 `2017-04-05 14:00` 或 `2017-04-01 14:00`。 在首次执行后，使用该计划计算后续执行。 因此，时间为 `2017-04-11 at 2:00pm`、`2017-04-13 at 2:00pm`、`2017-04-15 at 2:00pm` 等，依次类推。

最后，如果为触发器指定了计划，但未在计划中设置小时和/或分钟，则小时和分钟分别默认为第一次执行的小时和/或分钟。

### <a name="deep-dive-schedule"></a>深入探讨：schedule
一方面，schedule 可以限制触发器执行的次数。 例如，如果频率为“month”的触发器具有仅在 31 日运行的 schedule，该触发器仅在包含 31 日的月份运行。

另一方面，schedule 还可以增加触发器执行的次数。 例如，如果频率为“month”的触发器具有在 1 日和 2 日运行的 schedule，则该触发器将在每月的 1 日和 2 日运行，而不只是在每月运行一次。

如果指定了多个计划元素，则求值顺序为大到小 – 周次、月份日次、星期、小时和分钟。

下表详细描述了 schedule 元素：


JSON 名称 | 说明 | 有效值
--------- | ----------- | ------------
分钟数 | 运行触发器的小时中的分钟。 | <ul><li>Integer</li><li>整数数组</li></ul>
小时 | 运行触发器的日期中的小时。 | <ul><li>Integer</li><li>整数数组</li></ul>
工作日 | 运行触发器的星期日期。 只能配合每周频率指定。 | <ul><li>星期一、星期二、星期三、星期四、星期五、星期六或星期日</li><li>上述任意值的数组（最大数组大小为 7）</li></p>不区分大小写</p>
monthlyOccurrences | 确定运行触发器的月份日期。 只能配合每月频率指定。 | MonthlyOccurence 对象的数组：`{ "day": day,  "occurrence": occurence }`。 <p> day 是运行触发器的星期日期，例如，`{Sunday}` 表示月份中的每个星期日。 必需。<p>Occurrence 是月份中重复的日期，例如，`{Sunday, -1}` 表示月份中的最后一个星期日。 可选。
monthDays | 运行触发器的月份日期。 只能配合每月频率指定。 | <ul><li><= -1 且 >= -31 的任何值</li><li>>= 1 且 <= 31 的任何值</li><li>上述值的数组</li>


## <a name="examples-recurrence-schedules"></a>示例：循环计划
本部分提供了循环计划的示例 – 着重于计划对象及其子元素。

示例计划假定 interval 设置为 1。 此外，假设正确的频率符合 schedule，例如，不能使用频率“day”，并且计划中不能包含“monthDays”修改。 上一部分的表中提到了这些限制。 

示例 | 说明
------- | -----------
`{"hours":[5]}` | 在每天 5AM 运行
`{"minutes":[15], "hours":[5]}` | 在每天 5:15AM 运行
`{"minutes":[15], "hours":[5,17]}` | 在每天 5:15 AM 和 5:15 PM 运行
`{"minutes":[15,45], "hours":[5,17]}` | 在每天 5:15AM、5:45AM、5:15PM 和 5:45PM 运行
`{"minutes":[0,15,30,45]}` | 每隔 15 分钟运行一次
`{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` | 每隔一小时运行一次。 此触发器每小时运行一次。 分钟由 startTime（如果已指定）控制，如果未指定 startTime，则由创建时间控制。 例如，如果开始时间或创建时间（以适用的为准）为 12:25 PM，则触发器会在 00:25、01:25、02:25 ... 23:25 运行。 该计划相当于为触发器指定了 frequency 为“hour”、interval 为 1 且无 schedule。 区别是，也可以配合不同的 frequency 和 interval 使用此计划来创建其他触发器。 例如，如果 frequency 为“month”，则该计划将每月运行一次，而不是像 frequency 为“day”时那样每天运行一次。
`{"minutes":[0]}` | 每一小时整点运行一次。 此触发器也是每一小时运行一次，不过是在整点运行（例如，12AM、1AM、2AM 等等）。 此设置相当于触发器频率为“hour”，startTime 为零分钟，并且在频率为“day”时无计划。但是，如果频率为“week”或“month”，则计划分别只会在一个星期中的一天或一个月中的一天执行。
`{"minutes":[15]}` | 在过去每隔一小时的第 15 分钟运行一次。 以下作业将每隔一小时运行，从 00:15AM、1:15AM、2:15AM 等开始，在 10:15PM 和 11:15PM 结束。
`{"hours":[17], "weekDays":["saturday"]}` | 在每周星期六 5PM 运行
`{"hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | 在每周星期一、星期三和星期五 5PM 运行
`{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | 在每周星期一、星期三和星期五 5:15PM 和 5:45PM 运行
`{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | 在工作日每隔 15 分钟运行一次
`{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | 在工作日 9AM 到 4:45PM 之间每隔 15 分钟运行一次
`{"weekDays":["tuesday", "thursday"]}` | 在星期二和星期四的指定开始时间运行。
`{"minutes":[0], "hours":[6], "monthDays":[28]}` | 在每个月 28 日 6AM 运行（假设 frequency 为 month）
`{"minutes":[0], "hours":[6], "monthDays":[-1]}` | 在月份最后一天的 6AM 运行。 如果要在月份的最后一天运行触发器，请使用 -1 而不是 28 日、29 日、30 日或 31 日。
`{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` | 在每月第一天和最后一天的 6AM 运行
`{monthDays":[1,14]}` | 在每月第一天和第 14 天的指定开始时间运行。
`{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | 在每月第一个星期五的 5AM 运行
`{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | 在每月第一个星期五的指定开始时间运行。
`{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` | 在每月结束后第三个星期五的开始时间运行
`{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | 在每月第一个和最后一个星期五的 5:15AM 运行
`{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | 在每月第一个和最后一个星期五的指定开始时间运行
`{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` | 在每月第五个星期五的开始时间运行。 如果月份中没有第五个星期五，则管道不运行，因为管道计划只在第五个星期五运行。  如果想要在月份的最后一个星期五运行触发器，可以考虑为循环使用 -1 而不是 5。
`{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` | 在月份的最后一个星期五每 15 分钟运行一次。
`{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` | 在每月第三个星期三的 5:15AM、5:45AM、5:15PM 和 5:45PM 运行。




## <a name="next-steps"></a>后续步骤
参阅以下教程： 

- [快速入门：使用.NET 创建数据工厂](quickstart-create-data-factory-dot-net.md)

