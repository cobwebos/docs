---
title: "创建/计划管道、链接数据工厂中的活动 | Microsoft Docs"
description: "了解如何在 Azure 数据工厂中创建数据管道来移动和转换数据。 创建数据驱动工作流以生成可用信息。"
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
ms.date: 08/12/2017
ms.author: shlo
robots: noindex
ms.openlocfilehash: 94c820b23a91f3493a0fbc8e1fd38c86d371e985
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="pipelines-and-activities-in-azure-data-factory"></a>Azure 数据工厂中的管道和活动
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [版本 1 - GA](data-factory-create-pipelines.md)
> * [版本 2 - 预览版](../concepts-pipelines-activities.md)

> [!NOTE]
> 本文适用于数据工厂版本 1（即正式版 (GA)）。 如果使用数据工厂服务版本 2（预览版），请参阅 [V2 中的管道](../concepts-pipelines-activities.md)。

本文帮助你了解 Azure 数据工厂中的管道和活动，并帮助你利用它们为数据移动和数据处理方案构造端到端数据驱动工作流。  

> [!NOTE]
> 本文假定已浏览 [Azure 数据工厂简介](data-factory-introduction.md)。 如果还没有亲身体验如何创建数据工厂，请浏览[数据转换教程](data-factory-build-your-first-pipeline.md)和/或[数据移动教程](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)，以便更好地理解该文章。  

## <a name="overview"></a>概述
数据工厂可以包含一个或多个数据管道。 “管道”是共同执行一项任务的活动的逻辑分组。 管道中的活动定义对数据执行的操作。 例如，可使用复制活动将数据从本地 SQL Server 复制到 Azure Blob 存储。 然后，使用在 Azure HDInsight 群集上运行 Hive 脚本的 Hive 活动，将 Blob 存储中的数据处理/转换为生成输出数据。 最后，再使用一个复制活动将输出数据复制到 Azure SQL 数据仓库，基于该仓库构建紧商业智能 (BI) 报告解决方案。 

每个活动可获取零个或多个输入[数据集](data-factory-create-datasets.md)，并生成一个或多个输出[数据集](data-factory-create-datasets.md)。 下图显示了数据工厂中管道、活动和数据集之间的关系： 

![管道、活动和数据集之间的关系](media/data-factory-create-pipelines/relationship-pipeline-activity-dataset.png)

通过管道，可按集的形式而非单独管理活动。 例如，可部署、计划、暂停和继续执行管道，而不是单独处理管道中的活动。

数据工厂支持两种类型的活动：数据移动活动和数据转换活动。 每个活动可获取零个或多个输入[数据集](data-factory-create-datasets.md)，并生成一个或多个输出数据集。

输入数据集表示管道中活动的输入，输出数据集表示活动的输出。 数据集可识别不同数据存储（如表、文件、文件夹和文档）中的数据。 创建数据集后，可将其与管道中的活动一起使用。 例如，数据集可以是复制活动或 HDInsightHive 活动的输入/输出数据集。 有关数据集的详细信息，请参阅 [Azure 数据工厂中的数据集](data-factory-create-datasets.md)一文。

### <a name="data-movement-activities"></a>数据移动活动
数据工厂中的复制活动可以将数据从源数据存储复制到接收器数据存储。 数据工厂支持以下数据存储。 来自任何源的数据都可以写入到任何接收器。 单击某个数据存储即可了解如何将数据复制到该存储，以及如何从该存储复制数据。

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> 带 * 的数据存储既可位于本地，也可位于 Azure IaaS 上，需要用户在本地/Azure IaaS 计算机上安装[数据管理网关](data-factory-data-management-gateway.md)。

有关详细信息，请参阅[数据移动活动](data-factory-data-movement-activities.md)一文。

### <a name="data-transformation-activities"></a>数据转换活动
[!INCLUDE [data-factory-transformation-activities](../../../includes/data-factory-transformation-activities.md)]

有关详细信息，请参阅[数据转换活动](data-factory-data-transformation-activities.md)一文。

### <a name="custom-net-activities"></a>自定义 .NET 活动 
如果需要将数据移入/移出复制活动不支持的数据存储，或要使用自己的逻辑转换数据，可以创建自定义 .NET 活动。 有关创建和使用自定义活动的详细信息，请参阅[在 Azure数据工厂管道中使用自定义活动](data-factory-use-custom-activities.md)。

