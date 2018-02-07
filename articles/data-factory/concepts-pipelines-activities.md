---
title: "Azure 数据工厂中的管道和活动 | Microsoft Docs"
description: "了解有关 Azure 数据工厂中的管道和活动的信息。"
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
ms.date: 01/22/2018
ms.author: shlo
ms.openlocfilehash: f5384b4fa0a1baaafd8b2dbf3ed4d7776f4d80ea
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2018
---
# <a name="pipelines-and-activities-in-azure-data-factory"></a>Azure 数据工厂中的管道和活动 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [版本 1 - 正式版](v1/data-factory-create-pipelines.md)
> * [版本 2 - 预览版](concepts-pipelines-activities.md)

本文帮助你了解 Azure 数据工厂中的管道和活动，并帮助你利用它们为数据移动和数据处理方案构造端到端数据驱动工作流。

> [!NOTE]
> 本文适用于目前处于预览版的数据工厂版本 2。 如果使用数据工厂服务版本 1（即正式版 (GA)），请参阅 [数据工厂版本 1 中的管道](v1/data-factory-create-pipelines.md)。
> 
> 本文假定已浏览 [Azure 数据工厂简介](introduction.md)和[快速入门教程](quickstart-create-data-factory-powershell.md)。

## <a name="overview"></a>概述
数据工厂可以包含一个或多个数据管道。 “管道”是共同执行一项任务的活动的逻辑分组。 例如，管道可能包含一组引入和清理日志数据的活动，然后在 HDInsight 群集上启动 Spark 作业以分析日志数据。 这样做的好处是，通过管道可以将活动作为一个集来管理，而非单独管理每个活动。 例如，可以部署和计划管道，而不需单独对活动进行操作。  

管道中的活动定义对数据执行的操作。 例如，可使用复制活动将数据从本地 SQL Server 复制到 Azure Blob 存储。 然后，使用在 Azure HDInsight 群集上运行 Hive 脚本的 Hive 活动，将 Blob 存储中的数据处理/转换为生成输出数据。 最后，再使用一个复制活动将输出数据复制到 Azure SQL 数据仓库，基于该仓库构建紧商业智能 (BI) 报告解决方案。

数据工厂支持三种类型的活动：[数据移动活动](copy-activity-overview.md)、[数据转换活动](transform-data.md)和[控制活动](control-flow-web-activity.md)。 每个活动可获取零个或多个输入[数据集](concepts-datasets-linked-services.md)，并生成一个或多个输出[数据集](concepts-datasets-linked-services.md)。 下图显示了数据工厂中管道、活动和数据集之间的关系：

![数据集、活动和管道之间的关系](media/concepts-pipelines-activities/relationship-between-dataset-pipeline-activity.png)

输入数据集表示管道中活动的输入，输出数据集表示活动的输出。 数据集可识别不同数据存储（如表、文件、文件夹和文档）中的数据。 创建数据集后，可将其与管道中的活动一起使用。 例如，数据集可以是复制活动或 HDInsightHive 活动的输入/输出数据集。 有关数据集的详细信息，请参阅 [Azure 数据工厂中的数据集](concepts-datasets-linked-services.md)一文。

## <a name="data-movement-activities"></a>数据移动活动
数据工厂中的复制活动可以将数据从源数据存储复制到接收器数据存储。 数据工厂支持本部分中的表中列出的数据存储。 来自任何源的数据都可以写入到任何接收器。 单击某个数据存储即可了解如何将数据复制到该存储，以及如何从该存储复制数据。

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

有关详细信息，请参阅[复制活动 - 概述](copy-activity-overview.md)一文。

## <a name="data-transformation-activities"></a>数据转换活动
Azure 数据工厂支持以下转换活动，这些活动既可以单独添加到管道，也可以与其他活动关联在一起添加。

数据转换活动 | 计算环境
---------------------------- | -------------------
[Hive](transform-data-using-hadoop-hive.md) | HDInsight [Hadoop]
[Pig](transform-data-using-hadoop-pig.md) | HDInsight [Hadoop]
[MapReduce](transform-data-using-hadoop-map-reduce.md) | HDInsight [Hadoop]
[Hadoop 流式处理](transform-data-using-hadoop-streaming.md) | HDInsight [Hadoop]
[Spark](transform-data-using-spark.md) | HDInsight [Hadoop]
[机器学习活动：批处理执行和更新资源](transform-data-using-machine-learning.md) | Azure VM
[存储过程](transform-data-using-stored-procedure.md) | Azure SQL、Azure SQL 数据仓库或 SQL Server
[U-SQL](transform-data-using-data-lake-analytics.md) | Azure Data Lake Analytics

