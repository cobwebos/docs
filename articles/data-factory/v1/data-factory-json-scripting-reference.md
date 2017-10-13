---
title: "Azure 数据工厂 - JSON 脚本参考 | Microsoft Docs"
description: "提供数据工厂实体的 JSON 架构。"
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: spelluru
robots: noindex
ms.openlocfilehash: 2424aa2cd5b6c7935338a593fa25a6a5772f4e62
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="azure-data-factory---json-scripting-reference"></a>数据工厂 - JSON 脚本参考
本文提供用于定义 Azure 数据工厂实体（管道、活动、数据集和链接服务）的 JSON 架构和示例。  

## <a name="pipeline"></a>管道 
管道定义的高级结构如下所示： 

```json
{
  "name": "SamplePipeline",
  "properties": {
    "description": "Describe what pipeline does",
    "activities": [
    ],
    "start": "2016-07-12T00:00:00",
    "end": "2016-07-13T00:00:00"
  }
} 
```

下表描述了管道 JSON 定义中的属性：

| 属性 | 说明 | 必选
-------- | ----------- | --------
| 名称 | 管道的名称。 指定一个名称，表示配置为活动或管道要执行的操作<br/><ul><li>最大字符数：260</li><li>必须以字母、数字或下划线 (_) 开头</li><li>不允许使用以下字符：“.”、“+”、“?”、“/”、“<”、“>”、“*”、“%”、“&”、“:”、“\\”</li></ul> |是 |
| description |描述活动或管道用途的文本 | 否 |
| 活动 | 包含活动列表。 | 是 |
| start |管道的开始日期-时间。 必须为 [ISO 格式](http://en.wikipedia.org/wiki/ISO_8601)。 例如：2014-10-14T16:32:41。 <br/><br/>可指定本地时间，如 EST 时间。 下面是一个示例：`2016-02-27T06:00:00**-05:00`（美国东部标准时间上午 6 点）。<br/><br/>start 和 end 属性共同指定管道的活动期限。 仅在此活动期限内生成输出切片。 |否<br/><br/>如果要指定 end 属性值，必须指定 start 属性值。<br/><br/>创建管道时，开始和结束时间均可为空。 必须指定这两个值，才能设置管道运行的活动期限。 如果创建管道时未指定开始和结束时间，可稍后使用 Set-AzureRmDataFactoryPipelineActivePeriod cmdlet 进行设置。 |
| end |管道的结束日期-时间。 指定时必须采用 ISO 格式。 例如：2014-10-14T17:32:41 <br/><br/>可指定本地时间，如 EST 时间。 下面是一个示例：`2016-02-27T06:00:00**-05:00`（美国东部标准时间上午 6 点）。<br/><br/>若要无限期运行管道，请指定 9999-09-09 作为 end 属性的值。 |否 <br/><br/>如果指定 start 属性的值，必须指定 end 属性的值。<br/><br/>请参阅 **start** 属性的说明。 |
| isPaused |如果设置为 true，管道不会运行。 默认值 = false。 此属性可用于启用或禁用。 |否 |
| pipelineMode |针对管道计划运行的方式。 允许值为：已计划(默认)，一次性。<br/><br/>“Scheduled”表示管道根据管道活动期（开始和结束时间）按指定的时间间隔运行。 “Onetime”表示管道只运行一次。 当前，一次性管道创建后即无法修改/更新。 有关一次性设置的详细信息，请参阅[一次性管道](data-factory-create-pipelines.md#onetime-pipeline)。 |否 |
| expirationTime |创建后的持续时间，在该时间内管道需有效且保持预配状态。 如果管道不具有任何活动的、失败的或挂起的运行，则该管道在到期后会自动删除。 |否 |


## <a name="activity"></a>活动 
管道定义（活动元素）中活动的高级结构如下所示：

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

下表描述了活动 JSON 定义中的属性：

| 标记 | 说明 | 必选 |
| --- | --- | --- |
| 名称 |活动的名称。 指定一个名称，表示活动配置要执行的操作<br/><ul><li>最大字符数：260</li><li>必须以字母、数字或下划线 (_) 开头</li><li>不允许使用以下字符：“.”、“+”、“?”、“/”、“<”、“>”、“*”、“%”、“&”、“:”、“\\”</li></ul> |是 |
| description |描述活动用途的文本。 |是 |
| type |指定活动的类型。 请参阅[数据存储](#data-stores)和[数据转换活动](#data-transformation-activities)部分，了解不同的活动类型。 |是 |
| inputs |活动使用的输入表<br/><br/>`// one input table`<br/>`"inputs":  [ { "name": "inputtable1"  } ],`<br/><br/>`// two input tables` <br/>`"inputs":  [ { "name": "inputtable1"  }, { "name": "inputtable2"  } ],` |是 |
| outputs |活动使用的输出表。<br/><br/>`// one output table`<br/>`"outputs":  [ { "name": “outputtable1” } ],`<br/><br/>`//two output tables`<br/>`"outputs":  [ { "name": “outputtable1” }, { "name": “outputtable2” }  ],` |是 |
| linkedServiceName |活动使用的链接服务的名称。 <br/><br/>活动可能需要你指定链接到所需计算环境的链接服务。 |对 HDInsight 活动、Azure 机器学习活动和存储过程活动是必需的。 <br/><br/>对其他活动均非必需 |
| typeProperties |typeProperties 部分的属性取决于活动类型。 |否 |
| policy |影响活动运行时行为的策略。 如果未指定，将使用默认策略。 |否 |
| scheduler |“scheduler”属性用于定义所需的活动计划。 其子属性与[数据集中可用性属性](data-factory-create-datasets.md#dataset-availability)的子属性相同。 |否 |

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

### <a name="typeproperties-section"></a>typeProperties 节
每个活动的 typeProperties 节不同。 转换活动只有类型属性。 有关在管道中定义转换活动的 JSON 示例，请参阅本文中的[数据转换活动](#data-transformation-activities)部分。 

**复制活动**在 typeProperties 节中包含两个子节：**source** 和 **sink**。 有关演示如何使用数据存储作为源和/或接收器的 JSON 示例，请参阅本文中的[数据存储](#data-stores)部分。 

### <a name="sample-copy-pipeline"></a>复制管道示例
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
    "start": "2016-07-12T00:00:00",
    "end": "2016-07-13T00:00:00"
  }
} 
```

请注意以下几点：

* 在 activities 节中，只有一个活动的 **type** 设置为 **Copy**。
* 活动的输入设置为 **InputDataset**，活动的输出设置为 **OutputDataset**。
* 在 **typeProperties** 节中，**BlobSource** 指定为源类型，**SqlSink** 指定为接收器类型。

有关演示如何使用数据存储作为源和/或接收器的 JSON 示例，请参阅本文中的[数据存储](#data-stores)部分。    

有关创建此管道的完整演练，请参阅[教程：将 Blob 存储中的数据复制到 SQL 数据库](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。 

### <a name="sample-transformation-pipeline"></a>转换管道示例
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
        "start": "2016-04-01T00:00:00",
        "end": "2016-04-02T00:00:00",
        "isPaused": false
    }
}
```

请注意以下几点： 

* 在 activities 节中，只有一个活动的 **type** 设置为 **HDInsightHive**。
* Hive 脚本文件 **partitionweblogs.hql** 存储在 Azure 存储帐户（由 scriptLinkedService 指定，名为 **AzureStorageLinkedService**）中，以及 **adfgetstarted** 容器的 **script** 文件夹中。
* **defines** 节用于指定运行时设置，这些设置将作为 Hive 配置值（例如 `${hiveconf:inputtable}`、`${hiveconf:partitionedtable}`）传递到 Hive 脚本。

有关在管道中定义转换活动的 JSON 示例，请参阅本文中的[数据转换活动](#data-transformation-activities)部分。

有关创建此管道的完整演练，请参阅[教程：生成首个管道以使用 Hadoop 群集处理数据](data-factory-build-your-first-pipeline.md)。 

## <a name="linked-service"></a>链接服务
链接服务定义的高级结构如下所示：

```json
{
    "name": "<name of the linked service>",
    "properties": {
        "type": "<type of the linked service>",
        "typeProperties": {
        }
    }
}
```

下表描述了活动 JSON 定义中的属性：

| 属性 | 说明 | 必选 |
| -------- | ----------- | -------- | 
| 名称 | 链接服务的名称。 | 是 | 
| properties - type | 链接服务的类型。 例如：Azure 存储、Azure SQL 数据库。 |
| typeProperties | typeProperties 节中的元素根据每个数据存储或计算环境的不同而不同。 有关所有数据存储链接服务，请参阅[数据存储](#datastores)部分；有关所有计算链接服务，请参阅[计算环境](#compute-environments) |   

## <a name="dataset"></a>数据集 
在 Azure 数据工厂中定义数据集如下所示：

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "external": <boolean flag to indicate external data. only for input datasets>,
        "linkedServiceName": "<Name of the linked service that refers to a data store.>",
        "structure": [
            {
                "name": "<Name of the column>",
                "type": "<Name of the type>"
            }
        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        },
        "availability": {
            "frequency": "<Specifies the time unit for data slice production. Supported frequency: Minute, Hour, Day, Week, Month>",
            "interval": "<Specifies the interval within the defined frequency. For example, frequency set to 'Hour' and interval set to 1 indicates that new data slices should be produced hourly>"
        },
       "policy":
        {      
        }
    }
}
```

下表描述了上述 JSON 中的属性：   

| 属性 | 说明 | 必选 | 默认 |
| --- | --- | --- | --- |
| name | 数据集名称。 若要了解命名规则，请参阅 [Azure 数据工厂 - 命名规则](data-factory-naming-rules.md)。 |是 |不可用 |
| type | 数据集的类型。 指定 Azure 数据工厂支持的类型之一（例如：AzureBlob、AzureSqlTable）。 有关数据工厂支持的所有数据存储和数据集类型，请参阅[数据存储](#data-stores)部分。 | 
| structure | 数据集的架构。 其中包含列及其类型，等等。 | 否 |不可用 |
| typeProperties | 对应于所选类型的属性。 有关支持的类型及其属性，请参阅[数据存储](#data-stores)部分。 |是 |不可用 |
| external | 布尔标志，用于指定数据集是否由数据工厂管道显式生成。 |否 |false |
| availability | 定义数据集生产的处理窗口或切片模型。 有关数据集切片模型的详细信息，请参阅[计划和执行](data-factory-scheduling-and-execution.md)一文。 |是 |不可用 |
| policy |定义数据集切片必须满足的标准或条件。 <br/><br/>有关详细信息，请参阅[数据集策略](#Policy)部分。 |否 |不可用 |

**structure** 节中的每个列包含以下属性：

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| 名称 |列的名称。 |是 |
| type |列的数据类型。  |否 |
| culture |指定类型为 .NET 类型 `Datetime` 或 `Datetimeoffset` 时要使用的基于 .NET 的区域性。 默认为 `en-us`。 |否 |
| 格式 |指定类型为 .NET 类型 `Datetime` 或 `Datetimeoffset` 时要使用的格式字符串。 |否 |

在以下示例中，数据集具有三列 `slicetimestamp`、`projectname` 和 `pageviews`，它们的类型分别为：String、String 和 Decimal。

```json
structure:  
[
    { "name": "slicetimestamp", "type": "String"},
    { "name": "projectname", "type": "String"},
    { "name": "pageviews", "type": "Decimal"}
]
```

下表描述了可在 **availability** 节中使用的属性：

| 属性 | 说明 | 必选 | 默认 |
| --- | --- | --- | --- |
| frequency |指定数据集切片生成的时间单位。<br/><br/><b>支持的频率</b>：Minute、Hour、Day、Week、Month |是 |不可用 |
| interval |指定频率的乘数<br/><br/>“频率 x 间隔”确定生成切片的频率。<br/><br/>若需要数据集每小时生成切片，请将“Frequency”<b></b>设置为“Hour”<b></b>，将“interval”<b></b>设置为“1”<b></b>。<br/><br/><b>注意</b>：如果将 Frequency 指定为 Minute，建议将 interval 设置为小于 15 的值 |是 |不可用 |
| style |指定是否应在间隔的开头/结尾生成切片。<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>若将 Month 设置为 Month，style 设置为 EndOfInterval，则会在每月的最后一天生成切片。 若将 style 设为 StartOfInterval，会在每月的第一天生成切片。<br/><br/>若将 Frequency 设置为 Day，style 设置为 EndOfInterval，则会在一天的最后一小时生成切片。<br/><br/>若将 Frequency 设置为 Hour，style 设置为 EndOfInterval，则会在一小时结束时生成切片。 例如，对于下午 1 点到下午 2 点期间的切片，则在下午 2 点生成切片。 |否 |EndOfInterval |
| anchorDateTime |定义计划程序用于计算数据集切片边界的时间中的绝对位置。 <br/><br/><b>注意</b>：如果 AnchorDateTime 的日期部分比频率部分更精细，则忽略更精细部分。 <br/><br/>例如，如果 <b>interval</b> 为 <b>hourly</b>（frequency：hour，interval: 1），<b>AnchorDateTime</b> 包含<b>分钟和秒</b>，则会忽略 AnchorDateTime 的<b>分钟和秒</b>部分。 |否 |01/01/0001 |
| offset |所有数据集切片的开始和结束之间偏移的时间跨度。 <br/><br/><b>注意</b>：如果同时指定了 anchorDateTime 和 offset，则结果是组合偏移。 |否 |不可用 |

以下可用性部分指定每小时生成输出数据集或每小时提供输入数据集：

```json
"availability":    
{    
    "frequency": "Hour",        
    "interval": 1    
}
```

数据集定义中的**策略**部分定义了数据集切片必须满足的标准或条件。

| 策略名称 | 说明 | 适用对象 | 必选 | 默认 |
| --- | --- | --- | --- | --- |
| minimumSizeMB |验证 **Azure Blob** 中的数据是否满足最小大小要求（以兆字节为单位）。 |Azure Blob |否 |不可用 |
| minimumRows |验证 **Azure SQL 数据库**中的数据或 **Azure 表**是否包含最小行数。 |<ul><li>Azure SQL 数据库</li><li>Azure 表</li></ul> |否 |不可用 |

**示例：**

```json
"policy":

{
    "validation":
    {
        "minimumSizeMB": 10.0
    }
}
```

除非 Azure 数据工厂生成数据集，否则应将其标记为**外部**。 此设置通常适用于管道中第一个活动的输入，除非正在使用活动或管道链接。

| Name | 说明 | 必选 | 默认值 |
| --- | --- | --- | --- |
| dataDelay |延迟检查给定切片外部数据的可用性的时间。 例如，如果数据应为每小时可用，可使用 dataDelay 推迟检查外部数据是否可用以及相应切片是否就绪。<br/><br/>仅适用于当前时间。  例如，如果现在是下午 1:00 且此值为 10 分钟，则从下午 1:10 开始验证。<br/><br/>此设置不会影响过去的切片 (End Time + dataDelay < Now)，过去的切片不会有任何延迟。<br/><br/>大于 23:59 小时的时间需要使用 `day.hours:minutes:seconds` 格式进行指定。 例如，若要指定 24 小时，请不要使用 24:00:00；请改用 1.00:00:00。 如果使用 24:00:00，则将它视为 24 天 (24.00:00:00)。 对于 1 天又 4 小时，请指定 1:04:00:00。 |否 |0 |
| retryInterval |失败与下一次重试之间的等待时间。 如果尝试失败，下一次尝试会在 retryInterval 以后。 <br/><br/>如果现在是下午 1:00，我们将开始第一次尝试。 如果完成第一次验证检查的持续时间为 1 分钟，并且操作失败，则下一次重试为 1:00 + 1 分钟（持续时间）+ 1 分钟（重试间隔）= 下午 1:02。 <br/><br/>对于过去的切片，没有任何延迟。 重试会立即发生。 |否 |00:01:00（1 分钟） |
| retryTimeout |每次重试尝试的超时。<br/><br/>如果此属性设置为 10 分钟，则验证需要在 10 分钟内完成。 如果执行验证所需的时间超过 10 分钟，则重试超时。<br/><br/>如果验证的所有尝试超时，则将切片标记为 TimedOut。 |否 |00:10:00（10 分钟） |
| maximumRetry |检查外部数据可用性的次数。 允许的最大值为 10。 |否 |3 |


## <a name="data-stores"></a>数据存储
[链接服务](#linked-service)部分提供所有类型的链接服务通用的 JSON 元素的说明。 此部分提供有关特定于每个数据存储的 JSON 元素的详细信息。

[数据集](#dataset)部分提供所有类型的数据集通用的 JSON 元素的说明。 此部分提供有关特定于每个数据存储的 JSON 元素的详细信息。

[活动](#activity)部分提供所有类型的活动通用的 JSON 元素的说明。 此部分提供有关在复制活动中用作源/接收器的每个数据存储特定的 JSON 元素的详细信息。  

单击所需存储的链接可查看复制活动的链接服务、数据集和源/接收器的 JSON 架构。

| 类别 | 数据存储 
|:--- |:--- |
| **Azure** |[Azure Blob 存储](#azure-blob-storage) |
| &nbsp; |[Azure Data Lake Store](#azure-datalake-store) |
| &nbsp; |[Azure Cosmos DB](#azure-cosmos-db) |
| &nbsp; |[Azure SQL 数据库](#azure-sql-database) |
| &nbsp; |[Azure SQL 数据仓库](#azure-sql-data-warehouse) |
| &nbsp; |[Azure 搜索](#azure-search) |
| &nbsp; |[Azure 表存储](#azure-table-storage) |
| **数据库** |[Amazon Redshift](#amazon-redshift) |
| &nbsp; |[IBM DB2](#ibm-db2) |
| &nbsp; |[MySQL](#mysql) |
| &nbsp; |[Oracle](#oracle) |
| &nbsp; |[PostgreSQL](#postgresql) |
| &nbsp; |[SAP Business Warehouse](#sap-business-warehouse) |
| &nbsp; |[SAP HANA](#sap-hana) |
| &nbsp; |[SQL Server](#sql-server) |
| &nbsp; |[Sybase](#sybase) |
| &nbsp; |[Teradata](#teradata) |
| **NoSQL** |[Cassandra](#cassandra) |
| &nbsp; |[MongoDB](#mongodb) |
| **文件** |[Amazon S3](#amazon-s3) |
| &nbsp; |[文件系统](#file-system) |
| &nbsp; |[FTP](#ftp) |
| &nbsp; |[HDFS](#hdfs) |
| &nbsp; |[SFTP](#sftp) |
| **其他** |[HTTP](#http) |
| &nbsp; |[OData](#odata) |
| &nbsp; |[ODBC](#odbc) |
| &nbsp; |[Salesforce](#salesforce) |
| &nbsp; |[Web 表](#web-table) |

## <a name="azure-blob-storage"></a>Azure Blob 存储

### <a name="linked-service"></a>链接服务
有两种类型的链接服务：Azure 存储链接服务和 Azure 存储 SAS 链接服务。

#### <a name="azure-storage-linked-service"></a>Azure 存储链接服务
要使用**帐户密钥**将 Azure 存储帐户链接到数据工厂，请创建 Azure 存储链接服务。 要定义 Azure 存储链接服务，请将链接服务的**类型**设置为 **AzureStorage**。 然后，可在 **typeProperties** 节中指定以下属性：  

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| connectionString |为 connectionString 属性指定连接到 Azure 存储所需的信息。 |是 |

##### <a name="example"></a>示例  

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

#### <a name="azure-storage-sas-linked-service"></a>Azure 存储 SAS 链接服务
Azure 存储 SAS 链接服务可让你使用共享访问签名 (SAS) 将 Azure 存储帐户链接到 Azure 数据工厂。 这样，便可以将存储中所有/特定资源（Blob/容器）的受限/限时访问权限提供给数据工厂。 要使用共享访问签名将 Azure 存储帐户链接到数据工厂，请创建 Azure 存储 SAS 链接服务。 要定义 Azure 存储 SAS 链接服务，请将链接服务的**类型**设置为 **AzureStorageSas**。 然后，可在 **typeProperties** 节中指定以下属性：   

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| sasUri |指定 Azure 存储资源（例如 Blob、容器或表）的共享访问签名 URI。 |是 |

##### <a name="example"></a>示例

```json
{  
    "name": "StorageSasLinkedService",  
    "properties": {  
        "type": "AzureStorageSas",  
        "typeProperties": {  
            "sasUri": "<storageUri>?<sasToken>"   
        }  
    }  
}  
```

有关这些链接服务的详细信息，请参阅 [Azure Blob 存储连接器](data-factory-azure-blob-connector.md#linked-service-properties)一文。 

### <a name="dataset"></a>数据集
要定义 Azure Blob 数据集，请将数据集的**类型**设置为 **AzureBlob**。 然后，在 **typeProperties** 节中指定 Azure Blob 特定的以下属性： 

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| folderPath |到 Blob 存储中的容器和文件夹的路径。 示例：myblobcontainer\myblobfolder\ |是 |
| fileName |blob 的名称。 fileName 可选，并且区分大小写。<br/><br/>如果指定文件名，则活动（包括复制）将对特定 Blob 起作用。<br/><br/>如果未指定 fileName，则复制将包括输入数据集的 folderPath 中所有的 Blob。<br/><br/>如果没有为输出数据集指定 fileName，生成的文件的名称会采用以下格式：Data<Guid>.txt (for example: : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |否 |
| partitionedBy |partitionedBy 是一个可选属性。 它可用于指定时序数据的动态 folderPath 和 filename。 例如，folderPath 可针对每小时的数据参数化。 |否 |
| 格式 | 支持以下格式类型：**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat** 和 **ParquetFormat**。 请将格式中的 **type** 属性设置为上述值之一。 有关详细信息，请参阅[文本格式](data-factory-supported-file-and-compression-formats.md#text-format)、[Json 格式](data-factory-supported-file-and-compression-formats.md#json-format)、[Avro 格式](data-factory-supported-file-and-compression-formats.md#avro-format)、[Orc 格式](data-factory-supported-file-and-compression-formats.md#orc-format)和 [Parquet 格式](data-factory-supported-file-and-compression-formats.md#parquet-format)部分。 <br><br> 如果想要在基于文件的存储之间**按原样复制文件**（二进制副本），可以在输入和输出数据集定义中跳过格式节。 |否 |
| compression | 指定数据的压缩类型和级别。 支持的类型为：**GZip**、**Deflate**、**BZip2** 和 **ZipDeflate**。 支持的级别为：**最佳**和**最快**。 有关详细信息，请参阅 [Azure 数据工厂中的文件和压缩格式](data-factory-supported-file-and-compression-formats.md#compression-support)。 |否 |

#### <a name="example"></a>示例

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "fileName": "input.log",
            "folderPath": "adfgetstarted/inputdata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
 ```


有关详细信息，请参阅 [Azure Blob 连接器](data-factory-azure-blob-connector.md#dataset-properties)一文。

### <a name="blobsource-in-copy-activity"></a>复制活动中的 BlobSource
如果要从 Azure Blob 存储复制数据，请将复制活动的**源类型**设置为 **BlobSource**，并在 **source** 节中指定以下属性：

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| recursive |指示是要从子文件夹中以递归方式读取数据，还是只从指定的文件夹中读取数据。 |True（默认值）、False |否 |

#### <a name="example-blobsource"></a>示例：BlobSource**
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQL",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureSqlOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "SqlSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
### <a name="blobsink-in-copy-activity"></a>复制活动中的 BlobSink
如果要将数据复制到 Azure Blob 存储，请将复制活动的**接收器类型**设置为 **BlobSink**，并在 **sink** 节中指定以下属性：

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| copyBehavior |源为 BlobSource 或 FileSystem 时，请定义复制行为。 |<b>PreserveHierarchy</b>：保留目标文件夹中的文件层次结构。 从源文件到源文件夹的相对路径与从目标文件到目标文件夹的相对路径相同。<br/><br/><b>FlattenHierarchy：</b>源文件夹中的所有文件都位于目标文件夹的第一级。 目标文件具有自动生成的名称。 <br/><br/><b>MergeFiles：（默认值）</b>将源文件夹中的所有文件合并到一个文件中。 如果指定文件/Blob 名称，则合并的文件名称将为指定的名称；否则，会自动生成文件名。 |否 |

#### <a name="example-blobsink"></a>示例：BlobSink

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSQLInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

有关详细信息，请参阅 [Azure Blob 连接器](data-factory-azure-blob-connector.md#copy-activity-properties)一文。 

## <a name="azure-data-lake-store"></a>Azure Data Lake Store

### <a name="linked-service"></a>链接服务
要定义 Azure Data Lake Store 链接服务，请将链接服务的类型设置为 **AzureDataLakeStore**，并在 **typeProperties** 节中指定以下属性：  

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | type 属性必须设置为：**AzureDataLakeStore** | 是 |
| dataLakeStoreUri | 指定 Azure Data Lake Store 帐户相关信息。 其格式如下：`https://[accountname].azuredatalakestore.net/webhdfs/v1` 或 `adl://[accountname].azuredatalakestore.net/`。 | 是 |
| subscriptionId | Data Lake Store 所属的 Azure 订阅 ID。 | 接收器所需 |
| resourceGroupName | Data Lake Store 所属的 Azure 资源组名称。 | 接收器所需 |
| servicePrincipalId | 指定应用程序的客户端 ID。 | 是（对于服务主体身份验证） |
| servicePrincipalKey | 指定应用程序的密钥。 | 是（对于服务主体身份验证） |
| tenant | 指定应用程序的租户信息（域名或租户 ID）。 可将鼠标悬停在 Azure 门户右上角进行检索。 | 是（对于服务主体身份验证） |
| authorization | 单击“数据工厂编辑器”中的“授权”按钮，并输入会自动生成的授权 URL 分配给此属性的凭据。 | 是（对于用户凭据身份验证）|
| sessionId | OAuth 授权会话中的 OAuth 会话 ID。 每个会话 ID 都是唯一的，并且可能仅可使用一次。 使用数据工厂编辑器时，会自动生成此设置。 | 是（对于用户凭据身份验证） |

#### <a name="example-using-service-principal-authentication"></a>示例：使用服务主体身份验证
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info. Example: microsoft.onmicrosoft.com>"
        }
    }
}
```

#### <a name="example-using-user-credential-authentication"></a>示例：使用用户凭据身份验证
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<session ID>",
            "authorization": "<authorization URL>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

有关详细信息，请参阅 [Azure Data Lake Store 连接器](data-factory-azure-datalake-connector.md#linked-service-properties)一文。 

### <a name="dataset"></a>数据集
要定义 Azure Data Lake Store 数据集，请将数据集的**类型**设置为 **AzureDataLakeStore**，并在 **typeProperties** 节中指定以下属性： 

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| folderPath |到 Azure Data Lake Sore 中的容器和文件夹的路径。 |是 |
| fileName |Azure Data Lake Sore 中文件的名称。 fileName 可选，并且区分大小写。 <br/><br/>如果指定 filename，则活动（包括复制）将对特定文件起作用。<br/><br/>如果未指定 fileName，则复制将包括输入数据集的 folderPath 中的所有文件。<br/><br/>如果没有为输出数据集指定 fileName，生成的文件的名称会采用以下格式：Data<Guid>.txt (for example: : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |否 |
| partitionedBy |partitionedBy 是一个可选属性。 它可用于指定时序数据的动态 folderPath 和 filename。 例如，folderPath 可针对每小时的数据参数化。 |否 |
| 格式 | 支持以下格式类型：**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat** 和 **ParquetFormat**。 请将格式中的 **type** 属性设置为上述值之一。 有关详细信息，请参阅[文本格式](data-factory-supported-file-and-compression-formats.md#text-format)、[Json 格式](data-factory-supported-file-and-compression-formats.md#json-format)、[Avro 格式](data-factory-supported-file-and-compression-formats.md#avro-format)、[Orc 格式](data-factory-supported-file-and-compression-formats.md#orc-format)和 [Parquet 格式](data-factory-supported-file-and-compression-formats.md#parquet-format)部分。 <br><br> 如果想要在基于文件的存储之间**按原样复制文件**（二进制副本），可以在输入和输出数据集定义中跳过格式节。 |否 |
| compression | 指定数据的压缩类型和级别。 支持的类型为：**GZip**、**Deflate**、**BZip2** 和 **ZipDeflate**。 支持的级别为：**最佳**和**最快**。 有关详细信息，请参阅 [Azure 数据工厂中的文件和压缩格式](data-factory-supported-file-and-compression-formats.md#compression-support)。 |否 |

#### <a name="example"></a>示例
```json
{
    "name": "AzureDataLakeStoreInput",
    "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/input/",
            "fileName": "SearchLog.tsv",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            }
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

有关详细信息，请参阅 [Azure Data Lake Store 连接器](data-factory-azure-datalake-connector.md#dataset-properties)一文。 

### <a name="azure-data-lake-store-source-in-copy-activity"></a>复制活动中的 Azure Data Lake Store 源
如果要从 Azure Data Lake Store 复制数据，请将复制活动的**源类型**设置为 **AzureDataLakeStoreSource**，并在 **source** 节中指定以下属性：

**AzureDataLakeStoreSource** 支持以下属性 **typeProperties** 部分：

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| recursive |指示是要从子文件夹中以递归方式读取数据，还是只从指定的文件夹中读取数据。 |True（默认值）、False |否 |

#### <a name="example-azuredatalakestoresource"></a>示例：AzureDataLakeStoreSource

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureDakeLaketoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureDataLakeStoreInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "AzureDataLakeStoreSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

有关详细信息，请参阅 [Azure Data Lake Store 连接器](data-factory-azure-datalake-connector.md#copy-activity-properties)一文。

### <a name="azure-data-lake-store-sink-in-copy-activity"></a>复制活动中的 Azure Data Lake Store 接收器
如果要将数据复制到 Azure Data Lake Store，请将复制活动的**接收器类型**设置为 **AzureDataLakeStoreSink**，并在 **sink** 节中指定以下属性：

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| copyBehavior |指定复制行为。 |<b>PreserveHierarchy</b>：保留目标文件夹中的文件层次结构。 从源文件到源文件夹的相对路径与从目标文件到目标文件夹的相对路径相同。<br/><br/><b>FlattenHierarchy</b>：源文件夹中的所有文件在目标文件夹的第一个级别中创建。 创建目标文件时，自动生成名称。<br/><br/><b>MergeFiles</b>：将源文件夹的所有文件合并到一个文件中。 如果指定文件/Blob 名称，则合并的文件名称将为指定的名称；否则，会自动生成文件名。 |否 |

#### <a name="example-azuredatalakestoresink"></a>示例：AzureDataLakeStoreSink
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoDataLake",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureDataLakeStoreOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "AzureDataLakeStoreSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

有关详细信息，请参阅 [Azure Data Lake Store 连接器](data-factory-azure-datalake-connector.md#copy-activity-properties)一文。 

## <a name="azure-cosmos-db"></a>Azure Cosmos DB  

### <a name="linked-service"></a>链接服务
要定义 Azure Cosmos DB 链接服务，请将链接服务的“类型”设置为“DocumentDb”，并在“typeProperties”部分中指定以下属性：  

| **属性** | **说明** | **必需** |
| --- | --- | --- |
| connectionString |指定连接 Azure Cosmos DB 数据库所需的信息。 |是 |

#### <a name="example"></a>示例

```json
{
    "name": "CosmosDBLinkedService",
    "properties": {
        "type": "DocumentDb",
        "typeProperties": {
            "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
        }
    }
}
```
有关详细信息，请参阅 [Azure Cosmos DB 连接器](data-factory-azure-documentdb-connector.md#linked-service-properties)一文。

### <a name="dataset"></a>数据集
要定义 Azure Cosmos DB 数据集，请将数据集的“类型”设置为“DocumentDbCollection”，并在“typeProperties”部分中指定以下属性： 

| **属性** | **说明** | **必需** |
| --- | --- | --- |
| collectionName |Azure Cosmos DB 集合的名称。 |是 |

#### <a name="example"></a>示例

```json
{
    "name": "PersonCosmosDBTable",
    "properties": {
        "type": "DocumentDbCollection",
        "linkedServiceName": "CosmosDBLinkedService",
        "typeProperties": {
            "collectionName": "Person"
        },
        "external": true,
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```
有关详细信息，请参阅 [Azure Cosmos DB 连接器](data-factory-azure-documentdb-connector.md#dataset-properties)一文。

### <a name="azure-cosmos-db-collection-source-in-copy-activity"></a>复制活动中的 Azure Cosmos DB 集合源
如果要从 Azure Cosmos DB 复制数据，请将复制活动的“源类型”设置为“DocumentDbCollectionSource”，并在“源”部分中指定以下属性：


| **属性** | **说明** | **允许的值** | **必需** |
| --- | --- | --- | --- |
| query |指定要读取数据的查询。 |Azure Cosmos DB 支持的查询字符串。 <br/><br/>示例：`SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |否 <br/><br/>如果未指定，则执行的 SQL 语句为：`select <columns defined in structure> from mycollection` |
| nestingSeparator |指示嵌套文档的特殊字符 |任意字符。 <br/><br/>Azure Cosmos DB 是 JSON 文档的 NoSQL 存储，其中允许存在嵌套结构。 Azure 数据工厂允许用户通过 nestingSeparator 来表示层次结构，即 上述示例中的“.”。 通过该分隔符，复制活动会根据表定义中的“Name.First”、“Name.Middle”和“Name.Last”生成包含三个子元素（First、Middle 和 Last）的“Name”对象。 |否 |

#### <a name="example"></a>示例

```json
{
    "name": "DocDbToBlobPipeline",
    "properties": {
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "DocumentDbCollectionSource",
                    "query": "SELECT Person.Id, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person",
                    "nestingSeparator": "."
                },
                "sink": {
                    "type": "BlobSink",
                    "blobWriterAddHeader": true,
                    "writeBatchSize": 1000,
                    "writeBatchTimeout": "00:00:59"
                }
            },
            "inputs": [{
                "name": "PersonCosmosDBTable"
            }],
            "outputs": [{
                "name": "PersonBlobTableOut"
            }],
            "policy": {
                "concurrency": 1
            },
            "name": "CopyFromCosmosDbToBlob"
        }],
        "start": "2016-04-01T00:00:00",
        "end": "2016-04-02T00:00:00"
    }
}
```

### <a name="azure-cosmos-db-collection-sink-in-copy-activity"></a>复制活动中的 Azure Cosmos DB 集合接收器
如果要将数据复制到 Azure Cosmos DB，请将复制活动的“接收器类型”设置为“DocumentDbCollectionSink”，并在“接收器”部分中指定以下属性：

| **属性** | **说明** | **允许的值** | **必需** |
| --- | --- | --- | --- |
| nestingSeparator |源列名称中的特殊字符，指示需要嵌套的文档。 <br/><br/>在上述示例中：输出表中的 `Name.First` 在 Cosmos DB 文档中生成以下 JSON 结构：<br/><br/>"Name": {<br/>    "First":"John"<br/>}, |用于分隔嵌套级别的字符。<br/><br/>默认值为 `.`（点）。 |用于分隔嵌套级别的字符。 <br/><br/>默认值为 `.`（点）。 |
| writeBatchSize |向 Azure Cosmos DB 服务发送创建文档的并行请求数。<br/><br/>将数据复制到 Azure Cosmos DB 以及从其中复制数据时，可使用此属性对性能进行微调。 增加 writeBatchSize 时，由于会向 Azure Cosmos DB 发送更多的并行请求，因此可以获得更好的性能。 但是，需要避免可能会引发“请求速率大”的错误消息的限制。<br/><br/>限制由多种因素决定，包括文档大小、文档中的术语数、目标集合的索引策略等。对于复制操作，可以使用更佳的集合（例如 S3）实现最大吞吐量（2,500 请求单位/秒）。 |Integer |否（默认值：5） |
| writeBatchTimeout |超时之前等待操作完成的时间。 |timespan<br/><br/> 示例：“00:30:00”（30 分钟）。 |否 |

#### <a name="example"></a>示例

```json
{
    "name": "BlobToDocDbPipeline",
    "properties": {
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "DocumentDbCollectionSink",
                    "nestingSeparator": ".",
                    "writeBatchSize": 2,
                    "writeBatchTimeout": "00:00:00"
                },
                "translator": {
                    "type": "TabularTranslator",
                    "ColumnMappings": "FirstName: Name.First, MiddleName: Name.Middle, LastName: Name.Last, BusinessEntityID: BusinessEntityID, PersonType: PersonType, NameStyle: NameStyle, Title: Title, Suffix: Suffix"
                }
            },
            "inputs": [{
                "name": "PersonBlobTableIn"
            }],
            "outputs": [{
                "name": "PersonCosmosDbTableOut"
            }],
            "policy": {
                "concurrency": 1
            },
            "name": "CopyFromBlobToCosmosDb"
        }],
        "start": "2016-04-14T00:00:00",
        "end": "2016-04-15T00:00:00"
    }
}
```

有关详细信息，请参阅 [Azure Cosmos DB 连接器](data-factory-azure-documentdb-connector.md#copy-activity-properties)一文。

## <a name="azure-sql-database"></a>Azure SQL 数据库

### <a name="linked-service"></a>链接服务
要定义 Azure SQL 数据库链接服务，请将链接服务的**类型**设置为 **AzureSqlDatabase**，并在 **typeProperties** 节中指定以下属性：  

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| connectionString |为 connectionString 属性指定连接到 Azure SQL 数据库实例所需的信息。 |是 |

#### <a name="example"></a>示例
```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
    }
}
```

有关详细信息，请参阅 [Azure SQL 连接器](data-factory-azure-sql-connector.md#linked-service-properties)一文。 

### <a name="dataset"></a>数据集
要定义 Azure SQL 数据库数据集，请将数据集的**类型**设置为 **AzureSqlTable**，并在 **typeProperties** 节中指定以下属性： 

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| tableName |链接服务引用的 Azure SQL 数据库实例中的表名称或视图名称。 |是 |

#### <a name="example"></a>示例

```json
{
    "name": "AzureSqlInput",
    "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
有关详细信息，请参阅 [Azure SQL 连接器](data-factory-azure-sql-connector.md#dataset-properties)一文。 

### <a name="sql-source-in-copy-activity"></a>复制活动中的 SQL 源
如果要从 Azure SQL 数据库复制数据，请将复制活动的**源类型**设置为 **SqlSource**，并在 **source** 节中指定以下属性：


| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| sqlReaderQuery |使用自定义查询读取数据。 |SQL 查询字符串。 示例：`select * from MyTable`。 |否 |
| sqlReaderStoredProcedureName |从源表读取数据的存储过程的名称。 |存储过程的名称。 |否 |
| storedProcedureParameters |存储过程的参数。 |名称/值对。 参数的名称和大小写必须与存储过程参数的名称和大小写匹配。 |否 |

#### <a name="example"></a>示例

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSQLInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
有关详细信息，请参阅 [Azure SQL 连接器](data-factory-azure-sql-connector.md#copy-activity-properties)一文。 

### <a name="sql-sink-in-copy-activity"></a>复制活动中的 SQL 接收器
如果要将数据复制到 Azure SQL 数据库，请将复制活动的**接收器类型**设置为 **SqlSink**，并在 **sink** 节中指定以下属性：

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| writeBatchTimeout |超时之前等待批插入操作完成时的等待时间。 |timespan<br/><br/> 示例：“00:30:00”（30 分钟）。 |否 |
| writeBatchSize |缓冲区大小达到 writeBatchSize 时会数据插入 SQL 表。 |整数（行数） |否（默认值：10000） |
| sqlWriterCleanupScript |指定复制活动要执行的查询，以便清除特定切片的数据。 |查询语句。 |否 |
| sliceIdentifierColumnName |指定复制活动要填充自动生成切片标识符的列名，以便在重新运行时清除特定切片的数据。 |数据类型为 binary(32) 的列的列名。 |否 |
| sqlWriterStoredProcedureName |在目标表中更新/插入数据的存储过程的名称。 |存储过程的名称。 |否 |
| storedProcedureParameters |存储过程的参数。 |名称/值对。 参数的名称和大小写必须与存储过程参数的名称和大小写匹配。 |否 |
| sqlWriterTableType |指定要在存储过程中使用的表类型名称。 通过复制活动，使移动数据在具备此表类型的临时表中可用。 然后，存储过程代码可合并复制数据和现有数据。 |表类型名称。 |否 |

#### <a name="example"></a>示例

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQL",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureSqlOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource",
                    "blobColumnSeparators": ","
                },
                "sink": {
                    "type": "SqlSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

有关详细信息，请参阅 [Azure SQL 连接器](data-factory-azure-sql-connector.md#copy-activity-properties)一文。 

## <a name="azure-sql-data-warehouse"></a>Azure SQL 数据仓库

### <a name="linked-service"></a>链接服务
要定义 Azure SQL 数据仓库链接服务，请将链接服务的**类型**设置为 **AzureSqlDW**，并在 **typeProperties** 节中指定以下属性：  

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| connectionString |为 connectionString 属性指定连接到 Azure SQL 数据仓库实例所需的信息。 |是 |



#### <a name="example"></a>示例

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
    }
}
```

有关详细信息，请参阅 [Azure SQL 数据仓库连接器](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties)一文。 

### <a name="dataset"></a>数据集
要定义 Azure SQL 数据仓库数据集，请将数据集的**类型**设置为 **AzureSqlDWTable**，并在 **typeProperties** 节中指定以下属性： 

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| tableName |链接服务引用的 Azure SQL 数据仓库数据库中的表名称或视图。 |是 |

#### <a name="example"></a>示例

```json
{
    "name": "AzureSqlDWInput",
    "properties": {
    "type": "AzureSqlDWTable",
        "linkedServiceName": "AzureSqlDWLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

有关详细信息，请参阅 [Azure SQL 数据仓库连接器](data-factory-azure-sql-data-warehouse-connector.md#dataset-properties)一文。 

### <a name="sql-dw-source-in-copy-activity"></a>复制活动中的 SQL 数据仓库源
如果要从 Azure SQL 数据仓库复制数据，请将复制活动的**源类型**设置为 **SqlDWSource**，并在 **source** 节中指定以下属性：


| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| sqlReaderQuery |使用自定义查询读取数据。 |SQL 查询字符串。 例如： `select * from MyTable`。 |否 |
| sqlReaderStoredProcedureName |从源表读取数据的存储过程的名称。 |存储过程的名称。 |否 |
| storedProcedureParameters |存储过程的参数。 |名称/值对。 参数的名称和大小写必须与存储过程参数的名称和大小写匹配。 |否 |

#### <a name="example"></a>示例

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLDWtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSqlDWInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlDWSource",
                    "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

有关详细信息，请参阅 [Azure SQL 数据仓库连接器](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)一文。 

### <a name="sql-dw-sink-in-copy-activity"></a>复制活动中的 SQL 数据仓库接收器
如果要将数据复制到 Azure SQL 数据仓库，请将复制活动的**接收器类型**设置为 **SqlDWSink**，并在 **sink** 节中指定以下属性：

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| sqlWriterCleanupScript |指定复制活动要执行的查询，以便清除特定切片的数据。 |查询语句。 |否 |
| allowPolyBase |指示是否使用 PolyBase（如果适用）而不是 BULKINSERT 机制。 <br/><br/> **使用 PolyBase 是将数据加载到 SQL 数据仓库的建议方式。** |True <br/>False（默认值） |否 |
| polyBaseSettings |**allowPolybase** 属性设置为 **true** 时可以指定的一组属性。 |&nbsp; |否 |
| rejectValue |指定在查询失败之前可以拒绝的行数或百分比。 <br/><br/>有关 PolyBase 的拒绝选项的详细信息，请参阅[创建外部表 (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx) 主题的**参数**部分。 |0（默认值）、1、2 … |否 |
| rejectType |指定将 rejectValue 选项指定为文字值还是百分比。 |值（默认），百分比 |否 |
| rejectSampleValue |确定在 PolyBase 重新计算被拒绝行的百分比之前要检索的行数。 |1、2 … |如果 **rejectType** 是**百分比**，则为“是” |
| useTypeDefault |指定 PolyBase 从文本文件检索数据时如何处理分隔文本文件中的缺失值。<br/><br/>有关此属性的详细信息，请参阅[创建外部文件格式 (Transact SQL)](https://msdn.microsoft.com/library/dn935026.aspx) 中的参数部分。 |True、False（默认值） |否 |
| writeBatchSize |缓冲区大小达到 writeBatchSize 时会数据插入 SQL 表 |整数（行数） |否（默认值：10000） |
| writeBatchTimeout |超时之前等待批插入操作完成时的等待时间。 |timespan<br/><br/> 示例：“00:30:00”（30 分钟）。 |否 |

#### <a name="example"></a>示例

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQLDW",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureSqlDWOutput"
            }],
            "typeProperties": {
                "source": {
                "type": "BlobSource",
                    "blobColumnSeparators": ","
                },
                "sink": {
                    "type": "SqlDWSink",
                    "allowPolyBase": true
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

有关详细信息，请参阅 [Azure SQL 数据仓库连接器](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)一文。 

## <a name="azure-search"></a>Azure 搜索

### <a name="linked-service"></a>链接服务
要定义 Azure 搜索链接服务，请将链接服务的**类型**设置为 **AzureSearch**，并在 **typeProperties** 节中指定以下属性：  

| 属性 | 说明 | 必选 |
| -------- | ----------- | -------- |
| url | Azure 搜索服务的 URL。 | 是 |
| key | Azure 搜索服务的管理密钥。 | 是 |

#### <a name="example"></a>示例

```json
{
    "name": "AzureSearchLinkedService",
    "properties": {
        "type": "AzureSearch",
        "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": "<AdminKey>"
        }
    }
}
```

有关详细信息，请参阅 [Azure 搜索连接器](data-factory-azure-search-connector.md#linked-service-properties)一文。

### <a name="dataset"></a>数据集
要定义 Azure 搜索数据集，请将数据集的**类型**设置为 **AzureSearchIndex**，并在 **typeProperties** 节中指定以下属性： 

| 属性 | 说明 | 必选 |
| -------- | ----------- | -------- |
| type | type 属性必须设置为 **AzureSearchIndex**。| 是 |
| indexName | Azure 搜索索引的名称。 数据工厂不创建索引。 索引必须存在于 Azure 搜索中。 | 是 |

#### <a name="example"></a>示例

```json
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "linkedServiceName": "AzureSearchLinkedService",
        "typeProperties": {
            "indexName": "products"
        },
        "availability": {
            "frequency": "Minute",
            "interval": 15
        }
    }
}
```

有关详细信息，请参阅 [Azure 搜索连接器](data-factory-azure-search-connector.md#dataset-properties)一文。

### <a name="azure-search-index-sink-in-copy-activity"></a>复制活动中的 Azure 搜索索引接收器
如果要将数据复制到 Azure 搜索索引，请将复制活动的**接收器类型**设置为 **AzureSearchIndexSink**，并在 **sink** 节中指定以下属性：

| 属性 | 说明 | 允许的值 | 必选 |
| -------- | ----------- | -------------- | -------- |
| WriteBehavior | 指定索引中已存在文档时要合并还是替换该文档。 | 合并（默认值）<br/>上传| 否 |
| WriteBatchSize | 缓冲区大小达到 writeBatchSize 时会数据上传到 Azure 搜索索引。 | 1 到 1,000。 默认值为 1000。 | 否 |

#### <a name="example"></a>示例

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "SqlServertoAzureSearchIndex",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": " SqlServerInput"
            }],
            "outputs": [{
                "name": "AzureSearchIndexDataset"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "AzureSearchIndexSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

有关详细信息，请参阅 [Azure 搜索连接器](data-factory-azure-search-connector.md#copy-activity-properties)一文。

## <a name="azure-table-storage"></a>Azure 表存储

### <a name="linked-service"></a>链接服务
有两种类型的链接服务：Azure 存储链接服务和 Azure 存储 SAS 链接服务。

#### <a name="azure-storage-linked-service"></a>Azure 存储链接服务
要使用**帐户密钥**将 Azure 存储帐户链接到数据工厂，请创建 Azure 存储链接服务。 要定义 Azure 存储链接服务，请将链接服务的**类型**设置为 **AzureStorage**。 然后，可在 **typeProperties** 节中指定以下属性：  

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type |type 属性必须设置为：**AzureStorage** |是 |
| connectionString |为 connectionString 属性指定连接到 Azure 存储所需的信息。 |是 |

**示例：**  

```json
{  
    "name": "StorageLinkedService",  
    "properties": {  
        "type": "AzureStorage",  
        "typeProperties": {  
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"  
        }  
    }  
}  
```

#### <a name="azure-storage-sas-linked-service"></a>Azure 存储 SAS 链接服务
Azure 存储 SAS 链接服务可让你使用共享访问签名 (SAS) 将 Azure 存储帐户链接到 Azure 数据工厂。 这样，便可以将存储中所有/特定资源（Blob/容器）的受限/限时访问权限提供给数据工厂。 要使用共享访问签名将 Azure 存储帐户链接到数据工厂，请创建 Azure 存储 SAS 链接服务。 要定义 Azure 存储 SAS 链接服务，请将链接服务的**类型**设置为 **AzureStorageSas**。 然后，可在 **typeProperties** 节中指定以下属性：   

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type |type 属性必须设置为：**AzureStorageSas** |是 |
| sasUri |指定 Azure 存储资源（例如 Blob、容器或表）的共享访问签名 URI。 |是 |

**示例：**

```json
{  
    "name": "StorageSasLinkedService",  
    "properties": {  
        "type": "AzureStorageSas",  
        "typeProperties": {  
            "sasUri": "<storageUri>?<sasToken>"   
        }  
    }  
}  
```

有关这些链接服务的详细信息，请参阅 [Azure 表存储连接器](data-factory-azure-table-connector.md#linked-service-properties)一文。 

### <a name="dataset"></a>数据集
要定义 Azure 表数据集，请将数据集的**类型**设置为 **AzureTable**，并在 **typeProperties** 节中指定以下属性： 

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| tableName |链接服务指代的 Azure 表数据库实例中表的名称。 |是的。 指定了 tableName 但未指定 azureTableSourceQuery 时，表中的所有记录都将复制到目标中。 如果还指定了 azureTableSourceQuery，则会将满足查询的表中的记录复制到目标中。 |

#### <a name="example"></a>示例

```json
{
    "name": "AzureTableInput",
    "properties": {
        "type": "AzureTable",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

有关这些链接服务的详细信息，请参阅 [Azure 表存储连接器](data-factory-azure-table-connector.md#dataset-properties)一文。 

### <a name="azure-table-source-in-copy-activity"></a>复制活动中的 Azure 表源
如果要从 Azure 表存储复制数据，请将复制活动的**源类型**设置为 **AzureTableSource**，并在 **source** 节中指定以下属性：

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| azureTableSourceQuery |使用自定义查询读取数据。 |Azure 表查询字符串。 请参阅下一节中的示例。 |不会。 指定了 tableName 但未指定 azureTableSourceQuery 时，表中的所有记录都将复制到目标中。 如果还指定了 azureTableSourceQuery，则会将满足查询的表中的记录复制到目标中。 |
| azureTableSourceIgnoreTableNotFound |指示是否存在忽略表异常。 |TRUE<br/>FALSE |否 |

#### <a name="example"></a>示例

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureTabletoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureTableInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "AzureTableSource",
                    "AzureTableSourceQuery": "PartitionKey eq 'DefaultPartitionKey'"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

有关这些链接服务的详细信息，请参阅 [Azure 表存储连接器](data-factory-azure-table-connector.md#copy-activity-properties)一文。 

### <a name="azure-table-sink-in-copy-activity"></a>复制活动中的 Azure 表接收器
如果要将数据复制到 Azure 表存储，请将复制活动的**接收器类型**设置为 **AzureTableSink**，并在 **sink** 节中指定以下属性：

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| azureTableDefaultPartitionKeyValue |接收器可以使用的默认分区键值。 |字符串值。 |否 |
| azureTablePartitionKeyName |指定列名称，使用列值作为分区键。 如果未指定，则使用 AzureTableDefaultPartitionKeyValue 作为分区键。 |列名称。 |否 |
| azureTableRowKeyName |指定列名称，使用列值作为行键。 如果未指定，对每一行使用 GUID。 |列名称。 |否 |
| azureTableInsertType |将数据插入 Azure 表的模式。<br/><br/>此属性控制输出表中具有匹配的分区键和行键的现有行是否替换或合并其值。 <br/><br/>若要了解这些设置（合并和替换）的工作原理，请参阅 [Insert or Merge Entity](https://msdn.microsoft.com/library/azure/hh452241.aspx)（插入或合并实体）和 [Insert or Replace Entity](https://msdn.microsoft.com/library/azure/hh452242.aspx)（插入或替换实体）主题。 <br/><br> 此设置在行级别而不是表级别进行应用，并且两个选项都不会删除输入中不存在的输出表中的行。 |合并（默认值）<br/>replace |否 |
| writeBatchSize |writeBatchSize 或 writeBatchTimeout 命中时，将数据插入 Azure 表。 |整数（行数） |否（默认值：10000） |
| writeBatchTimeout |writeBatchSize 或 writeBatchTimeout 命中时，将数据插入 Azure 表 |timespan<br/><br/>示例：“00:20:00”（20 分钟） |否（默认为存储客户端默认超时值 90 秒） |

#### <a name="example"></a>示例

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoTable",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureTableOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "AzureTableSink",
                    "writeBatchSize": 100,
                    "writeBatchTimeout": "01:00:00"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
有关这些链接服务的详细信息，请参阅 [Azure 表存储连接器](data-factory-azure-table-connector.md#copy-activity-properties)一文。 

## <a name="amazon-redshift"></a>Amazon RedShift

### <a name="linked-service"></a>链接服务
要定义 Amazon Redshift 链接服务，请将链接服务的**类型**设置为 **AmazonRedshift**，并在 **typeProperties** 节中指定以下属性：  

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| server |Amazon Redshift 服务器的 IP 地址或主机名。 |是 |
| 端口 |Amazon Redshift 服务器用于侦听客户端连接的 TCP 端口数。 |否，默认值：5439 |
| database |Amazon Redshift 数据库名称。 |是 |
| username |有权访问数据库的用户的名称。 |是 |
| password |用户帐户密码。 |是 |

#### <a name="example"></a>示例

```json
{
    "name": "AmazonRedshiftLinkedService",
    "properties": {
        "type": "AmazonRedshift",
        "typeProperties": {
            "server": "<Amazon Redshift host name or IP address>",
            "port": 5439,
            "database": "<database name>",
            "username": "user",
            "password": "password"
        }
    }
}
```

有关详细信息，请参阅 [Amazon Redshift 连接器](#data-factory-amazon-redshift-connector.md#linked-service-properties)一文。 

### <a name="dataset"></a>数据集
要定义 Amazon Redshift 数据集，请将数据集的**类型**设置为 **RelationalTable**，并在 **typeProperties** 节中指定以下属性： 

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| tableName |Amazon Redshift 数据库中链接服务引用的表的名称。 |否（如果指定了 **RelationalSource** 的**query**） |


#### <a name="example"></a>示例

```json
{
    "name": "AmazonRedshiftInputDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "AmazonRedshiftLinkedService",
        "typeProperties": {
            "tableName": "<Table name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
有关详细信息，请参阅 [Amazon Redshift 连接器](#data-factory-amazon-redshift-connector.md#dataset-properties)一文。

### <a name="relational-source-in-copy-activity"></a>复制活动中的关系源 
如果要从 Amazon Redshift 复制数据，请将复制活动的**源类型**设置为 **RelationalSource**，并在 **source** 节中指定以下属性：

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| query |使用自定义查询读取数据。 |SQL 查询字符串。 例如： `select * from MyTable`。 |否（如果指定了**数据集**的 **tableName**） |

#### <a name="example"></a>示例

```json
{
    "name": "CopyAmazonRedshiftToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "AmazonRedshiftInputDataset"
            }],
            "outputs": [{
                "name": "AzureBlobOutputDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "AmazonRedshiftToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```
有关详细信息，请参阅 [Amazon Redshift 连接器](#data-factory-amazon-redshift-connector.md#copy-activity-properties)一文。

## <a name="ibm-db2"></a>IBM DB2

### <a name="linked-service"></a>链接服务
要定义 IBM DB2 链接服务，请将链接服务的**类型**设置为 **OnPremisesDB2**，并在 **typeProperties** 节中指定以下属性：  

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| server |DB2 服务器的名称。 |是 |
| database |DB2 数据库的名称。 |是 |
| schema |数据库中架构的名称。 架构名称区分大小写。 |否 |
| authenticationType |用于连接 DB2 数据库的身份验证类型。 可能的值为：Anonymous、Basic 和 Windows。 |是 |
| username |如果使用基本或 Windows 身份验证，请指定用户名。 |否 |
| password |指定为用户名指定的用户帐户的密码。 |否 |
| gatewayName |网关的名称 - 数据工厂服务应使用此网关连接到本地 DB2 数据库。 |是 |

#### <a name="example"></a>示例
```json
{
    "name": "OnPremDb2LinkedService",
    "properties": {
        "type": "OnPremisesDb2",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```
有关详细信息，请参阅 [IBM DB2 连接器](#data-factory-onprem-db2-connector.md#linked-service-properties)一文。

### <a name="dataset"></a>数据集
要定义 DB2 数据集，请将数据集的**类型**设置为 **RelationalTable**，并在 **typeProperties** 节中指定以下属性：

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| tableName |链接服务引用的 DB2 数据库实例中表的名称。 TableName 区分大小写。 |否（如果指定了 **RelationalSource** 的**query**） 

#### <a name="example"></a>示例
```json
{
    "name": "Db2DataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremDb2LinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

有关详细信息，请参阅 [IBM DB2 连接器](#data-factory-onprem-db2-connector.md#dataset-properties)一文。

### <a name="relational-source-in-copy-activity"></a>复制活动中的关系源
如果要从 IBM DB2 复制数据，请将复制活动的**源类型**设置为 **RelationalSource**，并在 **source** 节中指定以下属性：


| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| query |使用自定义查询读取数据。 |SQL 查询字符串。 例如： `"query": "select * from "MySchema"."MyTable""`。 |否（如果指定了**数据集**的 **tableName**） |

#### <a name="example"></a>示例
```json
{
    "name": "CopyDb2ToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "select * from \"Orders\""
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "inputs": [{
                "name": "Db2DataSet"
            }],
            "outputs": [{
                "name": "AzureBlobDb2DataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "Db2ToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```
有关详细信息，请参阅 [IBM DB2 连接器](#data-factory-onprem-db2-connector.md#copy-activity-properties)一文。

## <a name="mysql"></a>MySQL

### <a name="linked-service"></a>链接服务
要定义 MySQL 链接服务，请将链接服务的**类型**设置为 **OnPremisesMySql**，并在 **typeProperties** 节中指定以下属性：  

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| server |MySQL 服务器的名称。 |是 |
| database |MySQL 数据库的名称。 |是 |
| schema |数据库中架构的名称。 |否 |
| authenticationType |用于连接 MySQL 数据库的身份验证类型。 可能的值为：`Basic` |是 |
| username |指定用于连接到 MySQL 数据库的用户名。 |是 |
| password |指定该用户帐户的密码。 |是 |
| gatewayName |网关的名称 - 数据工厂服务应使用此网关连接到本地 MySQL数据库。 |是 |

#### <a name="example"></a>示例

```json
{
    "name": "OnPremMySqlLinkedService",
    "properties": {
        "type": "OnPremisesMySql",
        "typeProperties": {
            "server": "<server name>",
            "database": "<database name>",
            "schema": "<schema name>",
            "authenticationType": "<authentication type>",
            "userName": "<user name>",
            "password": "<password>",
            "gatewayName": "<gateway>"
        }
    }
}
```

有关详细信息，请参阅 [MySQL 连接器](data-factory-onprem-mysql-connector.md#linked-service-properties)一文。 

### <a name="dataset"></a>数据集
要定义 MySQL 数据集，请将数据集的**类型**设置为 **RelationalTable**，并在 **typeProperties** 节中指定以下属性： 

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| tableName |链接服务引用的 MySQL 数据库实例中表的名称。 |否（如果指定了 **RelationalSource** 的**query**） |

#### <a name="example"></a>示例

```json
{
    "name": "MySqlDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremMySqlLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
有关详细信息，请参阅 [MySQL 连接器](data-factory-onprem-mysql-connector.md#dataset-properties)一文。 

### <a name="relational-source-in-copy-activity"></a>复制活动中的关系源
如果要从 MySQL 数据库复制数据，请将复制活动的**源类型**设置为 **RelationalSource**，并在 **source** 节中指定以下属性：


| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| query |使用自定义查询读取数据。 |SQL 查询字符串。 例如： `select * from MyTable`。 |否（如果指定了**数据集**的 **tableName**） |


#### <a name="example"></a>示例
```json
{
    "name": "CopyMySqlToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "MySqlDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobMySqlDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "MySqlToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

有关详细信息，请参阅 [MySQL 连接器](data-factory-onprem-mysql-connector.md#copy-activity-properties)一文。 

## <a name="oracle"></a>Oracle 

### <a name="linked-service"></a>链接服务
要定义 Oracle 链接服务，请将链接服务的**类型**设置为 **OnPremisesOracle**，并在 **typeProperties** 节中指定以下属性：  

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| driverType | 指定在 Oracle 数据库复制/粘贴数据所使用的驱动程序。 允许的值为 **Microsoft** 或 **ODP**（默认值）。 请参阅驱动程序详细信息上的[支持版本和安装](#supported-versions-and-installation)部分。 | 否 |
| connectionString | 为 connectionString 属性指定连接到 Oracle 数据库实例所需的信息。 | 是 |
| gatewayName | 用于连接到本地 Oracle 服务器的网关的名称 |是 |

#### <a name="example"></a>示例
```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

有关详细信息，请参阅 [Oracle 连接器](data-factory-onprem-oracle-connector.md#linked-service-properties)一文。

### <a name="dataset"></a>数据集
要定义 Oracle 数据集，请将数据集的**类型**设置为 **OracleTable**，并在 **typeProperties** 节中指定以下属性： 

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| tableName |链接服务引用的 Oracle 数据库中表的名称。 |否（如果指定了 **OracleSource** 的 **oracleReaderQuery**） |

#### <a name="example"></a>示例

```json
{
    "name": "OracleInput",
    "properties": {
        "type": "OracleTable",
        "linkedServiceName": "OnPremisesOracleLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "offset": "01:00:00",
            "interval": "1",
            "anchorDateTime": "2016-02-27T12:00:00",
            "frequency": "Hour"
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
有关详细信息，请参阅 [Oracle 连接器](data-factory-onprem-oracle-connector.md#dataset-properties)一文。

### <a name="oracle-source-in-copy-activity"></a>复制活动中的 Oracle 源
如果要从 Oracle 数据库复制数据，请将复制活动的**源类型**设置为 **OracleSource**，并在 **source** 节中指定以下属性：

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| oracleReaderQuery |使用自定义查询读取数据。 |SQL 查询字符串。 例如： `select * from MyTable` <br/><br/>如果未指定，则执行的 SQL 语句为：`select * from MyTable` |否（如果指定了**数据集**的 **tableName**） |

#### <a name="example"></a>示例

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "OracletoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": " OracleInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "OracleSource",
                    "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
            "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

有关详细信息，请参阅 [Oracle 连接器](data-factory-onprem-oracle-connector.md#copy-activity-properties)一文。

### <a name="oracle-sink-in-copy-activity"></a>复制活动中的 Oracle 接收器
如果要将数据复制到 Oracle 数据库，请将复制活动的**接收器类型**设置为 **OracleSink**，并在 **sink** 节中指定以下属性：

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| writeBatchTimeout |超时之前等待批插入操作完成时的等待时间。 |timespan<br/><br/> 示例：00:30:00（30 分钟）。 |否 |
| writeBatchSize |缓冲区大小达到 writeBatchSize 时会数据插入 SQL 表。 |整数（行数） |否（默认值：100） |
| sqlWriterCleanupScript |指定复制活动要执行的查询，以便清除特定切片的数据。 |查询语句。 |否 |
| sliceIdentifierColumnName |指定复制活动要使用自动生成的切片标识符进行填充的列名，该标识符用于重新运行时清除特定切片的数据。 |数据类型为 binary(32) 的列的列名。 |否 |

#### <a name="example"></a>示例
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-05T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoOracle",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "OracleOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "OracleSink"
                }
            },
            "scheduler": {
                "frequency": "Day",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
有关详细信息，请参阅 [Oracle 连接器](data-factory-onprem-oracle-connector.md#copy-activity-properties)一文。

## <a name="postgresql"></a>PostgreSQL

### <a name="linked-service"></a>链接服务
要定义 PostgreSQL 链接服务，请将链接服务的**类型**设置为 **OnPremisesPostgreSql**，并在 **typeProperties** 节中指定以下属性：  

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| server |PostgreSQL 服务器的名称。 |是 |
| database |PostgreSQL 数据库的名称。 |是 |
| schema |数据库中架构的名称。 架构名称区分大小写。 |否 |
| authenticationType |用于连接到 PostgreSQL 数据库的身份验证类型。 可能的值为：Anonymous、Basic 和 Windows。 |是 |
| username |如果使用基本或 Windows 身份验证，请指定用户名。 |否 |
| password |指定为用户名指定的用户帐户的密码。 |否 |
| gatewayName |网关的名称 - 数据工厂服务应使用此网关连接到本地 PostgreSQL 数据库。 |是 |

#### <a name="example"></a>示例

```json
{
    "name": "OnPremPostgreSqlLinkedService",
    "properties": {
        "type": "OnPremisesPostgreSql",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```
有关详细信息，请参阅 [PostgreSQL 连接器](data-factory-onprem-postgresql-connector.md#linked-service-properties)一文。

### <a name="dataset"></a>数据集
要定义 PostgreSQL 数据集，请将数据集的**类型**设置为 **RelationalTable**，并在 **typeProperties** 节中指定以下属性： 

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| tableName |链接服务引用的 PostgreSQL 数据库实例中表的名称。 TableName 区分大小写。 |否（如果指定了 **RelationalSource** 的**query**） |

#### <a name="example"></a>示例
```json
{
    "name": "PostgreSqlDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremPostgreSqlLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
有关详细信息，请参阅 [PostgreSQL 连接器](data-factory-onprem-postgresql-connector.md#dataset-properties)一文。

### <a name="relational-source-in-copy-activity"></a>复制活动中的关系源
如果要从 PostgreSQL 数据库复制数据，请将复制活动的**源类型**设置为 **RelationalSource**，并在 **source** 节中指定以下属性：


| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| query |使用自定义查询读取数据。 |SQL 查询字符串。 例如：“query”：“从 \"MySchema\".\"MyTable\" 选择 *”。 |否（如果指定了**数据集**的 **tableName**） |

#### <a name="example"></a>示例

```json
{
    "name": "CopyPostgreSqlToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "select * from \"public\".\"usstates\""
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "inputs": [{
                "name": "PostgreSqlDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobPostgreSqlDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "PostgreSqlToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

有关详细信息，请参阅 [PostgreSQL 连接器](data-factory-onprem-postgresql-connector.md#copy-activity-properties)一文。

## <a name="sap-business-warehouse"></a>SAP Business Warehouse


### <a name="linked-service"></a>链接服务
要定义 SAP Business Warehouse (BW) 链接服务，请将链接服务的**类型**设置为 **SapBw**，并在 **typeProperties** 节中指定以下属性：  

属性 | 说明 | 允许的值 | 必选
-------- | ----------- | -------------- | --------
server | SAP BW 实例所驻留的服务器的名称。 | 字符串 | 是
systemNumber | SAP BW 系统的系统编号。 | 用字符串表示的两位十进制数。 | 是
clientId | SAP W 系统中的客户端的客户端 ID。 | 用字符串表示的三位十进制数。 | 是
username | 有权访问 SAP 服务器的用户名 | 字符串 | 是
password | 用户密码。 | 字符串 | 是
gatewayName | 网关的名称，数据工厂服务应使用此网关连接到本地 SAP BW 实例。 | 字符串 | 是
encryptedCredential | 加密的凭据字符串。 | 字符串 | 否

#### <a name="example"></a>示例

```json
{
    "name": "SapBwLinkedService",
    "properties": {
        "type": "SapBw",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "username": "<SAP user>",
            "password": "<Password for SAP user>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

有关详细信息，请参阅 [SAP Business Warehouse 连接器](data-factory-sap-business-warehouse-connector.md#linked-service-properties)一文。 

### <a name="dataset"></a>数据集
要定义 SAP BW 数据集，请将数据集的**类型**设置为 **RelationalTable**。 **RelationalTable** 类型的 SAP BW 数据集不支持任何类型特定的属性。  

#### <a name="example"></a>示例

```json
{
    "name": "SapBwDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapBwLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
有关详细信息，请参阅 [SAP Business Warehouse 连接器](data-factory-sap-business-warehouse-connector.md#dataset-properties)一文。 

### <a name="relational-source-in-copy-activity"></a>复制活动中的关系源
如果要从 SAP Business Warehouse 复制数据，请将复制活动的**源类型**设置为 **RelationalSource**，并在 **source** 节中指定以下属性：


| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| query | 指定要从 SAP BW 实例读取数据的 MDX 查询。 | MDX 查询。 | 是 |

#### <a name="example"></a>示例

```json
{
    "name": "CopySapBwToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "<MDX query for SAP BW>"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "SapBwDataset"
            }],
            "outputs": [{
                "name": "AzureBlobDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "SapBwToBlob"
        }],
        "start": "2017-03-01T18:00:00",
        "end": "2017-03-01T19:00:00"
    }
}
```

有关详细信息，请参阅 [SAP Business Warehouse 连接器](data-factory-sap-business-warehouse-connector.md#copy-activity-properties)一文。 

## <a name="sap-hana"></a>SAP HANA

### <a name="linked-service"></a>链接服务
要定义 SAP HANA 链接服务，请将链接服务的**类型**设置为 **SapHana**，并在 **typeProperties** 节中指定以下属性：  

属性 | 说明 | 允许的值 | 必选
-------- | ----------- | -------------- | --------
server | SAP HANA 实例所驻留的服务器的名称。 如果服务器使用的是自定义端口，则指定 `server:port`。 | 字符串 | 是
authenticationType | 身份验证的类型。 | 字符串。 “基本”或“Windows” | 是 
username | 有权访问 SAP 服务器的用户名 | 字符串 | 是
password | 用户密码。 | 字符串 | 是
gatewayName | 网关的名称，数据工厂服务应使用此网关连接到本地 SAP HANA 实例。 | 字符串 | 是
encryptedCredential | 加密的凭据字符串。 | 字符串 | 否

#### <a name="example"></a>示例

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "server": "<server name>",
            "authenticationType": "<Basic, or Windows>",
            "username": "<SAP user>",
            "password": "<Password for SAP user>",
            "gatewayName": "<gateway name>"
        }
    }
}

```
有关详细信息，请参阅 [SAP HANA 连接器](data-factory-sap-hana-connector.md#linked-service-properties)一文。
 
### <a name="dataset"></a>数据集
要定义 SAP HANA 数据集，请将数据集的**类型**设置为 **RelationalTable**。 **RelationalTable** 类型的 SAP HANA 数据集不支持任何类型特定的属性。 

#### <a name="example"></a>示例

```json
{
    "name": "SapHanaDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapHanaLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
有关详细信息，请参阅 [SAP HANA 连接器](data-factory-sap-hana-connector.md#dataset-properties)一文。 

### <a name="relational-source-in-copy-activity"></a>复制活动中的关系源
如果要从 SAP HANA 数据存储复制数据，请将复制活动的**源类型**设置为 **RelationalSource**，并在 **source** 节中指定以下属性：

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| query | 指定要从 SAP HANA 实例读取数据的 SQL 查询。 | SQL 查询。 | 是 |


#### <a name="example"></a>示例


```json
{
    "name": "CopySapHanaToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "<SQL Query for HANA>"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "SapHanaDataset"
            }],
            "outputs": [{
                "name": "AzureBlobDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "SapHanaToBlob"
        }],
        "start": "2017-03-01T18:00:00",
        "end": "2017-03-01T19:00:00"
    }
}
```

有关详细信息，请参阅 [SAP HANA 连接器](data-factory-sap-hana-connector.md#copy-activity-properties)一文。


## <a name="sql-server"></a>SQL Server

### <a name="linked-service"></a>链接服务
创建 **OnPremisesSqlServer** 类型的链接服务，将本地 SQL Server 数据库链接到数据工厂。 下表提供了有关特定于本地 SQL Server 链接服务的 JSON 元素的描述。

下表提供了有关特定于 SQL Server 链接服务的 JSON 元素的描述。

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| type |类型属性应设置为：**OnPremisesSqlServer**。 |是 |
| connectionString |指定使用 SQL 身份验证或 Windows 身份验证连接到本地 SQL Server 数据库时所需的 connectionString 信息。 |是 |
| gatewayName |网关的名称 - 数据工厂服务应使用此网关连接到本地 SQL Server 数据库。 |是 |
| username |如果使用的是 Windows 身份验证，请指定用户名。 示例：**域名\\用户名**。 |否 |
| password |指定为用户名指定的用户帐户的密码。 |否 |

可使用 **New-AzureRmDataFactoryEncryptValue** cmdlet 加密凭据，并在连接字符串中使用凭据，如下例所示（**EncryptedCredential** 属性）：  

```json
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```


#### <a name="example-json-for-using-sql-authentication"></a>示例：用于实现 SQL 身份验证的 JSON

```json
{
    "name": "MyOnPremisesSQLDB",
    "properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=False;User ID=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```
#### <a name="example-json-for-using-windows-authentication"></a>示例：用于实现 Windows 身份验证的 JSON

如果指定了用户名和密码，网关会使用用户名和密码模拟指定的用户帐户来连接到本地 SQL Server 数据库。 否则，网关使用网关（其启动帐户）的安全上下文直接连接到 SQL Server。

```json
{
    "Name": " MyOnPremisesSQLDB",
    "Properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "ConnectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=True;",
            "username": "<domain\\username>",
            "password": "<password>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

有关详细信息，请参阅 [SQL Server 连接器](data-factory-sqlserver-connector.md#linked-service-properties)一文。 

### <a name="dataset"></a>数据集
要定义 SQL Server 数据集，请将数据集的**类型**设置为 **SqlServerTable**，并在 **typeProperties** 节中指定以下属性： 

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| tableName |链接服务所引用的 SQL Server 数据库实例中的表或视图的名称。 |是 |

#### <a name="example"></a>示例
```json
{
    "name": "SqlServerInput",
    "properties": {
        "type": "SqlServerTable",
        "linkedServiceName": "SqlServerLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

有关详细信息，请参阅 [SQL Server 连接器](data-factory-sqlserver-connector.md#dataset-properties)一文。 

### <a name="sql-source-in-copy-activity"></a>复制活动中的 SQL 源
如果要从 SQL Server 数据库复制数据，请将复制活动的**源类型**设置为 **SqlSource**，并在 **source** 节中指定以下属性：


| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| sqlReaderQuery |使用自定义查询读取数据。 |SQL 查询字符串。 例如： `select * from MyTable`。 可引用输入数据集所引用的数据库中的多个表。 如果未指定，执行的 SQL 语句为：select from MyTable。 |否 |
| sqlReaderStoredProcedureName |从源表读取数据的存储过程的名称。 |存储过程的名称。 |否 |
| storedProcedureParameters |存储过程的参数。 |名称/值对。 参数的名称和大小写必须与存储过程参数的名称和大小写匹配。 |否 |

如果为 SqlSource 指定 **sqlReaderQuery**，则复制活动针对 SQL Server 数据库源运行此查询以获取数据。

此外，也可以通过指定 **sqlReaderStoredProcedureName** 和 **storedProcedureParameters** 来指定存储过程（如果存储过程使用参数）。

如果不指定 sqlReaderQuery 或 sqlReaderStoredProcedureName，则使用在结构部分中定义的列来生成针对 SQL Server 数据库运行的 select 查询。 如果数据集定义不具备该结构，则从表中选择所有列。

> [!NOTE]
> 使用 **sqlReaderStoredProcedureName** 时，仍需指定数据集 JSON 中 **tableName** 属性的值。 但不会对此表执行任何验证。


#### <a name="example"></a>示例
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "SqlServertoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": " SqlServerInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

在本例中，为 SqlSource 指定了 **sqlReaderQuery**。 复制活动针对 SQL Server 数据库源运行此查询以获取数据。 此外，也可以通过指定 **sqlReaderStoredProcedureName** 和 **storedProcedureParameters** 来指定存储过程（如果存储过程使用参数）。 SqlReaderQuery 可以引用输入数据集所引用的数据库中的多个表。 这不仅局限于设置为数据集的 tableName typeProperty 的表。

如果不指定 sqlReaderQuery 或 sqlReaderStoredProcedureName，则使用在结构部分中定义的列来生成针对 SQL Server 数据库运行的 select 查询。 如果数据集定义不具备该结构，则从表中选择所有列。

有关详细信息，请参阅 [SQL Server 连接器](data-factory-sqlserver-connector.md#copy-activity-properties)一文。 

### <a name="sql-sink-in-copy-activity"></a>复制活动中的 SQL 接收器
如果要将数据复制到 SQL Server 数据库，请将复制活动的**接收器类型**设置为 **SqlSink**，并在 **sink** 节中指定以下属性：

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| writeBatchTimeout |超时之前等待批插入操作完成时的等待时间。 |timespan<br/><br/> 示例：“00:30:00”（30 分钟）。 |否 |
| writeBatchSize |缓冲区大小达到 writeBatchSize 时会数据插入 SQL 表。 |整数（行数） |否（默认值：10000） |
| sqlWriterCleanupScript |指定复制活动要执行的查询，以便清除特定切片的数据。 有关详细信息，请参阅[重复性](#repeatability-during-copy)部分。 |查询语句。 |否 |
| sliceIdentifierColumnName |指定复制活动要使用自动生成的切片标识符进行填充的列名，该标识符用于重新运行时清除特定切片的数据。 有关详细信息，请参阅[重复性](#repeatability-during-copy)部分。 |数据类型为 binary(32) 的列的列名。 |否 |
| sqlWriterStoredProcedureName |在目标表中更新/插入数据的存储过程的名称。 |存储过程的名称。 |否 |
| storedProcedureParameters |存储过程的参数。 |名称/值对。 参数的名称和大小写必须与存储过程参数的名称和大小写匹配。 |否 |
| sqlWriterTableType |指定要在存储过程中使用的表类型名称。 通过复制活动，使移动数据在具备此表类型的临时表中可用。 然后，存储过程代码可合并复制数据和现有数据。 |表类型名称。 |否 |

#### <a name="example"></a>示例
管道包含配置为使用输入和输出数据集、且计划每小时运行一次的复制活动。 在管道 JSON 定义中，**源**类型设置为 **BlobSource**，**接收器**类型设置为 **SqlSink**。

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQL",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": " SqlServerOutput "
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource",
                    "blobColumnSeparators": ","
                },
                "sink": {
                    "type": "SqlSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

有关详细信息，请参阅 [SQL Server 连接器](data-factory-sqlserver-connector.md#copy-activity-properties)一文。 

## <a name="sybase"></a>Sybase

### <a name="linked-service"></a>链接服务
要定义 Sybase 链接服务，请将链接服务的**类型**设置为 **OnPremisesSybase**，并在 **typeProperties** 节中指定以下属性：  

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| server |Sybase 服务器的名称。 |是 |
| database |Sybase 数据库的名称。 |是 |
| schema |数据库中架构的名称。 |否 |
| authenticationType |用于连接 Sybase 数据库的身份验证类型。 可能的值为：Anonymous、Basic 和 Windows。 |是 |
| username |如果使用基本或 Windows 身份验证，请指定用户名。 |否 |
| password |指定为用户名指定的用户帐户的密码。 |否 |
| gatewayName |网关的名称 - 数据工厂服务应使用此网关连接到本地 Sybase 数据库。 |是 |

#### <a name="example"></a>示例
```json
{
    "name": "OnPremSybaseLinkedService",
    "properties": {
        "type": "OnPremisesSybase",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```

有关详细信息，请参阅 [Sybase 连接器](data-factory-onprem-sybase-connector.md#linked-service-properties)一文。 

### <a name="dataset"></a>数据集
要定义 Sybase 数据集，请将数据集的**类型**设置为 **RelationalTable**，并在 **typeProperties** 节中指定以下属性： 

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| tableName |链接服务指代的 Sybase 数据库实例中表的名称。 |否（如果指定了 **RelationalSource** 的**query**） |

#### <a name="example"></a>示例

```json
{
    "name": "SybaseDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremSybaseLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

有关详细信息，请参阅 [Sybase 连接器](data-factory-onprem-sybase-connector.md#dataset-properties)一文。 

### <a name="relational-source-in-copy-activity"></a>复制活动中的关系源
如果要从 Sybase 数据库复制数据，请将复制活动的**源类型**设置为 **RelationalSource**，并在 **source** 节中指定以下属性：


| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| query |使用自定义查询读取数据。 |SQL 查询字符串。 例如： `select * from MyTable`。 |否（如果指定了**数据集**的 **tableName**） |

#### <a name="example"></a>示例

```json
{
    "name": "CopySybaseToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "select * from DBA.Orders"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "inputs": [{
                "name": "SybaseDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobSybaseDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "SybaseToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

有关详细信息，请参阅 [Sybase 连接器](data-factory-onprem-sybase-connector.md#copy-activity-properties)一文。

## <a name="teradata"></a>Teradata

### <a name="linked-service"></a>链接服务
要定义 Teradata 链接服务，请将链接服务的**类型**设置为 **OnPremisesTeradata**，并在 **typeProperties** 节中指定以下属性：  

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| server |Teradata 服务器的名称。 |是 |
| authenticationType |用于连接 Teradata 数据库的身份验证类型。 可能的值为：Anonymous、Basic 和 Windows。 |是 |
| username |如果使用基本或 Windows 身份验证，请指定用户名。 |否 |
| password |指定为用户名指定的用户帐户的密码。 |否 |
| gatewayName |网关的名称 - 数据工厂服务应使用此网关连接到本地 Teradata 数据库。 |是 |

#### <a name="example"></a>示例
```json
{
    "name": "OnPremTeradataLinkedService",
    "properties": {
        "type": "OnPremisesTeradata",
        "typeProperties": {
            "server": "<server>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```

有关详细信息，请参阅 [Teradata 连接器](data-factory-onprem-teradata-connector.md#linked-service-properties)一文。

### <a name="dataset"></a>数据集
要定义 Teradata Blob 数据集，请将数据集的**类型**设置为 **RelationalTable**。 目前，Teradata 数据集没有支持的属性类型。 

#### <a name="example"></a>示例
```json
{
    "name": "TeradataDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremTeradataLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

有关详细信息，请参阅 [Teradata 连接器](data-factory-onprem-teradata-connector.md#dataset-properties)一文。

### <a name="relational-source-in-copy-activity"></a>复制活动中的关系源
如果要从 Teradata 数据库复制数据，请将复制活动的**源类型**设置为 **RelationalSource**，并在 **source** 节中指定以下属性：

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| query |使用自定义查询读取数据。 |SQL 查询字符串。 例如： `select * from MyTable`。 |是 |

#### <a name="example"></a>示例

```json
{
    "name": "CopyTeradataToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', SliceStart, SliceEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "TeradataDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobTeradataDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "TeradataToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "isPaused": false
    }
}
```

有关详细信息，请参阅 [Teradata 连接器](data-factory-onprem-teradata-connector.md#copy-activity-properties)一文。

## <a name="cassandra"></a>Cassandra


### <a name="linked-service"></a>链接服务
要定义 Cassandra 链接服务，请将链接服务的**类型**设置为 **OnPremisesCassandra**，并在 **typeProperties** 节中指定以下属性：  

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| 主机 |Cassandra 服务器的一个或多个 IP 地址或主机名。<br/><br/>指定以逗号分隔的 IP 地址或主机名列表，以同时连接到所有服务器。 |是 |
| 端口 |Cassandra 服务器用来侦听客户端连接的 TCP 端口。 |否，默认值：9042 |
| authenticationType |Basic 或 Anonymous |是 |
| username |为用户帐户指定用户名。 |是（如果 authenticationType 设置为 Basic）。 |
| password |指定用户帐户的密码。 |是（如果 authenticationType 设置为 Basic）。 |
| gatewayName |用于连接到本地 Cassandra 数据库的网关的名称。 |是 |
| encryptedCredential |网关加密的凭据。 |否 |

#### <a name="example"></a>示例

```json
{
    "name": "CassandraLinkedService",
    "properties": {
        "type": "OnPremisesCassandra",
        "typeProperties": {
            "authenticationType": "Basic",
            "host": "<cassandra server name or IP address>",
            "port": 9042,
            "username": "user",
            "password": "password",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

有关详细信息，请参阅 [Cassandra 连接器](data-factory-onprem-cassandra-connector.md#linked-service-properties)一文。 

### <a name="dataset"></a>数据集
要定义 Cassandra 数据集，请将数据集的**类型**设置为 **CassandraTable**，并在 **typeProperties** 节中指定以下属性： 

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| keyspace |Cassandra 数据库中密钥空间或架构的名称。 |是（如果未定义 **CassandraSource** 的**查询**）。 |
| tableName |Cassandra 数据库中表的名称。 |是（如果未定义 **CassandraSource** 的**查询**）。 |

#### <a name="example"></a>示例

```json
{
    "name": "CassandraInput",
    "properties": {
        "linkedServiceName": "CassandraLinkedService",
        "type": "CassandraTable",
        "typeProperties": {
            "tableName": "mytable",
            "keySpace": "<key space>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

有关详细信息，请参阅 [Cassandra 连接器](data-factory-onprem-cassandra-connector.md#dataset-properties)一文。 

### <a name="cassandra-source-in-copy-activity"></a>复制活动中的 Cassandra 源
如果要从 Cassandra 复制数据，请将复制活动的**源类型**设置为 **CassandraSource**，并在 **source** 节中指定以下属性：

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| query |使用自定义查询读取数据。 |SQL-92 查询或 CQL 查询。 请参阅 [CQL reference](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html)（CQL 参考）。 <br/><br/>使用 SQL 查询时，请指定 **keyspace name.table name** 来表示要查询的表。 |否（如果定义了数据集上的 tableName 和 keyspace）。 |
| consistencyLevel |一致性级别指定在将数据返回到客户端应用程序之前必须响应读取请求的副本的数量。 Cassandra 会检查指定数量的副本，以使数据满足读取请求。 |ONE, TWO, THREE, QUORUM, ALL, LOCAL_QUORUM, EACH_QUORUM, LOCAL_ONE。 有关详细信息，请参阅 [Configuring data consistency](http://docs.datastax.com/en//cassandra/2.0/cassandra/dml/dml_config_consistency_c.html)（配置数据一致性）。 |不会。 默认值为 ONE。 |

#### <a name="example"></a>示例
  
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "CassandraToAzureBlob",
            "description": "Copy from Cassandra to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "CassandraInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "CassandraSource",
                    "query": "select id, firstname, lastname from mykeyspace.mytable"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

有关详细信息，请参阅 [Cassandra 连接器](data-factory-onprem-cassandra-connector.md#copy-activity-properties)一文。

## <a name="mongodb"></a>MongoDB

### <a name="linked-service"></a>链接服务
要定义 MongoDB 链接服务，请将链接服务的**类型**设置为 **OnPremisesMongoDB**，并在 **typeProperties** 节中指定以下属性：  

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| server |MongoDB 服务器的 IP 地址或主机名。 |是 |
| 端口 |MongoDB 服务器用于侦听客户端连接的 TCP 端口。 |（可选）默认值：27017 |
| authenticationType |Basic 或 Anonymous。 |是 |
| username |用于访问 MongoDB 的用户帐户。 |是（如果使用基本身份验证）。 |
| password |用户密码。 |是（如果使用基本身份验证）。 |
| authSource |要用于检查身份验证凭据的 MongoDB 数据库名称。 |可选（如果使用基本身份验证）。 默认值：使用管理员帐户和使用 databaseName 属性指定的数据库。 |
| databaseName |要访问的 MongoDB 数据库名称。 |是 |
| gatewayName |用于访问数据存储的网关名称。 |是 |
| encryptedCredential |经过网关加密的凭据。 |可选 |

#### <a name="example"></a>示例

```json
{
    "name": "OnPremisesMongoDbLinkedService",
    "properties": {
        "type": "OnPremisesMongoDb",
        "typeProperties": {
            "authenticationType": "<Basic or Anonymous>",
            "server": "< The IP address or host name of the MongoDB server >",
            "port": "<The number of the TCP port that the MongoDB server uses to listen for client connections.>",
            "username": "<username>",
            "password": "<password>",
            "authSource": "< The database that you want to use to check your credentials for authentication. >",
            "databaseName": "<database name>",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

有关详细信息，请参阅 [MongoDB 连接器](data-factory-on-premises-mongodb-connector.md#linked-service-properties)一文

### <a name="dataset"></a>数据集
要定义 MongoDB 数据集，请将数据集的**类型**设置为 **MongoDbCollection**，并在 **typeProperties** 节中指定以下属性： 

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| collectionName |MongoDB 数据库中集合的名称。 |是 |

#### <a name="example"></a>示例

```json
{
    "name": "MongoDbInputDataset",
    "properties": {
        "type": "MongoDbCollection",
        "linkedServiceName": "OnPremisesMongoDbLinkedService",
        "typeProperties": {
            "collectionName": "<Collection name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

有关详细信息，请参阅 [MongoDB 连接器](data-factory-on-premises-mongodb-connector.md#dataset-properties)一文

#### <a name="mongodb-source-in-copy-activity"></a>复制活动中的 MongoDB 源
如果要从 MongoDB 复制数据，请将复制活动的**源类型**设置为 **MongoDbSource**，并在 **source** 节中指定以下属性：

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| query |使用自定义查询读取数据。 |SQL-92 查询字符串。 例如： `select * from MyTable`。 |否（如果指定了**数据集**的 **collectionName**） |

#### <a name="example"></a>示例

```json
{
    "name": "CopyMongoDBToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "MongoDbSource",
                    "query": "select * from MyTable"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "MongoDbInputDataset"
            }],
            "outputs": [{
                "name": "AzureBlobOutputDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "MongoDBToAzureBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

有关详细信息，请参阅 [MongoDB 连接器](data-factory-on-premises-mongodb-connector.md#copy-activity-properties)一文

## <a name="amazon-s3"></a>Amazon S3


### <a name="linked-service"></a>链接服务
要定义 Amazon S3 链接服务，请将链接服务的**类型**设置为 **AwsAccessKey**，并在 **typeProperties** 节中指定以下属性：  

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| accessKeyID |机密访问键 ID。 |字符串 |是 |
| secretAccessKey |机密访问键本身。 |加密的机密字符串 |是 |

#### <a name="example"></a>示例
```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AwsAccessKey",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": "<secret access key>"
        }
    }
}
```

有关详细信息，请参阅 [Amazon S3 连接器](data-factory-amazon-simple-storage-service-connector.md#linked-service-properties)一文。

### <a name="dataset"></a>数据集
要定义 Amazon S3 数据集，请将数据集的**类型**设置为 **AmazonS3**，并在 **typeProperties** 节中指定以下属性： 

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| bucketName |S3 存储桶的名称。 |String |是 |
| key |S3 对象键。 |String |否 |
| 前缀 |S3 对象键的前缀。 已选中其键以该前缀开头的对象。 仅当键为空时应用。 |String |否 |
| 版本 |启用 S3 版本控制时 S3 对象的版本。 |String |否 |
| 格式 | 支持以下格式类型：**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat** 和 **ParquetFormat**。 请将格式中的 **type** 属性设置为上述值之一。 有关详细信息，请参阅[文本格式](data-factory-supported-file-and-compression-formats.md#text-format)、[Json 格式](data-factory-supported-file-and-compression-formats.md#json-format)、[Avro 格式](data-factory-supported-file-and-compression-formats.md#avro-format)、[Orc 格式](data-factory-supported-file-and-compression-formats.md#orc-format)和 [Parquet 格式](data-factory-supported-file-and-compression-formats.md#parquet-format)部分。 <br><br> 如果想要在基于文件的存储之间**按原样复制文件**（二进制副本），可以在输入和输出数据集定义中跳过格式节。 |否 | |
| compression | 指定数据的压缩类型和级别。 支持的类型为：**GZip**、**Deflate**、**BZip2** 和 **ZipDeflate**。 支持的级别为：**最佳**和**最快**。 有关详细信息，请参阅 [Azure 数据工厂中的文件和压缩格式](data-factory-supported-file-and-compression-formats.md#compression-support)。 |否 | |


> [!NOTE]
> bucketName + key 指定S3 对象的位置，其中存储桶是 S3 对象的根容器，key 是 S3 对象的完整路径。

#### <a name="example-sample-dataset-with-prefix"></a>示例：包含前缀的示例数据集

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "prefix": "testFolder/test",
            "bucketName": "<S3 bucket name>",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
#### <a name="example-sample-data-set-with-version"></a>示例：示例数据集（包含版本）

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "key": "testFolder/test.orc",
            "bucketName": "<S3 bucket name>",
            "version": "XXXXXXXXXczm0CJajYkHf0_k6LhBmkcL",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

#### <a name="example-dynamic-paths-for-s3"></a>示例：S3 的动态路径
在示例中，对于 Amazon S3 数据集中的 key 和 bucketName 属性，使用固定的值。

```json
"key": "testFolder/test.orc",
"bucketName": "<S3 bucket name>",
```

可以通过使用 SliceStart 等系统变量，让数据工厂在运行时动态计算 key 和 bucketName。

```json
"key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
"bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"
```

也可对 Amazon S3 数据集的前缀属性执行该操作。 有关支持的函数和变量的列表，请参阅[数据工厂的函数和系统变量](data-factory-functions-variables.md)。

有关详细信息，请参阅 [Amazon S3 连接器](data-factory-amazon-simple-storage-service-connector.md#dataset-properties)一文。

### <a name="file-system-source-in-copy-activity"></a>复制活动中的文件系统源
如果要从 Amazon S3 复制数据，请将复制活动的**源类型**设置为 **FileSystemSource**，并在 **source** 节中指定以下属性：


| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| recursive |指定是否以递归方式列出目录下的 S3 对象。 |true/false |否 |


#### <a name="example"></a>示例


```json
{
    "name": "CopyAmazonS3ToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource",
                    "recursive": true
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "AmazonS3InputDataset"
            }],
            "outputs": [{
                "name": "AzureBlobOutputDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "AmazonS3ToBlob"
        }],
        "start": "2016-08-08T18:00:00",
        "end": "2016-08-08T19:00:00"
    }
}
```

有关详细信息，请参阅 [Amazon S3 连接器](data-factory-amazon-simple-storage-service-connector.md#copy-activity-properties)一文。

## <a name="file-system"></a>文件系统


### <a name="linked-service"></a>链接服务
可使用**本地文件服务器**链接服务将本地文件系统链接到 Azure 数据工厂。 下表提供本地文件服务器链接服务特定的 JSON 元素的说明。

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| type |确保类型属性设置为 **OnPremisesFileServer**。 |是 |
| 主机 |指定要复制的文件夹的根路径。 请对字符串中的特殊字符使用转义符“\”。 有关示例，请参阅 [Sample linked service and dataset definitions](#sample-linked-service-and-dataset-definitions)（链接服务和数据集定义示例）。 |是 |
| userid |指定有权访问服务器的用户的 ID。 |否（如果选择 encryptedCredential） |
| password |设置用户的密码 (userid)。 |否（如果选择 encryptedCredential） |
| encryptedCredential |指定可通过运行 New-AzureRmDataFactoryEncryptValue cmdlet 获取的加密凭据。 |否（如果选择在纯文本中指定 userid 和密码） |
| gatewayName |指定网关的名称，数据工厂应将其用于连接到本地文件服务器。 |是 |

#### <a name="sample-folder-path-definitions"></a>示例文件夹路径定义 
| 方案 | 链接服务定义中的主机 | 数据集定义中的 folderPath |
| --- | --- | --- |
| 数据管理网关计算机上的本地文件夹： <br/><br/>示例：D:\\\* 或 D:\folder\subfolder\\* |D:\\\\（适用于数据管理网关 2.0 以及更高版本） <br/><br/> localhost（适用于数据管理网关 2.0 之前的版本） |.\\\\ 或 folder\\\\subfolder（适用于数据管理网关 2.0 以及更高版本） <br/><br/>D:\\\\ 或 D:\\\\folder\\\\subfolder（适用于低于 2.0 的网关版本） |
| 远程共享文件夹： <br/><br/>示例：\\\\myserver\\share\\\* 或 \\\\myserver\\share\\folder\\subfolder\\* |\\\\\\\\myserver\\\\share |.\\\\ 或 folder\\\\subfolder |


#### <a name="example-using-username-and-password-in-plain-text"></a>示例：使用纯文本格式的用户名和密码

```json
{
    "Name": "OnPremisesFileServerLinkedService",
    "properties": {
        "type": "OnPremisesFileServer",
        "typeProperties": {
            "host": "\\\\Contosogame-Asia",
            "userid": "Admin",
            "password": "123456",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-using-encryptedcredential"></a>示例：使用 encryptedcredential

```json
{
    "Name": " OnPremisesFileServerLinkedService ",
    "properties": {
        "type": "OnPremisesFileServer",
        "typeProperties": {
            "host": "D:\\",
            "encryptedCredential": "WFuIGlzIGRpc3Rpbmd1aXNoZWQsIG5vdCBvbmx5IGJ5xxxxxxxxxxxxxxxxx",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

有关详细信息，请参阅[文件系统连接器](data-factory-onprem-file-system-connector.md#linked-service-properties)一文。

### <a name="dataset"></a>数据集
要定义文件系统数据集，请将数据集的**类型**设置为 **FileShare**，并在 **typeProperties** 节中指定以下属性： 

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| folderPath |指定文件夹的子路径。 请对字符串中的特殊字符使用转义符“\”。 有关示例，请参阅 [Sample linked service and dataset definitions](#sample-linked-service-and-dataset-definitions)（链接服务和数据集定义示例）。<br/><br/>可将此属性与 **partitionBy** 相组合，基于切片开始/结束日期时间构成文件夹路径。 |是 |
| fileName |如果希望表引用文件夹中的特定文件，请在 **folderPath** 中指定文件名。 如果没有为此属性指定任何值，表将指向文件夹中的所有文件。<br/><br/>如果没有为输出数据集指定 fileName，生成文件的名称会采用以下格式： <br/><br/>`Data.<Guid>.txt`（示例：Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt） |否 |
| fileFilter |指定在 folderPath 中选择一部分文件而不是所有文件时要使用的筛选器。 <br/><br/>允许的值为：`*`（多个字符）和 `?`（单个字符）。<br/><br/>示例 1："fileFilter": "*.log"<br/>示例 2："fileFilter": 2016-1-?.txt"<br/><br/>注意：fileFilter 适用于 FileShare 输入数据集。 |否 |
| partitionedBy |可使用 partitionedBy 指定时序数据的动态 folderPath/fileName。 例如，针对每小时数据参数化的 folderPath。 |否 |
| 格式 | 支持以下格式类型：**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat** 和 **ParquetFormat**。 请将格式中的 **type** 属性设置为上述值之一。 有关详细信息，请参阅[文本格式](data-factory-supported-file-and-compression-formats.md#text-format)、[Json 格式](data-factory-supported-file-and-compression-formats.md#json-format)、[Avro 格式](data-factory-supported-file-and-compression-formats.md#avro-format)、[Orc 格式](data-factory-supported-file-and-compression-formats.md#orc-format)和 [Parquet 格式](data-factory-supported-file-and-compression-formats.md#parquet-format)部分。 <br><br> 如果想要在基于文件的存储之间**按原样复制文件**（二进制副本），可以在输入和输出数据集定义中跳过格式节。 |否 |
| compression | 指定数据的压缩类型和级别。 支持的类型为：**GZip**、**Deflate**、**BZip2** 和 **ZipDeflate**；支持的级别为：**Optimal** 和 **Fastest**。 请参阅 [Azure 数据工厂中的文件和压缩格式](data-factory-supported-file-and-compression-formats.md#compression-support)。 |否 |

> [!NOTE]
> 不能同时使用 fileName 和 fileFilter。

#### <a name="example"></a>示例

```json
{
    "name": "OnpremisesFileSystemInput",
    "properties": {
        "type": " FileShare",
        "linkedServiceName": " OnPremisesFileServerLinkedService ",
        "typeProperties": {
            "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
            "fileName": "{Hour}.csv",
            "partitionedBy": [{
                "name": "Year",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                        "format": "yyyy"
                }
            }, {
                "name": "Month",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                    "format": "MM"
                }
            }, {
                "name": "Day",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                    "format": "dd"
                }
            }, {
                "name": "Hour",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                    "format": "HH"
                }
            }]
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

有关详细信息，请参阅[文件系统连接器](data-factory-onprem-file-system-connector.md#dataset-properties)一文。

### <a name="file-system-source-in-copy-activity"></a>复制活动中的文件系统源
如果要从文件系统复制数据，请将复制活动的**源类型**设置为 **FileSystemSource**，并在 **source** 节中指定以下属性：

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| recursive |指示是要从子文件夹中以递归方式读取数据，还是只从指定的文件夹中读取数据。 |True、False（默认值） |否 |

#### <a name="example"></a>示例

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2015-06-01T18:00:00",
        "end": "2015-06-01T19:00:00",
        "description": "Pipeline for copy activity",
        "activities": [{
            "name": "OnpremisesFileSystemtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "OnpremisesFileSystemInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
            "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
有关详细信息，请参阅[文件系统连接器](data-factory-onprem-file-system-connector.md#copy-activity-properties)一文。

### <a name="file-system-sink-in-copy-activity"></a>复制活动中的文件系统接收器
如果要将数据复制到文件系统，请将复制活动的**接收器类型**设置为 **FileSystemSink**，并在 **sink** 节中指定以下属性：

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| copyBehavior |源为 BlobSource 或 FileSystem 时，请定义复制行为。 |**PreserveHierarchy：**将文件层次结构保留到目标文件夹中。 也就是说，指向源文件夹的源文件相对路径与指向目标文件夹的目标文件相对路径相同。<br/><br/>**FlattenHierarchy：**源文件夹中的所有文件在目标文件夹的第一个级别中创建。 创建目标文件时，自动生成名称。<br/><br/>**MergeFiles：**将源文件夹的所有文件合并到一个文件中。 如果指定了文件名/blob 名称，则合并的文件名是指定的名称。 否则，它是自动生成的文件名。 |否 |
auto-

#### <a name="example"></a>示例

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2015-06-01T18:00:00",
        "end": "2015-06-01T20:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLtoOnPremisesFile",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSQLInput"
            }],
            "outputs": [{
                "name": "OnpremisesFileSystemOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "FileSystemSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 3,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

有关详细信息，请参阅[文件系统连接器](data-factory-onprem-file-system-connector.md#copy-activity-properties)一文。

## <a name="ftp"></a>FTP

### <a name="linked-service"></a>链接服务
要定义 FTP 链接服务，请将链接服务的**类型**设置为 **FtpServer**，并在 **typeProperties** 节中指定以下属性：  

| 属性 | 说明 | 必选 | 默认 |
| --- | --- | --- | --- |
| 主机 |FTP 服务器的名称或 IP 地址 |是 |&nbsp; |
| authenticationType |指定身份验证类型 |是 |基本、匿名 |
| username |有权访问 FTP 服务器的用户 |否 |&nbsp; |
| password |用户 (username) 的密码 |否 |&nbsp; |
| encryptedCredential |访问 FTP 服务器的加密凭据 |否 |&nbsp; |
| gatewayName |用于连接本地 FTP 服务器的数据管理网关名称 |否 |&nbsp; |
| 端口 |FTP 服务器侦听的端口 |否 |21 |
| enableSsl |指定是否使用 FTP over SSL/TLS 通道 |否 |是 |
| enableServerCertificateValidation |指定使用 FTP over SSL/TLS 通道时，是否启用服务器 SSL 证书验证 |否 |是 |

#### <a name="example-using-anonymous-authentication"></a>示例：使用匿名身份验证

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
            "typeProperties": {
            "authenticationType": "Anonymous",
            "host": "myftpserver.com"
        }
    }
}
```

#### <a name="example-using-username-and-password-in-plain-text-for-basic-authentication"></a>示例：使用纯文本用户名和密码进行基本身份验证

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456"
        }
    }
}
```

#### <a name="example-using-port-enablessl-enableservercertificatevalidation"></a>示例：使用 port、enableSsl、enableServerCertificateValidation

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",    
            "username": "Admin",
            "password": "123456",
            "port": "21",
            "enableSsl": true,
            "enableServerCertificateValidation": true
        }
    }
}
```

#### <a name="example-using-encryptedcredential-for-authentication-and-gateway"></a>示例：将 encryptedCredential 用于身份验证和网关

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "gatewayName": "<onpremgateway>"
        }
      }
}
```

有关详细信息，请参阅 [FTP 连接器](data-factory-ftp-connector.md#linked-service-properties)一文。

### <a name="dataset"></a>数据集
要定义 FTP 数据集，请将数据集的**类型**设置为 **FileShare**，并在 **typeProperties** 节中指定以下属性： 

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| folderPath |文件夹的子路径。 请对字符串中的特殊字符使用转义符“\”。 有关示例，请参阅 [Sample linked service and dataset definitions](#sample-linked-service-and-dataset-definitions)（链接服务和数据集定义示例）。<br/><br/>可将此属性与 **partitionBy** 相组合，基于切片开始/结束日期时间构成文件夹路径。 |是 
| fileName |如果希望表引用文件夹中的特定文件，请在 **folderPath** 中指定文件名。 如果没有为此属性指定任何值，表将指向文件夹中的所有文件。<br/><br/>如果没有为输出数据集指定 fileName，生成的文件的名称会采用以下格式： <br/><br/>Data.<Guid>.txt（示例：Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt） |否 |
| fileFilter |指定在 folderPath 中选择一部分文件而不是所有文件时要使用的筛选器。<br/><br/>允许的值为：`*`（多个字符）和 `?`（单个字符）。<br/><br/>示例 1：`"fileFilter": "*.log"`<br/>示例 2：`"fileFilter": 2016-1-?.txt"`<br/><br/> fileFilter 适用于 FileShare 输入数据集。 HDFS 不支持此属性。 |否 |
| partitionedBy |partitionedBy 可用于指定时序数据的动态 folderPath 和 filename。 例如，folderPath 可针对每小时的数据参数化。 |否 |
| 格式 | 支持以下格式类型：**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat** 和 **ParquetFormat**。 请将格式中的 **type** 属性设置为上述值之一。 有关详细信息，请参阅[文本格式](data-factory-supported-file-and-compression-formats.md#text-format)、[Json 格式](data-factory-supported-file-and-compression-formats.md#json-format)、[Avro 格式](data-factory-supported-file-and-compression-formats.md#avro-format)、[Orc 格式](data-factory-supported-file-and-compression-formats.md#orc-format)和 [Parquet 格式](data-factory-supported-file-and-compression-formats.md#parquet-format)部分。 <br><br> 如果想要在基于文件的存储之间**按原样复制文件**（二进制副本），可以在输入和输出数据集定义中跳过格式节。 |否 |
| compression | 指定数据的压缩类型和级别。 支持的类型为：**GZip**、**Deflate**、**BZip2** 和 **ZipDeflate**；支持的级别为：**Optimal** 和 **Fastest**。 有关详细信息，请参阅 [Azure 数据工厂中的文件和压缩格式](data-factory-supported-file-and-compression-formats.md#compression-support)。 |否 |
| useBinaryTransfer |指定是否使用二进制传输模式。 使用二进制模式时为 true，使用 ASCII 时为 false。 默认值：True。 仅当关联的链接服务类型为 FtpServer 时，才可以使用此属性。 |否 |

> [!NOTE]
> filename 和 fileFilter 不能同时使用。

#### <a name="example"></a>示例

```json
{
    "name": "FTPFileInput",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "FTPLinkedService",
        "typeProperties": {
            "folderPath": "<path to shared folder>",
            "fileName": "test.csv",
            "useBinaryTransfer": true
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

有关详细信息，请参阅 [FTP 连接器](data-factory-ftp-connector.md#dataset-properties)一文。

### <a name="file-system-source-in-copy-activity"></a>复制活动中的文件系统源
如果要从 FTP 服务器复制数据，请将复制活动的**源类型**设置为 **FileSystemSource**，并在 **source** 节中指定以下属性：

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| recursive |指示是要从子文件夹中以递归方式读取数据，还是只从指定的文件夹中读取数据。 |True、False（默认值） |否 |

#### <a name="example"></a>示例

```json
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "FTPToBlobCopy",
            "inputs": [{
                "name": "FtpFileInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2016-08-24T18:00:00",
        "end": "2016-08-24T19:00:00"
    }
}
```

有关详细信息，请参阅 [FTP 连接器](data-factory-ftp-connector.md#copy-activity-properties)一文。


## <a name="hdfs"></a>HDFS

### <a name="linked-service"></a>链接服务
要定义 HDFS 链接服务，请将链接服务的**类型**设置为 **Hdfs**，并在 **typeProperties** 节中指定以下属性：  

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| type |类型属性必须设置为：**Hdfs** |是 |
| URL |HDFS 的 URL |是 |
| authenticationType |匿名或 Windows。 <br><br> 若要对 HDFS 连接器使用 **Kerberos 身份验证**，请参阅[此部分](#use-kerberos-authentication-for-hdfs-connector)相应地设置本地环境。 |是 |
| userName |Windows 身份验证的用户名。 |是（对于 Windows 身份验证） |
| password |Windows 身份验证的密码。 |是（对于 Windows 身份验证） |
| gatewayName |数据工厂服务用于连接到 HDFS 的网关的名称。 |是 |
| encryptedCredential |访问凭据的 [New-AzureRMDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) 输出。 |否 |

#### <a name="example-using-anonymous-authentication"></a>示例：使用匿名身份验证

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "authenticationType": "Anonymous",
            "userName": "hadoop",
            "url": "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-using-windows-authentication"></a>示例：使用 Windows 身份验证

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "authenticationType": "Windows",
            "userName": "Administrator",
            "password": "password",
            "url": "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

有关详细信息，请参阅 [HDFS 连接器](#data-factory-hdfs-connector.md#linked-service-properties)一文。 

### <a name="dataset"></a>数据集
要定义 HDFS 数据集，请将数据集的**类型**设置为 **FileShare**，并在 **typeProperties** 节中指定以下属性： 

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| folderPath |文件夹路径。 示例：`myfolder`<br/><br/>请对字符串中的特殊字符使用转义符“\”。 例如：对于 folder\subfolder，请指定 folder\\\\subfolder；对于 d:\samplefolder，请指定 d:\\\\samplefolder。<br/><br/>可将此属性与 **partitionBy** 相组合，基于切片开始/结束日期时间构成文件夹路径。 |是 |
| fileName |如果希望表引用文件夹中的特定文件，请在 **folderPath** 中指定文件名。 如果没有为此属性指定任何值，表将指向文件夹中的所有文件。<br/><br/>如果没有为输出数据集指定 fileName，生成的文件的名称会采用以下格式： <br/><br/>Data<Guid>.txt（例如：Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |否 |
| partitionedBy |partitionedBy 可用于指定时序数据的动态 folderPath 和 filename。 示例：folderPath 可针对每小时的数据参数化。 |否 |
| 格式 | 支持以下格式类型：**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat** 和 **ParquetFormat**。 请将格式中的 **type** 属性设置为上述值之一。 有关详细信息，请参阅[文本格式](data-factory-supported-file-and-compression-formats.md#text-format)、[Json 格式](data-factory-supported-file-and-compression-formats.md#json-format)、[Avro 格式](data-factory-supported-file-and-compression-formats.md#avro-format)、[Orc 格式](data-factory-supported-file-and-compression-formats.md#orc-format)和 [Parquet 格式](data-factory-supported-file-and-compression-formats.md#parquet-format)部分。 <br><br> 如果想要在基于文件的存储之间**按原样复制文件**（二进制副本），可以在输入和输出数据集定义中跳过格式节。 |否 |
| compression | 指定数据的压缩类型和级别。 支持的类型为：**GZip**、**Deflate**、**BZip2** 和 **ZipDeflate**。 支持的级别为：**最佳**和**最快**。 有关详细信息，请参阅 [Azure 数据工厂中的文件和压缩格式](data-factory-supported-file-and-compression-formats.md#compression-support)。 |否 |

> [!NOTE]
> filename 和 fileFilter 不能同时使用。

#### <a name="example"></a>示例

```json
{
    "name": "InputDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "HDFSLinkedService",
        "typeProperties": {
            "folderPath": "DataTransfer/UnitTest/"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

有关详细信息，请参阅 [HDFS 连接器](#data-factory-hdfs-connector.md#dataset-properties)一文。 

### <a name="file-system-source-in-copy-activity"></a>复制活动中的文件系统源
如果要从 HDFS 复制数据，请将复制活动的**源类型**设置为 **FileSystemSource**，并在 **source** 节中指定以下属性：

**FileSystemSource** 支持以下属性：

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| recursive |指示是要从子文件夹中以递归方式读取数据，还是只从指定的文件夹中读取数据。 |True、False（默认值） |否 |

#### <a name="example"></a>示例

```json
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "HdfsToBlobCopy",
            "inputs": [{
                "name": "InputDataset"
            }],
            "outputs": [{
                "name": "OutputDataset"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

有关详细信息，请参阅 [HDFS 连接器](#data-factory-hdfs-connector.md#copy-activity-properties)一文。

## <a name="sftp"></a>SFTP


### <a name="linked-service"></a>链接服务
要定义 SFTP 链接服务，请将链接服务的**类型**设置为 **Sftp**，并在 **typeProperties** 节中指定以下属性：  

| 属性 | 说明 | 必选 |
| --- | --- | --- | --- |
| 主机 | SFTP 服务器的名称或 IP 地址。 |是 |
| 端口 |SFTP 服务器侦听的端口。 默认值为 21 |否 |
| authenticationType |指定身份验证类型。 允许的值：**Basic**、**SshPublicKey**。 <br><br> 有关其他属性和 JSON 示例，请分别参阅[使用基本身份验证](#using-basic-authentication)和[使用 SSH 公钥身份验证](#using-ssh-public-key-authentication)部分。 |是 |
| skipHostKeyValidation | 指定是否要跳过主机密钥验证。 | 不会。 默认值：false |
| hostKeyFingerprint | 指定主机密钥的指纹。 | `skipHostKeyValidation` 设置为 false 时表示 Yes。  |
| gatewayName |用于连接本地 SFTP 服务器的数据管理网关的名称。 | 如果从本地 SFTP 服务器复制数据，则值为 Yes。 |
| encryptedCredential | 访问 SFTP 服务器的加密凭据 在复制向导或 ClickOnce 弹出对话框中指定基本身份验证（用户名 + 密码）或 SshPublicKey 身份验证（用户名 + 私钥路径或内容）时自动生成。 | 不会。 仅当从本地 SFTP 服务器复制数据时才适用。 |

#### <a name="example-using-basic-authentication"></a>示例：使用基本身份验证

要使用基本身份验证，请将 `authenticationType` 设置为 `Basic`，并指定除上一部分所述 SFTP 连接器泛型属性以外的下列属性：

| 属性 | 说明 | 必选 |
| --- | --- | --- | --- |
| username | 有权访问 SFTP 服务器的用户。 |是 |
| password | 用户 (username) 的密码。 | 是 |

```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<SFTP server name or IP address>",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "password": "xxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-basic-authentication-with-encrypted-credential"></a>示例：使用加密的凭据进行基本身份验证**

```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<FTP server name or IP address>",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="using-ssh-public-key-authentication"></a>使用 SSH 公钥身份验证：**

要使用基本身份验证，请将 `authenticationType` 设置为 `SshPublicKey`，并指定除上一部分所述 SFTP 连接器泛型属性以外的下列属性：

| 属性 | 说明 | 必选 |
| --- | --- | --- | --- |
| username |有权访问 SFTP 服务器的用户 |是 |
| privateKeyPath | 指定网关可以访问的私钥文件的绝对路径。 | 指定 `privateKeyPath` 或 `privateKeyContent`。 <br><br> 仅当从本地 SFTP 服务器复制数据时才适用。 |
| privateKeyContent | 私钥内容的序列化字符串。 复制向导可以读取私钥文件，并自动提取私钥的内容。 如果使用其他任何工具/SDK，请改用 privateKeyPath 属性。 | 指定 `privateKeyPath` 或 `privateKeyContent`。 |
| passPhrase | 如果密钥文件受通行短语保护，请指定用于解密私钥的通行短语/密码。 | 如果私钥文件受通行短语的保护，则为 Yes。 |

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyPath",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<FTP server name or IP address>",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyPath": "D:\\privatekey_openssh",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true,
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-sshpublickey-authentication-using-private-key-content"></a>示例：使用私钥内容进行 SshPublicKey 身份验证**

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyContent",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver.westus.cloudapp.azure.com",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyContent": "<base64 string of the private key content>",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true
        }
    }
}
```

有关详细信息，请参阅 [SFTP 连接器](data-factory-sftp-connector.md#linked-service-properties)一文。 

### <a name="dataset"></a>数据集
要定义 SFTP 数据集，请将数据集的**类型**设置为 **FileShare**，并在 **typeProperties** 节中指定以下属性： 

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| folderPath |文件夹的子路径。 请对字符串中的特殊字符使用转义符“\”。 有关示例，请参阅 [Sample linked service and dataset definitions](#sample-linked-service-and-dataset-definitions)（链接服务和数据集定义示例）。<br/><br/>可将此属性与 **partitionBy** 相组合，基于切片开始/结束日期时间构成文件夹路径。 |是 |
| fileName |如果希望表引用文件夹中的特定文件，请在 **folderPath** 中指定文件名。 如果没有为此属性指定任何值，表将指向文件夹中的所有文件。<br/><br/>如果没有为输出数据集指定 fileName，生成的文件的名称会采用以下格式： <br/><br/>Data.<Guid>.txt（示例：Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt） |否 |
| fileFilter |指定在 folderPath 中选择一部分文件而不是所有文件时要使用的筛选器。<br/><br/>允许的值为：`*`（多个字符）和 `?`（单个字符）。<br/><br/>示例 1：`"fileFilter": "*.log"`<br/>示例 2：`"fileFilter": 2016-1-?.txt"`<br/><br/> fileFilter 适用于 FileShare 输入数据集。 HDFS 不支持此属性。 |否 |
| partitionedBy |partitionedBy 可用于指定时序数据的动态 folderPath 和 filename。 例如，folderPath 可针对每小时的数据参数化。 |否 |
| 格式 | 支持以下格式类型：**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat** 和 **ParquetFormat**。 请将格式中的 **type** 属性设置为上述值之一。 有关详细信息，请参阅[文本格式](data-factory-supported-file-and-compression-formats.md#text-format)、[Json 格式](data-factory-supported-file-and-compression-formats.md#json-format)、[Avro 格式](data-factory-supported-file-and-compression-formats.md#avro-format)、[Orc 格式](data-factory-supported-file-and-compression-formats.md#orc-format)和 [Parquet 格式](data-factory-supported-file-and-compression-formats.md#parquet-format)部分。 <br><br> 如果想要在基于文件的存储之间**按原样复制文件**（二进制副本），可以在输入和输出数据集定义中跳过格式节。 |否 |
| compression | 指定数据的压缩类型和级别。 支持的类型为：**GZip**、**Deflate**、**BZip2** 和 **ZipDeflate**。 支持的级别为：**最佳**和**最快**。 有关详细信息，请参阅 [Azure 数据工厂中的文件和压缩格式](data-factory-supported-file-and-compression-formats.md#compression-support)。 |否 |
| useBinaryTransfer |指定是否使用二进制传输模式。 使用二进制模式时为 true，使用 ASCII 时为 false。 默认值：True。 仅当关联的链接服务类型为 FtpServer 时，才可以使用此属性。 |否 |

> [!NOTE]
> filename 和 fileFilter 不能同时使用。

#### <a name="example"></a>示例

```json
{
    "name": "SFTPFileInput",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "SftpLinkedService",
        "typeProperties": {
            "folderPath": "<path to shared folder>",
            "fileName": "test.csv"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

有关详细信息，请参阅 [SFTP 连接器](data-factory-sftp-connector.md#dataset-properties)一文。 

### <a name="file-system-source-in-copy-activity"></a>复制活动中的文件系统源
如果要从 SFTP 源复制数据，请将复制活动的**源类型**设置为 **FileSystemSource**，并在 **source** 节中指定以下属性：

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| recursive |指示是要从子文件夹中以递归方式读取数据，还是只从指定的文件夹中读取数据。 |True、False（默认值） |否 |



#### <a name="example"></a>示例

```json
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "SFTPToBlobCopy",
            "inputs": [{
                "name": "SFTPFileInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2017-02-20T18:00:00",
        "end": "2017-02-20T19:00:00"
    }
}
```

有关详细信息，请参阅 [SFTP 连接器](data-factory-sftp-connector.md#copy-activity-properties)一文。


## <a name="http"></a>HTTP

### <a name="linked-service"></a>链接服务
要定义 HTTP 链接服务，请将链接服务的**类型**设置为 **Http**，并在 **typeProperties** 节中指定以下属性：  

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| url | Web 服务器的基 URL | 是 |
| authenticationType | 指定身份验证类型。 允许的值为：**Anonymous**、**Basic**、**Digest**、**Windows**、**ClientCertificate**。 <br><br> 有关这些身份验证类型的其他属性和 JSON 示例，请分别参阅此表格下面的部分。 | 是 |
| enableServerCertificateValidation | 指定当源为 HTTPS Web 服务器时，是否启用 SSL 证书验证 | 不启用，默认值为 true |
| gatewayName | 用于连接本地 HTTP 源的数据管理网关的名称 | 如果从本地 HTTP 源复制数据，则值为 Yes。 |
| encryptedCredential | 用于访问 HTTP 终结点的已加密凭据。 在复制向导或 ClickOnce 弹出对话框中配置身份验证信息时自动生成。 | 不会。 仅当从本地 HTTP 服务器复制数据时才适用。 |

#### <a name="example-using-basic-digest-or-windows-authentication"></a>示例：使用基本、摘要或 Windows 身份验证
将 `authenticationType` 设置为 `Basic`、`Digest` 或 `Windows`，并指定除上述 HTTP 连接器泛型属性以外的下列属性：

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| username | 用于访问 HTTP 终结点的用户名。 | 是 |
| password | 用户 (username) 的密码。 | 是 |

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "Http",
        "typeProperties": {
            "authenticationType": "basic",
            "url": "https://en.wikipedia.org/wiki/",
            "userName": "user name",
            "password": "password"
        }
    }
}
```

#### <a name="example-using-clientcertificate-authentication"></a>示例：使用 ClientCertificate 身份验证

要使用基本身份验证，请将 `authenticationType` 设置为 `ClientCertificate`，并指定除上述 HTTP 连接器泛型属性以外的下列属性：

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| embeddedCertData | 个人信息交换 (PFX) 文件二进制数据的 Base64 编码内容。 | 指定 `embeddedCertData` 或 `certThumbprint`。 |
| certThumbprint | 在网关计算机的证书存储中安装的证书的指纹。 仅当从本地 HTTP 源复制数据时才适用。 | 指定 `embeddedCertData` 或 `certThumbprint`。 |
| password | 与证书关联的密码 | 否 |

如果使用 `certThumbprint` 进行身份验证并在本地计算机的个人存储中安装了证书，则需要授予对网关服务的读取权限：

1. 启动 Microsoft 管理控制台 (MMC)。 添加面向“本地计算机”的“证书”管理单元。
2. 展开“证书”、“个人”，并单击“证书”。
3. 右键单击个人存储中的证书，并选择“所有任务”->“管理私钥...”
3. 在“安全性”选项卡上，添加运行数据管理网关主机服务的、对证书具有读取访问权限的用户帐户。  

**示例：使用客户端证书：**此链接服务将数据工厂链接到本地 HTTP Web 服务器。 它使用装有数据管理网关的计算机上安装的客户端证书。

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "Http",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "certThumbprint": "thumbprint of certificate",
            "gatewayName": "gateway name"
        }
    }
}
```

#### <a name="example-using-client-certificate-in-a-file"></a>示例：使用文件中的客户端证书
此链接服务将数据工厂链接到本地 HTTP Web 服务器。 它使用装有数据管理网关的计算机上的客户端证书文件。

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "Http",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "embeddedCertData": "base64 encoded cert data",
            "password": "password of cert"
        }
    }
}
```

有关详细信息，请参阅 [HTTP 连接器](data-factory-http-connector.md#linked-service-properties)一文。

### <a name="dataset"></a>数据集
要定义 HTTP 数据集，请将数据集的**类型**设置为 **Http**，并在 **typeProperties** 节中指定以下属性： 

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| relativeUrl | 包含数据的资源的相对 URL。 未指定路径时，仅使用链接服务定义中指定的 URL。 <br><br> 若要构造动态 URL，可以使用[数据工厂函数和系统变量](data-factory-functions-variables.md)，例如：`"relativeUrl": "$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)"`。 | 否 |
| requestMethod | Http 方法。 允许的值为 **GET** 或 **POST**。 | 不会。 默认值为 `GET`。 |
| additionalHeaders | 附加的 HTTP 请求标头。 | 否 |
| requestBody | HTTP 请求的正文。 | 否 |
| 格式 | 如果只想要**从 HTTP 终结点按原样检索数据**而不分析它，请跳过此格式设置。 <br><br> 如果想要在复制期间分析 HTTP 响应内容，下面是支持的格式类型：**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat** 和 **ParquetFormat**。 有关详细信息，请参阅[文本格式](data-factory-supported-file-and-compression-formats.md#text-format)、[Json 格式](data-factory-supported-file-and-compression-formats.md#json-format)、[Avro 格式](data-factory-supported-file-and-compression-formats.md#avro-format)、[Orc 格式](data-factory-supported-file-and-compression-formats.md#orc-format)和 [Parquet 格式](data-factory-supported-file-and-compression-formats.md#parquet-format)部分。 |否 |
| compression | 指定数据的压缩类型和级别。 支持的类型为：**GZip**、**Deflate**、**BZip2** 和 **ZipDeflate**。 支持的级别为：**最佳**和**最快**。 有关详细信息，请参阅 [Azure 数据工厂中的文件和压缩格式](data-factory-supported-file-and-compression-formats.md#compression-support)。 |否 |

#### <a name="example-using-the-get-default-method"></a>示例：使用 GET（默认）方法

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "XXX/test.xml",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

#### <a name="example-using-the-post-method"></a>示例：使用 POST 方法

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "/XXX/test.xml",
            "requestMethod": "Post",
            "requestBody": "body for POST HTTP request"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```
有关详细信息，请参阅 [HTTP 连接器](data-factory-http-connector.md#dataset-properties)一文。

### <a name="http-source-in-copy-activity"></a>复制活动中的 HTTP 源
如果要从 HTTP 源复制数据，请将复制活动的**源类型**设置为 **HttpSource**，并在 **source** 节中指定以下属性：

| 属性 | 说明 | 必选 |
| -------- | ----------- | -------- |
| httpRequestTimeout | 用于获取响应的 HTTP 请求的超时 (TimeSpan)。 这是获取响应而不是读取响应数据的超时。 | 不会。 默认值：00:01:40 |


#### <a name="example"></a>示例

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "HttpSourceToAzureBlob",
            "description": "Copy from an HTTP source to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "HttpSourceDataInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "HttpSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

有关详细信息，请参阅 [HTTP 连接器](data-factory-http-connector.md#copy-activity-properties)一文。

## <a name="odata"></a>OData

### <a name="linked-service"></a>链接服务
要定义 OData 链接服务，请将链接服务的**类型**设置为 **OData**，并在 **typeProperties** 节中指定以下属性：  

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| url |OData 服务的 URL。 |是 |
| authenticationType |用于连接 OData 源的身份验证类型。 <br/><br/> 对于云 OData，可能的值为 Anonymous、Basic 和 OAuth（请注意：Azure 数据工厂目前仅支持基于 Azure Active Directory 的 OAuth）。 <br/><br/> 对于本地 OData，可能的值为 Anonymous、Basic 和 Windows。 |是 |
| username |如果使用基本身份验证，请指定用户名。 |是（仅在使用基本身份验证时适用） |
| password |指定为用户名指定的用户帐户的密码。 |是（仅在使用基本身份验证时适用） |
| authorizedCredential |如果使用 OAuth，请在数据工厂复制向导或编辑器中单击“授权”按钮，并输入凭据，此时会自动生成此属性的值。 |是（仅在使用 OAuth 身份验证时适用） |
| gatewayName |网关名称 - 数据工厂服务应使用此网关连接到本地 OData 服务。 仅在从本地 OData 源复制数据时指定。 |否 |

#### <a name="example---using-basic-authentication"></a>示例 - 使用基本身份验证
```json
{
    "name": "inputLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "http://services.odata.org/OData/OData.svc",
            "authenticationType": "Basic",
            "username": "username",
            "password": "password"
        }
    }
}
```

#### <a name="example---using-anonymous-authentication"></a>示例 - 使用匿名身份验证

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "http://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        }
    }
}
```

#### <a name="example---using-windows-authentication-accessing-on-premises-odata-source"></a>示例 - 使用 Windows 身份验证访问本地 OData 源

```json
{
    "name": "inputLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of on-premises OData source, for example, Dynamics CRM>",
            "authenticationType": "Windows",
            "username": "domain\\user",
            "password": "password",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example---using-oauth-authentication-accessing-cloud-odata-source"></a>示例 - 使用 OAuth 身份验证访问云 OData 源
```json
{
    "name": "inputLinkedService",
    "properties":
    {
        "type": "OData",
            "typeProperties":
        {
            "url": "<endpoint of cloud OData source, for example, https://<tenant>.crm.dynamics.com/XRMServices/2011/OrganizationData.svc>",
            "authenticationType": "OAuth",
            "authorizedCredential": "<auto generated by clicking the Authorize button on UI>"
        }
    }
}
```

有关详细信息，请参阅 [OData 连接器](data-factory-odata-connector.md#linked-service-properties)一文。

### <a name="dataset"></a>数据集
要定义 OData 数据集，请将数据集的**类型**设置为 **ODataResource**，并在 **typeProperties** 节中指定以下属性： 

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| 路径 |OData 资源路径 |否 |

#### <a name="example"></a>示例

```json
{
    "name": "ODataDataset",
    "properties": {
        "type": "ODataResource",
        "typeProperties": {
            "path": "Products"
        },
        "linkedServiceName": "ODataLinkedService",
        "structure": [],
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "retryInterval": "00:01:00",
            "retryTimeout": "00:10:00",
            "maximumRetry": 3
        }
    }
}
```

有关详细信息，请参阅 [OData 连接器](data-factory-odata-connector.md#dataset-properties)一文。

### <a name="relational-source-in-copy-activity"></a>复制活动中的关系源
如果要从 OData 源复制数据，请将复制活动的**源类型**设置为 **RelationalSource**，并在 **source** 节中指定以下属性：

| 属性 | 说明 | 示例 | 必选 |
| --- | --- | --- | --- |
| query |使用自定义查询读取数据。 |"?$select=Name, Description&$top=5" |否 |

#### <a name="example"></a>示例

```json
{
    "name": "CopyODataToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "?$select=Name, Description&$top=5"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "ODataDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobODataDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "ODataToBlob"
        }],
        "start": "2017-02-01T18:00:00",
        "end": "2017-02-03T19:00:00"
    }
}
```

有关详细信息，请参阅 [OData 连接器](data-factory-odata-connector.md#copy-activity-properties)一文。


## <a name="odbc"></a>ODBC


### <a name="linked-service"></a>链接服务
要定义 ODBC 链接服务，请将链接服务的**类型**设置为 **OnPremisesOdbc**，并在 **typeProperties** 节中指定以下属性：  

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| connectionString |连接字符串的非访问凭据部分和可选的加密凭据。 请参阅以下部分中的示例。 |是 |
| credential |连接字符串的访问凭据部分，采用特定于驱动程序的属性值格式指定。 示例：“Uid=<user ID>;Pwd=<password>;RefreshToken=<secret refresh token>;”。 |否 |
| authenticationType |用于连接 ODBC 数据存储的身份验证类型。 可能值为：匿名和基本。 |是 |
| username |如果使用基本身份验证，请指定用户名。 |否 |
| password |指定为用户名指定的用户帐户的密码。 |否 |
| gatewayName |数据工厂服务应用于连接到 ODBC 数据存储的网关的名称。 |是 |

#### <a name="example---using-basic-authentication"></a>示例 - 使用基本身份验证

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "OnPremisesOdbc",
        "typeProperties": {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;",
            "userName": "username",
            "password": "password",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```
#### <a name="example---using-basic-authentication-with-encrypted-credentials"></a>示例 - 结合加密的凭据使用基本身份验证
可以使用 [New-AzureRMDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx)（Azure PowerShell 1.0 版） cmdlet 或 [New-AzureDataFactoryEncryptValue](https://msdn.microsoft.com/library/dn834940.aspx)（Azure PowerShell 0.9 版或更早版本）加密凭据。  

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "OnPremisesOdbc",
        "typeProperties": {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=myserver.database.windows.net; Database=TestDatabase;;EncryptedCredential=eyJDb25uZWN0...........................",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-using-anonymous-authentication"></a>示例：使用匿名身份验证

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "OnPremisesOdbc",
        "typeProperties": {
            "authenticationType": "Anonymous",
            "connectionString": "Driver={SQL Server};Server={servername}.database.windows.net; Database=TestDatabase;",
            "credential": "UID={uid};PWD={pwd}",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

有关详细信息，请参阅 [ODBC 连接器](data-factory-odbc-connector.md#linked-service-properties)一文。 

### <a name="dataset"></a>数据集
要定义 ODBC 数据集，请将数据集的**类型**设置为 **RelationalTable**，并在 **typeProperties** 节中指定以下属性： 

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| tableName |ODBC 数据存储中表的名称。 |是 |


#### <a name="example"></a>示例

```json
{
    "name": "ODBCDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "ODBCLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

有关详细信息，请参阅 [ODBC 连接器](data-factory-odbc-connector.md#dataset-properties)一文。 

### <a name="relational-source-in-copy-activity"></a>复制活动中的关系源
如果要从 ODBC 数据存储复制数据，请将复制活动的**源类型**设置为 **RelationalSource**，并在 **source** 节中指定以下属性：

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| query |使用自定义查询读取数据。 |SQL 查询字符串。 例如： `select * from MyTable`。 |是 |

#### <a name="example"></a>示例

```json
{
    "name": "CopyODBCToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "OdbcDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobOdbcDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "OdbcToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
``` 

有关详细信息，请参阅 [ODBC 连接器](data-factory-odbc-connector.md#copy-activity-properties)一文。

## <a name="salesforce"></a>Salesforce


### <a name="linked-service"></a>链接服务
要定义 Salesforce 链接服务，请将链接服务的**类型**设置为 **Salesforce**，并在 **typeProperties** 节中指定以下属性：  

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| environmentUrl | 指定 Salesforce 实例的 URL。 <br><br> - 默认值为“https://login.salesforce.com”。 <br> - 若要从沙盒复制数据，请指定“https://test.salesforce.com”。 <br> - 若要从自定义域复制数据，请指定（例如）“https://[domain].my.salesforce.com”。 |否 |
| username |为用户帐户指定用户名。 |是 |
| password |指定用户帐户的密码。 |是 |
| securityToken |为用户帐户指定安全令牌。 请参阅[获取安全令牌](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm)了解有关如何重置/获取安全令牌的说明。 若要了解有关安全令牌的一般信息，请参阅 [Security and the API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm)（安全性和 API）。 |是 |

#### <a name="example"></a>示例

```json
{
    "name": "SalesforceLinkedService",
    "properties": {
        "type": "Salesforce",
        "typeProperties": {
            "username": "<user name>",
            "password": "<password>",
            "securityToken": "<security token>"
        }
    }
}
```

有关详细信息，请参阅 [Salesforce 连接器](data-factory-salesforce-connector.md#linked-service-properties)一文。 

### <a name="dataset"></a>数据集
要定义 Salesforce 数据集，请将数据集的**类型**设置为 **RelationalTable**，并在 **typeProperties** 节中指定以下属性： 

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| tableName |在 Salesforce 中表的名称。 |否（如果指定了 **RelationalSource** 的**query**） |

#### <a name="example"></a>示例

```json
{
    "name": "SalesforceInput",
    "properties": {
        "linkedServiceName": "SalesforceLinkedService",
        "type": "RelationalTable",
        "typeProperties": {
            "tableName": "AllDataType__c"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

有关详细信息，请参阅 [Salesforce 连接器](data-factory-salesforce-connector.md#dataset-properties)一文。 

### <a name="relational-source-in-copy-activity"></a>复制活动中的关系源
如果要从 Salesforce 复制数据，请将复制活动的**源类型**设置为 **RelationalSource**，并在 **source** 节中指定以下属性：

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| query |使用自定义查询读取数据。 |SQL 92 查询或 [Salesforce 对象查询语言 (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) 查询。 例如：`select * from MyTable__c`。 |否（如果指定了**数据集**的 **tableName**） |

#### <a name="example"></a>示例  



```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "SalesforceToAzureBlob",
            "description": "Copy from Salesforce to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "SalesforceInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "SELECT Id, Col_AutoNumber__c, Col_Checkbox__c, Col_Currency__c, Col_Date__c, Col_DateTime__c, Col_Email__c, Col_Number__c, Col_Percent__c, Col_Phone__c, Col_Picklist__c, Col_Picklist_MultiSelect__c, Col_Text__c, Col_Text_Area__c, Col_Text_AreaLong__c, Col_Text_AreaRich__c, Col_URL__c, Col_Text_Encrypt__c, Col_Lookup__c FROM AllDataType__c"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

> [!IMPORTANT]
> 任何自定义对象均需要 API 名称的“__c”部分。

有关详细信息，请参阅 [Salesforce 连接器](data-factory-salesforce-connector.md#copy-activity-properties)一文。 

## <a name="web-data"></a>Web 数据 

### <a name="linked-service"></a>链接服务
要定义 Web 链接服务，请将链接服务的**类型**设置为 **Web**，并在 **typeProperties** 节中指定以下属性：  

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| URL |Web 源的 URL |是 |
| authenticationType |匿名。 |是 |
 

#### <a name="example"></a>示例


```json
{
    "name": "web",
    "properties": {
        "type": "Web",
        "typeProperties": {
            "authenticationType": "Anonymous",
            "url": "https://en.wikipedia.org/wiki/"
        }
    }
}
```

有关详细信息，请参阅 [Web 表连接器](data-factory-web-table-connector.md#linked-service-properties)一文。 

### <a name="dataset"></a>数据集
要定义 Web 数据集，请将数据集的**类型**设置为 **WebTable**，并在 **typeProperties** 节中指定以下属性： 

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type |数据集类型。 必须设置为 **WebTable** |是 |
| path |包含表的资源的相对 URL。 |否。 未指定路径时，仅使用链接服务定义中指定的 URL。 |
| index |资源中表的索引。 请参阅[获取 HTML 页中表的索引](#get-index-of-a-table-in-an-html-page)，了解获取 HTML 页中表的索引的步骤。 |是 |

#### <a name="example"></a>示例

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "linkedServiceName": "WebLinkedService",
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

有关详细信息，请参阅 [Web 表连接器](data-factory-web-table-connector.md#dataset-properties)一文。 

### <a name="web-source-in-copy-activity"></a>复制活动中的 Web 源
如果要从 Web 表复制数据，请将复制活动的**源类型**设置为 **WebSource**。 目前，复制活动中的源为 **WebSource** 类型时，不支持其他属性。

#### <a name="example"></a>示例

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "WebTableToAzureBlob",
            "description": "Copy from a Web table to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "WebTableInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "WebSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

有关详细信息，请参阅 [Web 表连接器](data-factory-web-table-connector.md#copy-activity-properties)一文。 

## <a name="compute-environments"></a>计算环境
下表列出了数据工厂支持的计算环境，以及可在这些环境中运行的转换活动。 单击所需计算环境的链接可以查看用于将该环境链接到数据工厂的链接服务的 JSON 架构。 

| 计算环境 | 活动 |
| --- | --- |
| [按需 HDInsight 群集](#on-demand-azure-hdinsight-cluster)或[自己的 HDInsight 群集](#existing-azure-hdinsight-cluster) |[.NET 自定义活动](#net-custom-activity)、[Hive 活动](#hdinsight-hive-activity)、[Pig 活动](#hdinsight-pig-activity)、[MapReduce activity](#hdinsight-mapreduce-activity)、[Hadoop 流式处理活动](#hdinsight-streaming-activityd)、[Spark 活动](#hdinsight-spark-activity) |
| [Azure Batch](#azure-batch) |[.NET 自定义活动](#net-custom-activity) |
| [Azure 机器学习](#azure-machine-learning) | [机器学习批处理执行活动](#machine-learning-batch-execution-activity)、[机器学习更新资源活动](#machine-learning-update-resource-activity) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics) |[Data Lake Analytics U-SQL](#data-lake-analytics-u-sql-activity) |
| [Azure SQL 数据库](#azure-sql-database-1)、[Azure SQL 数据仓库](#azure-sql-data-warehouse-1)、[SQL Server](#sql-server-1) |[存储过程](#stored-procedure-activity) |

## <a name="on-demand-azure-hdinsight-cluster"></a>按需 Azure HDInsight 群集
Azure 数据工厂服务可自动创建基于 Windows/Linux 的按需 HDInsight 群集，以处理数据。 群集创建在与该群集相关联的存储帐户（JSON 中的 linkedServiceName 属性）所在的同一区域中。 可以在此链接服务中运行以下转换活动：[.NET 自定义活动](#net-custom-activity)、[Hive 活动](#hdinsight-hive-activity)、[Pig 活动](#hdinsight-pig-activity)、[MapReduce activity](#hdinsight-mapreduce-activity)、[Hadoop 流式处理活动](#hdinsight-streaming-activityd)、[Spark 活动](#hdinsight-spark-activity)。 

### <a name="linked-service"></a>链接服务 
下表提供了按需 HDInsight 链接服务的 Azure JSON 定义中使用的属性的说明。

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| type |类型属性应设置为 **HDInsightOnDemand**。 |是 |
| clusterSize |群集中辅助进程/数据节点的数量。 HDInsight 群集创建时具有 2 个头节点以及一定数量的辅助进程节点（此节点的数量是为此属性所指定的数量）。 这些节点的大小为拥有 4 个核心的 Standard_D3，因此一个具有 4 个辅助节点的群集拥有 24 个核心（辅助节点有 4\*4 = 16 个核心，头节点有 2\*4 = 8 个核心）。 有关 Standard_D3 层的详细信息，请参阅[在 HDInsight 中创建基于 Linux 的 Hadoop 群集](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)。 |是 |
| timetolive |按需 HDInsight 群集允许的空闲时间。 指定当活动运行完成后，如果群集中没有其他的活动作业，按需 HDInsight 群集保持活动状态的时间。<br/><br/>例如，如果一个活动运行需要 6 分钟，而 timetolive 的设置是 5 分钟，则当 6 分钟的活动运行处理结束后，群集将保持 5 分钟的活动状态。 如果在这 6 分钟的时间内执行其他的活动运行，则由同一群集进行处理。<br/><br/>创建按需 HDInsight 群集是一项开销非常大的操作（可能会花费一定的时间），因此请根据需要使用此设置，以通过重复使用一个按需 HDInsight 群集来提高数据工厂的性能。<br/><br/>如果将 timetolive 值设置为 0，则群集在活动运行处理完后很快便会被删除。 另一方面，如果设置较高的值，群集可能会保持不必要的空闲状态，从而造成较高成本。 因此，根据具体需要设置适当的值非常重要。<br/><br/>如果设置了适当的 timetolive 属性值，多个管道则可以共享按需 HDInsight 群集的同一实例 |是 |
| 版本 |HDInsight 群集的版本。 若要了解详细信息，请参阅 [Azure 数据工厂中支持的 HDInsight 版本](data-factory-compute-linked-services.md#supported-hdinsight-versions-in-azure-data-factory)。 |否 |
| linkedServiceName |由按需群集用于存储和处理数据的 Azure 存储链接服务。 <p>目前，无法创建使用 Azure Data Lake Store 作为存储的按需 HDInsight 群集。 如果想要存储在 Azure Data Lake Store 中处理的来自 HDInsight 的结果数据，请使用复制活动将数据从 Azure Blob 存储复制到 Azure Data Lake Store。</p>  | 是 |
| additionalLinkedServiceNames |指定 HDInsight 链接服务的其他存储帐户，使数据工厂服务能够代为注册它们。 |否 |
| osType |操作系统的类型。 允许的值为：Windows（默认值）和 Linux |否 |
| hcatalogLinkedServiceName |指向 HCatalog 数据库的 Azure SQL 链接服务的名称。 将 Azure SQL 数据库用作元存储以创建按需 HDInsight 群集。 |否 |

### <a name="json-example"></a>JSON 示例
以下 JSON 定义基于 Linux 的按需 HDInsight 链接服务。 处理数据切片时，数据工厂服务会自动创建**基于 Linux 的** HDInsight 群集。 

```json
{
    "name": "HDInsightOnDemandLinkedService",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.5",
            "clusterSize": 1,
            "timeToLive": "00:05:00",
            "osType": "Linux",
            "linkedServiceName": "StorageLinkedService"
        }
    }
}
```

有关详细信息，请参阅[计算链接服务](data-factory-compute-linked-services.md)一文。 

## <a name="existing-azure-hdinsight-cluster"></a>现有的 Azure HDInsight 群集
可以创建 Azure HDInsight 链接服务，以向数据工厂注册自己的 HDInsight 群集。 可以在此链接服务中运行以下数据转换活动：[.NET 自定义活动](#net-custom-activity)、[Hive 活动](#hdinsight-hive-activity)、[Pig 活动](#hdinsight-pig-activity)、[MapReduce activity](#hdinsight-mapreduce-activity)、[Hadoop 流式处理活动](#hdinsight-streaming-activityd)、[Spark 活动](#hdinsight-spark-activity)。 

### <a name="linked-service"></a>链接服务
下表提供了 Azure HDInsight 链接服务的 Azure JSON 定义中使用的属性的说明。

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| type |类型属性应设置为 **HDInsight**。 |是 |
| clusterUri |HDInsight 群集的 URI。 |是 |
| username |指定用于连接到现有 HDInsight 群集的用户的名称。 |是 |
| password |指定用户帐户的密码。 |是 |
| linkedServiceName | Azure 存储链接服务（指 HDInsight 群集使用的 Azure Blob 存储）的名称。 <p>目前，不能为此属性指定 Azure Data Lake Store 链接服务。 如果 HDInsight 群集有权访问 Data Lake Store，那么可以使用 Hive/Pig 脚本访问 Azure Data Lake Store 中的数据。 </p>  |是 |

若要了解支持的 HDInsight 群集版本的信息，请参阅[支持的 HDInsight 版本](data-factory-compute-linked-services.md#supported-hdinsight-versions-in-azure-data-factory)。 

#### <a name="json-example"></a>JSON 示例

```json
{
    "name": "HDInsightLinkedService",
    "properties": {
        "type": "HDInsight",
        "typeProperties": {
            "clusterUri": " https://<hdinsightclustername>.azurehdinsight.net/",
            "userName": "admin",
            "password": "<password>",
            "linkedServiceName": "MyHDInsightStoragelinkedService"
        }
    }
}
```

## <a name="azure-batch"></a>Azure 批处理
可以创建 Azure 批处理链接服务，用于向数据工厂注册虚拟机 (VM) 的批处理池。 可以使用 Azure Batch 或 Azure HDInsight 运行 .NET 自定义活动。 可以在此链接服务中运行 [.NET 自定义活动](#net-custom-activity)。 

### <a name="linked-service"></a>链接服务
下表提供了 Azure 批处理链接服务的 Azure JSON 定义中使用的属性的说明。

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| type |类型属性应设置为 **AzureBatch**。 |是 |
| accountName |Azure Batch 帐户的名称。 |是 |
| accessKey |Azure Batch 帐户的访问密钥。 |是 |
| poolName |虚拟机的池名称。 |是 |
| linkedServiceName |与此 Azure Batch 链接服务相关联的 Azure 存储链接服务的名称。 此链接服务用于暂存运行活动所需的文件，以及用于存储活动执行日志。 |是 |


#### <a name="json-example"></a>JSON 示例

```json
{
    "name": "AzureBatchLinkedService",
    "properties": {
        "type": "AzureBatch",
        "typeProperties": {
            "accountName": "<Azure Batch account name>",
            "accessKey": "<Azure Batch account key>",
            "poolName": "<Azure Batch pool name>",
            "linkedServiceName": "<Specify associated storage linked service reference here>"
        }
    }
}
```

## <a name="azure-machine-learning"></a>Azure 机器学习
可以创建 Azure 机器学习链接服务，用于向数据工厂注册机器学习批处理评分终结点。 可以在此链接服务中运行的两个数据转换活动：[机器学习批处理执行活动](#machine-learning-batch-execution-activity)、[机器学习更新资源活动](#machine-learning-update-resource-activity)。 

### <a name="linked-service"></a>链接服务
下表提供了 Azure 机器学习链接服务的 Azure JSON 定义中使用的属性的说明。

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| 类型 |类型属性应设置为 **AzureML**。 |是 |
| mlEndpoint |批处理计分 URL。 |是 |
| apiKey |已发布的工作区模型的 API。 |是 |

#### <a name="json-example"></a>JSON 示例

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://[batch scoring endpoint]/jobs",
            "apiKey": "<apikey>"
        }
    }
}
```

## <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics
在使用管道中的 [Data Lake Analytics U-SQL 活动](data-factory-usql-activity.md)前，创建 **Azure Data Lake Analytics** 链接服务，以将 Azure Data Lake Analytics 计算服务链接到 Azure 数据工厂。

### <a name="linked-service"></a>链接服务

下表提供了 Azure Data Lake Analytics 链接服务的 JSON 定义中使用的属性的说明。 

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| 类型 |类型属性应设置为 **AzureDataLakeAnalytics**。 |是 |
| accountName |Azure Data Lake Analytics 帐户名。 |是 |
| dataLakeAnalyticsUri |Azure Data Lake Analytics URI。 |否 |
| authorization |在数据工厂编辑器中单击“授权”按钮并完成 OAuth 登录后，会自动检索授权代码。 |是 |
| subscriptionId |Azure 订阅 ID |否（如果未指定，则使用数据工厂的订阅）。 |
| resourceGroupName |Azure 资源组名称 |否（如果未指定，则使用数据工厂的资源组）。 |
| sessionId |OAuth 授权会话中的会话 ID。 每个会话 ID 都是唯一的，并且可能仅可使用一次。 使用数据工厂编辑器时，会自动生成此 ID。 |是 |


#### <a name="json-example"></a>JSON 示例
以下示例为 Azure Data Lake Analytics 链接服务提供 JSON 定义。

```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "<account name>",
            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
            "authorization": "<authcode>",
            "sessionId": "<session ID>",
            "subscriptionId": "<subscription id>",
            "resourceGroupName": "<resource group name>"
        }
    }
}
```

## <a name="azure-sql-database"></a>Azure SQL 数据库
创建 Azure SQL 链接服务，并将其与[存储过程活动](#stored-procedure-activity)配合使用，以从数据工厂管道调用存储过程。 

### <a name="linked-service"></a>链接服务
要定义 Azure SQL 数据库链接服务，请将链接服务的**类型**设置为 **AzureSqlDatabase**，并在 **typeProperties** 节中指定以下属性：  

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| connectionString |为 connectionString 属性指定连接到 Azure SQL 数据库实例所需的信息。 |是 |

#### <a name="json-example"></a>JSON 示例

```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
    }
}
```

请参阅 [Azure SQL 连接器](data-factory-azure-sql-connector.md#linked-service-properties)一文，以了解此链接服务的详细信息。

## <a name="azure-sql-data-warehouse"></a>Azure SQL 数据仓库
创建 Azure SQL 数据仓库链接服务，并将其与[存储的过程活动](data-factory-stored-proc-activity.md)配合使用，以从数据工厂管道调用存储的过程。 

### <a name="linked-service"></a>链接服务
要定义 Azure SQL 数据仓库链接服务，请将链接服务的**类型**设置为 **AzureSqlDW**，并在 **typeProperties** 节中指定以下属性：  

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| connectionString |为 connectionString 属性指定连接到 Azure SQL 数据仓库实例所需的信息。 |是 |

#### <a name="json-example"></a>JSON 示例

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
    }
}
```

有关详细信息，请参阅 [Azure SQL 数据仓库连接器](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties)一文。 

## <a name="sql-server"></a>SQL Server 
创建 SQL Server 链接服务，并将其与[存储的过程活动](data-factory-stored-proc-activity.md)配合使用，以从数据工厂管道调用存储的过程。 

### <a name="linked-service"></a>链接服务
创建 **OnPremisesSqlServer** 类型的链接服务，将本地 SQL Server 数据库链接到数据工厂。 下表提供了有关特定于本地 SQL Server 链接服务的 JSON 元素的描述。

下表提供了有关特定于 SQL Server 链接服务的 JSON 元素的描述。

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| type |类型属性应设置为：**OnPremisesSqlServer**。 |是 |
| connectionString |指定使用 SQL 身份验证或 Windows 身份验证连接到本地 SQL Server 数据库时所需的 connectionString 信息。 |是 |
| gatewayName |网关的名称 - 数据工厂服务应使用此网关连接到本地 SQL Server 数据库。 |是 |
| username |如果使用的是 Windows 身份验证，请指定用户名。 示例：**域名\\用户名**。 |否 |
| password |指定为用户名指定的用户帐户的密码。 |否 |

可使用 **New-AzureRmDataFactoryEncryptValue** cmdlet 加密凭据，并在连接字符串中使用凭据，如下例所示（**EncryptedCredential** 属性）：  

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```


#### <a name="example-json-for-using-sql-authentication"></a>示例：用于实现 SQL 身份验证的 JSON

```json
{
    "name": "MyOnPremisesSQLDB",
    "properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=False;User ID=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```
#### <a name="example-json-for-using-windows-authentication"></a>示例：用于实现 Windows 身份验证的 JSON

如果指定了用户名和密码，网关会使用用户名和密码模拟指定的用户帐户来连接到本地 SQL Server 数据库。 否则，网关使用网关（其启动帐户）的安全上下文直接连接到 SQL Server。

```json
{
    "Name": " MyOnPremisesSQLDB",
    "Properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "ConnectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=True;",
            "username": "<domain\\username>",
            "password": "<password>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

有关详细信息，请参阅 [SQL Server 连接器](data-factory-sqlserver-connector.md#linked-service-properties)一文。

## <a name="data-transformation-activities"></a>数据转换活动

活动 | 说明
-------- | -----------
[HDInsight Hive 活动](#hdinsight-hive-activity) | 数据工厂管道中的 HDInsight Hive 活动会在自己的或基于 Windows/Linux 的按需 HDInsight 群集上执行 Hive 查询。 
[HDInsight Pig 活动](#hdinsight-pig-activity) | 数据工厂管道中的 HDInsight Pig 活动会在自己或基于 Windows/Linux 的按需 HDInsight 群集上执行 Pig 查询。
[HDInsight MapReduce 活动](#hdinsight-mapreduce-activity) | 数据工厂管道中的 HDInsight MapReduce 活动会在自己或基于 Windows/Linux 的按需 HDInsight 群集上执行 MapReduce 程序。
[HDInsight Streaming 活动](#hdinsight-streaming-activity) | 数据工厂管道中的 HDInsight Streaming 活动会在自己或基于 Windows/Linux 的按需 HDInsight 群集上执行 HDInsight Streaming 程序。
[HDInsight Spark 活动](#hdinsight-spark-activity) | 数据工厂管道中的 HDInsight Spark 活动在自己的 HDInsight 群集上执行 Spark 程序。 
[机器学习批处理执行活动](#machine-learning-batch-execution-activity) | 借助 Azure 数据工厂，可轻松创建相关管道，利用已发布的 Azure 机器学习 Web 服务进行预测分析。 借助 Azure 数据工厂管道中的批处理执行活动，可以调用 Azure 机器学习 Web 服务预测批处理中的数据。 
[机器学习更新资源活动](#machine-learning-update-resource-activity) | 随着时间推移，需要使用新的输入数据集重新定型机器学习评分实验中的预测模型。 完成重新定型后，可使用重新定型的机器学习模型更新评分 Web 服务。 可以使用更新资源活动，通过新的训练模型更新 Web 服务。
[存储过程活动](#stored-procedure-activity) | 可在数据工厂管道中使用存储过程活动调用以下数据存储中的存储过程：Azure SQL 数据库、Azure SQL 数据仓库、企业中的 SQL Server 数据库或 Azure VM。 
[Data Lake Analytics U-SQL 活动](#data-lake-analytics-u-sql-activity) | Data Lake Analytics U-SQL 活动在 Azure Data Lake Analytics 群集上运行 U-SQL 脚本。  
[.NET 自定义活动](#net-custom-activity) | 如果需要采用数据工厂不支持的方式转换数据，可以使用自己的数据处理逻辑创建自定义活动，并在管道中使用该活动。 可以使用 Azure Batch 服务或 Azure HDInsight 群集配置要运行的自定义 .NET 活动。 

     
## <a name="hdinsight-hive-activity"></a>HDInsight Hive 活动
可以在 Hive 活动 JSON 定义中指定以下属性。 活动的类型属性必须是：**HDInsightHive**。 必须先创建 HDInsight 链接服务，然后指定该服务的名称作为 **linkedServiceName** 属性的值。 将活动类型设置为 HDInsightHive 时，**typeProperties** 节支持以下属性：

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| 脚本 |指定 Hive 脚本内联 |否 |
| 脚本路径 |将 Hive 脚本存储在 Azure blob 存储中，并提供该文件的路径。 使用“script”或“scriptPath”属性。 两者不能一起使用。 文件名称需区分大小写。 |否 |
| 定义 |使用“hiveconf”在 Hive 脚本中指定参数作为密钥/值对，以便参考 |否 |

这些类型属性特定于 Hive 活动。 所有活动支持其他属性（typeProperties 节外部的属性）。   

### <a name="json-example"></a>JSON 示例
以下 JSON 在管道中定义 HDInsight Hive 活动。  

```json
{
    "name": "Hive Activity",
    "description": "description",
    "type": "HDInsightHive",
    "inputs": [
      {
        "name": "input tables"
      }
    ],
    "outputs": [
      {
        "name": "output tables"
      }
    ],
    "linkedServiceName": "MyHDInsightLinkedService",
    "typeProperties": {
      "script": "Hive script",
      "scriptPath": "<pathtotheHivescriptfileinAzureblobstorage>",
      "defines": {
        "param1": "param1Value"
      }
    },
   "scheduler": {
      "frequency": "Day",
      "interval": 1
    }
}
```

有关详细信息，请参阅 [Hive 活动](data-factory-hive-activity.md)一文。 

## <a name="hdinsight-pig-activity"></a>HDInsight Pig 活动
可以在 Pig 活动 JSON 定义中指定以下属性。 活动的类型属性必须是：**HDInsightPig**。 必须先创建 HDInsight 链接服务，然后指定该服务的名称作为 **linkedServiceName** 属性的值。 将活动类型设置为 HDInsightPig 时，**typeProperties** 节支持以下属性： 

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| 脚本 |指定 Pig 脚本内联 |否 |
| 脚本路径 |将 Pig 脚本存储在 Azure Blob 存储中，并提供该文件的路径。 使用“script”或“scriptPath”属性。 两者不能一起使用。 文件名称需区分大小写。 |否 |
| 定义 |在 Pig 脚本中指定参数作为键/值对，以供引用 |否 |

这些类型属性特定于 Pig 活动。 所有活动支持其他属性（typeProperties 节外部的属性）。   

### <a name="json-example"></a>JSON 示例

```json
{
    "name": "HiveActivitySamplePipeline",
      "properties": {
    "activities": [
        {
            "name": "Pig Activity",
            "description": "description",
            "type": "HDInsightPig",
            "inputs": [
                  {
                    "name": "input tables"
                  }
            ],
            "outputs": [
                  {
                    "name": "output tables"
                  }
            ],
            "linkedServiceName": "MyHDInsightLinkedService",
            "typeProperties": {
                  "script": "Pig script",
                  "scriptPath": "<pathtothePigscriptfileinAzureblobstorage>",
                  "defines": {
                    "param1": "param1Value"
                  }
            },
               "scheduler": {
                  "frequency": "Day",
                  "interval": 1
            }
          }
    ]
  }
}
```

有关详细信息，请参阅 [Pig 活动](#data-factory-pig-activity.md)一文。 

## <a name="hdinsight-mapreduce-activity"></a>HDInsight MapReduce 活动
可以在 MapReduce 活动 JSON 定义中指定以下属性。 活动的类型属性必须是：**HDInsightMapReduce**。 必须先创建 HDInsight 链接服务，然后指定该服务的名称作为 **linkedServiceName** 属性的值。 将活动类型设置为 HDInsightMapReduce 时，**typeProperties** 节支持以下属性： 

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| jarLinkedService | 包含 JAR 文件的 Azure 存储的链接服务名称。 | 是 |
| jarFilePath | Azure 存储中 JAR 文件的路径。 | 是 | 
| className | JAR 文件中的 main 类名称。 | 是 | 
| arguments | MapReduce 程序的逗号分隔参数列表。 运行时，可在 MapReduce 框架中看到几个额外的参数（例如：mapreduce.job.tags）。 要区分自己的参数和 MapReduce 参数，请考虑将选项和值同时作为参数使用，如下例所示（-s、--input、--output 等选项后面紧接相应的值） | 否 | 

### <a name="json-example"></a>JSON 示例

```json
{
    "name": "MahoutMapReduceSamplePipeline",
    "properties": {
        "description": "Sample Pipeline to Run a Mahout Custom Map Reduce Jar. This job calculates an Item Similarity Matrix to determine the similarity between two items",
        "activities": [
            {
                "type": "HDInsightMapReduce",
                "typeProperties": {
                    "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
                    "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
                    "jarLinkedService": "StorageLinkedService",
                    "arguments": ["-s", "SIMILARITY_LOGLIKELIHOOD", "--input", "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/input", "--output", "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/output/", "--maxSimilaritiesPerItem", "500", "--tempDir", "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/temp/mahout"]
                },
                "inputs": [
                    {
                        "name": "MahoutInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "MahoutOutput"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "MahoutActivity",
                "description": "Custom Map Reduce to generate Mahout result",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-01-03T00:00:00",
        "end": "2017-01-04T00:00:00"
    }
}
```

有关详细信息，请参阅 [MapReduce 活动](data-factory-map-reduce.md)一文。 

## <a name="hdinsight-streaming-activity"></a>HDInsight Streaming 活动
可以在 Hadoop 流式处理活动 JSON 定义中指定以下属性。 活动的类型属性必须是：**HDInsightStreaming**。 必须先创建 HDInsight 链接服务，然后指定该服务的名称作为 **linkedServiceName** 属性的值。 将活动类型设置为 HDInsightStreaming 时，**typeProperties** 节支持以下属性： 

| 属性 | 说明 | 
| --- | --- |
| mapper | 映射器可执行文件的名称。 在示例中，cat.exe 即是映射器可执行文件。| 
| reducer | 化简器可执行文件的名称。 在示例中，wc.exe 即是减压器可执行文件。 | 
| input | 映射器的输入文件（包括位置）。 在此示例中："wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt": adfsample 是 blob 容器，example/data/Gutenberg 是文件夹，davinci.txt 是 blob。 |
| output | 化简器的输出文件（包括位置）。 将 Hadoop Streaming 作业的输出写入到为该属性指定的位置。 |
| filePaths | 映射器和化简器可执行文件的路径。 在此示例中：blob 容器为 "adfsample/example/apps/wc.exe"adfsample，文件夹为 example/apps，可执行文件为 wc.exe。 | 
| fileLinkedService | Azure 存储链接服务，表示包含 filePaths 节中指定的文件的 Azure 存储。 | 
| arguments | MapReduce 程序的逗号分隔参数列表。 运行时，可在 MapReduce 框架中看到几个额外的参数（例如：mapreduce.job.tags）。 要区分自己的参数和 MapReduce 参数，请考虑将选项和值同时作为参数使用，如下例所示（-s、--input、--output 等选项后面紧接相应的值） | 
| getDebugInfo | 一个可选元素。 将其设置为“Failure”时，仅针对失败下载日志。 将其设置为“All”时，不考虑执行状态，始终下载日志。 | 

> [!NOTE]
> 必须为 **outputs** 属性指定 Hadoop 流式处理活动的输出数据集。 此数据集可以是驱动管道计划（每小时、每日，等等）所需的虚构数据集。 如果活动未采用任何输入，可以不用为 **inputs** 属性指定活动的输入数据集。  

## <a name="json-example"></a>JSON 示例

```json
{
    "name": "HadoopStreamingPipeline",
    "properties": {
        "description": "Hadoop Streaming Demo",
        "activities": [
            {
                "type": "HDInsightStreaming",
                "typeProperties": {
                    "mapper": "cat.exe",
                    "reducer": "wc.exe",
                    "input": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                    "output": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                    "filePaths": ["<nameofthecluster>/example/apps/wc.exe","<nameofthecluster>/example/apps/cat.exe"],
                    "fileLinkedService": "StorageLinkedService",
                    "getDebugInfo": "Failure"
                },
                "outputs": [
                    {
                        "name": "StreamingOutputDataset"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "RunHadoopStreamingJob",
                "description": "Run a Hadoop streaming job",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2014-01-04T00:00:00",
        "end": "2014-01-05T00:00:00"
    }
}
```

有关详细信息，请参阅 [Hadoop 流式处理活动](data-factory-hadoop-streaming-activity.md)一文。 

## <a name="hdinsight-spark-activity"></a>HDInsight Spark 活动
可以在 Spark 活动 JSON 定义中指定以下属性。 活动的类型属性必须是：**HDInsightSpark**。 必须先创建 HDInsight 链接服务，然后指定该服务的名称作为 **linkedServiceName** 属性的值。 将活动类型设置为 HDInsightSpark 时，**typeProperties** 节支持以下属性： 

| 属性 | 说明 | 必选 |
| -------- | ----------- | -------- |
| rootPath | 包含 Spark 文件的 Azure Blob 容器和文件夹。 文件名称需区分大小写。 | 是 |
| entryFilePath | Spark 代码/包的根文件夹的相对路径 | 是 |
| className | 应用程序的 Java/Spark main 类 | 否 | 
| arguments | Spark 程序的命令行参数列表。 | 否 | 
| proxyUser | 用于模拟执行 Spark 程序的用户帐户 | 否 | 
| sparkConfig | Spark 配置属性。 | 否 | 
| getDebugInfo | 指定何时将 Spark 日志文件复制到 HDInsight 群集使用的（或者）sparkJobLinkedService 指定的 Azure 存储。 允许的值：None、Always 或 Failure。 默认值：None。 | 否 | 
| sparkJobLinkedService | 用于保存 Spark 作业文件、依赖项和日志的 Azure 存储链接服务。  如果未指定此属性的值，将使用与 HDInsight 群集关联的存储。 | 否 |

### <a name="json-example"></a>JSON 示例

```json
{
    "name": "SparkPipeline",
    "properties": {
        "activities": [
            {
                "type": "HDInsightSpark",
                "typeProperties": {
                    "rootPath": "adfspark\\pyFiles",
                    "entryFilePath": "test.py",
                    "getDebugInfo": "Always"
                },
                "outputs": [
                    {
                        "name": "OutputDataset"
                    }
                ],
                "name": "MySparkActivity",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-02-05T00:00:00",
        "end": "2017-02-06T00:00:00"
    }
}
```
请注意以下几点： 

- **type** 属性设置为 **HDInsightSpark**。
- **rootPath** 设置为 **adfspark\\pyFiles**，其中，adfspark 是 Azure Blob 容器，pyFiles 是该容器中的文件夹。 在此示例中，Azure Blob 存储是与 Spark 群集关联的存储。 可将文件上传到不同的 Azure 存储。 如果这样做，请创建 Azure 存储链接服务，将该存储帐户链接到数据工厂。 然后，将该链接的服务的名称指定为 **sparkJobLinkedService** 属性的值。 请参阅 [Spark 活动属性](#spark-activity-properties)，了解有关 Spark 活动支持的此属性及其他属性的详细信息。
- **entryFilePath** 设置为 **test.py**，这是 python 文件。 
- **getDebugInfo** 属性设置为 **Always**，表示始终生成日志文件（成功或失败）。  

    > [!IMPORTANT]
    > 在生产环境中，除非要排查问题，否则我们不建议将此属性设置为 Always。 
- **outputs** 节包含一个输出数据集。 必须指定一个输出数据集，即使 spark 程序不会生成任何输出。 输出数据集驱动管道的计划（每小时、每日，等等）。

有关该活动的详细信息，请参阅 [Spark 活动](data-factory-spark.md)一文。  

## <a name="machine-learning-batch-execution-activity"></a>机器学习批处理执行活动
可以在 Azure 机器学习批处理执行活动 JSON 定义中指定以下属性。 活动的类型属性必须是：**AzureMLBatchExecution**。 必须先创建 Azure 机器学习链接服务，然后指定该服务的名称作为 **linkedServiceName** 属性的值。 将活动类型设置为 AzureMLBatchExecution 时，**typeProperties** 节支持以下属性：

属性 | 说明 | 必选 
-------- | ----------- | --------
webServiceInput | 要作为 Azure 机器学习 Web 服务的输入传递的数据集。 此数据集也必须包含在活动的输入中。 |使用 webServiceInput 或 webServiceInputs。 | 
webServiceInputs | 指定要作为 Azure 机器学习 Web 服务的输入传递的数据集。 如果该 Web 服务采用多个输入，可以使用 webServiceInputs 属性，而不使用 webServiceInput 属性。 **webServiceInputs** 引用的数据集也必须包括在活动 **inputs** 中。 | 使用 webServiceInput 或 webServiceInputs。 | 
webServiceOutputs | 分配为 Azure 机器学习 Web 服务的输出的数据集。 Web 服务在此数据集中返回输出数据。 | 是 | 
globalParameters | 在此节中指定 Web 服务参数的值。 | 否 | 

### <a name="json-example"></a>JSON 示例
在此示例中，该活动使用数据集 **MLSqlInput** 作为输入，使用 **MLSqlOutput** 作为输出。 使用 **webServiceInput** JSON 属性将 **MLSqlInput** 作为输入传递给 Web 服务。 使用 **webServiceOutputs** JSON 属性将 **MLSqlOutput** 作为输出传递给 Web 服务。 

```json
{
   "name": "MLWithSqlReaderSqlWriter",
   "properties": {
      "description": "Azure ML model with sql azure reader/writer",
      "activities": [{
         "name": "MLSqlReaderSqlWriterActivity",
         "type": "AzureMLBatchExecution",
         "description": "test",
         "inputs": [ { "name": "MLSqlInput" }],
         "outputs": [ { "name": "MLSqlOutput" } ],
         "linkedServiceName": "MLSqlReaderSqlWriterDecisionTreeModel",
         "typeProperties":
         {
            "webServiceInput": "MLSqlInput",
            "webServiceOutputs": {
               "output1": "MLSqlOutput"
            },
            "globalParameters": {
               "Database server name": "<myserver>.database.windows.net",
               "Database name": "<database>",
               "Server user account name": "<user name>",
               "Server user account password": "<password>"
            }              
         },
         "policy": {
            "concurrency": 1,
            "executionPriorityOrder": "NewestFirst",
            "retry": 1,
            "timeout": "02:00:00"
         }
      }],
      "start": "2016-02-13T00:00:00",
       "end": "2016-02-14T00:00:00"
   }
}
```

在该 JSON 示例中，部署的 Azure 机器学习 Web 服务使用读取器和编写器模块从 Azure SQL 数据库读取数据，或将数据写入 Azure SQL 数据库。 此 Web 服务公开以下四个参数：数据库服务器名、数据库名、服务器用户帐户名和服务器用户帐户密码。

> [!NOTE]
> 仅 AzureMLBatchExecution 活动的输入和输出可作为参数传递给 Web 服务。 例如，在上面的 JSON 片段中，MLSqlInput 是通过 webServiceInput 参数作为输入传递给 Web 服务的 AzureMLBatchExecution 活动输入。

## <a name="machine-learning-update-resource-activity"></a>机器学习更新资源活动
可以在 Azure 机器学习更新资源活动 JSON 定义中指定以下属性。 活动的类型属性必须是：**AzureMLUpdateResource**。 必须先创建 Azure 机器学习链接服务，然后指定该服务的名称作为 **linkedServiceName** 属性的值。 将活动类型设置为 AzureMLUpdateResource 时，**typeProperties** 节支持以下属性：

属性 | 说明 | 必选 
-------- | ----------- | --------
trainedModelName | 重新训练的模型的名称。 | 是 |  
trainedModelDatasetName | 指向重新训练操作返回的 iLearner 文件的数据集。 | 是 | 

### <a name="json-example"></a>JSON 示例
管道具有两个活动：**AzureMLBatchExecution** 和 **AzureMLUpdateResource**。 Azure 机器学习批处理执行活动采用定型数据作为输入，并生成 iLearner 文件作为输出。 活动会通过输入定型数据调用定型 Web 服务（作为 Web 服务公开的训练实验），并从 Web 服务接收 ilearner 文件。 placeholderBlob 只是 Azure 数据工厂服务运行管道所需的虚拟输出数据集。


```json
{
    "name": "pipeline",
    "properties": {
        "activities": [
            {
                "name": "retraining",
                "type": "AzureMLBatchExecution",
                "inputs": [
                    {
                        "name": "trainingData"
                    }
                ],
                "outputs": [
                    {
                        "name": "trainedModelBlob"
                    }
                ],
                "typeProperties": {
                    "webServiceInput": "trainingData",
                    "webServiceOutputs": {
                        "output1": "trainedModelBlob"
                    }              
                 },
                "linkedServiceName": "trainingEndpoint",
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "02:00:00"
                }
            },
            {
                "type": "AzureMLUpdateResource",
                "typeProperties": {
                    "trainedModelName": "trained model",
                    "trainedModelDatasetName" :  "trainedModelBlob"
                },
                "inputs": [{ "name": "trainedModelBlob" }],
                "outputs": [{ "name": "placeholderBlob" }],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "AzureML Update Resource",
                "linkedServiceName": "updatableScoringEndpoint2"
            }
        ],
        "start": "2016-02-13T00:00:00",
        "end": "2016-02-14T00:00:00"
    }
}
```

## <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics U-SQL 活动
可以在 U-SQL 活动 JSON 定义中指定以下属性。 活动的类型属性必须是：**DataLakeAnalyticsU-SQL**。 必须创建 Azure Data Lake Analytics 链接服务，并指定该服务的名称作为 **linkedServiceName** 属性的值。 将活动类型设置为 DataLakeAnalyticsU-SQL 时，**typeProperties** 节支持以下属性： 

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| scriptPath |包含 U-SQL 脚本的文件夹路径。 文件的名称区分大小写。 |否（如果使用脚本） |
| scriptLinkedService |将包含脚本的存储链接到数据工厂的链接服务 |否（如果使用脚本） |
| 脚本 |指定内联脚本，而不是指定 scriptPath 和 scriptLinkedService。 例如："script": "CREATE DATABASE test"。 |否（如果使用 scriptPath 和 scriptLinkedService） |
| degreeOfParallelism |同时用于运行作业的最大节点数。 |否 |
| priority |确定应在所有排队的作业中选择哪些作业首先运行。 编号越低，优先级越高。 |否 |
| parameters |U-SQL 脚本的参数 |否 |

### <a name="json-example"></a>JSON 示例

```json
{
    "name": "ComputeEventsByRegionPipeline",
    "properties": {
        "description": "This pipeline computes events for en-gb locale and date less than Feb 19, 2012.",
        "activities": 
        [
            {
                "type": "DataLakeAnalyticsU-SQL",
                "typeProperties": {
                    "scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
                    "scriptLinkedService": "StorageLinkedService",
                    "degreeOfParallelism": 3,
                    "priority": 100,
                    "parameters": {
                        "in": "/datalake/input/SearchLog.tsv",
                        "out": "/datalake/output/Result.tsv"
                    }
                },
                "inputs": [
                    {
                        "name": "DataLakeTable"
                    }
                ],
                "outputs": 
                [
                    {
                        "name": "EventsByRegionTable"
                    }
                ],
                "policy": {
                    "timeout": "06:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "EventsByRegion",
                "linkedServiceName": "AzureDataLakeAnalyticsLinkedService"
            }
        ],
        "start": "2015-08-08T00:00:00",
        "end": "2015-08-08T01:00:00",
        "isPaused": false
    }
}
```

有关详细信息，请参阅 [Data Lake Analytics U-SQL 活动](data-factory-usql-activity.md)。 

## <a name="stored-procedure-activity"></a>存储过程活动
可以在存储过程活动 JSON 定义中指定以下属性。 活动的类型属性必须是：**SqlServerStoredProcedure**。 必须创建以下链接服务之一，并指定该链接服务的名称作为 **linkedServiceName** 属性的值：

- SQL Server 
- Azure SQL 数据库
- Azure SQL 数据仓库

将活动类型设置为 SqlServerStoredProcedure 时，**typeProperties** 节支持以下属性：

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| storedProcedureName |在 Azure SQL 数据库或 Azure SQL 数据仓库中指定存储过程的名称，用输出表使用的链接服务表示。 |是 |
| storedProcedureParameters |指定存储过程的参数值。 如果需要为参数传递 null，请使用语法："param1": null（全部小写）。 请参阅以下示例了解如何使用此属性。 |否 |

如果指定了输入数据集，则它必须可供使用（“就绪”状态），存储过程才能运行。 该输入数据集无法在存储过程中用作参数。 它仅用于在开始存储过程活动前检查依赖项。 必须指定存储过程活动的输出数据集。 

输出数据集为存储过程活动指定**计划**（每小时、每周、每月等）。 输出数据集必须使用**链接服务**，其指代 Azure SQL 数据库或 Azure SQL 数据仓库或要在其中运行存储过程的 SQL Server 数据库。 输出数据集可用于传递存储过程的结果，以供管道中另一活动（[链接活动](data-factory-scheduling-and-execution.md##multiple-activities-in-a-pipeline)）进行后续处理。 但是，数据工厂不会自动将存储过程的输出写入此数据集。 它是写入输出数据集指向的 SQL 表的存储过程。 在某些情况下，输出数据集可以是**虚拟数据集**，它仅用于指定运行存储过程活动的计划。  

### <a name="json-example"></a>JSON 示例

```json
{
    "name": "SprocActivitySamplePipeline",
    "properties": {
        "activities": [
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "sp_sample",
                    "storedProcedureParameters": {
                        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
                    }
                },
                "outputs": [{ "name": "sprocsampleout" }],
                "name": "SprocActivitySample"
            }
        ],
         "start": "2016-08-02T00:00:00",
         "end": "2016-08-02T05:00:00",
        "isPaused": false
    }
}
```

有关详细信息，请参阅[存储过程活动](data-factory-stored-proc-activity.md)一文。 

## <a name="net-custom-activity"></a>.NET 自定义活动
可以在 .NET 自定义活动 JSON 定义中指定以下属性。 活动的类型属性必须是：**DotNetActivity**。 必须创建一个 Azure HDInsight 链接服务或 Azure 批处理链接服务，并指定该链接服务的名称作为 **linkedServiceName** 属性的值。 将活动类型设置为 DotNetActivity 时，**typeProperties** 节支持以下属性：
 
| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| AssemblyName | 程序集的名称。 在本示例中，该名称为 **MyDotnetActivity.dll**。 | 是 |
| EntryPoint |实现 IDotNetActivity 接口的类名称。 在本示例中，该名称为 **MyDotNetActivityNS.MyDotNetActivity**，其中，MyDotNetActivityNS 是命名空间，MyDotNetActivity 是类。  | 是 | 
| PackageLinkedService | Azure 存储链接服务的名称，指向包含自定义活动 zip 文件的 Blob 存储。 在本示例中，该链接服务为 **AzureStorageLinkedService**。| 是 |
| PackageFile | zip 文件的名称。 在本示例中，该文件名为 **customactivitycontainer/MyDotNetActivity.zip**。 | 是 |
| extendedProperties | 可定义并传递给 .NET 代码的扩展属性。 在本示例中，**SliceStart** 变量设置为基于 SliceStart 系统变量的值。 | 否 | 

### <a name="json-example"></a>JSON 示例

```json
{
  "name": "ADFTutorialPipelineCustom",
  "properties": {
    "description": "Use custom activity",
    "activities": [
      {
        "Name": "MyDotNetActivity",
        "Type": "DotNetActivity",
        "Inputs": [
          {
            "Name": "InputDataset"
          }
        ],
        "Outputs": [
          {
            "Name": "OutputDataset"
          }
        ],
        "LinkedServiceName": "AzureBatchLinkedService",
        "typeProperties": {
          "AssemblyName": "MyDotNetActivity.dll",
          "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
          "PackageLinkedService": "AzureStorageLinkedService",
          "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
          "extendedProperties": {
            "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
          }
        },
        "Policy": {
          "Concurrency": 2,
          "ExecutionPriorityOrder": "OldestFirst",
          "Retry": 3,
          "Timeout": "00:30:00",
          "Delay": "00:00:00"
        }
      }
    ],
    "start": "2016-11-16T00:00:00",
    "end": "2016-11-16T05:00:00",
    "isPaused": false
  }
}
```

有关详细信息，请参阅[在数据工厂中使用自定义活动](data-factory-use-custom-activities.md)一文。 

## <a name="next-steps"></a>后续步骤
参阅以下教程： 

- [教程：创建包含复制活动的管道](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [教程：创建包含 Hive 活动的管道](data-factory-build-your-first-pipeline-using-editor.md)