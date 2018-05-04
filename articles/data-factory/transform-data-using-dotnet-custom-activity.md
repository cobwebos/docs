---
title: 在 Azure 数据工厂管道中使用自定义活动
description: 了解如何创建自定义活动并在 Azure 数据工厂管道中使用。
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: douglasl
ms.openlocfilehash: a9e70ad5296a832e711ebac97302d56429ab5bff
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
---
# <a name="use-custom-activities-in-an-azure-data-factory-pipeline"></a>在 Azure 数据工厂管道中使用自定义活动
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [版本 1 - 正式版](v1/data-factory-use-custom-activities.md)
> * [版本 2 - 预览版](transform-data-using-dotnet-custom-activity.md)

在 Azure 数据工厂管道中可使用两类活动。

- [数据移动活动](copy-activity-overview.md)：在[支持的源与接收器数据存储](copy-activity-overview.md#supported-data-stores-and-formats)之间移动数据。
- [数据转换活动](transform-data.md)：使用 Azure HDInsight、Azure Batch 和 Azure 机器学习等计算服务转换数据。 

若要将数据移入/移出数据工厂不支持的数据存储，或者要以数据工厂不支持的方式转换/处理数据，可以使用你自己的数据移动或转换逻辑创建**自定义活动**，并在管道中使用该活动。 自定义活动在虚拟机的 **Azure Batch** 池上运行自定义代码逻辑。

> [!NOTE]
> 本文适用于目前处于预览版的数据工厂版本 2。 如果使用正式版 (GA) 版本 1 的数据工厂服务，请参阅[数据工厂版本 1 中的（自定义）DotNet 活动](v1/data-factory-use-custom-activities.md)。
 

如果不熟悉 Azure Batch 服务，请参阅以下文章：

* [Azure Batch 基础知识](../batch/batch-technical-overview.md) - Azure Batch 服务的概述。
* [New-AzureRmBatchAccount](/powershell/module/azurerm.batch/New-AzureRmBatchAccount?view=azurermps-4.3.1) cmdlet - 创建 Azure Batch 帐户（或）[Azure 门户](../batch/batch-account-create-portal.md) - 使用 Azure 门户创建 Azure Batch 帐户。 请参阅 [Using PowerShell to manage Azure Batch Account](http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx)（使用 Azure PowerShell 管理 Azure Batch 帐户）一文，了解有关使用此 cmdlet 的详细说明。
* [New-AzureBatchPool](/powershell/module/azurerm.batch/New-AzureBatchPool?view=azurermps-4.3.1) cmdlet - 创建 Azure Batch 池。

## <a name="azure-batch-linked-service"></a>Azure Batch 链接服务 
下面的 JSON 定义了一个示例 Azure Batch 链接服务。 有关详细信息，请参阅 [Azure 数据工厂支持的计算环境](compute-linked-services.md)

```json
{
    "name": "AzureBatchLinkedService",
    "properties": {
        "type": "AzureBatch",
        "typeProperties": {
            "accountName": "batchaccount",
            "accessKey": {
                "type": "SecureString",
                "value": "access key"
            },
            "batchUri": "https://batchaccount.region.batch.azure.com",
            "poolName": "poolname",
            "linkedServiceName": {
                "referenceName": "StorageLinkedService",
                "type": "LinkedServiceReference"
            }
        }
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

 若要了解有关 Azure Batch 链接服务的详细信息，请参阅[计算链接服务](compute-linked-services.md)一文。 

## <a name="custom-activity"></a>自定义活动

下面的 JSON 代码片段定义了具有简单自定义活动的管道。 活动定义引用了 Azure Batch 链接服务。 

```json
{
    "name": "MyCustomActivityPipeline",
    "properties": {
      "description": "Custom activity sample",
      "activities": [{
        "type": "Custom",
        "name": "MyCustomActivity",
        "linkedServiceName": {
          "referenceName": "AzureBatchLinkedService",
          "type": "LinkedServiceReference"
        },
        "typeProperties": {
          "command": "helloworld.exe",
          "folderPath": "customactv2/helloworld",
          "resourceLinkedService": {
            "referenceName": "StorageLinkedService",
            "type": "LinkedServiceReference"
          }
        }
      }]
    }
  }
```

在此示例中，helloworld.exe 是一个自定义应用程序，存储在 resourceLinkedService 中使用的 Azure 存储帐户的 customactv2/helloworld 文件夹。 自定义活动提交此自定义应用程序以便在 Azure Batch 上执行。 可以将命令替换为可以在 Azure Batch 池节点的目标操作系统上执行的任何所需应用程序的命令。 

下表描述了此活动特有的属性的名称和描述。 

| 属性              | 说明                              | 必选 |
| :-------------------- | :--------------------------------------- | :------- |
| 名称                  | 管道中活动的名称     | 是      |
| description           | 描述活动用途的文本。  | 否       |
| type                  | 对于自定义活动，活动类型为 **Custom**。 | 是      |
| linkedServiceName     | Azure Batch 的链接服务。 若要了解此链接服务，请参阅[计算链接服务](compute-linked-services.md)一文。  | 是      |
| command               | 要执行的自定义应用程序的命令。 如果应用程序在 Azure Batch 池节点上已可用，可以跳过 resourceLinkedService 和 folderPath。 例如，可以将命令指定为 `cmd /c dir`，Windows Batch 池节点针对该命令提供了本机支持。 | 是      |
| resourceLinkedService | 存储着自定义应用程序的存储帐户的 Azure 存储链接服务 | 否       |
| folderPath            | 自定义应用程序及其所有依赖项所在的文件夹的路径 | 否       |
| referenceObjects      | 现有链接服务和数据集的数组。 所引用的链接服务和数据集采用 JSON 格式传递到自定义应用程序，因此，自定义代码可以引用数据工厂的资源 | 否       |
| extendedProperties    | 可以采用 JSON 格式传递到自定义应用程序的用户定义属性，以便自定义代码可以引用更多属性 | 否       |

## <a name="executing-commands"></a>执行命令

可以直接使用自定义活动执行命令。 以下示例在目标 Azure Batch 池节点上运行“echo hello world”命令，并将输出传输到 stdout。 

  ```json
  {
    "name": "MyCustomActivity",
    "properties": {
      "description": "Custom activity sample",
      "activities": [{
        "type": "Custom",
        "name": "MyCustomActivity",
        "linkedServiceName": {
          "referenceName": "AzureBatchLinkedService",
          "type": "LinkedServiceReference"
        },
        "typeProperties": {
          "command": "cmd /c echo hello world"
        }
      }]
    }
  } 
  ```

## <a name="passing-objects-and-properties"></a>传递对象和属性

此示例展示了如何使用 referenceObjects 和 extendedProperties 将数据工厂对象和用户定义的属性传递到自定义应用程序。 


```json
{
  "name": "MyCustomActivityPipeline",
  "properties": {
    "description": "Custom activity sample",
    "activities": [{
      "type": "Custom",
      "name": "MyCustomActivity",
      "linkedServiceName": {
        "referenceName": "AzureBatchLinkedService",
        "type": "LinkedServiceReference"
      },
      "typeProperties": {
        "command": "SampleApp.exe",
        "folderPath": "customactv2/SampleApp",
        "resourceLinkedService": {
          "referenceName": "StorageLinkedService",
          "type": "LinkedServiceReference"
        },
        "referenceObjects": {
          "linkedServices": [{
            "referenceName": "AzureBatchLinkedService",
            "type": "LinkedServiceReference"
          }]
        },
        "extendedProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "aSampleSecureString"
            },
            "PropertyBagPropertyName1": "PropertyBagValue1",
            "propertyBagPropertyName2": "PropertyBagValue2",
            "dateTime1": "2015-04-12T12:13:14Z"              
        }
      }
    }]
  }
}
```

执行活动时，referenceObjects 和 extendedProperties 存储在以下文件中，这些文件部署到 SampleApp.exe 所在的同一执行文件夹中： 

- activity.json

  存储自定义活动的 extendedProperties 和属性。 

- linkedServices.json

  存储 referenceObjects 属性中定义的链接服务的数组。 

- datasets.json

  存储 referenceObjects 属性中定义的数据集的数组。 

下面的示例代码演示了 SampleApp.exe 如何从 JSON 文件中访问所需的信息： 

```csharp
using Newtonsoft.Json;
using System;
using System.IO;