有关详细信息，请参阅[数据转换活动](transform-data.md)一文。 

## <a name="control-activities"></a>控制活动
支持以下控制流活动：

控制活动 | 说明
---------------- | -----------
[Execute Pipeline 活动](control-flow-execute-pipeline-activity.md) | Execute Pipeline 活动允许数据工厂管道调用另一个管道。
[ForEach 活动](control-flow-for-each-activity.md) | ForEach 活动在管道中定义重复的控制流。 此活动用于循环访问集合，并在循环中执行指定的活动。 此活动的循环实现类似于采用编程语言的 Foreach 循环结构。
[Web 活动](control-flow-web-activity.md) | Web 活动可用于从数据工厂管道调用自定义的 REST 终结点。 可以传递数据集和链接服务以供活动使用和访问。 
[Lookup 活动](control-flow-lookup-activity.md) | Lookup 活动可用于从任何外部源读取或查找记录/表名称/值。 此输出可进一步被后续活动所引用。 
[Get Metadata 活动](control-flow-get-metadata-activity.md) | GetMetadata 活动可用于检索 Azure 数据工厂中的任何数据的元数据。 
[Until 活动](control-flow-until-activity.md) | 实现类似于采用编程语言的 Do-Until 循环结构的 Do-Until 循环。 它在循环中将执行一组活动，直到与活动相关联的条件的计算结果为 true。 你可以在数据工厂中为 Until 活动指定超时值。
[If Condition 活动](control-flow-if-condition-activity.md) | If Condition 可用于基于计算结果为 true 或 false 的条件进行分支。 If Condition 活动可提供 if 语句在编程语言中提供相同的功能。 当条件计算结果为 `true` 时，它会计算一组活动，当条件计算结果为 `false` 时，它会计算另一组活动。
[Wait 活动](control-flow-wait-activity.md) | 在管道中使用等待活动时，管道将等待一段指定的时间，然后继续执行后续活动。 

## <a name="pipeline-json"></a>管道 JSON
下面介绍如何以 JSON 格式定义管道： 

```json
{
    "name": "PipelineName",
    "properties": 
    {
        "description": "pipeline description",
        "activities":
        [
        ],
        "parameters": {
         }
    }
}
```

