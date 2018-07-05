---
title: 在 Azure 数据工厂中创建数据集 | Microsoft Docs
description: 了解如何通过使用 offset 和 anchorDateTime 等属性的示例在 Azure 数据工厂中创建数据集。
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 0614cd24-2ff0-49d3-9301-06052fd4f92a
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: f33ff3f588dac49e295a5aa96d71557d32407e46
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37046980"
---
# <a name="datasets-in-azure-data-factory"></a>Azure 数据工厂中的数据集
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [第 1 版](data-factory-create-datasets.md)
> * [版本 2（当前版本）](../concepts-datasets-linked-services.md)

> [!NOTE]
> 本文适用于数据工厂版本 1。 如果使用数据工厂服务的当前版本，请参阅 [V2 中的数据集](../concepts-datasets-linked-services.md)。

本文介绍了数据集的涵义，采用 JSON 格式定义数据集的方式以及数据集在 Azure 数据工厂管道中的用法。 本文的数据集 JSON 定义中详细介绍了每个部分（例如，结构、可用性和策略）。 本文还提供有关在数据集 JSON 定义中使用 offset、anchorDateTime 和 style 属性的示例。

> [!NOTE]
> 如果对数据工厂不熟悉，请参阅 [Azure 数据工厂简介](data-factory-introduction.md)了解相关概述。 如果还没有亲身体验如何创建数据工厂，可通过阅读[数据转换教程](data-factory-build-your-first-pipeline.md)和[数据移动教程](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)加深了解。 

## <a name="overview"></a>概述
数据工厂可以包含一个或多个数据管道。 “管道”是共同执行一项任务的活动的逻辑分组。 管道中的活动定义对数据执行的操作。 例如，可使用复制活动将数据从本地 SQL Server 复制到 Azure Blob 存储。 然后，可使用在 Azure HDInsight 群集上运行 Hive 脚本的 Hive 活动，将 Blob 存储中的数据处理为生成输出数据。 最后，可再使用一个复制活动将输出数据复制到 Azure SQL 数据仓库，基于该仓库构建商业智能 (BI) 报告解决方案。 有关管道和活动的详细信息，请参阅 [Azure 数据工厂中的管道和活动](data-factory-create-pipelines.md)。

每个活动可采用零个或多个输入数据集，并生成一个或多个输出数据集。 输入数据集表示管道中活动的输入，输出数据集表示活动的输出。 数据集可识别不同数据存储（如表、文件、文件夹和文档）中的数据。 例如，Azure Blob 数据集可在 Blob 存储中指定供管道读取数据的 Blob 容器和文件夹。 

创建数据集之前，请创建一个链接服务，将数据存储链接到数据工厂。 链接的服务类似于连接字符串，它定义数据工厂连接到外部资源时所需的连接信息。 数据集可识别链接的数据存储（如 SQL 表、文件、文件夹和文档）中的数据。 例如，Azure 存储链接服务可将存储帐户链接到数据工厂。 Azure Blob 数据集表示 blob 容器以及包含要处理的输入 blob 的文件夹。 

下面是一个示例方案。 要将数据从 Blob 存储复制到 SQL 数据库，请创建两个链接服务，即 Azure 存储和 Azure SQL 数据库。 然后创建两个数据集：Azure Blob 数据集（即 Azure 存储链接服务）和 Azure SQL 表数据集（即 Azure SQL 数据库链接服务）。 Azure 存储和 Azure SQL 数据库链接服务分别包含数据工厂在运行时用于连接到 Azure 存储和 Azure SQL 数据库的连接字符串。 Azure Blob 数据集指定 blob 容器和 blob 文件夹，该文件夹包含 Blob 存储中的输入 blob。 Azure SQL 表数据集指定要向其复制数据的 SQL 数据库中的 SQL 表。

下图显示了数据工厂中管道、活动、数据集和链接服务之间的关系： 

![管道、活动、数据集和链接服务之间的关系](media/data-factory-create-datasets/relationship-between-data-factory-entities.png)