## <a name="schedule-pipelines"></a>计划管道
仅在开始时间和结束时间之间，管道才处于活动状态。 开始时间之前或结束时间之后，不会执行管道。 如果暂停管道，则无论开始和结束时间，都不会执行管道。 不暂停才可运行管道。 若要了解如何在 Azure 数据工厂中计划和执行工作，请参阅[计划和执行](data-factory-scheduling-and-execution.md)。

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
        "end": "<end date-time>",
        "isPaused": true/false,
        "pipelineMode": "scheduled/onetime",
        "expirationTime": "15.00:00:00",
        "datasets": 
        [
        ]
    }
}
```

| 标记 | 说明 | 必选 |
| --- | --- | --- |
| 名称 |管道的名称。 指定一个名称，它表示管道要执行的操作。 <br/><ul><li>最大字符数：260</li><li>必须以字母、数字或下划线 (_) 开头</li><li>不允许使用以下字符：“.”、“+”、“?”、“/”、“<”、“>”、“*”、“%”、“&”、“:”、“\\”</li></ul> |是 |
| description | 指定描述管道用途的文本。 |是 |
| 活动 | **activities** 节中可定义有一个或多个活动。 请参阅下一节，了解有关活动 JSON 元素的详细信息。 | 是 |  
| start | 管道的开始日期-时间。 必须为 [ISO 格式](http://en.wikipedia.org/wiki/ISO_8601)。 例如： `2016-10-14T16:32:41Z`。 <br/><br/>可指定本地时间，如 EST 时间。 示例如下：`2016-02-27T06:00:00-05:00`" 东部标准时间早上 6 点。<br/><br/>start 和 end 属性共同指定管道的活动期限。 仅在此活动期限内生成输出切片。 |否<br/><br/>如果要指定 end 属性值，必须指定 start 属性值。<br/><br/>创建管道时，开始和结束时间均可为空。 必须指定这两个值，才能设置管道运行的活动期限。 如果创建管道时未指定开始和结束时间，可稍后使用 Set-AzureRmDataFactoryPipelineActivePeriod cmdlet 进行设置。 |
| end | 管道的结束日期-时间。 指定时必须采用 ISO 格式。 例如： `2016-10-14T17:32:41Z` <br/><br/>可指定本地时间，如 EST 时间。 下面是一个示例：`2016-02-27T06:00:00-05:00`（美国东部标准时间上午 6 点）。<br/><br/>若要无限期运行管道，请指定 9999-09-09 作为 end 属性的值。 <br/><br/> 仅在开始时间和结束时间之间，管道才处于活动状态。 开始时间之前或结束时间之后，不会执行管道。 如果暂停管道，则无论开始和结束时间，都不会执行管道。 不暂停才可运行管道。 若要了解如何在 Azure 数据工厂中计划和执行工作，请参阅[计划和执行](data-factory-scheduling-and-execution.md)。 |否 <br/><br/>如果指定 start 属性的值，必须指定 end 属性的值。<br/><br/>请参阅 **start** 属性的说明。 |
| isPaused | 若设为 true，则管道不运行。 它处于已暂停状态。 默认值 = false。 此属性可用于启用或禁用管道。 |否 |
| pipelineMode | 针对管道计划运行的方式。 允许值为：已计划(默认)，一次性。<br/><br/>“Scheduled”表示管道根据管道活动期（开始和结束时间）按指定的时间间隔运行。 “Onetime”表示管道只运行一次。 当前，一次性管道创建后即无法修改/更新。 有关一次性设置的详细信息，请参阅[一次性管道](#onetime-pipeline)。 |否 |
| expirationTime | 创建后的持续时间，在该时间内[一次性管道](#onetime-pipeline)需有效且保持预配状态。 如果管道不具有任何运行（活动、失败或挂起），则该管道在到期后会自动删除。 默认值：`"expirationTime": "3.00:00:00"`|否 |
| datasets |数据集列表，这些数据集由管道中定义的活动使用。 此属性可用于此管道特定的但未在数据工厂中定义的数据集。 此管道中定义的数据集仅可用于此管道且不可共享。 有关详细信息，请参阅[指定了作用域的数据集](data-factory-create-datasets.md#scoped-datasets)。 |否 |

## <a name="activity-json"></a>活动 JSON
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
    },
    "scheduler":
    {
    }
}
```

下表描述了活动 JSON 定义中的属性：

