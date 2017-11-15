---
title: "使用数据工厂向/从 Azure SQL 数据仓库复制数据 | Microsoft Docs"
description: "了解如何使用数据工厂将数据从受支持的源存储复制到 Azure SQL 数据仓库，或从 SQL 数据仓库复制到受支持的接收器存储。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: jingwang
ms.openlocfilehash: fa660dd72acb5b19a49fc0100c3c1e5fc8e87dee
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2017
---
# <a name="copy-data-to-or-from-azure-sql-data-warehouse-by-using-azure-data-factory"></a>使用 Azure 数据工厂将数据复制到 Azure SQL 数据仓库或从 Azure SQL 数据仓库复制数据
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [版本 1 - GA](v1/data-factory-azure-sql-data-warehouse-connector.md)
> * [版本 2 - 预览版](connector-azure-sql-data-warehouse.md)

本文概述了如何使用 Azure 数据工厂中的复制活动将数据复制到 Azure SQL 数据仓库以及从 Azure SQL 数据仓库复制数据。 它是基于概述复制活动总体的[复制活动概述](copy-activity-overview.md)一文。

> [!NOTE]
> 本文适用于目前处于预览状态的数据工厂版本 2。 如果使用数据工厂服务版本 1（即正式版 (GA)），请参阅 [V1 中的 Azure SQL 数据仓库连接器](v1/data-factory-azure-sql-data-warehouse-connector.md)。

## <a name="supported-capabilities"></a>支持的功能

可将数据从 Azure SQL 数据仓库复制到任意受支持的接收器数据存储，或从任意受支持的源数据存储复制到 Azure SQL 数据仓库。 有关复制活动支持作为源/接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

具体而言，此 Azure SQL 数据仓库连接器支持：

- 使用 SQL 身份验证复制数据。
- 作为源，使用 SQL 查询或存储过程检索数据。
- 作为接收器，使用 PolyBase 或大容量插入加载数据。 若要获得更好的复制性能，**建议**使用前者。

## <a name="getting-started"></a>入门
可以使用 .NET SDK、Python SDK、Azure PowerShell、REST API 或 Azure 资源管理器模板创建包含复制活动的管道。 有关创建包含复制活动的管道的分步说明，请参阅[复制活动教程](quickstart-create-data-factory-dot-net.md)。

对于特定于 Azure SQL 数据仓库连接器的数据工厂实体，以下部分提供了有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

Azure SQL 数据仓库链接服务支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | type 属性必须设置为：**AzureSqlDW** | 是 |
| connectionString |为 connectionString 属性指定连接到 Azure SQL 数据仓库实例所需的信息。 仅支持基本身份验证。 将此字段标记为 SecureString。 |是 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如果数据存储位于专用网络，则可以使用 Azure 集成运行时或自承载集成运行时。 如果未指定，则使用默认 Azure 集成运行时。 |否 |


> [!IMPORTANT]
> 配置 [Azure SQL 数据仓库防火墙](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure)和数据库服务器以[允许 Azure 服务访问该服务器](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure)。 此外，如果将数据从 Azure 外部（包括配有自我托管集成运行时的本地数据源）复制到 Azure SQL 数据仓库，请针对将数据发送到 Azure SQL 数据仓库的计算机配置适当的 IP 地址范围。

**示例：**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各个部分和属性的完整列表，请参阅数据集一文。 本部分提供 Azure SQL 数据仓库数据集支持的属性列表。

要从/向 Azure SQL 数据仓库复制数据，请将数据集的 type 属性设置为“AzureSqlDWTable”。 支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为：AzureSqlDWTable | 是 |
| tableName |链接服务引用的 Azure SQL 数据仓库实例中的表名称或视图名称。 | 是 |

**示例：**

