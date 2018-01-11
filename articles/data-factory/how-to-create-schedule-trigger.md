---
title: "如何在 Azure 数据工厂中创建触发器 | Microsoft Docs"
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
ms.openlocfilehash: eed286c01604ab0e9ac2113d56cbce268503668d
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/12/2017
---
# <a name="how-to-create-a-trigger-that-runs-a-pipeline-on-a-schedule"></a>如何创建按计划运行管道的触发器
本文提供了创建、启动和监视触发器的步骤。 有关触发器的概念信息，请参阅[管道执行和触发器](concepts-pipeline-execution-triggers.md)。

> [!NOTE]
> 本文适用于目前处于预览状态的数据工厂版本 2。 如果使用数据工厂服务版本 1（即正式版 (GA)），请参阅[数据工厂版本 1 入门](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。


## <a name="use-azure-powershell"></a>使用 Azure PowerShell
本部分展示了如何使用 Azure PowerShell 创建、启动和监视触发器。 如果希望看到此示例工作，请首先完成[快速入门：使用 Azure PowerShell 创建数据工厂](quickstart-create-data-factory-powershell.md)。 然后，将以下代码添加到 main 方法，该代码用于创建并启动一个每 15 分钟运行一次的计划触发器。 该触发器与你在快速入门中创建的一个管道 (**Adfv2QuickStartPipeline**) 相关联。

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