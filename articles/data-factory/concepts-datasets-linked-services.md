---
title: Azure 数据工厂中的数据集和链接服务 | Microsoft Docs
description: 了解数据工厂中的数据集和链接服务。 链接服务将计算/数据存储链接到数据工厂。 数据集表示输入/输出数据。
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: ''
ms.date: 01/22/2018
ms.author: shlo
ms.openlocfilehash: d88e4a068841f68feffb094b2aa07faaf5a6a8cc
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="datasets-and-linked-services-in-azure-data-factory"></a>Azure 数据工厂中的数据集和链接服务 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [版本 1 - 正式版](v1/data-factory-create-datasets.md)
> * [版本 2 - 预览版](concepts-datasets-linked-services.md)

本文介绍了数据集的涵义，采用 JSON 格式定义数据集的方式以及数据集在 Azure 数据工厂 V2 管道中的用法。 

> [!NOTE]
> 本文适用于目前处于预览状态的数据工厂版本 2。 如果使用正式版 (GA) - 版本 1 的数据工厂服务，请参阅[数据工厂 V1 中的数据集](v1/data-factory-create-datasets.md)。

如果对数据工厂不熟悉，请参阅 [Azure 数据工厂简介](introduction.md)了解相关概述。 

## <a name="overview"></a>概述
数据工厂可以包含一个或多个数据管道。 “管道”是共同执行一项任务的活动的逻辑分组。 管道中的活动定义对数据执行的操作。 例如，可使用复制活动将数据从本地 SQL Server 复制到 Azure Blob 存储。 然后，可使用在 Azure HDInsight 群集上运行 Hive 脚本的 Hive 活动，将 Blob 存储中的数据处理为生成输出数据。 最后，可再使用一个复制活动将输出数据复制到 Azure SQL 数据仓库，基于该仓库构建商业智能 (BI) 报告解决方案。 有关管道和活动的详细信息，请参阅 Azure 数据工厂中的[管道和活动](concepts-pipelines-activities.md)。

现在，数据集这一名称的意义已经变为看待数据的一种方式，就是以输入和输出的形式指向或引用活动中要使用的数据。 数据集可识别不同数据存储（如表、文件、文件夹和文档）中的数据。 例如，Azure Blob 数据集可在 Blob 存储中指定供活动读取数据的 Blob 容器和文件夹。

创建数据集之前，必须创建“链接的服务”，将数据存储链接到数据工厂。 链接的服务类似于连接字符串，它定义数据工厂连接到外部资源时所需的连接信息。 不妨这样考虑：数据集代表链接的数据存储中的数据结构，而链接服务则定义到数据源的连接。 例如，Azure 存储链接服务可将存储帐户链接到数据工厂。 Azure Blob 数据集表示 blob 容器以及包含要处理的输入 blob 的 Azure 存储帐户的文件夹。

下面是一个示例方案。 要将数据从 Blob 存储复制到 SQL 数据库，请创建两个链接服务，即 Azure 存储和 Azure SQL 数据库。 然后创建两个数据集：Azure Blob 数据集（即 Azure 存储链接服务）和 Azure SQL 表数据集（即 Azure SQL 数据库链接服务）。 Azure 存储和 Azure SQL 数据库链接服务分别包含数据工厂在运行时用于连接到 Azure 存储和 Azure SQL 数据库的连接字符串。 Azure Blob 数据集指定 blob 容器和 blob 文件夹，该文件夹包含 Blob 存储中的输入 blob。 Azure SQL 表数据集指定要向其复制数据的 SQL 数据库中的 SQL 表。

下图显示了数据工厂中管道、活动、数据集和链接服务之间的关系：

![管道、活动、数据集和链接服务之间的关系](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)

## <a name="linked-service-json"></a>链接服务 JSON
数据工厂中的链接服务采用 JSON 格式定义，如下所示：

