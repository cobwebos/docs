---
title: Azure 数据工厂中的数据集 | Microsoft Docs
description: 了解数据工厂中的数据集。 数据集表示输入/输出数据。
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/25/2019
ms.openlocfilehash: c4daa5989013ba8d5c5a7136fe0878fae64f0357
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030558"
---
# <a name="datasets-in-azure-data-factory"></a>Azure 数据工厂中的数据集
> [!div class="op_single_selector" title1="选择在使用数据工厂服务版本："]
> * [版本 1](v1/data-factory-create-datasets.md)
> * [当前版本](concepts-datasets-linked-services.md)

本文介绍了数据集的涵义，采用 JSON 格式定义数据集的方式以及数据集在 Azure 数据工厂管道中的用法。

如果对数据工厂不熟悉，请参阅 [Azure 数据工厂简介](introduction.md)了解相关概述。

## <a name="overview"></a>概述
数据工厂可以包含一个或多个数据管道。 “管道”是共同执行一项任务的活动的逻辑分组。 管道中的活动定义对数据执行的操作。 现在，数据集这一名称的意义已经变为看待数据的一种方式，就是以输入和输出的形式指向或引用活动中要使用的数据。 数据集可识别不同数据存储（如表、文件、文件夹和文档）中的数据。 例如，Azure Blob 数据集可在 Blob 存储中指定供活动读取数据的 Blob 容器和文件夹。

创建数据集之前，必须创建[链接服务](concepts-linked-services.md)，将数据存储链接到数据工厂。 链接的服务类似于连接字符串，它定义数据工厂连接到外部资源时所需的连接信息。 不妨这样考虑：数据集代表链接的数据存储中的数据结构，而链接服务则定义到数据源的连接。 例如，Azure 存储链接服务可将存储帐户链接到数据工厂。 Azure Blob 数据集表示 blob 容器以及包含要处理的输入 blob 的 Azure 存储帐户的文件夹。

下面是一个示例方案。 要将数据从 Blob 存储复制到 SQL 数据库，请创建以下两个链接服务：Azure 存储和 Azure SQL 数据库。 然后创建两个数据集：Azure Blob 数据集（即 Azure 存储链接服务）和 Azure SQL 表数据集（即 Azure SQL 数据库链接服务）。 Azure 存储和 Azure SQL 数据库链接服务分别包含数据工厂在运行时用于连接到 Azure 存储和 Azure SQL 数据库的连接字符串。 Azure Blob 数据集指定 blob 容器和 blob 文件夹，该文件夹包含 Blob 存储中的输入 blob。 Azure SQL 表数据集指定要向其复制数据的 SQL 数据库中的 SQL 表。

下图显示了数据工厂中管道、活动、数据集和链接服务之间的关系：

![管道、活动、数据集和链接服务之间的关系](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)


## <a name="dataset-json"></a>数据集 JSON
数据工厂中的数据集采用以下 JSON 格式定义：

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

