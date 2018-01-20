---
title: "如何在 Azure 数据工厂中创建翻转窗口触发器 | Microsoft Docs"
description: "了解如何在 Azure 数据工厂中创建按翻转窗口运行管道的触发器。"
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
ms.date: 01/05/2018
ms.author: shlo
ms.openlocfilehash: a3b056ae4bb4eda26fec58ca3b6bed7f0744e36e
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2018
---
# <a name="how-to-create-a-trigger-that-runs-a-pipeline-on-a-tumbling-window"></a>如何创建按翻转窗口运行管道的触发器
本文提供了创建、启动和监视翻转窗口触发器的步骤。 有关支持的触发器和类型的一般信息，请参阅[管道执行和触发器](concepts-pipeline-execution-triggers.md)。

> [!NOTE]
> 本文适用于目前处于预览版的数据工厂版本 2。 如果使用数据工厂服务版本 1（即正式版 (GA)），请参阅[数据工厂版本 1 入门](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。

翻转窗口触发器是一类可以在保留状态的同时按周期性的时间间隔（从指定的开始时间算起）触发的触发器。 翻转窗口是一系列固定大小、非重叠且连续的时间间隔。 翻转窗口触发器与管道存在 1:1 的关系，一个这样的触发器只能引用一个管道。

## <a name="tumbling-window-trigger-type-properties"></a>翻转窗口触发器类型属性
```  
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
        "runtimeState": "<<Started/Stopped/Disabled - readonly>>",
        "typeProperties": {
            "frequency": "<<Minute/Hour>>",
            "interval": <<int>>,
            "startTime": "<<datetime>>",
            "endTime: "<<datetime – optional>>"",
            "delay": "<<timespan – optional>>",
            “maxConcurrency”: <<int>> (required, max allowed: 50),
            "retryPolicy": {
                "count":  <<int - optional, default: 0>>,
                “intervalInSeconds”: <<int>>,
            }
        },
        "pipeline":
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "MyPipelineName"
                },
                "parameters": {
                    "parameter1": {
                        "type": "Expression",
                        "value": "@{concat('output',formatDateTime(trigger().outputs.windowStartTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                    },
                    "parameter2": {
                        "type": "Expression",
                        "value": "@{concat('output',formatDateTime(trigger().outputs.windowEndTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                    },
                    "parameter3": "https://mydemo.azurewebsites.net/api/demoapi"
                }
            }
      }    
}
```  

下表概述了与翻转窗口触发器中的循环和计划相关的主要元素。

| **JSON 名称** | **说明** | **允许的值** | **必需** |
|:--- |:--- |:--- |:--- |
| **类型** | 触发器的类型。 此项固定为“TumblingWindowTrigger”。 | 字符串 | 是 |
| **runtimeState** | <readOnly> 可能值：Started、Stopped、Disabled | 字符串 | 是，readOnly |
| **frequency** |*frequency* 字符串，表示触发器重复触发的频率单位。 支持的值为“minute”和“hour”。 如果开始时间的日期部分比频率更细致，则在计算窗口边界时会考虑日期部分。 例如：如果频率为每小时，开始时间为 2016-04-01T10:10:10Z，则第一个窗口为 (2017-09-01T10:10:10Z, 2017-09-01T11:10:10Z)。  | 字符串。 支持的类型为“minute”、“hour” | 是 |
| **interval** |*interval* 是一个正整数，表示决定触发器多久运行一次的频率间隔。 例如，如果 *interval* 为 3，*frequency* 为“hour”，则触发器每 3 小时重复触发一次。 | Integer | 是 |
| **startTime**|*startTime* 采用日期时间格式。 *startTime* 是第一个匹配项，可以是过去的时间。 第一个触发器间隔将是 (startTime, startTime + interval)。 | DateTime | 是 |
| **endTime**|*endTime* 采用日期时间格式。 *endTime* 是最后一个匹配项，可以是过去的时间。 | DateTime | 是 |
| **delay** | 指定窗口的数据处理开始前的延迟。 管道运行在预期的执行时间 + 延迟之后启动。 延迟的定义是：在预期的执行时间过后，触发器在触发新的运行之前等待的时间。 它不会改变窗口开始时间。 | 时间跨度（示例：00:10:00，表示延迟 10 分钟） |  不会。 默认为“00:00:00” |
| **max concurrency** | 同时针对已就绪窗口触发的触发器运行数。 示例：如果尝试针对昨天进行每小时一次的回填，则此项为 24 个窗口。 如果 concurrency = 10，则仅针对头 10 个窗口 (00:00-01:00 - 09:00-10:00) 触发触发器事件。 在头 10 个触发的管道运行完成后，将针对接下来的 10 个窗口 (10:00-11:00 - 19:00-20:00) 触发触发器运行。 继续 concurrency = 10 的示例，如果有 10 个窗口就绪，则会有 10 个管道运行。 如果只有 1 个窗口就绪，则只会有 1 个管道运行。 | Integer | 是的。 可能的值为 1-50 |
| **retryPolicy: Count** | 将管道运行标记为“失败”之前的重试次数  | Integer |  不会。 默认为 0 次重试 |
| **retryPolicy: intervalInSeconds** | 重试之间的延迟（以秒为单位） | Integer |  不会。 默认为 30 秒 |

### <a name="using-system-variables-windowstart-and-windowend"></a>使用系统变量：WindowStart 和 WindowEnd

若要在**管道**定义中使用翻转窗口触发器的 WindowStart 和 WindowEnd（例如，将其用作查询的一部分），则必须在**触发器**定义中将这两个变量作为参数传递给管道，像这样：
```  
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
            ...
        "pipeline":
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "MyPipelineName"
                },
                "parameters": {
                    "MyWindowStart": {
                        "type": "Expression",
                        "value": "@{concat('output',formatDateTime(trigger().outputs.windowStartTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                    },
                    "MyWindowEnd": {
                        "type": "Expression",
                        "value": "@{concat('output',formatDateTime(trigger().outputs.windowEndTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                    }
                }
            }
      }    
}
```  

然后，在管道定义中，若要使用 WindowStart 和 WindowEnd 值，请相应地使用参数“MyWindowStart”和“MyWindowEnd”

### <a name="notes-on-backfill"></a>有关回填的说明
有多个窗口需要执行时（尤其是在回填的情况下），窗口的执行顺序是确定的，将根据时间间隔的新旧按从旧到新的顺序执行。 目前无法更改此行为。

### <a name="updating-an-existing-triggerresource"></a>更新现有的 TriggerResource
* 如果触发器的频率（或窗口大小）已更改，则已处理窗口的状态不会重置。 触发器会根据新的窗口大小继续触发一段时间（从上次执行开始计算）。
* 如果触发器的结束时间更改（进行了添加或更新），则已处理窗口的状态不会重置。 触发器会直接遵循新的结束时间。 如果结束时间早于已执行的窗口，触发器会停止。 否则，触发器会在新的结束时间到了之后停止。

## <a name="sample-using-azure-powershell"></a>Azure PowerShell 使用示例
本部分展示了如何使用 Azure PowerShell 创建、启动和监视触发器。

1. 在 C:\ADFv2QuickStartPSH\ 文件夹中创建一个名为 MyTrigger.json 的 JSON 文件，使其包含以下内容：

   > [!IMPORTANT]
   > 将 **startTime** 设置为当前 UTC 时间，将 **endTime** 设置为比当前 UTC 时间晚一小时，然后保存 JSON 文件。

    ```json   
    {
      "name": "PerfTWTrigger",
      "properties": {
        "type": "TumblingWindowTrigger",
        "typeProperties": {
          "frequency": "Minute",
          "interval": "15",
          "startTime": "2017-09-08T05:30:00Z",
          "delay": "00:00:01",
          "retryPolicy": {
            "count": 2,
            "intervalInSeconds": 30
          },
          "maxConcurrency": 50
        },
        "pipeline": {
          "pipelineReference": {
            "type": "PipelineReference",
            "referenceName": "DynamicsToBlobPerfPipeline"
          },
          "parameters": {
            "windowStart": "@trigger().outputs.windowStartTime",
            "windowEnd": "@trigger().outputs.windowEndTime"
          }
        },
        "runtimeState": "Started"
      }
    }
    ```  
2. 使用 **Set-AzureRmDataFactoryV2Trigger** cmdlet 创建一个触发器。

    ```powershell
    Set-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    
3. Confirm that the status of the trigger is **Stopped** by using the **Get-AzureRmDataFactoryV2Trigger** cmdlet.

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

## <a name="next-steps"></a>后续步骤
有关触发器的详细信息，请参阅[管道执行和触发器](concepts-pipeline-execution-triggers.md#triggers)。