```json
{
    "name": "<Name of the linked service>",
    "properties": {
        "type": "<Type of the linked service>",
        "typeProperties": {
              "<data store or compute-specific type properties>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

下表描述了上述 JSON 中的属性：

属性 | 说明 | 必选 |
-------- | ----------- | -------- |
名称 | 链接服务的名称。 请参阅 [Azure 数据工厂 - 命名规则](naming-rules.md)。 |  是 |
type | 链接服务的类型。 例如：AzureStorage（数据存储）或 AzureBatch（计算）。 请参阅 typeProperties 说明。 | 是 |
typeProperties | 每个数据存储或计算的类型属性各不相同。 <br/><br/> 有关支持的数据存储类型及其类型属性，请参阅本文中的[数据集类型](#dataset-type)表。 导航到数据存储连接器一文，了解特定于数据存储的类型属性。 <br/><br/> 有关支持的计算类型及其类型属性，请参阅[计算链接服务](compute-linked-services.md)。 | 是 |
connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如果数据存储位于专用网络，则可以使用 Azure 集成运行时或自承载集成运行时。 如果未指定，则使用默认 Azure 集成运行时。 | 否

## <a name="linked-service-example"></a>链接服务示例
以下链接服务是 Azure 存储链接服务。 请注意：类型设置为“AzureStorage”。 Azure 存储链接服务的类型属性包含连接字符串。 数据工厂服务使用此连接字符串在运行时连接到数据存储。 

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-json"></a>数据集 JSON
采用 JSON 格式定义数据工厂中的数据集，如下所示：

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "linkedServiceName": {
                "referenceName": "<name of linked service>",
                 "type": "LinkedServiceReference",
        },
        "structure": [
            {
                "name": "<Name of the column>",
                "type": "<Name of the type>"
            }
        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        }
    }
}

```
下表描述了上述 JSON 中的属性：