属性 | 说明 | 需要 |
-------- | ----------- | -------- |
name | 数据集名称。 请参阅 [Azure 数据工厂 - 命名规则](naming-rules.md)。 |  是 |
type | 数据集的类型。 指定数据工厂支持的类型之一（例如：AzureBlob、AzureSqlTable）。 <br/><br/>有关详细信息，请参阅[数据集类型](#dataset-type)。 | 是 |
structure | 数据集的架构。 有关详细信息，请参阅[数据集架构](#dataset-structure-or-schema)。 | 否 |
typeProperties | 每种类型的类型属性各不相同（例如：Azure Blob、Azure SQL 表）。 若要详细了解受支持的类型及其属性，请参阅[数据集类型](#dataset-type)。 | 是 |

### <a name="data-flow-compatible-dataset"></a>与数据流兼容的数据集



有关[与数据流兼容的](concepts-data-flow-overview.md)数据集类型的列表，请参阅支持的数据[集类型](#dataset-type)。 与数据流兼容的数据集需要用于转换的细化数据集定义。 因此，JSON 定义略有不同。 与数据流兼容的数据集具有_架构_属性，而不是_结构_属性。

在数据流中，数据集用于源和接收器转换。 数据集定义基本数据架构。 如果数据没有架构，可以对源和接收器使用架构偏差。 数据集中的架构表示物理数据类型和形状。

通过从数据集定义架构，你将从关联的链接服务获取相关的数据类型、数据格式、文件位置和连接信息。 来自数据集的元数据在源转换中显示为源投影。 源转换中的投影表示具有定义名称和类型的数据流数据。

导入数据流数据集的架构时，请选择“导入架构”按钮，然后选择从源或本地文件导入。 在大多数情况下，将直接从源导入架构。 但是，如果你已有本地架构文件（Parquet 文件或带标题的 CSV），则可以指示数据工厂基于该文件设置架构。


```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "linkedServiceName": {
                "referenceName": "<name of linked service>",
                "type": "LinkedServiceReference",
        },
        "schema": [
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

属性 | 说明 | 需要 |
-------- | ----------- | -------- |
name | 数据集名称。 请参阅 [Azure 数据工厂 - 命名规则](naming-rules.md)。 |  是 |
type | 数据集的类型。 指定数据工厂支持的类型之一（例如：AzureBlob、AzureSqlTable）。 <br/><br/>有关详细信息，请参阅[数据集类型](#dataset-type)。 | 是 |
schema | 数据集的架构。 有关详细信息，请参阅[与数据流兼容的数据集](#dataset-type)。 | 否 |
typeProperties | 每种类型的类型属性各不相同（例如：Azure Blob、Azure SQL 表）。 若要详细了解受支持的类型及其属性，请参阅[数据集类型](#dataset-type)。 | 是 |


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
数据集的类型很多，具体取决于使用的数据存储。 可以从 "[连接器概述](connector-overview.md)" 一文中找到数据工厂支持的数据列表。 单击数据存储，了解如何创建链接服务和该数据存储的数据集。

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

## <a name="dataset-structure-or-schema"></a>数据集结构或架构
“结构”部分或“架构”（数据流兼容）部分数据集是可选的。 它通过包含列的名称和数据类型的集合来定义数据集架构。 使用结构部分提供用于隐藏类型以及将列从源映射到目标的类型信息。

结构中的每个列都包含以下属性：

属性 | 说明 | 需要
-------- | ----------- | --------
name | 列的名称。 | 是
type | 列的数据类型。 数据工厂支持以下临时数据类型作为允许的值：**Int16、Int32、Int64、Single、Double、Decimal、Byte[]、Boolean、String、Guid、Datetime、Datetimeoffset 和 Timespan** | 否
culture | 类型为 .NET 类型 `Datetime` 或 `Datetimeoffset` 时要使用的基于 .NET 的区域性。 默认值为 `en-us`。 | 否
format | 类型为 .NET 类型 `Datetime` 或 `Datetimeoffset` 时要使用的格式字符串。 请参阅[自定义日期和时间格式字符串](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)，了解如何设置日期时间格式。 | 否

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
- **对于无/弱架构数据源（例如 blob 存储中的文本文件）** ，当数据集是复制活动的输入且应将源数据集的数据类型转换为接收器的本机类型时，请加入结构。 另外，当需要将源列映射到接收器列时，请加入结构。

## <a name="create-datasets"></a>创建数据集
可以使用以下任一工具或 SDK 创建数据集：[.NET API](quickstart-create-data-factory-dot-net.md)、[PowerShell](quickstart-create-data-factory-powershell.md)、[REST API](quickstart-create-data-factory-rest-api.md)、Azure 资源管理器模板和 Azure 门户

## <a name="current-version-vs-version-1-datasets"></a>当前版本与版本 1 数据集的比较

以下是数据工厂和数据工厂版本 1 数据集之间的差异：

- 当前版本不支持外部属性。 它已被[触发器](concepts-pipeline-execution-triggers.md)取代。
- 当前版本不支持策略和可用性属性。 管道的开始时间取决于[触发器](concepts-pipeline-execution-triggers.md)。
- 当前版本不支持指定了作用域的数据集（管道中定义的数据集）。

## <a name="next-steps"></a>后续步骤
请参阅以下教程，了解使用下列某个工具或 SDK 创建管道和数据集的分步说明。

- [快速入门：使用 .NET 创建数据工厂](quickstart-create-data-factory-dot-net.md)
- [快速入门：使用 PowerShell 创建数据工厂](quickstart-create-data-factory-powershell.md)
- [快速入门：使用 REST API 创建数据工厂](quickstart-create-data-factory-rest-api.md)
- [快速入门：使用 Azure 门户创建数据工厂](quickstart-create-data-factory-portal.md)