namespace SampleApp
{
    class Program
    {
        static void Main(string[] args)
        {
            //From Extend Properties
            dynamic activity = JsonConvert.DeserializeObject(File.ReadAllText("activity.json"));
            Console.WriteLine(activity.typeProperties.extendedProperties.connectionString.value);

            // From LinkedServices
            dynamic linkedServices = JsonConvert.DeserializeObject(File.ReadAllText("linkedServices.json"));
            Console.WriteLine(linkedServices[0].properties.typeProperties.connectionString.value);
        }
    }
}
```

## <a name="retrieve-execution-outputs"></a>检索执行输出

  可以使用以下 PowerShell 命令启动管道运行： 

  ```.powershell
  $runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName $pipelineName
  ```
  管道运行时，可以使用以下命令查看执行输出： 

  ```.powershell
  while ($True) {
      $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

      if(!$result) {
          Write-Host "Waiting for pipeline to start..." -foregroundcolor "Yellow"
      }
      elseif (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
          Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
      }
      else {
          Write-Host "Pipeline '"$pipelineName"' run finished. Result:" -foregroundcolor "Yellow"
          $result
          break
      }
      ($result | Format-List | Out-String)
      Start-Sleep -Seconds 15
  }

  Write-Host "Activity `Output` section:" -foregroundcolor "Yellow"
  $result.Output -join "`r`n"

  Write-Host "Activity `Error` section:" -foregroundcolor "Yellow"
  $result.Error -join "`r`n"
  ```

  自定义应用程序的 **stdout** 和 **stderr** 保存到在使用任务的 GUID 创建 Azure Batch 链接服务时定义的 Azure 存储链接服务中的 **adfjobs** 容器。 可以从活动运行输出中获取详细路径，如以下代码片段中所示： 

  ```shell
  Pipeline ' MyCustomActivity' run finished. Result:

  ResourceGroupName : resourcegroupname
  DataFactoryName   : datafactoryname
  ActivityName      : MyCustomActivity
  PipelineRunId     : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
  PipelineName      : MyCustomActivity
  Input             : {command}
  Output            : {exitcode, outputs, effectiveIntegrationRuntime}
  LinkedServiceName : 
  ActivityRunStart  : 10/5/2017 3:33:06 PM
  ActivityRunEnd    : 10/5/2017 3:33:28 PM
  DurationInMs      : 21203
  Status            : Succeeded
  Error             : {errorCode, message, failureType, target}

  Activity Output section:
  "exitcode": 0
  "outputs": [
    "https://shengcstorbatch.blob.core.windows.net/adfjobs/<GUID>/output/stdout.txt",
    "https://shengcstorbatch.blob.core.windows.net/adfjobs/<GUID>/output/stderr.txt"
  ]
  "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)"
  Activity Error section:
  "errorCode": ""
  "message": ""
  "failureType": ""
  "target": "MyCustomActivity"
  ```
如果要在下游活动中使用 stdout.txt 的内容，则可以在表达式“@activity('MyCustomActivity').output.outputs[0]”中获取 stdout.txt 文件的路径。 

  > [!IMPORTANT]
  > - activity.json、linkedServices.json 和 datasets.json 存储在 Batch 任务的 runtime 文件夹中。 在此示例中，activity.json、linkedServices.json 和 datasets.json 存储在“https://adfv2storage.blob.core.windows.net/adfjobs/<GUID>/runtime/”路径中。 必要时需要单独清理它们。 
  > - 对于使用自承载集成运行时的链接服务，将通过自承载集成运行时对敏感信息（例如密钥或密码）进行加密，以确保凭据保留在客户定义的专用网络环境中。 以此方式在自定义应用程序代码中进行引用时，可能会丢掉一些敏感字段。 如果需要，请在 extendedProperties 中使用 SecureString 而非使用链接服务引用。 

## <a name="compare-v2-v1"></a>比较 v2 自定义活动和版本 1（自定义）DotNet 活动

  在 Azure 数据工厂版本 1 中，通过使用实现 `IDotNetActivity` 接口的 `Execute` 方法的类创建 .Net 类库项目来实现（自定义）DotNet 活动。 （自定义）DotNet 活动的 JSON 负载中的链接服务、数据集和扩展属性作为强类型对象传递到执行方法。 有关版本 1 行为的详细信息，请参阅[版本 1 中的（自定义）DotNet](v1/data-factory-use-custom-activities.md)。 由于此实现，版本 1 DotNet 活动节点必须面向 .Net Framework 4.5.2。 版本 1 DotNet 活动还必须在基于 Windows 的 Azure Batch 池节点上执行。 

  在 Azure 数据工厂 V2 自定义活动中，不需要实现 .Net 接口。 现在可以直接运行命令、脚本和自己的已编译为可执行文件的自定义代码。 要配置该实现，请指定 `Command` 属性和 `folderPath` 属性。 自定义活动会将可执行文件及其依赖项上传到 `folderpath`，并执行命令。 

  可执行文件可以将链接服务、数据集（在 referenceObjects 中定义）和数据工厂 v2 自定义活动的 JSON 有效负载中定义的扩展属性作为 JSON 文件进行访问。 可以使用 JSON 序列化程序访问所需的属性，如前面的 SampleApp.exe 代码示例所示。 

  借助数据工厂 V2 自定义活动中引入的更改，可以使用自己喜欢的语言编写自定义代码逻辑，然后在 Azure Batch 支持的 Windows 和 Linux 操作系统上执行该代码逻辑。 

  下表介绍数据工厂 V2 自定义活动和数据工厂版本 1（自定义）DotNet 活动之间的差异： 


|差异      |版本 2 自定义活动      | 版本 1（自定义）DotNet 活动      |
| ---- | ---- | ---- |
|如何定义自定义逻辑      |通过提供可执行文件      |通过实施 .Net DLL      |
|自定义逻辑的执行环境      |Windows 或 Linux      |Windows (.Net Framework 4.5.2)      |
|执行脚本      |直接支持执行脚本（如 Windows VM 上的“cmd /c echo hello world”）      |需要在 .Net DLL 中实施      |
|所需数据集      |可选      |需要链接活动并传递信息      |
|将信息从活动传递到自定义逻辑      |通过 ReferenceObjects（LinkedServices 和数据集）与 ExtendedProperties（自定义属性）      |通过 ExtendedProperties（自定义属性）、输入和输出数据集      |
|在自定义逻辑中检索信息      |分析可执行文件所在文件夹中存储的 activity.json、linkedServices.json 和 datasets.json      |通过 .Net SDK (.Net Frame 4.5.2)      |
|日志记录      |直接写入到 STDOUT      |在 .Net DLL 中实施记录器      |


  如果有针对版本 1（自定义）DotNet 活动编写的现有 .Net 代码，则需要修改代码，使其可用于版本 2 自定义活动。 按照以下高级准则更新代码：  

   - 将项目从 .Net 类库更改到控制台应用。 
   - 使用 `Main` 方法启动应用程序。 不再需要 `IDotNetActivity` 接口的 `Execute` 方法。 
   - 使用 JSON 序列化程序并且不作为强类型对象读取和分析链接服务、数据集和活动。 将所需属性的值传递到主要自定义代码逻辑。 可将前面的 SampleApp.exe 代码作为示例进行参考。 
   - 不再支持 Logger 对象。 可将可执行文件的输出打印到控制台并保存到 stdout.txt。 
   - 不再需要 Microsoft.Azure.Management.DataFactories NuGet 包。 
   - 编译代码，将可执行文件及其依赖项上传到 Azure 存储，并在 `folderPath` 属性中定义路径。 

有关端到端 DLL 和数据工厂版本 1 文章[在 Azure 数据工厂管道中使用自定义活动](https://docs.microsoft.com/azure/data-factory/v1/data-factory-use-custom-activities)中所述的管道示例如何重写为数据工厂版本 2 自定义活动的完整示例，请参阅[数据工厂版本 2 自定义活动示例](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ADFv2CustomActivitySample)。 

## <a name="auto-scaling-of-azure-batch"></a>Azure Batch 的自动缩放
还可以使用**自动缩放**功能创建 Azure Batch 池。 例如，可以根据挂起任务的数量不使用专用 VM 但使用自动缩放公式创建 Azure 批处理池。 

此处的示例公式实现以下行为：最初创建池时，它从 1 个 VM 开始。 $PendingTasks 度量值定义处于正在运行状态和活动（已排队）状态中的任务数。  该公式查找过去 180 秒内的平均挂起任务数，并相应地设置 TargetDedicated。 它可确保 TargetDedicated 永不超过 25 个 VM。 因此，随着新任务的提交，池会自动增长；随着任务的完成，VM 会逐个释放，并且自动缩放功能会收缩这些 VM。 可根据自己的需要调整 startingNumberOfVMs 和 maxNumberofVMs。

自动缩放公式：

``` 
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
```

有关详细信息，请参阅 [Automatically scale compute nodes in an Azure Batch pool](../batch/batch-automatic-scaling.md)（自动缩放 Azure Batch 池中的计算节点）。

如果池使用默认 [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx)，则在运行自定义活动之前，Batch 服务可能需要 15-30 分钟准备 VM。  如果池使用其他 autoScaleEvaluationInterval，则 Batch 服务可能需要 autoScaleEvaluationInterval + 10 分钟。


## <a name="next-steps"></a>后续步骤
参阅以下文章了解如何以其他方式转换数据： 

* [U-SQL 活动](transform-data-using-data-lake-analytics.md)
* [Hive 活动](transform-data-using-hadoop-hive.md)
* [Pig 活动](transform-data-using-hadoop-pig.md)
* [MapReduce 活动](transform-data-using-hadoop-map-reduce.md)
* [Hadoop 流式处理活动](transform-data-using-hadoop-streaming.md)
* [Spark 活动](transform-data-using-spark.md)
* [机器学习“批处理执行”活动](transform-data-using-machine-learning.md)
* [存储过程活动](transform-data-using-stored-procedure.md)