```json
{
    "name": "AzureSQLDWDataset",
    "properties":
    {
        "type": "AzureSqlDWTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Data Warehouse linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各个部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 Azure SQL 数据仓库源和接收器支持的属性列表。

### <a name="azure-sql-data-warehouse-as-source"></a>Azure SQL 数据仓库作为源

要从 Azure SQL 数据仓库复制数据，请将复制活动中的源类型设置为“SqlDWSource”。 复制活动**源**部分支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动源的 type 属性必须设置为：SqlDWSource | 是 |
| sqlReaderQuery |使用自定义 SQL 查询读取数据。 示例：`select * from MyTable`。 |否 |
| sqlReaderStoredProcedureName |从源表读取数据的存储过程的名称。 存储过程中的最后一条 SQL 语句必须是 SELECT 语句。 |否 |
| storedProcedureParameters |存储过程的参数。<br/>允许的值为：名称/值对。 参数的名称和大小写必须与存储过程参数的名称和大小写匹配。 |否 |

**需要注意的要点：**

- 如果为 SqlDWSource 指定 sqlReaderQuery，则复制活动针对 Azure SQL 数据仓库源运行此查询以获取数据。 此外，也可以通过指定 **sqlReaderStoredProcedureName** 和 **storedProcedureParameters** 来指定存储过程（如果存储过程使用参数）。
- 如果不指定“sqlReaderQuery”或“sqlReaderStoredProcedureName”，则使用在数据集 JSON 的“结构”部分定义的列来构造针对 Azure SQL 数据仓库运行的查询 (`select column1, column2 from mytable`)。 如果数据集定义不具备该“结构”，则从表中选择所有列。
- 使用 sqlReaderStoredProcedureName 时，仍需指定数据集 JSON 中虚拟的 tableName属性。

**示例：使用 SQL 查询**

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlDWSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**示例：使用存储过程**

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlDWSource",
                "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
                "storedProcedureParameters": {
                    "stringData": { "value": "str3" },
                    "identifier": { "value": "$$Text.Format('{0:yyyy}', <datetime parameter>)", "type": "Int"}
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**存储过程定义：**

```sql
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
     select *
     from dbo.UnitTestSrcTable
     where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="azure-sql-data-warehouse-as-sink"></a>Azure SQL 数据仓库作为接收器

