---
title: "使用数据工厂计划和执行 | Microsoft Docs"
description: "了解 Azure 数据工厂应用程序模型的计划和执行方面。"
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 088a83df-4d1b-4ac1-afb3-0787a9bd1ca5
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: spelluru
robots: noindex
ms.openlocfilehash: 22005f6b0f655a68dbf8fab600872d8d20102dfe
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2018
---
# <a name="data-factory-scheduling-and-execution"></a>数据工厂计划和执行
> [!NOTE]
> 本文适用于数据工厂版本 1（正式版 (GA)）。 如果使用数据工厂服务版本 2（即预览版），请参阅[管道执行和触发器](../concepts-pipeline-execution-triggers.md)一文。

此文介绍 Azure 数据工厂应用程序模型的计划和执行方面。 本文假定用户了解数据工厂应用程序模型概念的基础知识（包括活动、管道、链接服务和数据集）。 有关的 Azure 数据工厂的基本概念，请参阅以下文章：

* [数据工厂简介](data-factory-introduction.md)
* [管道](data-factory-create-pipelines.md)
* [数据集](data-factory-create-datasets.md) 

## <a name="start-and-end-times-of-pipeline"></a>管道的开始和结束时间
仅在**开始**时间和**结束**时间之间，管道才处于活动状态。 开始时间之前或结束时间之后，不会执行管道。 如果暂停管道，则无论开始和结束时间，都不会执行管道。 不暂停才可运行管道。 可以在管道定义中找到这些设置（开始、结束、暂停）： 

```json
"start": "2017-04-01T08:00:00Z",
"end": "2017-04-01T11:00:00Z"
"isPaused": false
```

有关这些属性的详细信息，请参阅[创建管道](data-factory-create-pipelines.md)一文。 


## <a name="specify-schedule-for-an-activity"></a>为活动指定计划
执行的不是管道。 它是在管道的总体上下文中执行的管道中的活动。 可以使用活动 JSON 的**计划程序**部分指定活动的定期计划。 例如，可将活动计划为每小时运行一次，如下所示：  

```json
"scheduler": {
    "frequency": "Hour",
    "interval": 1
},
```

如下图中所示，指定为活动计划创建一系列翻转与窗口在管道开始和结束时间。 翻转时段是一系列固定大小、非重叠、连续的时间间隔。 活动的这些逻辑翻转时段称为“活动时段”。

![活动计划程序示例](media/data-factory-scheduling-and-execution/scheduler-example.png)

活动的**计划程序**属性是可选的。 如果要指定一个属性，它必须与在输出数据集定义中指定的频率匹配。 目前，输出数据集驱动计划。 因此，必须创建一个输出数据集，即使活动不会生成任何输出。 

## <a name="specify-schedule-for-a-dataset"></a>为数据集指定计划
数据工厂管道中的每个活动可获取零个或多个输入**数据集**，并生成一个或多个输出数据集。 对于活动，可以指定从该处有可用的输入的数据或使用生成的输出数据的频率**可用性**的数据集定义中的部分。 

**可用性**部分中的**频率**指定时间单位。 频率的允许值为：分钟、小时、天、周和月。 可用性部分中的**间隔**属性指定频率的乘数。 例如：如果频率设置为“天”且间隔设置为 1 的输出数据集，每天生成输出数据。 如果将频率指定为分钟，建议将间隔设置为小于 15 的值。 

在下面的示例中，输入有可用的数据每小时和每小时生成一次输出数据 (`"frequency": "Hour", "interval": 1`)。 

**输入数据集：** 

```json
{
    "name": "AzureSqlInput",
    "properties": {
        "published": false,
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```


**输出数据集**