## <a name="dataset-json"></a>数据集 JSON
采用 JSON 格式定义数据工厂中的数据集，如下所示：

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
| 名称 |数据集名称。 若要了解命名规则，请参阅 [Azure 数据工厂 - 命名规则](data-factory-naming-rules.md)。 |是 |NA |
| type |数据集的类型。 指定数据工厂支持的类型之一（例如：AzureBlob、AzureSqlTable）。 <br/><br/>有关详细信息，请参阅[数据集类型](#Type)。 |是 |NA |
| structure |数据集的架构。<br/><br/>有关详细信息，请参阅[数据集结构](#Structure)。 |否 |NA |
| typeProperties | 每种类型（例如 Azure Blob、Azure SQL 表）的类型属性各不相同。 若要详细了解受支持的类型及其属性，请参阅[数据集类型](#Type)。 |是 |NA |
| external | 布尔标志，用于指定数据集是否由数据工厂管道显式生成。 如果活动的输入数据集不由当前管道生成，请将此标志设置为 true。 针对管道中第一个活动的输入数据集，将此标志设置为 true。  |否 |false |
| availability | 定义数据集生产的处理时段（如每小时或每天）或切片模型。 活动运行使用和生成的每个数据单元称为数据切片。 如果输出数据集的可用性设置为每天（频率 - 天，间隔 - 1），则将每天生成一个切片。 <br/><br/>有关详细信息，请参阅[数据集可用性](#Availability)。 <br/><br/>有关数据集切片模型的详细信息，请参阅[计划和执行](data-factory-scheduling-and-execution.md)一文。 |是 |NA |
| policy |定义数据集切片必须满足的标准或条件。 <br/><br/>有关详细信息，请参阅[数据集策略](#Policy)部分。 |否 |NA |

## <a name="dataset-example"></a>数据集示例
在以下示例中，数据集表示 SQL 数据库中名为 MyTable 的表。

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

* “type”设置为 AzureSqlTable。
* “tableName”类型属性（特定于 AzureSqlTable 类型）设置为 MyTable。
* “linkedServiceName”是指 AzureSqlDatabase 类型的链接服务，该类型在下一 JSON 片段中定义。 
* “availability frequency”设置为 Day，而“interval”设置为 1。 这意味着，每日都将生成数据集切片。  

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

在前面的 JSON 片段中：

* “type”设置为 AzureSqlDatabase。
* “connectionString”类型属性指定连接到 SQL 数据库的信息。  

如你所见，链接服务用于定义连接到 SQL 数据库的方式。 数据集用于定义将用作管道中活动的输入和输出的表类型。   

> [!IMPORTANT]
> 除非由管道生成数据集，否则应将其标记为“external”。 此设置通常适用于管道中第一个活动的输入。   


## <a name="Type"></a> 数据集类型
数据集的类型取决于所用的数据存储。 请参阅下表，获取数据工厂支持的数据存储的列表。 单击数据存储，了解如何创建链接服务和该数据存储的数据集。

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> 包含 * 的数据存储可为本地，也可为 Azure 服务架构 (IaaS)。 这些数据存储要求安装[数据管理网关](data-factory-data-management-gateway.md)。

在上一节中的示例中，数据集的类型设置为 AzureSqlTable。 同样，对于 Azure Blob 数据集，数据集的类型设置为 AzureBlob，如以下 JSON 中所示：

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

## <a name="Structure"></a> 数据集结构
“结构”部分是可选部分。 它通过包含列的名称和数据类型的集合来定义数据集架构。 使用结构部分提供用于隐藏类型以及将列从源映射到目标的类型信息。 在下面的示例中，数据集有三列：`slicetimestamp`、`projectname` 和 `pageviews`。 它们的类型分别为 String、String 和 Decimal。

```json
structure:  
[
    { "name": "slicetimestamp", "type": "String"},
    { "name": "projectname", "type": "String"},
    { "name": "pageviews", "type": "Decimal"}
]
```

结构中的每个列都包含以下属性：

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| 名称 |列的名称。 |是 |
| type |列的数据类型。  |否 |
| culture |类型为 .NET 类型 `Datetime` 或 `Datetimeoffset` 时要使用的基于 .NET 的区域性。 默认为 `en-us`。 |否 |
| 格式 |类型为 .NET 类型 `Datetime` 或 `Datetimeoffset` 时要使用的格式字符串。 |否 |

若要了解何时加入“结构”信息以及在结构部分包含哪些信息，请遵循以下指南。

* 对于结构化的数据源，仅在要将源列映射到接收器列且其名称不同时，才指定“结构”部分。 此类结构化的数据源将存储数据架构和类型信息，以及数据本身。 结构化的数据源的示例包括 SQL Server、Oracle 和 Azure 表。 
  
    由于类型信息已可用于结构化数据源，因此包含结构部分时不应包含类型信息。
* 对于读取数据源（尤其是 Blob 存储）的架构，可以选择存储数据但不存储数据的任何架构或类型信息。 对于这些类型的数据源，当希望将源列映射到接收器列时请包括“结构”。 当数据集是复制活动的输入数据集并且需要将源数据集的数据类型转换为接收器的本机类型时，也请将“结构”包括在内。 
    
    数据工厂支持以下值，用于在结构中提供类型信息：Int16、Int32、Int64、Single、Double、Decimal、Byte[]、Bool、String、Guid、Datetime、Datetimeoffset 和 Timespan。 这些值符合公共语言规范 (CLS)，且基于 .NET 的类型值。

将数据从源数据存储移到接收器数据存储时，数据工厂自动执行类型转换。 
  

## <a name="dataset-availability"></a>数据集可用性
数据集中的“可用性”部分定义了数据集的处理时段（如每小时、每天和每周）。 有关活动时段的详细信息，请参阅[计划和执行](data-factory-scheduling-and-execution.md)。

以下可用性部分指定每小时生成输出数据集或每小时提供输入数据集：

```json
"availability":    
{    
    "frequency": "Hour",        
    "interval": 1    
}
```

如果管道具有以下开始和结束时间：  

```json
    "start": "2016-08-25T00:00:00Z",
    "end": "2016-08-25T05:00:00Z",
```

每小时在管道开始和结束时间内生成输出数据集。 因此，此管道生成 5 个数据集切片，每个活动时段一个（晚上 12 点 - 凌晨 1 点、凌晨 1 点 - 凌晨 2 点、凌晨 2 点 - 凌晨 3 点、凌晨 3 点 - 凌晨 4 点和凌晨 4 点 - 凌晨 5 点）。 

下表介绍了可用于可用性部分的属性：

| 属性 | 说明 | 必选 | 默认 |
| --- | --- | --- | --- |
| 频率 |指定数据集切片生成的时间单位。<br/><br/><b>支持的频率</b>：Minute、Hour、Day、Week、Month |是 |NA |
| interval |指定频率的乘数。<br/><br/>“频率 x 间隔”确定生成切片的频率。 例如，如果需要数据集每小时生成切片，请将“frequency”<b></b>设置为“Hour”<b></b>，将“interval”<b></b>设置为“1”<b></b>。<br/><br/>注意：如果将“frequency”指定为“Minute”，则应将“interval”设置为小于 15 的值。 |是 |NA |
| style |指定是否应在间隔的开头/结尾生成切片。<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul>若将“frequency”设置为“Month”，将“style”设置为“EndOfInterval”，则会在每月的最后一天生成切片。 若将“style”设置为“StartOfInterval”，则会在每月的第一天生成切片。<br/><br/>若将“frequency”设置为“Day”，将“style”设置为“EndOfInterval”，则会在一天的最后一小时生成切片。<br/><br/>若将 **frequency** 设置为 **Hour**，**style** 设置为 **EndOfInterval**，则会在一小时结束时生成切片。 例如，对于下午 1 点到下午 2 点期间的切片，则在下午 2 点生成切片。 |否 |EndOfInterval |
| anchorDateTime |定义计划程序用于计算数据集切片边界的时间中的绝对位置。 <br/><br/>注意：如果此属性的日期部分比指定的频率更精细，则忽略更精细部分。 例如，如果“interval”是“每小时”（frequency: hour 且 interval: 1），而 anchorDateTime 包含“分钟和秒”，则将忽略 anchorDateTime 的“分钟和秒”部分。 |否 |01/01/0001 |
| offset |所有数据集切片的开始和结束之间偏移的时间跨度。 <br/><br/>注意：如果同时指定了 anchorDateTime 和 offset，则结果是组合偏移。 |否 |NA |

### <a name="offset-example"></a>偏移示例
默认情况下，日常 (`"frequency": "Day", "interval": 1`) 切片在协调世界时凌晨零点（午夜）开始。 要将开始时间改为 UTC 时间早上 6 点，请按以下片段中所示设置偏移量： 

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

### <a name="offsetstyle-example"></a>offset/style 示例
以下数据集是每月数据集，在每月第三天上午 8:00 (`3.08:00:00`) 生成：

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "offset": "3.08:00:00", 
    "style": "StartOfInterval"
}
```

## <a name="Policy"></a> 数据集策略
数据集定义中的“策略”部分定义了数据集切片必须满足的标准或条件。

### <a name="validation-policies"></a>验证策略
| 策略名称 | 说明 | 适用对象 | 必选 | 默认 |
| --- | --- | --- | --- | --- |
| minimumSizeMB |验证 Azure Blob 存储中的数据是否满足最小大小要求（以兆字节为单位）。 |Azure Blob 存储 |否 |NA |
| minimumRows |验证 **Azure SQL 数据库**中的数据或 **Azure 表**是否包含最小行数。 |<ul><li>Azure SQL 数据库</li><li>Azure 表</li></ul> |否 |NA |

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

minimumRows：

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

除非由数据工厂生成数据集，否则应将其标记为“external”。 此设置通常适用于管道中第一个活动的输入，除非正在使用活动或管道链接。

| 名称 | 说明 | 必选 | 默认值 |
| --- | --- | --- | --- |
| dataDelay |延迟检查给定切片外部数据的可用性的时间。 例如，可使用此设置延迟每小时检查。<br/><br/>该设置仅适用于当前时间。  例如，如果现在是下午 1:00 且此值为 10 分钟，则从下午 1:10 开始验证。<br/><br/>请注意，此设置不影响过去的切片。 处理包含 Slice End Time + dataDelay < Now 的切片不会有任何延迟。<br/><br/>大于 23:59 小时的时间应使用 `day.hours:minutes:seconds` 格式进行指定。 例如，若要指定 24 小时，请不要使用 24:00:00。 请改用 1.00:00:00。 如果使用 24:00:00，则将它视为 24 天 (24.00:00:00)。 对于 1 天又 4 小时，请指定 1:04:00:00。 |否 |0 |
| retryInterval |失败与下一次尝试之间的等待时间。 此设置适用于当前时间。 如果上一次尝试失败，则在 retryInterval 时间段后进行下一次尝试。 <br/><br/>如果现在是下午 1:00，我们将开始第一次尝试。 如果完成第一次验证检查的持续时间为 1 分钟，并且操作失败，则下一次重试为 1:00 + 1 分钟（持续时间）+ 1 分钟（重试间隔）= 下午 1:02。 <br/><br/>对于过去的切片，没有任何延迟。 重试会立即发生。 |否 |00:01:00（1 分钟） |
| retryTimeout |每次重试尝试的超时。<br/><br/>如果此属性设置为 10 分钟，则验证应在 10 分钟内完成。 如果执行验证所需的时间超过 10 分钟，则重试超时。<br/><br/>如果验证的所有尝试超时，则将切片标记为 TimedOut。 |否 |00:10:00（10 分钟） |
| maximumRetry |检查外部数据可用性的次数。 允许的最大值为 10。 |否 |3 |


## <a name="create-datasets"></a>创建数据集
可使用下列某个工具或 SDK 创建数据集： 

- 复制向导 
- Azure 门户
- Visual Studio
- PowerShell
- Azure 资源管理器模板
- REST API
- .NET API

请参阅以下教程，了解使用下列某个工具或 SDK 创建管道和数据集的分步说明：
 
- [使用数据转换活动生成管道](data-factory-build-your-first-pipeline.md)
- [使用数据移动活动生成管道](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

创建并部署管道之后，可使用 Azure 门户边栏选项卡或“监视与管理”应用来监视和管理这些管道。 请参阅下列主题中的分步说明： 

- [使用 Azure 门户边栏选项卡监视和管理管道](data-factory-monitor-manage-pipelines.md)
- [使用“监视和管理”应用监视和管理管道](data-factory-monitor-manage-app.md)


## <a name="scoped-datasets"></a>指定了作用域的数据集
可使用 **datasets** 属性创建作用域指定到管道的数据集。 这些数据集只能由此管道中的活动使用，而不能由其他管道中的活动使用。 以下示例使用要用于管道的两个数据集（InputDataset-rdc 和 OutputDataset-rdc）定义管道。  

> [!IMPORTANT]
> 指定了作用域的数据集仅可使用一次性管道（pipelineMode 设置为 OneTime）。 有关详细信息，请参阅[一次性管道](data-factory-create-pipelines.md#onetime-pipeline)。
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

## <a name="next-steps"></a>后续步骤
- 有关管道的详细信息，请参阅[创建管道](data-factory-create-pipelines.md)。 
- 若要深入了解如何计划和执行管道，请参阅 [Azure 数据工厂中的计划和执行](data-factory-scheduling-and-execution.md)。 
