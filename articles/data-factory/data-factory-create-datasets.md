---
title: "在 Azure 数据工厂中创建数据集 | Microsoft Docs"
description: "了解如何通过使用 offset 和 anchorDateTime 等属性的示例在 Azure 数据工厂中创建数据集。"
keywords: "创建数据集, 数据集示例, 偏移示例"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: 0614cd24-2ff0-49d3-9301-06052fd4f92a
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2016
ms.author: shlo
translationtype: Human Translation
ms.sourcegitcommit: 6ec8ac288a4daf6fddd6d135655e62fad7ae17c2
ms.openlocfilehash: 2ed6b838608f0f2249ef16b62ff2fb0159fc6e7f


---
# <a name="datasets-in-azure-data-factory"></a>Azure 数据工厂中的数据集
本文介绍 Azure 数据工厂中的数据集，包括偏移、anchorDateTime 和偏移/样式数据库等示例。

创建数据集时，将创建一个指向要处理的数据的指针。 数据在活动中进行处理（输入/输出），而活动包含在管道中。 输入数据集表示管道中活动的输入，输出数据集表示活动的输出。

数据集可识别不同数据存储（如表、文件、文件夹和文档）中的数据。 创建数据集后，可将其与管道中的活动一起使用。 例如，数据集可以是复制活动或 HDInsightHive 活动的输入/输出数据集。 Azure 门户提供了一种可视布局，其中包括所有管道以及数据输入和输出。 你可一览所有源中管道的所有关系和依赖关系，这样就能始终知道数据的来源和去处。

在 Azure 数据工厂中，可使用管道中的复制活动从数据集获取数据。

> [!NOTE]
> 如果对 Azure 数据工厂不熟悉，请参阅 [Azure 数据工厂简介](data-factory-introduction.md)，查看 Azure 数据工厂服务的概述。 如需了解如何创建数据工厂，请参阅[构建第一个数据工厂](data-factory-build-your-first-pipeline.md)一文。 这两篇文章介绍了相关的背景信息，有助于你更好地理解此文。
>
>

