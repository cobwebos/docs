---
title: "创建/计划管道、链接数据工厂中的活动 | Microsoft Docs"
description: "了解如何在 Azure 数据工厂中创建数据管道来移动和转换数据。 创建数据驱动工作流以生成可用信息。"
keywords: "数据管道, 数据驱动工作流"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: 13b137c7-1033-406f-aea7-b66f25b313c0
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2016
ms.author: shlo
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: d841c57dc736f7d690a6dc97863b7568365fd01a


---
# <a name="pipelines-and-activities-in-azure-data-factory"></a>Azure 数据工厂中的管道和活动
本文帮助你了解 Azure 数据工厂中的管道和活动，并帮助你利用它们为数据移动和数据处理方案构造端对端数据驱动工作流。  

> [!NOTE]
> 本文假定你已浏览 [Azure 数据工厂简介](data-factory-introduction.md)。 如果还没有亲身体验如何创建数据工厂，请浏览[生成首个数据工厂](data-factory-build-your-first-pipeline.md)教程，更好地理解该文章。  
> 
> 

## <a name="what-is-a-data-pipeline"></a>什么是数据管道？
**管道**是逻辑上相关**活动**的分组。 它用于将活动分组成执行某项任务的单位。 若要更好地理解管道，需要先了解活动。 

## <a name="what-is-an-activity"></a>什么是活动？
活动定义对数据执行的操作。 每个活动使用零个或多个[数据集](data-factory-create-datasets.md)作为输入，生成一个或多个数据集作为输出。 

例如，可使用“复制活动”协调两个数据存储间的数据复制操作。 同样，通过 HDInsight Hive 活动，可对 Azure HDInsight 群集运行 Hive 查询以转换数据。 Azure 数据工厂提供各种类型的[数据转换](data-factory-data-transformation-activities.md)和[数据移动](data-factory-data-movement-activities.md)活动。 还可选择创建自定义 .NET 活动来运行自己的代码。 

## <a name="sample-copy-pipeline"></a>复制管道示例
在以下示例管道中，**activities** 节有一个 **Copy** 类型的活动。 在此示例中，[复制活动](data-factory-data-movement-activities.md)将 Azure Blob 存储中的数据复制到 Azure SQL 数据库。 

```json
{
  "name": "CopyPipeline",
  "properties": {
    "description": "Copy data from a blob to Azure SQL table",
    "activities": [
      {
        "name": "CopyFromBlobToSQL",
        "type": "Copy",
        "inputs": [
          {
            "name": "InputDataset"
          }
        ],
        "outputs": [
          {
            "name": "OutputDataset"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink",
            "writeBatchSize": 10000,
            "writeBatchTimeout": "60:00:00"
          }
        },
        "Policy": {
          "concurrency": 1,
          "executionPriorityOrder": "NewestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ],
    "start": "2016-07-12T00:00:00Z",
    "end": "2016-07-13T00:00:00Z"
  }
} 
```

请注意以下几点：

* 在 activities 节中，只有一个活动的 **type** 设置为 **Copy**。
* 活动的输入设置为 **InputDataset**，活动的输出设置为 **OutputDataset**。
* 在 **typeProperties** 节中，**BlobSource** 指定为源类型，**SqlSink** 指定为接收器类型。