要向 Azure SQL 数据仓库复制数据，请将复制活动中的接收器类型设置为“SqlDWSink”。 复制活动接收器部分中支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动源的 type 属性必须设置为：SqlDWSink | 是 |
| allowPolyBase |指示是否使用 PolyBase（如果适用）而不是 BULKINSERT 机制。 <br/><br/> **使用 PolyBase 是将数据加载到 SQL 数据仓库的建议方式。** 有关约束和详细信息，请参阅[使用 PolyBase 将数据加载到 Azure SQL 数据仓库](#use-polybase-to-load-data-into-azure-sql-data-warehouse)部分。<br/><br/>允许的值是：true（默认）和 false。  |否 |
| polyBaseSettings |**allowPolybase** 属性设置为 **true** 时可以指定的一组属性。 |否 |
| rejectValue |指定在查询失败之前可以拒绝的行数或百分比。<br/><br/>有关 PolyBase 的拒绝选项的详细信息，请参阅[创建外部表 (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx) 主题的**参数**部分。 <br/><br/>允许的值是：0（默认）、1、2 … |否 |
| rejectType |指定将 rejectValue 选项指定为文字值还是百分比。<br/><br/>允许值为：值（默认）和百分比。 |否 |
| rejectSampleValue |确定在 PolyBase 重新计算被拒绝行的百分比之前要检索的行数。<br/><br/>允许值为：1、2 … |如果 **rejectType** 是**百分比**，则为“是” |
| useTypeDefault |指定 PolyBase 从文本文件检索数据时如何处理分隔文本文件中的缺失值。<br/><br/>有关此属性的详细信息，请参阅[创建外部文件格式 (Transact SQL)](https://msdn.microsoft.com/library/dn935026.aspx) 中的参数部分。<br/><br/>允许的值是：True、False（默认）。 |否 |
| writeBatchSize |缓冲区大小达到 writeBatchSize 时会数据插入 SQL 表。 仅在未使用 PolyBase 时适用。<br/><br/>允许的值为：整数（行数）。 |否（默认值为 10000） |
| writeBatchTimeout |超时之前等待批插入操作完成时的等待时间。仅在未使用 PolyBase 时适用。<br/><br/>允许的值为：timespan。 示例：“00:30:00”（30 分钟）。 |否 |
| preCopyScript |每次运行时，将数据写入到 Azure SQL 数据仓库之前，指定复制活动要执行的 SQL 查询。 此属性可用于清理预先加载的数据。 |否 |(#repeatability-during-copy)。 |查询语句。 |否 |

**示例：**

```json
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true,
    "polyBaseSettings":
    {
        "rejectType": "percentage",
        "rejectValue": 10.0,
        "rejectSampleValue": 100,
        "useTypeDefault": true
    }
}
```

可在下一节中详细了解如何使用 PolyBase 高效地加载到 SQL 数据仓库。

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>使用 PolyBase 将数据加载到 Azure SQL 数据仓库

使用 **[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)** 是将大量数据加载到高吞吐量的 Azure SQL 数据仓库的有效方法。 可通过使用 PolyBase 而非默认 BULKINSERT 机制实现吞吐量的巨大增加。 请参阅[复制性能参考数量](copy-activity-performance.md#performance-reference)了解详细比较。 有关带有用例的演练，请参阅[在不到 15 分钟的时间里通过 Azure 数据工厂将 1 TB 的数据加载到 Azure SQL 数据仓库](connector-azure-sql-data-warehouse.md)。

* 如果源数据位于 **Azure Blob 或 Azure Data Lake Store** 中，并且格式与 PolyBase 兼容，则可使用 PolyBase 直接复制到 Azure SQL 数据仓库。 有关详细信息，请参阅**[使用 PolyBase 直接复制](#direct-copy-using-polybase)**。
* 如果 PolyBase 最初不支持源数据存储和格式，可改用**[使用 PolyBase 的暂存复制](#staged-copy-using-polybase)**功能。 通过自动将数据转换为 PolyBase 兼容的格式并将数据存储在 Azure Blob 存储中，它还可提供更高的吞吐量。 然后，它会将数据载入 SQL 数据仓库。

### <a name="direct-copy-using-polybase"></a>使用 PolyBase 直接复制

SQL 数据仓库 PolyBase 直接支持作为源并具有特定文件格式要求的 Azure Blob 和 Azure Data Lake Store（使用服务主体）。 如果源数据满足本节所述的标准，则可使用 PolyBase 从源数据存储直接复制到 Azure SQL 数据仓库。 否则，可改用[使用 PolyBase 的暂存复制](#staged-copy-using-polybase)。

> [!TIP]
> 要将数据从 Data Lake Store 有效地复制到 SQL 数据仓库，请从[将 Data Lake Store 与 SQL 数据仓库配合使用时，Azure 数据工厂能够更轻松且方便地观察数据](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/)中了解详细信息。

如果不满足要求，Azure 数据工厂会检查设置，并自动回退到 BULKINSERT 机制以进行数据移动。

1. **源链接服务**的类型为：**AzureStorage** 或 **AzureDataLakeStore**。
2. 输入数据集的类型为：AzureBlob 或 AzureDataLakeStoreFile，`type` 属性下的格式类型为 OrcFormat 或 TextFormat，其配置如下：

   1. `rowDelimiter` 必须是 \n。
   2. `nullValue` 设置为**空字符串** ("")，或者 `treatEmptyAsNull` 设置为“true”。
   3. `encodingName` 设置为“utf-8”，即**默认**值。
   4. 未指定 `escapeChar`、`quoteChar`、`firstRowAsHeader` 和 `skipLineCount`。
   5. `compression` 可为**无压缩**、**GZip** 或 **Deflate**。

    ```json
    "typeProperties": {
       "folderPath": "<blobpath>",
       "format": {
           "type": "TextFormat",
           "columnDelimiter": "<any delimiter>",
           "rowDelimiter": "\n",
           "nullValue": "",
           "encodingName": "utf-8"
       },
       "compression": {
           "type": "GZip",
           "level": "Optimal"
       }
    },
    ```

3. 管道中复制活动的 **BlobSource** 或 **AzureDataLakeStore** 下没有 `skipHeaderLineCount` 设置。
4. 管道中复制活动的 **SqlDWSink** 下没有 `sliceIdentifierColumnName` 设置。 （PolyBase 保证所有数据都已更新或在单次运行中没有任何更新。 若要实现**可重复性**，可使用 `sqlWriterCleanupScript`）。
5. 复制活动的关联内容中没有使用 `columnMapping`。

```json
"activities":[
    {
        "name": "CopyFromAzureBlobToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "BlobDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "SqlDwSink",
                "allowPolyBase": true
            }
        }
    }
]
```

### <a name="staged-copy-using-polybase"></a>使用 PolyBase 的暂存复制

源数据不满足上一部分中介绍的标准时，可通过暂存 Azure Blob 存储（不能是高级存储）启用复制数据。 在这种情况下，Azure 数据工厂会自动转换数据以满足 PolyBase 的数据格式要求，然后使用 PolyBase 将数据加载到 SQL 数据仓库中，然后清理 Blob 存储的临时数据。 有关通常如何通过暂存 Azure Blob 复制数据的详细信息，请参阅[暂存复制](copy-activity-performance.md#staged-copy)。

要使用此功能，请创建 [Azure 存储链接服务](connector-azure-blob-storage.md#linked-service-properties)（引用具有临时 blob 存储的 Azure 存储帐户），并指定复制活动的 `enableStaging` 和 `stagingSettings` 属性，如下方代码所示：

```json
"activities":[
    {
        "name": "CopyFromSQLServerToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "SQLServerDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlDwSink",
                "allowPolyBase": true
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": "MyStagingBlob"
            }
        }
    }
]
```

## <a name="best-practices-when-using-polybase"></a>使用 PolyBase 的最佳实践

除了 [Azure SQL 数据仓库的最佳做法](../sql-data-warehouse/sql-data-warehouse-best-practices.md)中提到的最佳做法外，以下部分提供其他最佳做法。

### <a name="required-database-permission"></a>所需数据库权限

若要使用 PolyBase，要求将数据加载到 SQL 数据仓库的用户具有目标数据库上的[“CONTROL”权限](https://msdn.microsoft.com/library/ms191291.aspx)。 一种实现方法是将该用户添加为“db_owner”角色的成员。 参阅[本节](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization)了解如何进行此操作。

### <a name="row-size-and-data-type-limitation"></a>行大小和数据类型限制

Polybase 加载限制为加载小于 **1 MB** 的行，并且无法加载到 VARCHR(MAX)、NVARCHAR(MAX) 或 VARBINARY(MAX)。 请参阅[此处](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads)。

如果源数据的行大小大于 1 MB，则需要将源表垂直拆分为几个小的源表，其中每个源表的最大行大小不超过限制。 然后可以使用 PolyBase 加载这些较小的表，并在 Azure SQL 数据仓库中将它们合并在一起。

### <a name="sql-data-warehouse-resource-class"></a>SQL 数据仓库资源类

要实现最佳吞吐量，请考虑向通过 PolyBase 将数据加载到 SQL 数据仓库的用户分配更大的资源类。 请参阅[更改用户资源类示例](../sql-data-warehouse/sql-data-warehouse-develop-concurrency.md#changing-user-resource-class-example)，了解如何执行该操作。

### <a name="tablename-in-azure-sql-data-warehouse"></a>Azure SQL 数据仓库中的 tableName

下表提供了示例，说明如何在数据集 JSON 中为架构和表名的各种组合指定 **tableName** 属性。

| DB 架构 | 表名称 | tableName JSON 属性 |
| --- | --- | --- |
| dbo |MyTable |MyTable 或 dbo.MyTable 或 [dbo].[MyTable] |
| dbo1 |MyTable |dbo1.MyTable 或 [dbo1].[MyTable] |
| dbo |My.Table |[My.Table] 或 [dbo].[My.Table] |
| dbo1 |My.Table |[dbo1].[My.Table] |

如果看到以下错误，此问题可能与为 tableName 属性指定的值有关。 有关为 tableName JSON 属性指定值的正确方法，请参阅表。

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

### <a name="columns-with-default-values"></a>具有默认值的列

目前，数据工厂中的 PolyBase 功能只接受与目标表中相同数量的列。 假设一个表包含四列，其中一列由默认值定义。 则输入数据仍应包含四列。 提供包含 3 列的输入数据集将产生与以下消息类似的错误：

```
All columns of the table must be specified in the INSERT BULK statement.
```

NULL 值是特殊形式的默认值。 如果列可为 null，则该列的输入数据（以 blob 为单位）可以为空（输入数据集中不能缺失数据）。 PolyBase 在 Azure SQL 数据仓库中插入 NULL 来表示它们。

## <a name="data-type-mapping-for-azure-sql-data-warehouse"></a>Azure SQL 数据仓库的数据类型映射

从/向 Azure SQL 数据仓库复制数据时，以下映射用于从 Azure SQL 数据仓库数据类型映射到 Azure 数据工厂临时数据类型。 若要了解复制活动如何将源架构和数据类型映射到接收器，请参阅[架构和数据类型映射](copy-activity-schema-and-type-mapping.md)。

| Azure SQL 数据仓库数据类型 | 数据工厂临时数据类型 |
|:--- |:--- |
| bigint |Int64 |
| binary |Byte[] |
| bit |布尔 |
| char |String, Char[] |
| 日期 |DateTime |
| Datetime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| 小数 |小数 |
| FILESTREAM 特性 (varbinary(max)) |Byte[] |
| Float |Double |
| 图像 |Byte[] |
| int |Int32 |
| money |小数 |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numeric |小数 |
| nvarchar |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |小数 |
| sql_variant |对象 * |
| text |String, Char[] |
| time |TimeSpan |
| timestamp |Byte[] |
| tinyint |Byte |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |Xml |

## <a name="next-steps"></a>后续步骤
有关 Azure 数据工厂中复制活动支持作为源和接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md##supported-data-stores-and-formats)。