| 标记 | 说明 | 必选 |
| --- | --- | --- |
| 名称 | 活动的名称。 指定一个名称，它表示活动要执行的操作。 <br/><ul><li>最大字符数：260</li><li>必须以字母、数字或下划线 (_) 开头</li><li>不允许使用以下字符：“.”、“+”、“?”、“/”、“<”、“>”、“*”、“%”、“&”、“:”、“\\”</li></ul> |是 |
| description | 描述活动用途的文本 |是 |
| type | 活动的类型。 有关不同的活动类型，请参阅[数据移动活动](#data-movement-activities)和[数据转换活动](#data-transformation-activities)部分。 |是 |
| inputs |活动使用的输入表<br/><br/>`// one input table`<br/>`"inputs":  [ { "name": "inputtable1"  } ],`<br/><br/>`// two input tables` <br/>`"inputs":  [ { "name": "inputtable1"  }, { "name": "inputtable2"  } ],` |是 |
| outputs |活动使用的输出表。<br/><br/>`// one output table`<br/>`"outputs":  [ { "name": "outputtable1" } ],`<br/><br/>`//two output tables`<br/>`"outputs":  [ { "name": "outputtable1" }, { "name": "outputtable2" }  ],` |是 |
| linkedServiceName |活动使用的链接服务的名称。 <br/><br/>活动可能需要你指定链接到所需计算环境的链接服务。 |对 HDInsight 活动和 Azure 机器学习批处理评分活动是必需的 <br/><br/>对其他活动均非必需 |
| typeProperties |typeProperties 部分中的属性取决于活动类型。 要查看活动的类型属性，请单击链接转到上一节中的活动。 | 否 |
| policy |影响活动运行时行为的策略。 如果未指定，将使用默认策略。 |否 |
| scheduler | “scheduler”属性用于定义所需的活动计划。 其子属性与[数据集中可用性属性](data-factory-create-datasets.md#dataset-availability)的子属性相同。 |否 |


### <a name="policies"></a>策略
策略会影响活动的运行时行为，尤其在处理表的切片时。 下表提供详细信息。

| 属性 | 允许的值 | 默认值 | 说明 |
| --- | --- | --- | --- |
| concurrency |Integer <br/><br/>最大值：10 |1 |活动的并发执行次数。<br/><br/>它决定可在不同切片上发生的并行活动执行次数。 例如，如果活动需要完成大量可用数据，更大的并发值能加快数据处理速度。 |
| executionPriorityOrder |NewestFirst<br/><br/>OldestFirst |OldestFirst |确定正在处理的数据切片的顺序。<br/><br/>例如，有两个切片（分别发生在下午 4 点和下午 5 点），且均在等待执行。 如果将 executionPriorityOrder 设置为 NewestFirst，则首先处理下午 5 点的切片。 同理，如果将 executionPriorityORder 设置为 OldestFIrst，则先处理下午 4 点的切片。 |
| retry |Integer<br/><br/>最大值可为 10 |0 |将切片的数据处理标记为“失败”之前的重试次数。 数据切片的活动执行次数不能超过指定的重试次数。 出现故障后尽快重试。 |
| timeout |TimeSpan |00:00:00 |活动的超时。 示例：00:10:00（表示超时 10 分钟）<br/><br/>如果不指定值或值为 0，则表示无限超时。<br/><br/>如果某个切片的数据处理时间超出了超时值，将取消该处理，且系统尝试重试处理。 重试次数取决于重试属性。 发生超时时，会将状态设置为“超时”。 |
| delay |TimeSpan |00:00:00 |启动切片的数据处理前，需指定延迟。<br/><br/>延迟超过预期执行时间后，启动数据切片的活动执行。<br/><br/>示例：00:10:00（表示延迟 10 分钟） |
| longRetry |Integer<br/><br/>最大值：10 |1 |切片执行失败之前的长重试次数。<br/><br/>longRetryInterval 指定尝试 longRetry 的间隔。 因此，如果需要指定重试尝试之间的时间，请使用 longRetry。 如果同时指定 Retry 和 longRetry，则每次 longRetry 尝试均包含 Retry 尝试，且最大尝试次数为 Retry * longRetry。<br/><br/>例如，如果活动策略中具有以下设置：<br/>Retry: 3<br/>longRetry: 2<br/>longRetryInterval: 01:00:00<br/><br/>假设仅执行一个切片（状态为“等待”），且活动执行每次都失败。 最初将有 3 次连续执行尝试。 每次尝试后，切片状态为“重试”。 前 3 次尝试结束后，切片状态为“长重试”。<br/><br/>1 小时（即 longRetryInteval 的值）后，开始另一组 3 次连续执行尝试。 之后，切片的状态变为“失败”，且不会再进行重试。 因此，共进行了 6 次尝试。<br/><br/>如果某次执行成功，切片状态“就绪”，且不会再重试。<br/><br/>如果依赖数据在非确定性时间到达，或处理数据的总体环境难以捉摸，可以使用 longRetry。 在这种情况下，一遍遍重试效果可能不理想，但一段时间后再重试可能会输出想要的结果。<br/><br/>注意：不要为 longRetry 或 longRetryInterval 设置高值。 通常，较高值可能会引起其他系统问题。 |
| longRetryInterval |TimeSpan |00:00:00 |长重试尝试之间的延迟 |

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
* 活动的输入设置为 **InputDataset**，活动的输出设置为 **OutputDataset**。 有关在 JSON 中定义数据集的信息，请参阅[数据集](data-factory-create-datasets.md)文章。 
* 在 **typeProperties** 节中，**BlobSource** 指定为源类型，**SqlSink** 指定为接收器类型。 在[数据移动活动](#data-movement-activities)部分，单击要用作源或接收器的数据存储，以深入了解如何将数据移入/移出数据存储。 

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
* `defines` 节用于指定运行时设置，这些设置将作为 Hive 配置值（例如 `${hiveconf:inputtable}`、`${hiveconf:partitionedtable}`）传递给 Hive 脚本。

每个转换活动的 typeProperties 节都不同。 要了解转换活动支持的类型属性，请单击[数据转换活动](#data-transformation-activities)表中的转换活动。 

有关创建此管道的完整演练，请参阅[教程：生成首个管道以使用 Hadoop 群集处理数据](data-factory-build-your-first-pipeline.md)。 

## <a name="multiple-activities-in-a-pipeline"></a>管道中的多个活动
前两个示例管道中只有一个活动。 可在管道中添加多个活动。  

如果管道中有多个活动，且一个活动的输出不是其他活动的输入，则若活动的输入数据切片准备就绪，活动可能会并行运行。 

通过让一个活动的输出数据集作为另一活动的输入数据集，可以链接两个活动。 只有第一个活动成功完成时才执行第二个活动。

![链接相同管道中的活动](./media/data-factory-create-pipelines/chaining-one-pipeline.png)

本例中，该管道拥有两个活动：Activity1 和 Activity2。 Activity1 将 Dataset1 作为输入，并生成输出 Dataset2。 Activity 将 Dataset2 作为输入，并生成输出 Dataset3。 由于 Activity1 (Dataset2) 的输出是 Activity2 的输入，因此 Activity2 仅在 Activity 成功完成并生成 Dataset2 切片后才运行。 如果 Activity1 因某些原因失败，且未生成 Dataset2 切片，则 Activity 2 不会为该切片运行（例如上午 9 点至 10 点）。 

还可将不同管道中的活动链接在一起。

![链接两个管道中的活动](./media/data-factory-create-pipelines/chaining-two-pipelines.png)

本例中，Pipeline1 仅拥有一个活动，它将 Dataset1 作为输入，并生成 Dataset2 作为输出。 Pipeline2 同样仅拥有一个活动，它将 Dataset2 作为输入，并生成 Dataset3 作为输出。 

有关详细信息，请参阅[计划和执行](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline)。 

## <a name="create-and-monitor-pipelines"></a>创建和监视管道
可使用下列某个工具或 SDK 创建管道。 

- 复制向导。 
- Azure 门户
- Visual Studio
- Azure PowerShell
- Azure Resource Manager 模板
- REST API
- .NET API

请参阅以下教程，了解使用下列某个工具或 SDK 创建管道的分步说明。
 
- [使用数据转换活动生成管道](data-factory-build-your-first-pipeline.md)
- [使用数据移动活动生成管道](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

管道创建/部署完毕后，可使用 Azure 门户边栏选项卡或“监视与管理”来监视和管理这些管道。 请参阅下列主题中的分步说明。 

- [使用 Azure 门户边栏选项卡监视和管理管道](data-factory-monitor-manage-pipelines.md)。
- [使用“监视与管理”应用监视和管理管道](data-factory-monitor-manage-app.md)


## <a name="onetime-pipeline"></a>一次性管道
在管道定义中指定的开始和结束时间内，可创建一个管道并计划为定期运行（例如：每小时或每天）。 请参阅[计划活动](#scheduling-and-execution)了解详细信息。 还可以创建只运行一次的管道。 要执行此操作，请将管道定义中的 **pipelineMode** 属性设置为 **onetime**，如以下 JSON 示例所示。 此属性的默认值是 **scheduled**。

```json
{
    "name": "CopyPipeline",
    "properties": {
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource",
                        "recursive": false
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "InputDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataset"
                    }
                ]
                "name": "CopyActivity-0"
            }
        ]
        "pipelineMode": "OneTime"
    }
}
```

注意以下事项：

* 不指定管道的**开始**和**结束**时间。
* 指定输入和输出数据集的 **Availability**（**frequency** 和 **interval**），尽管数据工厂不使用该值。  
* “关系图”视图不显示一次性管道。 这是设计的行为。
* 一次性管道无法更新。 可对一次性管道执行克隆、重命名、更新属性，还可以对其部署以生成另外一个一次性管道。


## <a name="next-steps"></a>后续步骤
- 有关数据集的详细信息，请参阅[创建数据集](data-factory-create-datasets.md)文章。 
- 若要深入了解如何计划和执行管道，请参阅[在 Azure 数据工厂中计划和执行](data-factory-scheduling-and-execution.md)一文。 
  

