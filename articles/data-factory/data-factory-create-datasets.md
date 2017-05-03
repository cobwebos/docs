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
ms.date: 04/12/2017
ms.author: shlo
translationtype: Human Translation
ms.sourcegitcommit: e0c999b2bf1dd38d8a0c99c6cdd4976cc896dd99
ms.openlocfilehash: 88e653f6e46f3e8eb72e620b495d1769f17bdfbf
ms.lasthandoff: 04/20/2017


---
# <a name="datasets-in-azure-data-factory"></a>Azure 数据工厂中的数据集
本文介绍 Azure 数据工厂中的数据集，包括偏移、anchorDateTime 和偏移/样式数据库等示例。

> [!NOTE]
> 如果对 Azure 数据工厂不熟悉，请参阅 [Azure 数据工厂简介](data-factory-introduction.md)，查看 Azure 数据工厂服务的概述。 如果还没有亲身体验如何创建数据工厂，请浏览[数据转换教程](data-factory-build-your-first-pipeline.md)和/或[数据移动教程](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)，以便更好地理解该文章。 

## <a name="overview"></a>概述
数据工厂可以包含一个或多个数据管道。 “管道”是共同执行一项任务的活动的逻辑分组。 管道中的活动定义对数据执行的操作。 例如，可使用复制活动将数据从本地 SQL Server 复制到 Azure Blob 存储。 然后，使用在 Azure HDInsight 群集上运行 Hive 脚本的 Hive 活动，将 Blob 存储中的数据处理/转换为生成输出数据。 最后，再使用一个复制活动将输出数据复制到 Azure SQL 数据仓库，基于该仓库构建紧商业智能 (BI) 报告解决方案。 有关管道和活动的详细信息，请参阅 [Azure 数据工厂中的管道和活动](data-factory-create-pipelines.md)一文。

每个活动可获取零个或多个输入数据集，并生成一个或多个输出数据集。 输入数据集表示管道中活动的输入，输出数据集表示活动的输出。 数据集可识别不同数据存储（如表、文件、文件夹和文档）中的数据。 例如，Azure Blob 数据集在 Azure Blob 存储中指定管道要从中读取数据的 Blob 容器和文件夹。 

创建数据集之前，需要创建“链接的服务”，将数据存储链接到数据工厂。 链接的服务类似于连接字符串，它定义数据工厂连接到外部资源时所需的连接信息。 数据集可识别链接的数据存储（如 SQL 表、文件、文件夹和文档）中的数据。 例如，Azure 存储链接服务将 Azure 存储帐户链接到数据工厂。 Azure Blob 数据集表示 blob 容器以及包含要处理的输入 blob 的文件夹。 

示例方案如下：若要将数据从 Azure Blob 存储复制到 Azure SQL 数据库，需创建两个链接的服务，即 Azure 存储和 Azure SQL 数据库。 然后创建两个数据集：Azure Blob 数据集（即 Azure 存储链接服务）、Azure SQL 表数据集（即 Azure SQL 数据库链接服务）。 Azure 存储和 Azure SQL 数据库链接服务分别包含数据工厂在运行时用于连接到 Azure 存储和 Azure SQL 数据库的连接字符串。 Azure Blob 数据集指定 blob 容器和 blob 文件夹，该文件夹包含 Azure Blob 存储中的输入 blob。 Azure SQL 表数据集指定要向其复制数据的 Azure SQL 数据库中的 SQL 表。

下图显示了数据工厂中管道、活动、数据集和链接服务之间的关系： 

![管道、活动、数据集和链接服务之间的关系](media/data-factory-create-datasets/relationship-between-data-factory-entities.png)