```json
{
    "name": "AzureBlobOutput",
    "properties": {
        "published": false,
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mypath/{Year}/{Month}/{Day}/{Hour}",
            "format": {
                "type": "TextFormat"
            },
            "partitionedBy": [
                { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
                { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
                { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
                { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "HH" }}
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

目前，**输出数据集驱动计划**。 换而言之，使用指定的输出数据集的计划在运行时运行的活动。 因此，必须创建一个输出数据集，即使活动不会生成任何输出。 如果活动没有任何输入，可以跳过创建输入数据集。 

在下面的管道定义中，**计划程序**属性用于指定活动的计划。 此属性是可选的。 目前，该活动的计划必须匹配指定的输出数据集的计划。
 
```json
{
    "name": "SamplePipeline",
    "properties": {
        "description": "copy activity",
        "activities": [
            {
                "type": "Copy",
                "name": "AzureSQLtoBlob",
                "description": "copy activity",
                "typeProperties": {
                    "source": {
                        "type": "SqlSource",
                        "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 100000,
                        "writeBatchTimeout": "00:05:00"
                    }
                },
                "inputs": [
                    {
                        "name": "AzureSQLInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }
        ],
        "start": "2017-04-01T08:00:00Z",
        "end": "2017-04-01T11:00:00Z"
    }
}
```

在此示例中，活动运行每小时之间的管道的开始和结束时间。 将在三个小时的时间范围内（上午 8-9 点、上午 9-10 点和上午 10-11 点），每小时生成输出数据。 

活动运行使用或生成的每个数据单元称为**数据切片**。 下图显示了具有 1 个输入数据集和 1 个输出数据集的活动示例： 

![可用性计划程序](./media/data-factory-scheduling-and-execution/availability-scheduler.png)

上图中显示了输入和输出数据集的每小时数据切片。 图中还显示已准备好进行处理的三个输入切片。 “上午 10-11 点”活动正在进行，生成“上午 10-11 点”输出切片。 

可通过变量 [SliceStart](data-factory-functions-variables.md#data-factory-system-variables) 和 [SliceEnd](data-factory-functions-variables.md#data-factory-system-variables) 访问与正在数据集 JSON 中生成的当前切片关联的时间间隔。 同样，可以使用 WindowStart 和 WindowEnd 访问与活动窗口关联的时间间隔。 活动的计划必须匹配活动的输出数据集的计划。 因此，SliceStart 和 SliceEnd 值分别与 WindowStart 和 WindowEnd 值相同。 有关这些变量的详细信息，请参阅[数据工厂函数和系统变量](data-factory-functions-variables.md#data-factory-system-variables)一文。  

可在活动 JSON 中将这些变量用于不同目的。 例如，可用于从表示时序数据的输入和输出数据集中选择数据（例如：上午 8-9 点）。 此示例还使用 **WindowStart** 和 **WindowEnd** 选择活动运行的相关数据，并使用相应 **folderPath** 将其复制到 blob。 **FolderPath** 参数化为每小时具有一个单独文件夹。  

在前面的示例中，计划指定为输入和输出数据集是相同的（每小时）。 如果活动的输入数据集位于不同的频率，假设每隔 15 分钟，则生成此输出数据集的活动都将仍运行每小时一次，如下所输出数据集是什么驱动器活动计划。 有关详细信息，请参阅[具有不同频率的模型数据集](#model-datasets-with-different-frequencies)。

## <a name="dataset-availability-and-policies"></a>数据集可用性和策略
在数据集定义的可用性部分中，已了解频率和间隔属性。 有几个其他属性会影响活动的计划和执行。 

### <a name="dataset-availability"></a>数据集可用性 
下表描述了可在 **availability** 节中使用的属性：

| 属性 | 说明 | 必选 | 默认 |
| --- | --- | --- | --- |
| 频率 |指定数据集切片生成的时间单位。<br/><br/><b>支持的频率</b>：Minute、Hour、Day、Week、Month |是 |不可用 |
| interval |指定频率的乘数<br/><br/>“频率 x 间隔”确定生成切片的频率。<br/><br/>若需要数据集每小时生成切片，请将“Frequency”<b></b>设置为“Hour”<b></b>，将“interval”<b></b>设置为“1”<b></b>。<br/><br/><b>注意</b>：如果将 Frequency 指定为 Minute，建议将 interval 设置为小于 15 的值 |是 |不可用 |
| style |指定是否应在间隔的开头/结尾生成切片。<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>若将 Month 设置为 Month，style 设置为 EndOfInterval，则会在每月的最后一天生成切片。 若将 style 设为 StartOfInterval，会在每月的第一天生成切片。<br/><br/>若将 Frequency 设置为 Day，style 设置为 EndOfInterval，则会在一天的最后一小时生成切片。<br/><br/>若将 Frequency 设置为 Hour，style 设置为 EndOfInterval，则会在一小时结束时生成切片。 例如，对于下午 1 点到下午 2 点期间的切片，则在下午 2 点生成切片。 |否 |EndOfInterval |
| anchorDateTime |定义计划程序用于计算数据集切片边界的时间中的绝对位置。 <br/><br/><b>注意</b>：如果 AnchorDateTime 的日期部分比频率部分更精细，则忽略更精细部分。 <br/><br/>例如，如果“interval”<b></b>是“每小时”<b></b>（frequency: hour 且 interval: 1），而 <b> AnchorDateTime</b> 包含<b>分钟和秒</b>，则将忽略 AnchorDateTime 的<b>分钟和秒</b>部分。 |否 |01/01/0001 |
| offset |所有数据集切片的开始和结束之间偏移的时间跨度。 <br/><br/><b>注意</b>：如果同时指定了 anchorDateTime 和 offset，则结果是组合偏移。 |否 |不可用 |

### <a name="offset-example"></a>偏移示例
默认情况下，每天 (`"frequency": "Day", "interval": 1`) 在 UTC 时间晚上 12 点（午夜）开始切片。 要将开始时间改为 UTC 时间早上 6 点，请按以下片段中所示设置偏移量： 

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
### <a name="anchordatetime-example"></a>anchorDateTime 示例
下例中，每 23 小时生成一次数据集。 第一个切片在 anchorDateTime 指定的时间启动，该时间设置为 `2017-04-19T08:00:00`（UTC 时间）。

```json
"availability":    
{    
    "frequency": "Hour",        
    "interval": 23,    
    "anchorDateTime":"2017-04-19T08:00:00"    
}
```

### <a name="offsetstyle-example"></a>偏移/样式示例
以下数据集是每月数据集，在每月第三天上午 8:00 (`3.08:00:00`) 生成：

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "offset": "3.08:00:00", 
    "style": "StartOfInterval"
}
```

### <a name="dataset-policy"></a>数据集策略
数据集可以具有定义的验证策略，该策略指定切片执行生成的数据在准备好进行使用之前应如何验证。 在这种情况下，切片执行完成后，输出切片状态将变为“等待”且子状态为“验证”。 切片验证后，切片状态将更改为“就绪”。 如果数据切片已生成但没有通过验证，因此将不会处理依赖于此切片的下游切片的活动运行。 [监视和管理管道](data-factory-monitor-manage-pipelines.md)介绍数据工厂中的数据切片的各种状态。

数据集定义中的**策略**部分定义了数据集切片必须满足的标准或条件。 下表描述了可在 **policy** 节中使用的属性：

| 策略名称 | 说明 | 适用对象 | 必选 | 默认 |
| --- | --- | --- | --- | --- |
| minimumSizeMB | 验证 **Azure Blob** 中的数据是否满足最小大小要求（以兆字节为单位）。 |Azure Blob |否 |不可用 |
| minimumRows | 验证 **Azure SQL 数据库**中的数据或 **Azure 表**是否包含最小行数。 |<ul><li>Azure SQL 数据库</li><li>Azure 表</li></ul> |否 |不可用 |

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

有关这些属性的详细信息和示例，请参阅[创建数据集](data-factory-create-datasets.md)一文。 

## <a name="activity-policies"></a>活动策略
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

有关详细信息，请参阅[管道](data-factory-create-pipelines.md)一文。 

## <a name="parallel-processing-of-data-slices"></a>并行处理数据切片
可设置过去管道的开始日期。 执行此操作时，数据工厂会自动计算（回填）过去的所有数据切片，并开始处理。 例如：如果创建开始日期为 2017-04-01 的管道，而当前日期是 2017-04-10。 如果输出数据集的频率为每日，则数据工厂将立即开始处理所有从 2017-04-01 到 2017-04-09 的切片，因为开始日期已过去。 从自 2017-04-10 未处理切片尚未因为可用性部分中的样式属性的值是 EndOfInterval 默认情况下。 处理最旧的切片 executionPriorityOrder 值是 OldestFirst 首先为默认值。 有关样式属性的说明，请参阅[数据集可用性](#dataset-availability)部分。 有关 executionPriorityOrder 部分的说明，请参阅[活动策略](#activity-policies)部分。 

通过在活动 JSON 的**策略**部分中设置**并发**属性，用户可将回填数据切片配置为并行处理。 此属性决定可在不同切片上发生的并行活动执行次数。 并发性属性的默认值为 1。 因此，一个切片默认被处理一次。 最大值为 10。 当管道需要完成大量可用数据，更大的并发值能加快数据处理速度。 

## <a name="rerun-a-failed-data-slice"></a>重新运行失败的数据切片
如果处理数据切片时出错，可以通过 Azure 门户边栏选项卡或监视器和管理应用程序的切片了解处理失败的原因。 有关详细信息，请参阅[使用 Azure 门户边栏选项卡监视和管理管道](data-factory-monitor-manage-pipelines.md)或[监视和管理应用](data-factory-monitor-manage-app.md)。

请参考以下示例，其中演示了两个活动。 Activity1 和 Activity2。 Activity1 使用 Dataset1 的切片，并生成 Dataset2，作为输入由 Activity2 以生成最终的数据集的切片的切片。

![失败的切片](./media/data-factory-scheduling-and-execution/failed-slice.png)

该图显示在最近的三个片段中，为 Dataset2 生成“上午 9-10 点”切片时失败。 数据工厂自动跟踪时序数据集的依赖项。 因此，未启动“上午 9-10 点”下游切片的活动运行。

使用数据工厂监视和管理工具可以深入查看失败切片的诊断日志，从而轻松找到问题的根本原因并进行修复。 修复问题后，便可轻松地启动活动运行以生成失败切片。 有关如何重新运行和了解数据切片的状态转换的详细信息，请参阅[使用 Azure 门户边栏选项卡监视和管理管道](data-factory-monitor-manage-pipelines.md)或[监视和管理应用](data-factory-monitor-manage-app.md)。

重新运行“Dataset2”的“上午 9-10 点”切片后，数据工厂会在最终数据集上启动运行“上午 9-10 点”依赖切片。

![重新运行失败的切片](./media/data-factory-scheduling-and-execution/rerun-failed-slice.png)

## <a name="multiple-activities-in-a-pipeline"></a>管道中的多个活动
可在管道中添加多个活动。 如果管道中有多个活动，且一个活动的输出不是其他活动的输入，则若活动的输入数据切片准备就绪，活动可能会并行运行。

通过将一个活动的输出数据集设置为另一个活动的输入数据集，可链接两个活动（两个活动先后运行）。 这两个活动可在相同管道中，也可在不同管道中。 仅当第一个活动成功完成后第二个活动才能执行。

例如，考虑以下情况下管道其中有两个活动：

1. 需要外部输入数据集 D1 的活动 A1，并生成 输出数据集 D2。
2. 需要来自数据集 D2 的输入的活动 A2，并生成输出数据集 D3。

在此示例中，活动 A1 和 A2 在相同的管道中。 当外部数据可用且达到计划可用性频率时，运行活动 A1。 来自 D2 的计划切片可用且达到计划可用性频率时，运行活动 A2。 如果数据集 D2 中的某个切片发生错误，则在该切片可用之前，A2 不会运行该切片。

两个活动位于相同管道的“关系图”视图如下图所示：

![链接相同管道中的活动](./media/data-factory-scheduling-and-execution/chaining-one-pipeline.png)

如前文所述，活动可以位于不同的管道中。 在此情况下，图示视图如下图所示：

![链接两个管道中的活动](./media/data-factory-scheduling-and-execution/chaining-two-pipelines.png)

有关示例，请参阅[按顺序复制](#copy-sequentially)部分中的附录。

## <a name="model-datasets-with-different-frequencies"></a>具有不同频率的模型数据集
在这些示例中，输入和输出数据集的频率与活动计划时段相同。 某些方案需要以某一频率生成输出的功能，其中此频率不同于一个或多个输入的频率。 数据工厂支持对这些方案进行建模。

### <a name="sample-1-produce-a-daily-output-report-for-input-data-that-is-available-every-hour"></a>示例 1：为每小时可用的输入数据生成每日输出报告
请考虑这样一种方案，方案中具有来自 Azure Blob 存储中每小时可用的传感器的输入测量数据。 希望使用[数据工厂 hive 活动](data-factory-hive-activity.md)生成包含当天平均值、最大值和最小值等统计数据的每日汇总报告。

下面介绍如何使用数据工厂对这种方案建模：

**输入数据集**

删除文件夹中某给定日期的每小时输入文件。 输入的 Availability 设置为 **Hour** (frequency: Hour, interval: 1)。

```json
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**输出数据集**

每天在当天的文件夹中创建一个输出文件。 输出的 Availability 设置为 **Day** (frequency: Day and interval: 1)。

```json
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**活动：管道中的 hive 活动**

Hive 脚本接收相应的 *DateTime* 信息作为参数，这些参数使用 **WindowStart** 变量，如以下代码段中所示。 hive 脚本使用此变量从正确的文件夹加载当天数据，并运行聚合以生成输出。

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2015-01-01T08:00:00",
    "end":"2015-01-01T11:00:00",
    "description":"hive activity",
    "activities": [
        {
            "name": "SampleHiveActivity",
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
            "linkedServiceName": "HDInsightLinkedService",
            "type": "HDInsightHive",
            "typeProperties": {
                "scriptPath": "adftutorial\\hivequery.hql",
                "scriptLinkedService": "StorageLinkedService",
                "defines": {
                    "Year": "$$Text.Format('{0:yyyy}',WindowStart)",
                    "Month": "$$Text.Format('{0:MM}',WindowStart)",
                    "Day": "$$Text.Format('{0:dd}',WindowStart)"
                }
            },
            "scheduler": {
                "frequency": "Day",
                "interval": 1
            },            
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 2,
                "timeout": "01:00:00"
            }
         }
     ]
   }
}
```

下图从数据依赖项的角度演示该方案。

![数据依赖项](./media/data-factory-scheduling-and-execution/data-dependency.png)

每天的输出切片依赖于输入数据集的 24 小时切片。 数据工厂会自动计算这些依赖项，方法是计算在同一时间段内作为待生成输出切片的输入数据切片。 如果 24 个输入切片均不可用，数据工厂将等待输入切片准备就绪，然后启动每日活动运行。

### <a name="sample-2-specify-dependency-with-expressions-and-data-factory-functions"></a>示例 2：使用表达式和数据工厂函数指定依赖项
现在考虑下另一种方案。 假设某个 hive 活动处理两个输入数据集。 其中一个每日获取新数据，但另一个每周获取新数据。 假设希望联接两个输入并每天生成输出。

数据工厂通过对齐到输出数据切片时间段来自动计算相应的输入切片，这种简单方法不起作用。

必须为每个活动运行指定：数据工厂应对每周输入数据集使用上一周的数据切片。 使用以下代码段中所示的 Azure 数据工厂函数实现此行为。

**Input1：Azure blob**

第一个输入是每日更新的 Azure blob。

```json
{
  "name": "AzureBlobInputDaily",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Input2：Azure blob**

Input2 是每周更新的 Azure blob。

```json
{
  "name": "AzureBlobInputWeekly",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 7
    }
  }
}
```

**输出：Azure blob**

每天在当天的文件夹中创建一个输出文件。 输出的 Availability 设置为 **day** (frequency: Day, interval: 1)。

```json
{
  "name": "AzureBlobOutputDaily",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**活动：管道中的 hive 活动**

hive 活动每天采用两个输入并且生成输出切片。 可将每天的输出切片指定为依赖每周输入的上一周输入切片，如下所示。

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2015-01-01T08:00:00",
    "end":"2015-01-01T11:00:00",
    "description":"hive activity",
    "activities": [
      {
        "name": "SampleHiveActivity",
        "inputs": [
          {
            "name": "AzureBlobInputDaily"
          },
          {
            "name": "AzureBlobInputWeekly",
            "startTime": "Date.AddDays(SliceStart, - Date.DayOfWeek(SliceStart))",
            "endTime": "Date.AddDays(SliceEnd,  -Date.DayOfWeek(SliceEnd))"  
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutputDaily"
          }
        ],
        "linkedServiceName": "HDInsightLinkedService",
        "type": "HDInsightHive",
        "typeProperties": {
          "scriptPath": "adftutorial\\hivequery.hql",
          "scriptLinkedService": "StorageLinkedService",
          "defines": {
            "Year": "$$Text.Format('{0:yyyy}',WindowStart)",
            "Month": "$$Text.Format('{0:MM}',WindowStart)",
            "Day": "$$Text.Format('{0:dd}',WindowStart)"
          }
        },
        "scheduler": {
          "frequency": "Day",
          "interval": 1
        },            
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 2,  
          "timeout": "01:00:00"
        }
       }
     ]
   }
}
```

有关数据工厂支持的函数和系统变量列表，请参阅[数据工厂的函数和系统变量](data-factory-functions-variables.md)。

## <a name="appendix"></a>附录

### <a name="example-copy-sequentially"></a>示例：按顺序复制
可以按顺序或以有序的方式依次运行多个复制操作。 例如，管道中的两个复制活动（CopyActivity1 和 CopyActivity2）可能具有以下输入数据输出数据集：   

CopyActivity1

输入：Dataset1。 输出：Dataset2。

CopyActivity2

输入：Dataset2。  输出：Dataset3。

仅当 CopyActivity1 已成功运行且 Dataset2 可用时，CopyActivity2 才会运行。

以下是示例管道 JSON：

```json
{
    "name": "ChainActivities",
    "properties": {
        "description": "Run activities in sequence",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset1"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset2"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlob1ToBlob2",
                "description": "Copy data from a blob to another"
            },
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset2"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset3"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlob2ToBlob3",
                "description": "Copy data from a blob to another"
            }
        ],
        "start": "2016-08-25T01:00:00Z",
        "end": "2016-08-25T01:00:00Z",
        "isPaused": false
    }
}
```

请注意，示例中将第一个复制活动 (Dataset2) 的输出数据集指定为第二个活动的输入。 因此，仅当第一个活动的输出数据集准备就绪后，第二个活动才会运行。  

在示例中，CopyActivity2 可以具有不同的输入，如 Dataset3，但由于将 Dataset2 指定为 CopyActivity2 的输入，因此该活动仅在 CopyActivity1 完成后才可运行。 例如：

CopyActivity1

输入：Dataset1。 输出：Dataset2。

CopyActivity2

输入：Dataset3、Dataset2。 输出：Dataset4。

```json
{
    "name": "ChainActivities",
    "properties": {
        "description": "Run activities in sequence",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset1"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset2"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlobToBlob",
                "description": "Copy data from a blob to another"
            },
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset3"
                    },
                    {
                        "name": "Dataset2"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset4"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlob3ToBlob4",
                "description": "Copy data from a blob to another"
            }
        ],
        "start": "2017-04-25T01:00:00Z",
        "end": "2017-04-25T01:00:00Z",
        "isPaused": false
    }
}
```

请注意，示例中为第二个复制活动指定了两个输入数据集。 如果指定了多个输入，则仅将第一个输入数据集用于复制数据，其他数据集用作依赖项。 仅当满足以下条件后 CopyActivity2 才会启动：

* CopyActivity1 已成功完成且 Dataset2 可用。 将数据复制到 Dataset4 时不会使用此数据集。 它仅可充当 CopyActivity2 的计划依赖项。   
* Dataset3 可用。 此数据集表示复制到目标的数据。 