标记 | 说明 | Type | 必选
--- | ----------- | ---- | --------
name | 管道的名称。 指定一个名称，它表示管道要执行的操作。 <br/><ul><li>最大字符数：260</li><li>必须以字母、数字或下划线 (_) 开头</li><li>•   不允许使用以下字符：“.”、“+”、“?”、“/”、“<”、“>”、“*”、“%”、“&”、“:”、“\”</li></ul> | String | 是
description | 指定描述管道用途的文本。 | String | 否
活动 | **activities** 节中可定义有一个或多个活动。 请参阅[活动 JSON](#activity-json) 一节，以了解有关活动 JSON 元素的详细信息。 | Array | 是
parameters | **参数**部分可在在管道内定义一个或多个参数，使你的管道能够灵活地重复使用。 | 列出 | 否

## <a name="activity-json"></a>活动 JSON
**activities** 节中可定义有一个或多个活动。 有两种主要的活动类型：执行活动和控制活动。

### <a name="execution-activities"></a>执行活动
执行活动包括[数据移动](#data-movement-activities)和[数据转换活动](#data-transformation-activities)。 它们具有以下顶级结构：

```json
{
    "name": "Execution Activity Name",
    "description": "description", 
    "type": "<ActivityType>",
    "typeProperties":
    {
    },
    "linkedServiceName": "MyLinkedService",
    "policy":
    {
    },
    "dependsOn":
    {
    }
}
```

下表描述了活动 JSON 定义中的属性：

标记 | 说明 | 必选
--- | ----------- | ---------
name | 活动的名称。 指定一个名称，它表示活动要执行的操作。 <br/><ul><li>最大字符数：260</li><li>必须以字母、数字或下划线 (_) 开头</li><li>不允许使用以下字符：“.”、“+”、“?”、“/”、“<”、“>”、“*”、“%”、“&”、“:”、“\” | 是</li></ul>
description | 描述活动用途的文本 | 是
type | 活动的类型。 有关不同的活动类型，请参阅[数据移动活动](#data-movement-activities)、[数据转换活动](#data-transformation-activities)和[控制活动](#control-activities)部分。 | 是
linkedServiceName | 活动使用的链接服务的名称。<br/><br/>活动可能需要你指定链接到所需计算环境的链接服务。 | 对 HDInsight 活动、Azure 机器学习批处理评分活动和存储过程活动是必需的。 <br/><br/>对其他活动均非必需
typeProperties | typeProperties 部分的属性取决于每个活动类型。 要查看活动的类型属性，请单击链接转到上一节中的活动。 | 否
policy | 影响活动运行时行为的策略。 该属性包括超时和重试行为。 如果未指定，将使用默认值。 有关详细信息，请参阅[活动策略](#activity-policy)部分。 | 否
dependsOn | 该属性用于定义活动依赖项，以及后续活动对以前活动的依赖方式。 有关详细信息，请参阅[活动依赖项](#activity-dependency) | 否

### <a name="activity-policy"></a>活动策略
影响活动运行时行为的策略，提供可配置性选项。 活动策略仅对执行活动可用。 

### <a name="activity-policy-json-definition"></a>活动策略 JSON 定义

```json
{
    "name": "MyPipelineName",
    "properties": {
      "activities": [
        {
          "name": "MyCopyBlobtoSqlActivity"
          "type": "Copy",
          "typeProperties": {
            ...
          },
         "policy": {
            "timeout": "00:10:00",
            "retry": 1,
            "retryIntervalInSeconds": 60
         }
        }
      ],
        "parameters": {
           ...
        }
    }
}
```
JSON 名称 | 说明 | 允许值 | 必选
--------- | ----------- | -------------- | --------
timeout | 指定活动运行的超时。 | Timespan | 不会。 默认超时为 7 天。
retry | 最大重试次数 | Integer | 不会。 默认值为 0
retryIntervalInSeconds | 重试之间的延迟（以秒为单位） | Integer | 不会。 默认为 20 秒

### <a name="control-activity"></a>控制活动
控制活动具有以下顶级结构：

```json
{
    "name": "Control Activity Name",
    "description": "description", 
    "type": "<ActivityType>",
    "typeProperties":
    {
    },
    "dependsOn":
    {
    }
}
```

标记 | 说明 | 必选
--- | ----------- | --------
name | 活动的名称。 指定一个名称，它表示活动要执行的操作。<br/><ul><li>最大字符数：260</li><li>必须以字母、数字或下划线 (_) 开头</li><li>不允许使用以下字符：“.”、“+”、“?”、“/”、“<”、“>”、“*”、“%”、“&”、“:”、“\” | 是</li><ul> 
description | 描述活动用途的文本 | 是
type | 活动的类型。 有关不同的活动类型，请参阅[数据移动活动](#data-movement-activities)、[数据转换活动](#data-transformation-activities)和[控制活动](#control-activities)部分。 | 是
typeProperties | typeProperties 部分的属性取决于每个活动类型。 要查看活动的类型属性，请单击链接转到上一节中的活动。 | 否
dependsOn | 该属性用于定义活动依赖项，以及后续活动对以前活动的依赖方式。 有关详细信息，请参阅[活动依赖项](#activity-dependency)。 | 否

### <a name="activity-dependency"></a>活动依赖项
活动依赖项定义后续活动对以前活动的依赖方式，确定是否继续执行下一个任务的条件。 活动可能依赖于具有不同依赖项条件的一个或多个以前的活动。 

不同的依赖项条件有：成功、失败、跳过和完成。

例如，如果管道具有活动 A -> 活动 B，则可能发生的不同情况是：

- 活动 B 对活动 A 具有的依赖项条件为**成功**：活动 B 仅在活动 A 的最终状态为成功时才运行
- 活动 B 对活动 A 具有的依赖项条件为**失败**：活动 B 仅在活动 A 的最终状态为失败时才运行
- 活动 B 对活动 A 具有的依赖项条件为**完成**：活动 B 在活动 A 的最终状态为成功或失败时运行
- 活动 B 对活动 A 具有的依赖项条件为**跳过**：活动 B 在活动 A 的最终状态为跳过时运行。 在活动 X -> 活动 Y -> 活动 Z 的情况下出现跳过，其中每个活动仅在以前的活动成功后才运行。 如果活动 X 失败，则活动 Y 的状态为“跳过”，因为它从不执行。 类似，活动 Z 的状态也为“跳过”。

#### <a name="example-activity-2-depends-on-the-activity-1-succeeding"></a>示例：活动 2 是否运行取决于活动 1 是否成功运行

```json
{
    "name": "PipelineName",
    "properties": 
    {
        "description": "pipeline description",
        "activities": [
         {
            "name": "MyFirstActivity",
            "type": "Copy",
            "typeProperties": {
            },
            "linkedServiceName": {
            }
        },
        {
            "name": "MySecondActivity",
            "type": "Copy",
            "typeProperties": {
            },
            "linkedServiceName": {
            },
            "dependsOn": [
            {
                "activity": "MyFirstActivity",
                "dependencyConditions": [
                    "Succeeded"
                ]
            }
          ]
        }
      ],
      "parameters": {
       }
    }
}

```

## <a name="sample-copy-pipeline"></a>复制管道示例
在以下示例管道中，**activities** 节有一个 **Copy** 类型的活动。 在此示例中，[复制活动](copy-activity-overview.md)将 Azure Blob 存储中的数据复制到 Azure SQL 数据库。

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
        "policy": {
          "retry": 2,
          "timeout": "01:00:00"
        }
      }
    ]
  }
} 
```
注意以下几点：

- 在 activities 节中，只有一个活动的 **type** 设置为 **Copy**。
- 活动的输入设置为 **InputDataset**，活动的输出设置为 **OutputDataset**。 有关在 JSON 中定义数据集的信息，请参阅[数据集](concepts-datasets-linked-services.md)文章。
- 在 **typeProperties** 节中，**BlobSource** 指定为源类型，**SqlSink** 指定为接收器类型。 在[数据移动活动](#data-movement-activities)部分，单击想要用作源或接收器的数据存储，以了解有关将数据移动到该数据存储或从该数据存储移出的详细信息。

有关创建此管道的完整演练，请参阅[快速入门：创建数据工厂](quickstart-create-data-factory-powershell.md)。

## <a name="sample-transformation-pipeline"></a>转换管道示例
在以下示例管道中，**activities** 节有一个 **HDInsightHive** 类型的活动。 在此示例中，[HDInsight Hive 活动](transform-data-using-hadoop-hive.md)通过在 Azure HDInsight Hadoop 群集上运行 Hive 脚本文件，转换 Azure Blob 存储中的数据。

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
                    "retry": 3
                },
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "HDInsightOnDemandLinkedService"
            }
        ]
    }
}
```
注意以下几点：

- 在 activities 节中，只有一个活动的 **type** 设置为 **HDInsightHive**。
- 配置单元脚本文件，**partitionweblogs.hql**，被存储在 Azure 存储帐户中（由 scriptLinkedService 指定，调用 AzureStorageLinkedService），并位于容器 `adfgetstarted` 中的脚本文件夹中。
- `defines` 部分用于指定以配置单元配置值传递到配置单元脚本的运行时设置（例如，$`{hiveconf:inputtable}`，`${hiveconf:partitionedtable}`）。

每个转换活动的 typeProperties 节都不同。 若要了解有关转换活动所支持的类型属性的详细信息，请单击[数据转换活动](#data-transformation-activities)中的转换活动。

有关创建此管道的完整演练，请参阅[教程：使用 Spark 转换数据](tutorial-transform-data-spark-powershell.md)。 

## <a name="multiple-activities-in-a-pipeline"></a>管道中的多个活动
前两个示例管道中只有一个活动。 可在管道中添加多个活动。 如果在管道中具有多个活动且后续活动不依赖于以前的活动，则活动可能并行运行。 

可以使用[活动依赖项](#activity-dependency)将两个活动进行链接，以定义后续活动对以前活动的依赖方式，确定是否继续执行下一个任务的条件。 活动可以保护具有不同依赖项条件的一个或多个以前的活动。 

## <a name="scheduling-pipelines"></a>计划管道
管道由触发器计划 有不同类型的触发器（允许管道在时钟计划上触发的计划触发器，以及按需触发管道的手动触发器）。 有关触发器的详细信息，请参阅[管道执行和触发器](concepts-pipeline-execution-triggers.md)一文。 

若要使你的触发器启动管道运行，必须包含对触发器定义中的特定管道的管道引用。 管道和触发器具有 n-m 关系。 多个触发器可以启动单个管道，并且同一个触发器可以启动多个管道。 定义管道后，必须启动触发器，以使其开始触发管道。 有关触发器的详细信息，请参阅[管道执行和触发器](concepts-pipeline-execution-triggers.md)一文。 

例如，假设有一个计划触发器“触发器 A”，我希望使用该触发器启动我的管道“MyCopyPipeline”。 定义该触发器，如以下示例中所示：

### <a name="trigger-a-definition"></a>触发器 A 定义

```json
{
  "name": "TriggerA",
  "properties": {
    "type": "ScheduleTrigger",
    "typeProperties": {
      ...
      }
    },
    "pipeline": {
      "pipelineReference": {
        "type": "PipelineReference",
        "referenceName": "MyCopyPipeline"
      },
      "parameters": {
        "copySourceName": "FileSource"
      }
    }
  }
}
```



## <a name="next-steps"></a>后续步骤
参阅以下教程，了解创建包含以下活动的管道的分步说明： 

- [构建包含复制活动的管道](quickstart-create-data-factory-powershell.md)
- [使用数据转换活动生成管道](tutorial-transform-data-spark-powershell.md)