## <a name="dataset-json"></a>数据集 JSON
采用 JSON 格式定义 Azure 数据工厂中的数据集，如下所示：

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
| structure |数据集架构<br/><br/>有关详细信息，请参阅[数据集结构](#Structure)。 |否。 |不可用 |
| typeProperties | 每种类型（例如 Azure Blob、Azure SQL 表）的类型属性各不相同。 若要详细了解受支持的类型及其属性，请参阅[数据集类型](#Type)。 |是 |不可用 |
| external | 布尔标志，用于指定数据集是否由数据工厂管道显式生成。 如果活动的输入数据集不由当前管道生成，请将此标志设置为 true。 针对管道中第一个活动的输入数据集，将此标志设置为 true。  |否 |false |
| availability | 定义数据集生产的处理时段（每小时、每天等）或切片模型。 活动运行使用和生成的每个数据单元称为数据切片。 如果输出数据集的可用性设置为每天（频率 - 天，间隔 - 1），则将每天生成一个切片。 <br/><br/>有关详细信息，请参阅[数据集可用性](#Availability)。 <br/><br/>有关数据集切片模型的详细信息，请参阅[计划和执行](data-factory-scheduling-and-execution.md)一文。 |是 |不可用 |
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
* linkedServiceName 引用 AzureSqlDatabase 类型的链接服务，该类型在下一 JSON 片段中定义。 
* 可用性频率设置为每天且间隔设置为 1，意味着每天都会生成数据集切片。  

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

如你所见，链接服务定义如何连接到 Azure SQL 数据库。 数据集定义将哪个表用作管道中活动的输入/输出。   

> [!IMPORTANT]
> 除非将由管道生成数据集，否则应将其标记为“external”。 此设置通常适用于管道中第一个活动的输入。   


## <a name="Type"></a>数据集类型
数据集的类型取决于所用的数据存储。 请参阅下表，获取数据工厂支持的数据存储的列表。 单击数据存储，了解如何创建链接服务和该数据存储的数据集。

[!INCLUDE [data-factory-supported-data-stores](../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> 带 * 的数据存储既可位于本地，也可位于 Azure IaaS 上，需要用户在本地/Azure IaaS 计算机上安装[数据管理网关](data-factory-data-management-gateway.md)。

在上一节中的示例中，数据集的类型设置为 AzureSqlTable。 同样，对于 Azure Blob 数据集，数据集的类型设置为 **AzureBlob**，如以下 JSON 中所示：

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

## <a name="Structure"></a>数据集结构
**结构**部分是**可选**部分，用于定义数据集的架构。 它包含列的名称和数据类型的集合。 使用结构部分提供用于隐藏类型以及将列从源映射到目标的类型信息。 在以下示例中，数据集具有三列 `slicetimestamp`、`projectname` 和 `pageviews`，它们的类型分别为：String、String 和 Decimal。

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
| culture |类型为 .NET 类型 `Datetime` 或 `Datetimeoffset` 时要使用的基于 .NET 的区域性。 默认值为 `en-us`。 |否 |
| 格式 |类型为 .NET 类型 `Datetime` 或 `Datetimeoffset` 时要使用的格式字符串。 |否 |

若要了解何时加入“结构”信息以及在**结构**部分包含哪些信息，请遵循以下指南。

* 对于存储数据架构、类型信息和数据本身的“结构化数据源”（如 SQL Server、Oracle、Azure 表等源），仅在要将源列映射到接收器列且其名称不同时，才指定“结构”部分。 
  
    由于类型信息已可用于结构化数据源，因此包含“结构”部分时不应包含类型信息。
* **对于读取数据源（尤其是 Azure Blob）的架构**，可以选择存储数据但不存储数据的任何架构或类型信息。 对于这些类型的数据源，当你希望将源列映射到接收器列时，（或者）当数据集是复制活动的输入数据集并且需要将源数据集的数据类型转换为接收器的本机类型时，请包括“结构”。 
    
    数据工厂支持使用以下符合 CLS 标准、基于 .NET 的类型值在“结构”中为读取数据源（如 Azure Blob）的架构提供类型信息：Int16、Int32、Int64、Single、Double、Decimal、Byte[]、Bool、String、Guid、Datetime、Datetimeoffset、Timespan。

将数据从源数据存储移到接收器数据存储时，数据工厂自动执行类型转换。 
  

## <a name="Availability"></a>数据集的可用性
数据集中的“可用性”部分定义了数据集的处理时段（每小时、每天和每周等）。 有关活动窗口的详细信息，请参阅[计划和执行](data-factory-scheduling-and-execution.md)一文。

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

每小时在管道开始和结束时间内生成输出数据集。 因此，此管道生成 5 个数据集切片，每个活动窗口一个（晚上 12 点 - 凌晨 1 点、凌晨 1 点 - 凌晨 2 点、凌晨 2 点 - 凌晨 3 点、凌晨 3 点 - 凌晨 4 点和凌晨 4 点 - 凌晨 5 点）。 

下表介绍了可用于可用性部分的属性：

| 属性 | 说明 | 必选 | 默认 |
| --- | --- | --- | --- |
| frequency |指定数据集切片生成的时间单位。<br/><br/><b>支持的频率</b>：Minute、Hour、Day、Week、Month |是 |不可用 |
| interval |指定频率的乘数<br/><br/>“频率 x 间隔”确定生成切片的频率。<br/><br/>若需要数据集每小时生成切片，请将“Frequency”<b></b>设置为“Hour”<b></b>，将“interval”<b></b>设置为“1”<b></b>。<br/><br/><b>注意</b>：如果将 Frequency 指定为 Minute，建议将 interval 设置为小于 15 的值 |是 |不可用 |
| style |指定是否应在间隔的开头/结尾生成切片。<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>若将 Month 设置为 Month，style 设置为 EndOfInterval，则将在每月的最后一天生成切片。 若将 style 设为 StartOfInterval，将在每月的第一天生成切片。<br/><br/>若将 Frequency 设置为 Day，style 设置为 EndOfInterval，则将在一天的最后一小时生成切片。<br/><br/>若将 Frequency 设置为 Hour，style 设置为 EndOfInterval，则将在一小时结束时生成切片。 例如，对于下午 1 点到下午 2 点期间的切片，则在下午 2 点生成切片。 |否 |EndOfInterval |
| anchorDateTime |定义计划程序用于计算数据集切片边界的时间中的绝对位置。 <br/><br/><b>注意</b>：如果 AnchorDateTime 的日期部分比频率部分更精细，则忽略更精细部分。 <br/><br/>例如，如果“interval”<b></b>是“每小时”<b></b>（frequency: hour 且 interval: 1），而 <b> AnchorDateTime</b> 包含<b>分钟和秒</b>，则将忽略 AnchorDateTime 的<b>分钟和秒</b>部分。 |否 |01/01/0001 |
| offset |所有数据集切片的开始和结束之间偏移的时间跨度。 <br/><br/><b>注意</b>：如果同时指定了 anchorDateTime 和 offset，则结果是组合偏移。 |否 |不可用 |

### <a name="offset-example"></a>偏移示例
默认情况下，每天 (`"frequency": "Day", "interval": 1`) 在 UTC 时间晚上 12 点（午夜）开始切片。 若要将开始时间改为 UTC 时间早上 6 点，请按以下片段中所示设置偏移量： 

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

## <a name="Policy"></a>数据集策略
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
| dataDelay |延迟检查给定切片外部数据的可用性的时间。 例如，如果数据应为每小时可用，可使用 dataDelay 推迟检查外部数据是否可用以及相应切片是否就绪。<br/><br/>仅适用于当前时间。  例如，如果现在是下午 1:00 且此值为 10 分钟，则从下午 1:10 开始验证。<br/><br/>此设置不会影响过去的切片 (End Time + dataDelay < Now)，过去的切片不会有任何延迟。<br/><br/>大于 23:59 小时的时间需要使用 `day.hours:minutes:seconds` 格式进行指定。 例如，若要指定 24 小时，请不要使用 24:00:00；请改用 1.00:00:00。 如果使用 24:00:00，则将它视为 24 天 (24.00:00:00)。 对于 1 天又 4 小时，请指定 1:04:00:00。 |否 |0 |
| retryInterval |失败与下一次重试之间的等待时间。 适用于当前时间；如果前一次尝试失败，则在 retryInterval 时间段后进行下一次尝试。 <br/><br/>如果现在是下午 1:00，我们将开始第一次尝试。 如果完成第一次验证检查的持续时间为 1 分钟，并且操作失败，则下一次重试为 1:00 + 1 分钟（持续时间）+ 1 分钟（重试间隔）= 下午 1:02。 <br/><br/>对于过去的切片，没有任何延迟。 重试会立即发生。 |否 |00:01:00（1 分钟） |
| retryTimeout |每次重试尝试的超时。<br/><br/>如果此属性设置为 10 分钟，则验证需要在 10 分钟内完成。 如果执行验证所需的时间超过 10 分钟，则重试超时。<br/><br/>如果验证的所有尝试超时，则将切片标记为 TimedOut。 |否 |00:10:00（10 分钟） |
| maximumRetry |检查外部数据可用性的次数。 允许的最大值为 10。 |否 |3 |


## <a name="create-datasets"></a>创建数据集
可使用下列某个工具或 SDK 创建数据集。 

- 复制向导。 
- Azure 门户
- Visual Studio
- Azure PowerShell
- Azure Resource Manager 模板
- REST API
- .NET API

请参阅以下教程，了解使用下列某个工具或 SDK 创建管道和数据集的分步说明。
 
- [使用数据转换活动生成管道](data-factory-build-your-first-pipeline.md)
- [使用数据移动活动生成管道](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

管道创建/部署完毕后，可使用 Azure 门户边栏选项卡或“监视与管理”来监视和管理这些管道。 请参阅下列主题中的分步说明。 

- [使用 Azure 门户边栏选项卡监视和管理管道](data-factory-monitor-manage-pipelines.md)。
- [使用“监视与管理”应用监视和管理管道](data-factory-monitor-manage-app.md)


## <a name="scoped-datasets"></a>指定了作用域的数据集
可使用 **datasets** 属性创建作用域指定到管道的数据集。 这些数据集只能由此管道中的活动使用，而不能由其他管道中的活动使用。 以下示例使用要用于管道的两个数据集（InputDataset-rdc 和 OutputDataset-rdc）定义管道：  

> [!IMPORTANT]
> 指定了作用域的数据集仅可使用一次性管道（pipelineMode 设置为 OneTime）。 有关详细信息，请参阅[一次性管道](data-factory-scheduling-and-execution.md#onetime-pipeline)。
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
- 有关管道的详细信息，请参阅[创建管道](data-factory-create-pipelines.md)一文。 
- 若要深入了解如何计划和执行管道，请参阅[在 Azure 数据工厂中计划和执行](data-factory-scheduling-and-execution.md)一文。 