属性 | 说明 | 必选 |
-------- | ----------- | -------- |
名称 | 数据集名称。 请参阅 [Azure 数据工厂 - 命名规则](naming-rules.md)。 |  是 |
type | 数据集的类型。 指定数据工厂支持的类型之一（例如：AzureBlob、AzureSqlTable）。 <br/><br/>有关详细信息，请参阅[数据集类型](#dataset-types)。 | 是 |
structure | 数据集的架构。 有关详细信息，请参阅[数据集结构](#dataset-structure)。 | 否 |
typeProperties | 每种类型（例如 Azure Blob、Azure SQL 表）的类型属性各不相同。 若要详细了解受支持的类型及其属性，请参阅[数据集类型](#dataset-type)。 | 是 |

## <a name="dataset-example"></a>数据集示例
在以下示例中，数据集表示 SQL 数据库中名为 MyTable 的表。

```json
{
    "name": "DatasetSample",
    "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": {
                "referenceName": "MyAzureSqlLinkedService",
                 "type": "LinkedServiceReference",
        },
        "typeProperties":
        {
            "tableName": "MyTable"
        },
    }
}

```
请注意以下几点：

- type 设置为 AzureSqlTable。
- tableName 类型属性（特定于 AzureSqlTable 类型）设置为 MyTable。
- linkedServiceName 引用 AzureSqlDatabase 类型的链接服务，该类型在下一 JSON 片段中定义。

## <a name="dataset-type"></a>数据集类型
数据集的类型很多，具体取决于使用的数据存储。 请参阅下表，获取数据工厂支持的数据存储的列表。 单击数据存储，了解如何创建链接服务和该数据存储的数据集。

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

在上一节中的示例中，数据集的类型设置为 AzureSqlTable。 同样，对于 Azure Blob 数据集，数据集的类型设置为 AzureBlob，如以下 JSON 中所示：

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
                "referenceName": "MyAzureStorageLinkedService",
                 "type": "LinkedServiceReference",
        }, 
 
        "typeProperties": {
            "fileName": "input.log",
            "folderPath": "adfgetstarted/inputdata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        }
    }
}
```
## <a name="dataset-structure"></a>数据集结构
“结构”部分是可选部分。 它通过包含列的名称和数据类型的集合来定义数据集架构。 使用结构部分提供用于隐藏类型以及将列从源映射到目标的类型信息。

结构中的每个列都包含以下属性：

属性 | 说明 | 必选
-------- | ----------- | --------
名称 | 列的名称。 | 是
type | 列的数据类型。 数据工厂支持将以下临时数据类型作为允许的值：Int16、Int32、Int64、Single、Double、Decimal、Byte[]、Boolean、String、Guid、Datetime、Datetimeoffset 和 Timespan | 否
culture | 类型为 .NET 类型 `Datetime` 或 `Datetimeoffset` 时要使用的基于 .NET 的区域性。 默认为 `en-us`。 | 否
格式 | 类型为 .NET 类型 `Datetime` 或 `Datetimeoffset` 时要使用的格式字符串。 请参阅[自定义日期和时间格式字符串](https://docs.microsoft.com/en-us/dotnet/standard/base-types/custom-date-and-time-format-strings)，了解如何设置日期时间格式。 | 否

### <a name="example"></a>示例
在下面的示例中，假设源 Blob 数据采用 CSV 格式，并且包含三列： userid、name 和 lastlogindate。 它们的类型分别为 Int64、String 和 Datetime，并采用使用星期几的缩写法语名称的自定义日期时间格式。

将按如下所示定义 Blob 数据集结构并指定列的类型定义：

```json
"structure":
[
    { "name": "userid", "type": "Int64"},
    { "name": "name", "type": "String"},
    { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
]
```

### <a name="guidance"></a>指南

若要了解何时加入结构信息以及在结构部分包含哪些信息，请参阅以下指南。 详细了解数据工厂如何通过[架构和类型映射](copy-activity-schema-and-type-mapping.md)将源数据映射到接收器，以及何时指定结构信息。

- **对于强架构数据源**，仅当要将源列映射到接收器列且其名称不同时，才指定“结构”部分。 此类结构化的数据源将存储数据架构和类型信息，以及数据本身。 结构化的数据源的示例包括 SQL Server、Oracle 和 Azure SQL 数据库。<br/><br/>由于类型信息已可用于结构化数据源，因此包含结构部分时不应包含类型信息。
- **对于无/弱架构数据源（例如 blob 存储中的文本文件）**，当数据集是复制活动的输入且应将源数据集的数据类型转换为接收器的本机类型时，请加入结构。 另外，当需要将源列映射到接收器列时，请加入结构。

## <a name="create-datasets"></a>创建数据集
可以使用以下任一工具或 SDK 创建数据集：[.NET API](quickstart-create-data-factory-dot-net.md)、[PowerShell](quickstart-create-data-factory-powershell.md)、[REST API](quickstart-create-data-factory-rest-api.md)、Azure 资源管理器模板和 Azure 门户

## <a name="v1-vs-v2-datasets"></a>V1 与V2 数据集

以下是数据工厂 v1 和 v2 数据集之间的一些区别： 

- v2 不支持外部属性。 它已被[触发器](concepts-pipeline-execution-triggers.md)取代。
- V2 不支持策略和可用性属性。 管道的开始时间取决于[触发器](concepts-pipeline-execution-triggers.md)。
- V2 不支持范围内的数据集（管道中定义的数据集）。 

## <a name="next-steps"></a>后续步骤
请参阅以下教程，了解使用下列某个工具或 SDK 创建管道和数据集的分步说明。 

- [快速入门：使用 .NET 创建数据工厂](quickstart-create-data-factory-dot-net.md)
- [快速入门：使用 PowerShell 创建数据工厂](quickstart-create-data-factory-powershell.md)
- [快速入门：使用 REST API 创建数据工厂](quickstart-create-data-factory-rest-api.md)
- 快速入门：使用 Azure 门户创建数据工厂
