---
title: "在 Azure 数据工厂中创建翻转窗口触发器 | Microsoft Docs"
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
ms.openlocfilehash: 1f026683ebc9b3d2bc935cd78aa9d16684e7db40
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2018
---
# <a name="create-a-trigger-that-runs-a-pipeline-on-a-tumbling-window"></a>创建按翻转窗口运行管道的触发器
本文提供了创建、启动和监视翻转窗口触发器的步骤。 有关触发器和支持的类型的一般信息，请参阅[管道执行和触发器](concepts-pipeline-execution-triggers.md)。

> [!NOTE]
> 本文适用于目前处于预览状态的 Azure 数据工厂第 2 版。 如果使用 Azure 数据工厂第 1 版（即正式版 (GA)），请参阅 [Azure 数据工厂第 1 版入门](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。

翻转窗口触发器是一类可以在保留状态的同时按周期性的时间间隔（从指定的开始时间算起）触发的触发器。 翻转窗口是一系列固定大小、非重叠且连续的时间间隔。 翻转窗口触发器与管道存在一对一关系，一个这样的触发器只能引用一个管道。

## <a name="tumbling-window-trigger-type-properties"></a>翻转窗口触发器类型属性
翻转窗口具有以下触发器类型属性：

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

下表概述了与翻转窗口触发器中的循环和计划相关的主要 JSON 元素：

| JSON 元素 | 说明 | Type | 允许的值 | 必选 |
|:--- |:--- |:--- |:--- |:--- |
| **类型** | 触发器的类型。 类型为固定值“TumblingWindowTrigger”。 | String | "TumblingWindowTrigger" | 是 |
| **runtimeState** | 触发器运行时的当前状态。<br/>**注意**：此元素是 \<readOnly>。 | String | “Started”、“Stopped”、“Disabled” | 是 |
| **frequency** | 一个字符串，表示触发器重复出现的频率单位（分钟或小时）。 如果 **startTime** 日期值粒度比 **frequency** 值更细，则会在计算窗口边界时考虑 **startTime** 日期。 例如：如果 **frequency** 值为每小时，**startTime** 值为 2016-04-01T10:10:10Z，则第一个窗口为 (2017-09-01T10:10:10Z, 2017-09-01T11:10:10Z)。 | String | “minute”、“hour”  | 是 |
| **interval** | 一个正整数，表示 **frequency** 值对应的时间间隔，决定了触发器的运行频率。 例如，如果 **interval** 为 3，**frequency** 为“hour”，则触发器每 3 小时重复触发一次。 | Integer | 正整数。 | 是 |
| **startTime**| 第一个匹配项，可以是过去的时间。 第一个触发器间隔是 (**startTime**, **startTime** + **interval**)。 | DateTime | 一个日期时间值。 | 是 |
| **endTime**| 最后一个匹配项，可以是过去的时间。 | DateTime | 一个日期时间值。 | 是 |
| **delay** | 延迟窗口数据处理开始的时间量。 管道运行在预期的执行时间加上 **delay** 的量之后启动。 **delay** 的定义是：在预期的执行时间过后，触发器在触发新的运行之前等待的时间。 **delay** 不改变窗口 **startTime**。 例如，值为 00:10:00 的 **delay** 意味着 10 分钟的延迟。 | Timespan  | 一个时间值，默认值为 00:00:00。 | 否 |
| **maxConcurrency** | 同时针对已就绪窗口触发的触发器运行数。 例如，若要每小时回填，昨天的运行会产生 24 个 windows。 如果 **maxConcurrency** = 10，则仅针对头 10 个窗口 (00:00-01:00 - 09:00-10:00) 触发触发器事件。 在头 10 个触发的管道运行完成后，将针对接下来的 10 个窗口 (10:00-11:00 - 19:00-20:00) 触发触发器运行。 继续进行 **maxConcurrency** = 10 的此示例，如果有 10 个窗口就绪，则总共有 10 个管道运行。 如果只有 1 个窗口就绪，则只有 1 管道运行。 | Integer | 一个介于 1 到 50 之间的整数。 | 是 |
| **retryPolicy: Count** | 将管道运行标记为“失败”之前的重试次数。  | Integer | 一个整数，其默认值为 0（不重试）。 | 否 |
| **retryPolicy: intervalInSeconds** | 重试之间的延迟（以秒为单位指定）。 | Integer | 秒数，其默认值为 30。 | 否 |

### <a name="windowstart-and-windowend-system-variables"></a>WindowStart 和 WindowEnd 系统变量

可以在**管道**定义中（即，作为查询的一部分），使用翻转窗口触发器的 **WindowStart** 和 **WindowEnd** 系统变量。 **触发器**定义中将系统变量作为参数传递给管道。 下面的示例演示如何将这些变量作为参数传递：

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

若要在管道定义中使用 **WindowStart** 和 **WindowEnd** 系统变量值，请相应地使用“MyWindowStart”和“MyWindowEnd”参数。

### <a name="execution-order-of-windows-in-a-backfill-scenario"></a>回填方案中的窗口执行顺序
有多个窗口需要执行时（尤其是在回填的情况下），窗口的执行顺序是确定的，将根据时间间隔的新旧按从旧到新的顺序执行。 当前无法修改此行为。

### <a name="existing-triggerresource-elements"></a>现有 TriggerResource 元素
以下各点适用于现有 **TriggerResource** 元素：

* 如果触发器的 **frequency** 元素（或窗口大小）的值更改，则已处理窗口的状态不会重置。 触发器会根据新的窗口大小继续触发一段时间（从上次执行开始计算）。
* 如果触发器的 **endTime** 元素的值更改（添加或更新），则已处理窗口的状态不会重置。 触发器会遵循新的 **endTime** 值。 如果新的 **endTime** 值在已执行的窗口之前，则触发器会停止。 否则，触发器会在遇到新的 **endTime** 值停止。

## <a name="sample-for-azure-powershell"></a>Azure PowerShell 示例
本部分展示了如何使用 Azure PowerShell 创建、启动和监视触发器。

1. 在 C:\ADFv2QuickStartPSH\ 文件夹中创建一个名为 **MyTrigger.json** 的 JSON 文件，使其包含以下内容：

   > [!IMPORTANT]
   > 保存 JSON 文件之前，将 **startTime** 元素的值设置为当前 UTC 时间。 将 **endTime** 元素的值设置为比当前 UTC 时间早一小时。

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

2. 使用 **Set-AzureRmDataFactoryV2Trigger** cmdlet 创建一个触发器：

    ```powershell
    Set-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    ```
    
3. 使用 **Get-AzureRmDataFactoryV2Trigger** cmdlet 确认触发器的状态为 **Stopped**：

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

4. 使用 **Start-AzureRmDataFactoryV2Trigger** cmdlet 启动触发器：

    ```powershell
    Start-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

5. 使用 **Get-AzureRmDataFactoryV2Trigger** cmdlet 确认触发器的状态为 **Started**：

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

6. 使用 **Get-AzureRmDataFactoryV2TriggerRun** cmdlet 在 Azure PowerShell 中获取触发器运行。 若要获取有关触发器运行的信息，请定期执行以下命令。 更新 **TriggerRunStartedAfter** 和 **TriggerRunStartedBefore** 值以匹配触发器定义中的值：

    ```powershell
    Get-AzureRmDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```
    
若要在 Azure 门户中监视触发器运行和管道运行，请参阅[监视管道运行](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)。

## <a name="next-steps"></a>后续步骤
有关触发器的详细信息，请参阅[管道执行和触发器](concepts-pipeline-execution-triggers.md#triggers)。