有关创建此管道的完整演练，请参阅[教程：将 Blob 存储中的数据复制到 SQL 数据库](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。 

## <a name="sample-transformation-pipeline"></a>转换管道示例
在以下示例管道中，**activities** 节有一个 **HDInsightHive** 类型的活动。 在此示例中，[HDInsight Hive 活动](data-factory-hive-activity.md)通过在 Azure HDInsight Hadoop 群集上运行 Hive 脚本文件，转换 Azure Blob 存储中的数据。 

```json
{
    "name": "TransformPipeline",
    "properties": {
        "description": "My first Azure Data Factory pipeline",
        "activities": [
            {
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                    "scriptLinkedService": "AzureStorageLinkedService",
                    "defines": {
                        "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                        "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
                    }
                },
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "policy": {
                    "concurrency": 1,
                    "retry": 3
                },
                "scheduler": {
                    "frequency": "Month",
                    "interval": 1
                },
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "HDInsightOnDemandLinkedService"
            }
        ],
        "start": "2016-04-01T00:00:00Z",
        "end": "2016-04-02T00:00:00Z",
        "isPaused": false
    }
}
```

请注意以下几点： 

* 在 activities 节中，只有一个活动的 **type** 设置为 **HDInsightHive**。
* Hive 脚本文件 **partitionweblogs.hql** 存储在 Azure 存储帐户（由 scriptLinkedService 指定，名为 **AzureStorageLinkedService**）中，以及 **adfgetstarted** 容器的 **script** 文件夹中。
* **defines** 节用于指定运行时设置，这些设置将作为 Hive 配置值（例如 ${hiveconf:inputtable}、${hiveconf:partitionedtable}）传递到 Hive 脚本。

有关创建此管道的完整演练，请参阅[教程：生成首个管道以使用 Hadoop 群集处理数据](data-factory-build-your-first-pipeline.md)。 

## <a name="chaining-activities"></a>链接活动
如果管道中有多个活动，且一个活动的输出不是其他活动的输入，则若活动的输入数据切片准备就绪，活动可能会并行运行。 

通过让一个活动的输出数据集作为另一活动的输入数据集，可以链接两个活动。 这两个活动可在相同管道中，也可在不同管道中。 只有第一个活动成功完成时才执行第二个活动。 

例如，考虑以下情况：

1. 管道 P1 具有需要外部输入数据集 D1 的活动 A1，并生成 **输出**数据集 **D2**。
2. 管道 P2 具有活动 A2，该活动要求来自数据集 **D2** 的**输入**，并生成输出数据集 D3。

此情况下，当外部数据可用且达到计划可用性频率时，运行活动 A1。  来自 D2 的计划切片可用且达到计划可用性频率时，运行活动 A2。 如果数据集 D2 中的某个切片发生错误，则在该切片可用之前，A2 不会运行该切片。

图示视图：

![链接两个管道中的活动](./media/data-factory-create-pipelines/chaining-two-pipelines.png)

相同管道中两个活动的图示视图： 

![链接相同管道中的活动](./media/data-factory-create-pipelines/chaining-one-pipeline.png)

有关详细信息，请参阅[计划和执行](#chaining-activities)。 

## <a name="scheduling-and-execution"></a>计划和执行
此时，你已了解何为管道和活动。 也了解了如何定义它们以及 Azure 数据工厂中的高级活动视图。 现在来了解执行方式。 

仅在开始时间和结束时间之间，管道才处于活动状态。 开始时间之前或结束时间之后，不会执行管道。 如果暂停管道，则无论开始和结束时间，都不会执行管道。 不暂停才可运行管道。 事实上，执行的不是管道， 而是管道中的活动。 但是，这些活动是在整体管道环境中执行的。 

若要了解如何在 Azure 数据工厂中计划和执行工作，请参阅[计划和执行](data-factory-scheduling-and-execution.md)。

## <a name="create-pipelines"></a>创建管道
Azure 数据工厂提供各种机制来创作和部署管道（管道包含一个或多个活动）。 

### <a name="using-azure-portal"></a>使用 Azure 门户
可使用 Azure 门户中的数据工厂编辑器创建管道。 有关端到端的演练，请参阅 [Azure 数据工厂入门（数据工厂编辑器）](data-factory-build-your-first-pipeline-using-editor.md)。 

### <a name="using-visual-studio"></a>使用 Visual Studio
可使用 Visual Studio 创作管道并将其部署到 Azure 数据工厂。 有关端到端的演练，请参阅 [Azure 数据工厂 (Visual Studio)](data-factory-build-your-first-pipeline-using-vs.md)。 

### <a name="using-azure-powershell"></a>使用 Azure PowerShell
可在 Azure 数据工厂中使用 Azure PowerShell 创建管道。 假设你已在 c:\DPWikisample.json 处的文件中定义管道 JSON。 可将其上传到 Azure 数据工厂实例，如下例所示：

```PowerShell
New-AzureRmDataFactoryPipeline -ResourceGroupName ADF -Name DPWikisample -DataFactoryName wikiADF -File c:\DPWikisample.json
```

有关使用管道创建数据工厂的端到端演练，请参阅 [Azure 数据工厂入门 (Azure PowerShell)](data-factory-build-your-first-pipeline-using-powershell.md)。 

### <a name="using-net-sdk"></a>使用 .NET SDK
也可通过 .NET SDK 创建和部署管道。 此机制可用于以编程方式创建管道。 有关详细信息，请参阅[以编程方式创建、管理和监视数据工厂](data-factory-create-data-factories-programmatically.md)。 

### <a name="using-azure-resource-manager-template"></a>使用 Azure Resource Manager 模板
可使用 Azure Resource Manager 模板创建和部署管道。 有关详细信息，请参阅 [Azure 数据工厂入门 (Azure Resource Manager)](data-factory-build-your-first-pipeline-using-arm.md)。 

### <a name="using-rest-api"></a>使用 REST API
也可通过 REST API 创建和部署管道。 此机制可用于以编程方式创建管道。 有关详细信息，请参阅[创建或更新管道](https://msdn.microsoft.com/library/azure/dn906741.aspx)。 

## <a name="monitor-and-manage-pipelines"></a>监视和管理管道
管道部署后，可管理和监视管道、切片以及运行。 在此处阅读详细信息：[监视器和管理管道](data-factory-monitor-manage-pipelines.md)。

## <a name="pipeline-json"></a>管道 JSON
让我们深入了解如何以 JSON 格式定义管道。 管道的泛型结构如下所示：

```json
{
    "name": "PipelineName",
    "properties": 
    {
        "description" : "pipeline description",
        "activities":
        [

        ],
        "start": "<start date-time>",
        "end": "<end date-time>"
    }
}
```

**activities** 节中可定义有一个或多个活动。 每个活动均具有以下顶级结构：

```json
{
    "name": "ActivityName",
    "description": "description", 
    "type": "<ActivityType>",
    "inputs":  "[]",
    "outputs":  "[]",
    "linkedServiceName": "MyLinkedService",
    "typeProperties":
    {

    },
    "policy":
    {
    }
    "scheduler":
    {
    }
}
```

下表介绍了活动和管道 JSON 定义中的属性：

| 标记 | 说明 | 必选 |
| --- | --- | --- |
| name |活动或管道的名称。 指定一个名称，表示配置为活动或管道要执行的操作<br/><ul><li>最大字符数：260</li><li>必须以字母、数字或下划线 (_) 开头</li><li>不允许使用以下字符：“.”、“+”、“?”、“/”、“<”、“>”、“*”、“%”、“&”、“:”、“\\”</li></ul> |是 |
| description |描述活动或管道用途的文本 |是 |
| type |指定活动的类型。 有关不同的活动类型，请参阅[数据移动活动](data-factory-data-movement-activities.md)和[数据转换活动](data-factory-data-transformation-activities.md)文章。 |是 |
| inputs |活动使用的输入表<br/><br/>//一个输入表<br/>"inputs":  [ { "name": "inputtable1"  } ],<br/><br/>//两个输入表 <br/>"inputs":  [ { "name": "inputtable1"  }, { "name": "inputtable2"  } ], |是 |
| outputs |活动使用的输出表.// 一个输出表<br/>"outputs":  [ { "name": “outputtable1” } ],<br/><br/>//两个输出表<br/>"outputs":  [ { "name": “outputtable1” }, { "name": “outputtable2” }  ], |是 |
| linkedServiceName |活动使用的链接服务的名称。 <br/><br/>活动可能需要你指定链接到所需计算环境的链接服务。 |对 HDInsight 活动和 Azure 机器学习批处理评分活动是必需的 <br/><br/>对其他活动均非必需 |
| typeProperties |typeProperties 部分的属性取决于活动类型。 |否 |
| policy |影响活动运行时行为的策略。 如果未指定，将使用默认策略。 |否 |
| start |管道的开始日期-时间。 必须为 [ISO 格式](http://en.wikipedia.org/wiki/ISO_8601)。 例如：2014-10-14T16:32:41Z。 <br/><br/>可指定本地时间，如 EST 时间。 示例为："2016-02-27T06:00:00**-05:00**"，表示 EST 上午 6 点。<br/><br/>start 和 end 属性共同指定管道的活动期限。 仅在此活动期限内生成输出切片。 |否<br/><br/>如果要指定 end 属性值，必须指定 start 属性值。<br/><br/>创建管道时，开始和结束时间均可为空。 必须指定这两个值，才能设置管道运行的活动期限。 如果创建管道时未指定开始和结束时间，可稍后使用 Set-AzureRmDataFactoryPipelineActivePeriod cmdlet 进行设置。 |
| end |管道的结束日期-时间。 指定时必须采用 ISO 格式。 例如：2014-10-14T17:32:41Z <br/><br/>可指定本地时间，如 EST 时间。 示例为："2016-02-27T06:00:00**-05:00**"，表示 EST 上午 6 点。<br/><br/>若要无限期运行管道，请指定 9999-09-09 作为 end 属性的值。 |否 <br/><br/>如果指定 start 属性的值，必须指定 end 属性的值。<br/><br/>请参阅 **start** 属性的说明。 |
| isPaused |如果设置为 true，管道不会运行。 默认值 = false。 此属性可用于启用或禁用。 |否 |
| scheduler |“scheduler”属性用于定义所需的活动计划。 其子属性与[数据集中可用性属性](data-factory-create-datasets.md#Availability)的子属性相同。 |否 |
| pipelineMode |针对管道计划运行的方式。 允许值为：已计划(默认)，一次性。<br/><br/>“Scheduled”表示管道根据管道活动期（开始和结束时间）按指定的时间间隔运行。 “Onetime”表示管道只运行一次。 当前，一次性管道创建后即无法修改/更新。 有关一次性设置的详细信息，请参阅[一次性管道](data-factory-scheduling-and-execution.md#onetime-pipeline)。 |否 |
| expirationTime |创建后的持续时间，在该时间内管道需有效且保持预配状态。 如果管道不具有任何活动的、失败的或挂起的运行，则该管道在到期后将自动删除。 |否 |
| datasets |数据集列表，这些数据集将由管道中定义的活动使用。 此属性可用于此管道特定的但未在数据工厂中定义的数据集。 此管道中定义的数据集仅可用于此管道且不可共享。 有关详细信息，请参阅[指定了作用域的数据集](data-factory-create-datasets.md#scoped-datasets)。 |否 |

### <a name="policies"></a>策略
策略会影响活动的运行时行为，尤其在处理表的切片时。 下表提供详细信息。

| 属性 | 允许的值 | 默认值 | 说明 |
| --- | --- | --- | --- |
| concurrency |Integer <br/><br/>最大值：10 |1 |活动的并发执行次数。<br/><br/>它决定可在不同切片上发生的并行活动执行次数。 例如，如果活动需要完成大量可用数据，更大的并发值能加快数据处理速度。 |
| executionPriorityOrder |NewestFirst<br/><br/>OldestFirst |OldestFirst |确定正在处理的数据切片的顺序。<br/><br/>例如，你有两个切片（分别发生在下午 4 点和下午 5 点），且均在等待执行。 如果将 executionPriorityOrder 设置为 NewestFirst，则首先处理下午 5 点的切片。 同理，如果将 executionPriorityORder 设置为 OldestFIrst，则先处理下午 4 点的切片。 |
| retry |Integer<br/><br/>最大值可为 10 |0 |将切片的数据处理标记为“失败”之前的重试次数。 数据切片的活动执行次数不能超过指定的重试次数。 出现故障后尽快重试。 |
| timeout |TimeSpan |00:00:00 |活动的超时。 示例：00:10:00（表示超时 10 分钟）<br/><br/>如果不指定值或值为 0，则表示无限超时。<br/><br/>如果某个切片的数据处理时间超出了超时值，将取消该处理，且系统尝试重试处理。 重试次数取决于重试属性。 发生超时时，会将状态设置为“超时”。 |
| delay |TimeSpan |00:00:00 |启动切片的数据处理前，需指定延迟。<br/><br/>延迟超过预期执行时间后，启动数据切片的活动执行。<br/><br/>示例：00:10:00（表示延迟 10 分钟） |
| longRetry |Integer<br/><br/>最大值：10 |1 |切片执行失败之前的长重试次数。<br/><br/>longRetryInterval 指定尝试 longRetry 的间隔。 因此，如果需要指定重试尝试之间的时间，请使用 longRetry。 如果同时指定 Retry 和 longRetry，则每次 longRetry 尝试均包含 Retry 尝试，且最大尝试次数为 Retry * longRetry。<br/><br/>例如，如果活动策略中具有以下设置：<br/>Retry: 3<br/>longRetry: 2<br/>longRetryInterval: 01:00:00<br/><br/>假设仅执行一个切片（状态为“等待”），且活动执行每次都失败。 最初将有 3 次连续执行尝试。 每次尝试后，切片状态为“重试”。 前 3 次尝试结束后，切片状态为“长重试”。<br/><br/>1 小时（即 longRetryInteval 的值）后，开始另一组 3 次连续执行尝试。 之后，切片的状态变为“失败”，且不会再进行重试。 因此，共进行了 6 次尝试。<br/><br/>如果某次执行成功，切片状态“就绪”，且不会再重试。<br/><br/>如果依赖数据在非确定性时间到达，或处理数据的总体环境难以捉摸，可以使用 longRetry。 在这种情况下，一遍遍重试效果可能不理想，但一段时间后再重试可能会输出想要的结果。<br/><br/>注意：不要为 longRetry 或 longRetryInterval 设置高值。 通常，较高值可能会引起其他系统问题。 |
| longRetryInterval |TimeSpan |00:00:00 |长重试尝试之间的延迟 |

## <a name="next-steps"></a>后续步骤
* 了解 [Azure 数据工厂中的计划和执行](data-factory-scheduling-and-execution.md)。  
* 阅读 Azure 数据工厂中的[数据移动](data-factory-data-movement-activities.md)和[数据转换功能](data-factory-data-transformation-activities.md)
* 了解 [Azure 数据工厂中的管理和监视](data-factory-monitor-manage-pipelines.md)。
* [生成和部署第一个管道](data-factory-build-your-first-pipeline.md)。 




<!--HONumber=Nov16_HO3-->