## <a name="define-datasets"></a>定义数据集
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
| name |数据集名称。 若要了解命名规则，请参阅 [Azure 数据工厂 - 命名规则](data-factory-naming-rules.md)。 |是 |不可用 |
| type |数据集的类型。 指定 Azure 数据工厂支持的类型之一（例如：AzureBlob、AzureSqlTable）。 <br/><br/>有关详细信息，请参阅[数据集类型](#Type)。 |是 |不可用 |
| structure |数据集架构<br/><br/>有关详细信息，请参阅[数据集结构](#Structure)部分。 |不会。 |不可用 |
| typeProperties |对应于所选类型的属性。 有关受支持的类型及其属性的详细信息，请参阅[数据集类型](#Type)部分。 |是 |不可用 |
| external |布尔标志，用于指定数据集是否由数据工厂管道显式生成。 |否 |false |
| availability |定义数据集生产的处理窗口或切片模型。 <br/><br/>有关详细信息，请参阅[数据集可用性](#Availability)部分。 <br/><br/>有关数据集切片模型的详细信息，请参阅[计划和执行](data-factory-scheduling-and-execution.md)一文。 |是 |不可用 |
| policy |定义数据集切片必须满足的标准或条件。 <br/><br/>有关详细信息，请参阅[数据集策略](#Policy)部分。 |否 |不可用 |

## <a name="dataset-example"></a>数据集示例
在以下示例中，数据集表示 **Azure SQL 数据库**中名为 **MyTable** 的表。

```json
{
    "name": "DatasetSample",
    "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties":
        {
            "tableName": "MyTable"
        },
        "availability":
        {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

请注意以下几点：

* type 设置为 AzureSqlTable。
* tableName 类型属性（特定于 AzureSqlTable 类型）设置为 MyTable。
* linkedServiceName 指的是 AzureSqlDatabase 类型的链接服务。 请参阅以下链接服务的定义。
* availability frequency 设置为“Day”且 interval 设置为 1，意味着每天都会生成切片。  

AzureSqlLinkedService 定义如下：

```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "description": "",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>@<servername>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30"
        }
    }
}
```

在上述 JSON 中：

* type 设置为 AzureSqlDatabase
* connectionString 类型属性指定连接到 Azure SQL 数据库的信息。  

如你所见，链接服务定义如何连接到 Azure SQL 数据库。 数据集定义将哪个表用作管道中活动的输入/输出。 [管道](data-factory-create-pipelines.md) JSON 中的活动部分指定数据集是用作输入数据集还是输出数据集。

> [!IMPORTANT]
> 除非将由 Azure 数据工厂生成数据集，否则应将其标记为 **external**。 此设置通常适用于管道中第一个活动的输入。   
>
>

## <a name="a-nametypea-dataset-type"></a><a name="Type"></a>数据集类型
支持的数据源和数据集类型是一致的。 有关数据集类型和配置的信息，请参阅[数据移动活动](data-factory-data-movement-activities.md#supported-data-stores-and-formats)一文中引用的主题。 例如，如果使用 Azure SQL 数据库中的数据，可单击支持的数据存储列表中的 Azure SQL 数据库以查看详细信息。  

## <a name="a-namestructureadataset-structure"></a><a name="Structure"></a>数据集结构
**结构**部分定义数据集的架构。 它包含列的名称和数据类型的集合。  在以下示例中，数据集具有三列 slicetimestamp、projectname 和 pageviews，它们的类型分别为：String、String 和 Decimal。

```json
structure:  
[
    { "name": "slicetimestamp", "type": "String"},
    { "name": "projectname", "type": "String"},
    { "name": "pageviews", "type": "Decimal"}
]
```

## <a name="a-nameavailabilitya-dataset-availability"></a><a name="Availability"></a>数据集的可用性
数据集中的**可用性**部分定义了处理时段（每小时、每天和每周等）或数据集的切片模型。 有关数据集切片和依赖关系模型的详细信息，请参阅[计划和执行](data-factory-scheduling-and-execution.md)一文。

以下可用性部分指定每小时生成输出数据集或每小时提供输入数据集：

```json
"availability":    
{    
    "frequency": "Hour",        
    "interval": 1    
}
```

下表介绍了可用于可用性部分的属性：

| 属性 | 说明 | 必选 | 默认 |
| --- | --- | --- | --- |
| frequency |指定数据集切片生成的时间单位。<br/><br/>**支持的频率**：Minute、Hour、Day、Week、Month |是 |不可用 |
| interval |指定频率的乘数<br/><br/>“频率 x 间隔”确定生成切片的频率。<br/><br/>若需要数据集每小时生成切片，请将“Frequency”设置为“Hour”，将“interval”设置为“1”。<br/><br/>**注意：**如果将 Frequency 指定为 Minute，建议将 interval 设置为小于 15 的值 |是 |不可用 |
| style |指定是否应在间隔的开头/结尾生成切片。<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>若将 Month 设置为 Month，style 设置为 EndOfInterval，则将在每月的最后一天生成切片。 若将 style 设为 StartOfInterval，将在每月的第一天生成切片。<br/><br/>若将 Frequency 设置为 Day，style 设置为 EndOfInterval，则将在一天的最后一小时生成切片。<br/><br/>若将 Frequency 设置为 Hour，style 设置为 EndOfInterval，则将在一小时结束时生成切片。 例如，对于下午 1 点到下午 2 点期间的切片，则在下午 2 点生成切片。 |否 |EndOfInterval |
| anchorDateTime |定义计划程序用于计算数据集切片边界的时间中的绝对位置。 <br/><br/>**注意：**如果 AnchorDateTime 的日期部分比频率部分更精细，则忽略更精细的部分。 <br/><br/>例如，如果“interval”是“每小时”（frequency: hour 且 interval: 1），而 ** AnchorDateTime** 包含**分钟和秒**，则将忽略 AnchorDateTime 的**分钟和秒**部分。 |否 |01/01/0001 |
| offset |所有数据集切片的开始和结束之间偏移的时间跨度。 <br/><br/>**注意：**如果同时指定了 anchorDateTime 和 offset ，则结果是组合偏移。 |否 |不可用 |

### <a name="offset-example"></a>偏移示例
从早上 6 点（非默认值 - 午夜）开始的每日切片。

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```

“frequency”设置为“Day”，“interval”设置为“1”（每天一次）：如果要在早上 6 点生成切片（而非默认时间：午夜 12 点）。 请记住此时间为 UTC 时间。

## <a name="anchordatetime-example"></a>anchorDateTime 示例
**示例：**从 2007-04-19T08:00:00 开始的 23 个小时的数据集切片

```json
"availability":    
{    
    "frequency": "Hour",        
    "interval": 23,    
    "anchorDateTime":"2007-04-19T08:00:00"    
}
```

## <a name="offsetstyle-example"></a>偏移/样式示例
如果需要每月特定日期和时间（假设每月第三天的上午 8:00）的数据集，请使用 **offset** 标记来设置应该运行的日期和时间。

```json
{
  "name": "MyDataset",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "availability": {
      "frequency": "Month",
      "interval": 1,
      "offset": "3.08:10:00",
      "style": "StartOfInterval"
    }
  }
}
```

## <a name="a-namepolicyadataset-policy"></a><a name="Policy"></a>数据集策略
数据集定义中的**策略**部分定义了数据集切片必须满足的标准或条件。

### <a name="validation-policies"></a>验证策略
| 策略名称 | 说明 | 适用对象 | 必选 | 默认 |
| --- | --- | --- | --- | --- |
| minimumSizeMB |验证 **Azure Blob** 中的数据是否满足最小大小要求（以兆字节为单位）。 |Azure Blob |否 |不可用 |
| minimumRows |验证 **Azure SQL 数据库**中的数据或 **Azure 表**是否包含最小行数。 |<ul><li>Azure SQL 数据库</li><li>Azure 表</li></ul> |否 |不可用 |

#### <a name="examples"></a>示例
**minimumSizeMB：**

```json
"policy":

{
    "validation":
    {
        "minimumSizeMB": 10.0
    }
}
```

**minimumRows**

```json
"policy":
{
    "validation":
    {
        "minimumRows": 100
    }
}
```

### <a name="external-datasets"></a>外部数据集
外部数据集是不由数据工厂中的运行管道生成的数据集。 如果数据集标记为 **external**，则可定义 **ExternalData** 策略来影响数据集切片可用性的行为。

除非将由 Azure 数据工厂生成数据集，否则应将其标记为 **external**。 此设置通常适用于管道中第一个活动的输入，除非正在使用活动或管道链接。

| Name | 说明 | 必选 | 默认值 |
| --- | --- | --- | --- |
| dataDelay |延迟检查给定切片外部数据的可用性的时间。 例如，如果数据应为每小时可用，可使用 dataDelay 推迟检查外部数据是否可用以及相应切片是否就绪。<br/><br/>仅适用于当前时间。  例如，如果现在是下午 1:00 且此值为 10 分钟，则从下午 1:10 开始验证。<br/><br/>此设置不会影响过去的切片 (End Time + dataDelay < Now)，过去的切片不会有任何延迟。<br/><br/>大于 23:59 小时的时间需要使用 day.hours:minutes:seconds 格式进行指定。 例如，若要指定 24 小时，请不要使用 24:00:00；请改用 1.00:00:00。 如果使用 24:00:00，则将它视为 24 天 (24.00:00:00)。 对于 1 天又 4 小时，请指定 1:04:00:00。 |否 |0 |
| retryInterval |失败与下一次重试之间的等待时间。 适用于当前时间；如果上一次尝试失败，我们将在最后一次尝试后等待这么长时间。 <br/><br/>如果现在是下午 1:00，我们将开始第一次尝试。 如果完成第一次验证检查的持续时间为 1 分钟，并且操作失败，则下一次重试为 1:00 + 1 分钟（持续时间）+ 1 分钟（重试间隔）= 下午 1:02。 <br/><br/>对于过去的切片，没有任何延迟。 重试会立即发生。 |否 |00:01:00（1 分钟） |
| retryTimeout |每次重试尝试的超时。<br/><br/>如果此属性设置为 10 分钟，则验证需要在 10 分钟内完成。 如果执行验证所需的时间超过 10 分钟，则重试超时。<br/><br/>如果验证的所有尝试超时，则将切片标记为 TimedOut。 |否 |00:10:00（10 分钟） |
| maximumRetry |检查外部数据可用性的次数。 允许的最大值为 10。 |否 |3 |

## <a name="scoped-datasets"></a>指定了作用域的数据集
可使用 **datasets** 属性创建作用域指定到管道的数据集。 这些数据集只能由此管道中的活动使用，而不能由其他管道中的活动使用。 以下示例使用要用于管道的两个数据集（InputDataset-rdc 和 OutputDataset-rdc）定义管道：  

> [!IMPORTANT]
> 仅对指定了作用域的数据集提供一次性管道（**pipelineMode** 设置为 **OneTime**）。 有关详细信息，请参阅[一次性管道](data-factory-scheduling-and-execution.md#onetime-pipeline)。
>
>

```json
{
    "name": "CopyPipeline-rdc",
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
                        "name": "InputDataset-rdc"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataset-rdc"
                    }
                ],
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1,
                    "style": "StartOfInterval"
                },
                "name": "CopyActivity-0"
            }
        ],
        "start": "2016-02-28T00:00:00Z",
        "end": "2016-02-28T00:00:00Z",
        "isPaused": false,
        "pipelineMode": "OneTime",
        "expirationTime": "15.00:00:00",
        "datasets": [
            {
                "name": "InputDataset-rdc",
                "properties": {
                    "type": "AzureBlob",
                    "linkedServiceName": "InputLinkedService-rdc",
                    "typeProperties": {
                        "fileName": "emp.txt",
                        "folderPath": "adftutorial/input",
                        "format": {
                            "type": "TextFormat",
                            "rowDelimiter": "\n",
                            "columnDelimiter": ","
                        }
                    },
                    "availability": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "external": true,
                    "policy": {}
                }
            },
            {
                "name": "OutputDataset-rdc",
                "properties": {
                    "type": "AzureBlob",
                    "linkedServiceName": "OutputLinkedService-rdc",
                    "typeProperties": {
                        "fileName": "emp.txt",
                        "folderPath": "adftutorial/output",
                        "format": {
                            "type": "TextFormat",
                            "rowDelimiter": "\n",
                            "columnDelimiter": ","
                        }
                    },
                    "availability": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "external": false,
                    "policy": {}
                }
            }
        ]
    }
}
```


<!--HONumber=Nov16_HO3-->


