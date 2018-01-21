---
title: "如何在 Azure 数据工厂中创建计划触发器 | Microsoft Docs"
description: "了解如何在 Azure 数据工厂中创建按计划运行管道的触发器。"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2017
ms.author: shlo
ms.openlocfilehash: 6b92e8402d372e29e264dc70b128124973d66bb9
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2018
---
# <a name="how-to-create-a-trigger-that-runs-a-pipeline-on-a-schedule"></a>如何创建按计划运行管道的触发器
本文提供有关计划触发器和创建、启动和监视触发器的步骤的信息。 有关其他类型的触发器，请参阅[管道执行和触发器](concepts-pipeline-execution-triggers.md)。

> [!NOTE]
> 本文适用于目前处于预览版的数据工厂版本 2。 如果使用数据工厂服务版本 1（即正式版 (GA)），请参阅[数据工厂版本 1 入门](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。

### <a name="schedule-trigger-json-definition"></a>计划触发器 JSON 定义
创建计划触发器时，可以使用 JSON 指定计划和循环，如本部分中的示例所示。

若要使用计划触发器启动管道运行，请在触发器定义中包括特定管道的管道引用。 管道和触发器具有“多对多”关系。 多个触发器可以启动单个管道。 单个触发器可以启动多个管道。

```json
{
  "properties": {
    "type": "ScheduleTrigger",
    "typeProperties": {
      "recurrence": {
        "frequency": <<Minute, Hour, Day, Week, Month>>,
        "interval": <<int>>,             // optional, how often to fire (default to 1)
        "startTime": <<datetime>>,
        "endTime": <<datetime - optional>>,
        "timeZone": "UTC"
        "schedule": {                    // optional (advanced scheduling specifics)
          "hours": [<<0-23>>],
          "weekDays": : [<<Monday-Sunday>>],
          "minutes": [<<0-59>>],
          "monthDays": [<<1-31>>],
          "monthlyOccurences": [
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
                    "<parameter 2 Name>" : "<parameter 2 Value>"
                }
           }
      ]
  }
}
```

> [!IMPORTANT]
>  参数属性是管道中的必需属性。 即使管道不使用任何参数，也请包括一个空的 json 作为参数，因为该属性必须存在。


### <a name="overview-schedule-trigger-schema"></a>概述：计划触发器架构
下表提供了与触发器中的循环和计划相关的主要元素的高级概述：

JSON 属性 |     说明
------------- | -------------
startTime | startTime 采用日期时间格式。 对于简单的计划，startTime 是第一个匹配项。 对于复杂的计划，触发器的启动时间不早于 startTime。
endTime | 指定触发器的结束日期时间。 触发器在这个时间之后不执行。 发生在过去的 endTime 是无效项。 这是一个可选属性。
timezone | 目前仅支持 UTC。
recurrence | recurrence 对象指定触发器的循环规则。 recurrence 对象支持以下元素：frequency、interval、endTime、count 和 schedule。 如果定义了 recurrence，则必须定义 frequency；recurrence 的其他元素是可选项。
频率 | 表示触发器重复的频率的单元。 支持的值为：`minute`、`hour`、`day`、`week` 或 `month`。
interval | interval 是一个正整数。 它表示确定触发器运行频率的频率间隔。 例如，如果 interval 为 3，frequency 为“week”，则触发器每 3 周重复一次。
schedule | 指定了频率的触发器会根据循环计划更改其循环。 schedule 包含基于分钟、小时、星期、月份日次和周次的修改。


### <a name="overview-schedule-trigger-schema-defaults-limits-and-examples"></a>概述：计划触发器架构默认值、限制和示例

JSON 名称 | 值类型 | 必需？ | 默认值 | 有效值 | 示例
--------- | ---------- | --------- | ------------- | ------------ | -------
startTime | 字符串 | 是 | 无 | ISO-8601 日期时间 | ```"startTime" : "2013-01-09T09:30:00-08:00"```
recurrence | 对象 | 是 | 无 | Recurrence 对象 | ```"recurrence" : { "frequency" : "monthly", "interval" : 1 }```
interval | Number | 否 | 1 | 1 到 1000。 | ```"interval":10```
endTime | 字符串 | 是 | 无 | 代表将来某个时间的日期时间值 | `"endTime" : "2013-02-09T09:30:00-08:00"`
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

然后，计划还可以增加触发器执行的次数。 例如，如果频率为“month”的触发器具有在 1 日和 2 日运行的 schedule，则该触发器将在每月的 1 日和 2 日运行，而不只是在每月运行一次。

如果指定了多个计划元素，则求值顺序为大到小 – 周次、月份日次、星期、小时和分钟。

下表详细描述了 schedule 元素：


JSON 名称 | 说明 | 有效值
--------- | ----------- | ------------
分钟数 | 运行触发器的小时中的分钟。 | <ul><li>Integer</li><li>整数数组</li></ul>
小时 | 运行触发器的日期中的小时。 | <ul><li>Integer</li><li>整数数组</li></ul>
工作日 | 运行触发器的星期日期。 只能配合每周频率指定。 | <ul><li>星期一、星期二、星期三、星期四、星期五、星期六或星期日</li><li>任意值的数组（最大数组大小为 7）</li></p>不区分大小写</p>
monthlyOccurrences | 确定运行触发器的月份日期。 只能配合每月频率指定。 | MonthlyOccurence 对象的数组：`{ "day": day,  "occurrence": occurence }`。 <p> day 是运行触发器的星期日期，例如，`{Sunday}` 表示月份中的每个星期日。 必需。<p>Occurrence 是月份中重复的日期，例如，`{Sunday, -1}` 表示月份中的最后一个星期日。 可选。
monthDays | 运行触发器的月份日期。 只能配合每月频率指定。 | <ul><li><= -1 且 >= -31 的任何值</li><li>>= 1 且 <= 31 的任何值</li><li>值组成的数组</li>


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


## <a name="use-azure-powershell"></a>使用 Azure PowerShell
本部分展示了如何使用 Azure PowerShell 创建、启动和监视计划程序触发器。 如果希望看到此示例工作，请首先完成[快速入门：使用 Azure PowerShell 创建数据工厂](quickstart-create-data-factory-powershell.md)。 然后，将以下代码添加到 main 方法，该代码用于创建并启动一个每 15 分钟运行一次的计划触发器。 该触发器与你在快速入门中创建的一个管道 (**Adfv2QuickStartPipeline**) 相关联。

1. 在 C:\ADFv2QuickStartPSH\ 文件夹中创建一个名为 MyTrigger.json 的 JSON 文件，使其包含以下内容：

    > [!IMPORTANT]
    > 将 **startTime** 设置为当前 UTC 时间，将 **endTime** 设置为比当前 UTC 时间晚一小时，然后保存 JSON 文件。

    ```json   
    {
        "properties": {
            "name": "MyTrigger",
            "type": "ScheduleTrigger",
            "typeProperties": {
                "recurrence": {
                    "frequency": "Minute",
                    "interval": 15,
                    "startTime": "2017-12-08T00:00:00",
                    "endTime": "2017-12-08T01:00:00"
                }
            },
            "pipelines": [{
                    "pipelineReference": {
                        "type": "PipelineReference",
                        "referenceName": "Adfv2QuickStartPipeline"
                    },
                    "parameters": {
                        "inputPath": "adftutorial/input",
                        "outputPath": "adftutorial/output"
                    }
                }
            ]
        }
    }
    ```

    在 JSON 代码片段中：
    - 触发器的 **type** 设置为 **ScheduleTrigger**。
    - **frequency** 设置为 **Minute**，**interval** 设置为 **15**。 因此，触发器在开始时间与结束时间之间每 15 分钟运行一次管道。
    - **endTime** 比 **startTime** 晚一小时，因此，触发器将在 startTime 之后的 15 分钟、30 分钟和 45 分钟运行管道。 不要忘记将 startTime 更新为当前 UTC 时间，将 endTime 更新为比 startTime 晚一小时。  
    - 触发器与 **Adfv2QuickStartPipeline** 管道相关联。 若要将多个管道与某个触发器相关联，请添加多个 **pipelineReference** 节。
    - 快速入门中的管道采用两个**参数**。 因此，可以通过触发器传递这些参数的值。
2. 使用 **Set-AzureRmDataFactoryV2Trigger** cmdlet 创建一个触发器。

    ```powershell
    Set-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    ```
3. 使用 **Get-AzureRmDataFactoryV2Trigger** cmdlet 确认触发器的状态为 **Stopped**。

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```
4. 使用 **Start-AzureRmDataFactoryV2Trigger** cmdlet 启动触发器：

    ```powershell
    Start-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```
5. 使用 **Get-AzureRmDataFactoryV2Trigger** cmdlet 确认触发器的状态为 **Started**。

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```
6.  通过 PowerShell 使用 **Get-AzureRmDataFactoryV2TriggerRun** cmdlet 获取触发器运行。 若要获取有关触发器运行的信息，请定期运行以下命令：需更新 **TriggerRunStartedAfter** 和 **TriggerRunStartedBefore** 值以匹配触发器定义中的值。

    ```powershell
    Get-AzureRmDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```

    若要在 Azure 门户中监视触发器运行/管道运行，请参阅[监视管道运行](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)

## <a name="use-net-sdk"></a>使用 .NET SDK
本部分展示了如何使用 .NET SDK 创建、启动和监视触发器。 如果希望看到此代码工作，请首先完成[使用 .NET SDK 创建数据工厂快速入门](quickstart-create-data-factory-dot-net.md)。 然后，将以下代码添加到 main 方法，该代码用于创建并启动一个每 15 分钟运行一次的计划触发器。 该触发器与你在快速入门中创建的一个管道 (**Adfv2QuickStartPipeline**) 相关联。

```csharp
            //create the trigger
            Console.WriteLine("Creating the trigger");

            // set the start time to the current UTC time
            DateTime startTime = DateTime.UtcNow;

            // specify values for the inputPath and outputPath parameters
            Dictionary<string, object> pipelineParameters = new Dictionary<string, object>();
            pipelineParameters.Add("inputPath", "adftutorial/input");
            pipelineParameters.Add("outputPath", "adftutorial/output");

            // create a schedule trigger
            string triggerName = "MyTrigger";
            ScheduleTrigger myTrigger = new ScheduleTrigger()
            {
                Pipelines = new List<TriggerPipelineReference>()
                {
                    // associate the Adfv2QuickStartPipeline pipeline with the trigger
                    new TriggerPipelineReference()
                    {
                        PipelineReference = new PipelineReference(pipelineName),
                        Parameters = pipelineParameters,
                    }
                },
                Recurrence = new ScheduleTriggerRecurrence()
                {
                    // set the start time to current UTC time and the end time to be one hour after.
                    StartTime = startTime,
                    TimeZone = "UTC",
                    EndTime = startTime.AddHours(1),
                    Frequency = RecurrenceFrequency.Minute,
                    Interval = 15,
                }
            };

            // now, create the trigger by invoking CreateOrUpdate method.
            TriggerResource triggerResource = new TriggerResource()
            {
                Properties = myTrigger
            };
            client.Triggers.CreateOrUpdate(resourceGroup, dataFactoryName, triggerName, triggerResource);

            //start the trigger
            Console.WriteLine("Starting the trigger");
            client.Triggers.Start(resourceGroup, dataFactoryName, triggerName);

```

若要监视触发器运行，请在最后一个 `Console.WriteLine` 语句之前添加以下代码：

```csharp
            // Check the trigger runs every 15 minutes
            Console.WriteLine("Trigger runs. You see the output every 15 minutes");

            for (int i = 0; i < 3; i++)
            {
                System.Threading.Thread.Sleep(TimeSpan.FromMinutes(15));
                List<TriggerRun> triggerRuns = client.Triggers.ListRuns(resourceGroup, dataFactoryName, triggerName, DateTime.UtcNow.AddMinutes(-15 * (i + 1)), DateTime.UtcNow.AddMinutes(2)).ToList();
                Console.WriteLine("{0} trigger runs found", triggerRuns.Count);

                foreach (TriggerRun run in triggerRuns)
                {
                    foreach (KeyValuePair<string, string> triggeredPipeline in run.TriggeredPipelines)
                    {
                        PipelineRun triggeredPipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, triggeredPipeline.Value);
                        Console.WriteLine("Pipeline run ID: {0}, Status: {1}", triggeredPipelineRun.RunId, triggeredPipelineRun.Status);
                        List<ActivityRun> runs = client.ActivityRuns.ListByPipelineRun(resourceGroup, dataFactoryName, triggeredPipelineRun.RunId, run.TriggerRunTimestamp.Value, run.TriggerRunTimestamp.Value.AddMinutes(20)).ToList();
                    }
                }
            }
```

若要在 Azure 门户中监视触发器运行/管道运行，请参阅[监视管道运行](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)

## <a name="use-python-sdk"></a>使用 Python SDK
本部分展示了如何使用 Python SDK 创建、启动和监视触发器。 如果希望看到此代码工作，请首先完成[使用 Python SDK 创建数据工厂快速入门](quickstart-create-data-factory-python.md)。 然后，在 python 脚本中的“monitor the pipeline run”代码块之后添加以下代码块。 此代码将创建在指定的开始时间和结束时间之间每 15 分钟运行一次的计划触发器。 请将 start_time 更新为当前 UTC 时间，将 end_time 更新为比当前 UTC 时间晚一小时。

```python
    # Create a trigger
    tr_name = 'mytrigger'
    scheduler_recurrence = ScheduleTriggerRecurrence(frequency='Minute', interval='15',start_time='2017-12-12T04:00:00', end_time='2017-12-12T05:00:00', time_zone='UTC')
    pipeline_parameters = {'inputPath':'adftutorial/input', 'outputPath':'adftutorial/output'}
    pipelines_to_run = []
    pipeline_reference = PipelineReference('copyPipeline')
    pipelines_to_run.append(TriggerPipelineReference(pipeline_reference, pipeline_parameters))
    tr_properties = ScheduleTrigger(description='My scheduler trigger', pipelines = pipelines_to_run, recurrence=scheduler_recurrence)    
    adf_client.triggers.create_or_update(rg_name, df_name, tr_name, tr_properties)

    # start the trigger
    adf_client.triggers.start(rg_name, df_name, tr_name)
```
若要在 Azure 门户中监视触发器运行/管道运行，请参阅[监视管道运行](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)

## <a name="use-resource-manager-template"></a>使用 Resource Manager 模板
可以使用 Azure 资源管理器模板创建触发器。 有关分步说明，请参阅[使用资源管理器模板创建 Azure 数据工厂](quickstart-create-data-factory-resource-manager-template.md)。  

## <a name="pass-the-trigger-start-time-to-a-pipeline"></a>将触发器开始时间传递给管道
在版本 1 中，Azure 数据工厂支持使用 SliceStart/SliceEnd/WindowStart/WindowEnd 系统变量读取或写入分区的数据。 在版本 2 中，可使用管道参数和触发器的开始时间/计划时间作为参数值实现此行为。 在以下示例中，触发器的计划时间被作为值传递给管道参数 scheduledRunTime。

```json
"parameters": {
    "scheduledRunTime": "@trigger().scheduledTime"
}
```    
有关详细信息，请参阅[如何读取或写入分区的数据](how-to-read-write-partitioned-data.md)。

## <a name="next-steps"></a>后续步骤
有关触发器的详细信息，请参阅[管道执行和触发器](concepts-pipeline-execution-triggers.md#triggers)。
