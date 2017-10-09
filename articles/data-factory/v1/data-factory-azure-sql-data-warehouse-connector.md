---
title: "从 Azure SQL 数据仓库复制数据/将数据复制到 Azure SQL 数据仓库| Microsoft Docs"
description: "了解如何使用 Azure 数据工厂从 Azure SQL 数据仓库复制数据/将数据复制到 Azure SQL 数据仓库"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: d90fa9bd-4b79-458a-8d40-e896835cfd4a
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/04/2017
ms.author: jingwang
robots: noindex
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: ddd7d9ffb4f55f82deb394fbe7ef55e43ba4febd
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---
# <a name="copy-data-to-and-from-azure-sql-data-warehouse-using-azure-data-factory"></a>使用 Azure 数据工厂从 Azure SQL 数据仓库复制数据/将数据复制到 Azure SQL 数据仓库
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [版本 1 - 正式版](data-factory-azure-sql-data-warehouse-connector.md)
> * [版本 2 - 预览版](../connector-azure-sql-data-warehouse.md)

> [!NOTE]
> 本文适用于数据工厂版本 1（即正式版 (GA)）。 如果使用数据工厂服务版本 2（预览版），请参阅 [V2 中的 Azure SQL 数据仓库连接器](../connector-azure-sql-data-warehouse.md)。

本文介绍如何使用 Azure 数据工厂中的复制活动将数据移入/移出 Azure SQL 数据仓库。 它基于[数据移动活动](data-factory-data-movement-activities.md)一文，其中总体概述了如何使用复制活动移动数据。  

> [!TIP]
> 要实现最佳性能，请使用 PolyBase 将数据加载到 Azure SQL 数据仓库。 有关详细信息，请参阅[使用 PolyBase 将数据加载到 Azure SQL 数据仓库](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse)部分。 有关带有用例的演练，请参阅[在不到 15 分钟的时间里通过 Azure 数据工厂将 1 TB 的数据加载到 Azure SQL 数据仓库](data-factory-load-sql-data-warehouse.md)。

## <a name="supported-scenarios"></a>支持的方案
可以将数据**从 Azure SQL 数据仓库**复制到以下数据存储：

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

可以将数据从以下数据存储复制**到 Azure SQL 数据仓库**：

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!TIP]
> 将数据从 SQL Server 或 Azure SQL 数据库复制到 Azure SQL 数据仓库时，如果目标存储中不存在该表，数据工厂可以通过使用源数据存储中的表架构自动在 SQL 数据仓库中创建该表。 有关详细信息，请参阅[自动表创建](#auto-table-creation)。

## <a name="supported-authentication-type"></a>支持的身份验证类型
Azure SQL 数据仓库连接器支持基本身份验证。

## <a name="getting-started"></a>入门
可以使用不同的工具/API 创建包含复制活动的管道，以将数据移入/移出 Azure SQL 数据仓库。

若要创建从/向 Azure SQL 数据仓库复制数据的管道，最简单的方法是使用复制数据向导。 有关使用“复制数据”向导创建管道的快速演练，请参阅[教程：使用数据工厂将数据加载到 SQL 数据仓库](../../sql-data-warehouse/sql-data-warehouse-load-with-data-factory.md)。

也可以使用以下工具创建管道：**Azure 门户**、**Visual Studio**、**Azure PowerShell**、**Azure Resource Manager 模板**、**.NET API** 和 **REST API**。 有关创建包含复制活动的管道的分步说明，请参阅[复制活动教程](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。

无论使用工具还是 API，执行以下步骤都可创建管道，以便将数据从源数据存储移到接收器数据存储：

1. 创建**数据工厂**。 数据工厂可以包含一个或多个管道。 
2. 创建**链接服务**可将输入和输出数据存储链接到数据工厂。 例如，如果要将数据从 Azure Blob 存储复制到 Azure SQL 数据仓库，可创建两个链接服务，将 Azure 存储帐户和 Azure SQL 数据仓库链接到数据工厂。 有关特定于 Azure SQL 数据仓库的链接服务属性，请参阅[链接服务属性](#linked-service-properties)部分。 
3. 创建**数据集**以表示复制操作的输入和输出数据。 在上一个步骤所述的示例中，创建了一个数据集来指定 Blob 容器和包含输入数据的文件夹。 创建了另一个数据集来指定 Azure SQL 数据仓库中用于保存从 Blob 存储复制的数据的表。 有关特定于 Azure SQL 数据仓库的数据集属性，请参阅[数据集属性](#dataset-properties)部分。
4. 创建包含复制活动的**管道**，该活动将一个数据集作为输入，将一个数据集作为输出。 在前面所述的示例中，对复制活动使用 BlobSource 作为源，SqlDWSink 作为接收器。 同样，如果要从 Azure SQL 数据仓库复制到 Azure Blob 存储，则在复制活动中使用 SqlDWSource 和 BlobSink。 有关特定于 Azure SQL 数据仓库的复制活动属性，请参阅[复制活动属性](#copy-activity-properties)部分。 有关如何将数据存储用作源或接收器的详细信息，请单击前面章节中的相应数据存储链接。

使用向导时，会自动创建这些数据工厂实体（链接服务、数据集和管道）的 JSON 定义。 使用工具/API（.NET API 除外）时，使用 JSON 格式定义这些数据工厂实体。  有关用于向/从 Azure SQL 数据仓库复制数据的数据工厂实体的 JSON 定义示例，请参阅本文的 [JSON 示例](#json-examples-for-copying-data-to-and-from-sql-data-warehouse)部分。

对于特定于 Azure SQL 数据仓库的数据工厂实体，以下部分提供了有关用于定义这些实体的 JSON 属性的详细信息：

## <a name="linked-service-properties"></a>链接服务属性
下表提供 Azure SQL 数据仓库链接服务专属 JSON 元素的说明。

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| type |type 属性必须设置为：**AzureSqlDW** |是 |
| connectionString |为 connectionString 属性指定连接到 Azure SQL 数据仓库实例所需的信息。 仅支持基本身份验证。 |是 |

> [!IMPORTANT]
> 配置 [Azure SQL 数据库防火墙](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure)和数据库服务器以[允许 Azure 服务访问该服务器](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure)。 此外，如果将数据从 Azure 外部（包括具有数据工厂网关的本地部署数据源）复制到 Azure SQL 数据仓库，请为将数据发送到 Azure SQL 数据仓库的计算机配置适当 IP 地址范围。

## <a name="dataset-properties"></a>数据集属性
有关可用于定义数据集的节和属性的完整列表，请参阅 [Creating datasets](data-factory-create-datasets.md)（创建数据集）一文。 结构、可用性和数据集 JSON 的策略等部分与所有数据集类型（Azure SQL、Azure blob、Azure 表等）类似。

每种数据集的 typeProperties 节有所不同，该部分提供有关数据在数据存储区中的位置信息。 **AzureSqlDWTable** 类型数据集的 **typeProperties** 节具有以下属性：

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| tableName |链接服务引用的 Azure SQL 数据仓库数据库中的表名称或视图。 |是 |

## <a name="copy-activity-properties"></a>复制活动属性
有关可用于定义活动的各节和属性的完整列表，请参阅[创建管道](data-factory-create-pipelines.md)一文。 名称、说明、输入和输出表格等属性和策略可用于所有类型的活动。

> [!NOTE]
> 复制活动只使用一个输入，只生成一个输出。

而可用于此活动的 typeProperties 节的属性因每个活动类型而异。 对于复制活动，这些属性则因源和接收器的类型而异。

### <a name="sqldwsource"></a>SqlDWSource
源为 **SqlDWSource** 类型时，可在 **typeProperties** 节中使用以下属性：

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| sqlReaderQuery |使用自定义查询读取数据。 |SQL 查询字符串。 例如：从 MyTable 中选择 *。 |否 |
| sqlReaderStoredProcedureName |从源表读取数据的存储过程的名称。 |存储过程的名称。 存储过程中的最后一条 SQL 语句必须是 SELECT 语句。 |否 |
| storedProcedureParameters |存储过程的参数。 |名称/值对。 参数的名称和大小写必须与存储过程参数的名称和大小写匹配。 |否 |

如果为 SqlDWSource 指定 **sqlReaderQuery**，则复制活动针对 Azure SQL 数据仓库源运行此查询以获取数据。

此外，也可以通过指定 **sqlReaderStoredProcedureName** 和 **storedProcedureParameters** 来指定存储过程（如果存储过程获取参数）。

如果不指定 sqlReaderQuery 或 sqlReaderStoredProcedureName，则使用在数据集 JSON 的结构部分定义的列来生成针对 Azure SQL 数据仓库运行的查询。 示例：`select column1, column2 from mytable`。 如果数据集定义不具备该结构，则从表中选择所有列。

#### <a name="sqldwsource-example"></a>SqlDWSource 示例

```JSON
"source": {
    "type": "SqlDWSource",
    "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
    "storedProcedureParameters": {
        "stringData": { "value": "str3" },
        "identifier": { "value": "$$Text.Format('{0:yyyy}', SliceStart)", "type": "Int"}
    }
}
```
**存储过程定义：**

```SQL
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

### <a name="sqldwsink"></a>SqlDWSink
**SqlDWSink** 支持以下属性：

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| sqlWriterCleanupScript |指定复制活动要执行的查询，以便清除特定切片的数据。 有关详细信息，请参阅[可重复性部分](#repeatability-during-copy)。 |查询语句。 |否 |
| allowPolyBase |指示是否使用 PolyBase（如果适用）而不是 BULKINSERT 机制。 <br/><br/> **使用 PolyBase 是将数据加载到 SQL 数据仓库的建议方式。** 有关约束和详细信息，请参阅[使用 PolyBase 将数据加载到 Azure SQL 数据仓库](#use-polybase-to-load-data-into-azure-sql-data-warehouse)部分。 |True <br/>False（默认值） |否 |
| polyBaseSettings |**allowPolybase** 属性设置为 **true** 时可以指定的一组属性。 |&nbsp; |否 |
| rejectValue |指定在查询失败之前可以拒绝的行数或百分比。 <br/><br/>有关 PolyBase 的拒绝选项的详细信息，请参阅[创建外部表 (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx) 主题的**参数**部分。 |0（默认值）、1、2 … |否 |
| rejectType |指定将 rejectValue 选项指定为文字值还是百分比。 |值（默认），百分比 |否 |
| rejectSampleValue |确定在 PolyBase 重新计算被拒绝行的百分比之前要检索的行数。 |1、2 … |如果 **rejectType** 是**百分比**，则为“是” |
| useTypeDefault |指定 PolyBase 从文本文件检索数据时如何处理分隔文本文件中的缺失值。<br/><br/>有关此属性的详细信息，请参阅[创建外部文件格式 (Transact SQL)](https://msdn.microsoft.com/library/dn935026.aspx) 中的参数部分。 |True、False（默认值） |否 |
| writeBatchSize |缓冲区大小达到 writeBatchSize 时会数据插入 SQL 表 |整数（行数） |否（默认值：10000） |
| writeBatchTimeout |超时之前等待批插入操作完成时的等待时间。 |timespan<br/><br/> 示例：“00:30:00”（30 分钟）。 |否 |

#### <a name="sqldwsink-example"></a>SqlDWSink 示例

```JSON
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true
}
```

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>使用 PolyBase 将数据加载到 Azure SQL 数据仓库
使用 **[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)** 是将大量数据加载到高吞吐量的 Azure SQL 数据仓库的有效方法。 可通过使用 PolyBase 而非默认 BULKINSERT 机制实现吞吐量的巨大增加。 请参阅[复制性能参考数量](data-factory-copy-activity-performance.md#performance-reference)了解详细比较。 有关带有用例的演练，请参阅[在不到 15 分钟的时间里通过 Azure 数据工厂将 1 TB 的数据加载到 Azure SQL 数据仓库](data-factory-load-sql-data-warehouse.md)。

* 如果源数据位于 **Azure Blob 或 Azure Data Lake Store** 中，并且格式与 PolyBase 兼容，则可使用 PolyBase 直接复制到 Azure SQL 数据仓库。 有关详细信息，请参阅**[使用 PolyBase 直接复制](#direct-copy-using-polybase)**。
* 如果 PolyBase 最初不支持源数据存储和格式，可改用**[使用 PolyBase 的暂存复制](#staged-copy-using-polybase)**功能。 通过自动将数据转换为 PolyBase 兼容的格式并将数据存储在 Azure Blob 存储中，它还可提供更高的吞吐量。 然后，它会将数据载入 SQL 数据仓库。

如以下示例所示，将 `allowPolyBase` 属性设置为“true”，以便 Azure 数据工厂使用 PolyBase 将数据复制到 Azure SQL 数据仓库。 将 allowPolyBase 设置为“true”时，可使用 `polyBaseSettings` 属性组指定特定于 PolyBase 的属性。 有关可与 polyBaseSettings 配合使用的属性的详细信息，请参阅 [SqlDWSink](#SqlDWSink) 部分。

```JSON
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

### <a name="direct-copy-using-polybase"></a>使用 PolyBase 直接复制
SQL 数据仓库 PolyBase 直接支持作为源并具有特定文件格式要求的 Azure Blob 和 Azure Data Lake Store（使用服务主体）。 如果源数据满足本节所述的标准，则可使用 PolyBase 从源数据存储直接复制到 Azure SQL 数据仓库。 否则，可改用[使用 PolyBase 的暂存复制](#staged-copy-using-polybase)。

> [!TIP]
> 要将数据从 Data Lake Store 有效地复制到 SQL 数据仓库，请从[将 Data Lake Store 与 SQL 数据仓库配合使用时，Azure 数据工厂能够更轻松且方便地观察数据](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/)中了解详细信息。

如果不满足要求，Azure 数据工厂会检查设置，并自动回退到 BULKINSERT 机制以进行数据移动。

1. **源链接服务**的类型为：**AzureStorage** 或**使用服务主体身份验证的 AzureDataLakeStore**。  
2. **输入数据集**的类型为：**AzureBlob** 或 **AzureDataLakeStore**，`type` 属性下的格式类型为 **OrcFormat** 或 **TextFormat**，其配置如下：

   1. `rowDelimiter` 必须是 \n。
   2. `nullValue` 设置为**空字符串** ("")，或者 `treatEmptyAsNull` 设置为“true”。
   3. `encodingName` 设置为“utf-8”，即**默认**值。
   4. 未指定 `escapeChar`、`quoteChar`、`firstRowAsHeader` 和 `skipLineCount`。
   5. `compression` 可为**无压缩**、**GZip** 或 **Deflate**。

    ```JSON
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

### <a name="staged-copy-using-polybase"></a>使用 PolyBase 的暂存复制
源数据不满足上一部分中介绍的标准时，可通过暂存 Azure Blob 存储（不能是高级存储）启用复制数据。 在这种情况下，Azure 数据工厂会自动转换数据以满足 PolyBase 的数据格式要求，然后使用 PolyBase 将数据加载到 SQL 数据仓库中，并在最后清除 Blob 存储的临时数据。 有关通常如何通过暂存 Azure Blob 复制数据的详细信息，请参阅[暂存复制](data-factory-copy-activity-performance.md#staged-copy)。

> [!NOTE]
> 使用 PolyBase 和暂存将数据从本地数据存储复制到 Azure SQL 数据仓库时，如果数据管理网关版本低于 2.4，则网关计算机上需要 JRE（Java 运行时环境），其用于将源数据转换为正确格式。 建议将网关升级到最新版本，以避免此类依赖项。
>

要使用此功能，请创建 [Azure 存储链接服务](data-factory-azure-blob-connector.md#azure-storage-linked-service)（引用具有临时 blob 存储的 Azure 存储帐户），并指定复制活动的 `enableStaging` 和 `stagingSettings` 属性，如下方代码所示：

```json
"activities":[  
{
    "name": "Sample copy activity from SQL Server to SQL Data Warehouse via PolyBase",
    "type": "Copy",
    "inputs": [{ "name": "OnpremisesSQLServerInput" }],
    "outputs": [{ "name": "AzureSQLDWOutput" }],
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
除了 [Azure SQL 数据仓库的最佳做法](../../sql-data-warehouse/sql-data-warehouse-best-practices.md)中提到的最佳做法外，以下部分提供其他最佳做法。

### <a name="required-database-permission"></a>所需数据库权限
若要使用 PolyBase，要求将数据加载到 SQL 数据仓库的用户具有目标数据库上的[“CONTROL”权限](https://msdn.microsoft.com/library/ms191291.aspx)。 一种实现方法是将该用户添加为“db_owner”角色的成员。 参阅[本节](../../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization)了解如何进行此操作。

### <a name="row-size-and-data-type-limitation"></a>行大小和数据类型限制
Polybase 加载限制为加载小于 **1 MB** 的行，并且无法加载到 VARCHR(MAX)、NVARCHAR(MAX) 或 VARBINARY(MAX)。 请参阅[此处](../../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads)。

如果源数据的行大小大于 1 MB，则需要将源表垂直拆分为几个小的源表，其中每个源表的最大行大小不超过限制。 然后可以使用 PolyBase 加载这些较小的表，并在 Azure SQL 数据仓库中将它们合并在一起。

### <a name="sql-data-warehouse-resource-class"></a>SQL 数据仓库资源类
要实现最佳吞吐量，请考虑向通过 PolyBase 将数据加载到 SQL 数据仓库的用户分配更大的资源类。 请参阅[更改用户资源类示例](../../sql-data-warehouse/sql-data-warehouse-develop-concurrency.md)，了解如何执行该操作。

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

## <a name="auto-table-creation"></a>自动表创建
如果使用“复制向导”将数据从 SQL Server 或 Azure SQL 数据库复制到 Azure SQL 数据仓库，并且目标存储中不存在对应于源表的表，则数据工厂可以通过使用源表架构自动在数据仓库中创建该表。

数据工厂在目标存储中创建与源数据存储中具有相同表名称的表。 根据以下类型映射选择列的数据类型。 如果需要，它会执行类型转换，从而修复源存储和目标存储之间的任何不兼容问题。 它还使用轮循机制表分布。

| 源 SQL 数据库列类型 | 目标 SQL DW 列类型（大小限制） |
| --- | --- |
| int | int |
| BigInt | BigInt |
| SmallInt | SmallInt |
| TinyInt | TinyInt |
| Bit | Bit |
| 小数 | 小数 |
| 数字 | 小数 |
| Float | Float |
| Money | Money |
| Real | Real |
| SmallMoney | SmallMoney |
| 二进制 | 二进制 |
| Varbinary | Varbinary（最多 8000） |
| 日期 | 日期 |
| DateTime | DateTime |
| DateTime2 | DateTime2 |
| 时间 | 时间 |
| DateTimeOffset | DateTimeOffset |
| SmallDateTime | SmallDateTime |
| 文本 | Varchar（最多 8000） |
| NText | NVarChar（最多 4000） |
| 映像 | VarBinary（最多 8000） |
| UniqueIdentifier | UniqueIdentifier |
| Char | Char |
| NChar | NChar |
| VarChar | VarChar（最多 8000） |
| NVarChar | NVarChar（最多 4000） |
| Xml | Varchar（最多 8000） |

[!INCLUDE [data-factory-type-repeatability-for-sql-sources](../../../includes/data-factory-type-repeatability-for-sql-sources.md)]

## <a name="type-mapping-for-azure-sql-data-warehouse"></a>Azure SQL 数据仓库的类型映射
如[数据移动活动](data-factory-data-movement-activities.md)一文中所述，复制活动通过以下 2 步方法执行从源类型到接收器类型的自动类型转换：

1. 从本机源类型转换为 .NET 类型
2. 从 .NET 类型转换为本机接收器类型

将数据移入和移出 Azure SQL 数据仓库时，会使用以下从 SQL 类型到 .NET 类型的映射以及从 .NET 类型到 SQL 类型的映射。

此映射与[用于 ADO.NET 的 SQL Server 数据类型映射](https://msdn.microsoft.com/library/cc716729.aspx)相同。

| SQL Server 数据库引擎类型 | .NET Framework 类型 |
| --- | --- |
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

还可以在复制活动定义中将源数据集中的列映射到接收器数据集中的列。 有关详细信息，请参阅[映射 Azure 数据工厂中的数据集列](data-factory-map-columns.md)。

## <a name="json-examples-for-copying-data-to-and-from-sql-data-warehouse"></a>向/从 SQL 数据仓库复制数据的 JSON 示例
以下示例提供示例 JSON 定义，可使用该定义通过 [Azure 门户](data-factory-copy-activity-tutorial-using-azure-portal.md)、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 或 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) 创建管道。 它们演示如何从/向 Azure SQL 数据仓库和 Azure Blob 存储复制数据。 但是，可使用 Azure 数据工厂中的复制活动将数据**直接**从任何源复制到[此处](data-factory-data-movement-activities.md#supported-data-stores-and-formats)所述的任何接收器。

### <a name="example-copy-data-from-azure-sql-data-warehouse-to-azure-blob"></a>示例：将数据从 Azure SQL 数据仓库复制到 Azure Blob
此示例定义以下数据工厂实体：

1. [AzureSqlDW](#linked-service-properties) 类型的链接服务。
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) 类型的链接服务。
3. [AzureSqlDWTable](#dataset-properties) 类型的输入[数据集](data-factory-create-datasets.md)。
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 类型的输出[数据集](data-factory-create-datasets.md)。
5. 包含复制活动的[管道](data-factory-create-pipelines.md)，它使用 [SqlDWSource](#copy-activity-properties) 和 [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)。

示例每小时将时间序列（每小时、每天等）数据从 Azure SQL 数据仓库数据集中的表复制到 blob。 对于这些示例中使用的 JSON 属性，在示例后的部分对其进行描述。

**Azure SQL 数据仓库链接服务：**

```JSON
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
**Azure Blob 存储链接服务：**

```JSON
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
**Azure SQL 数据仓库输入数据集：**

该示例假定已在 Azure SQL 数据仓库中创建表“MyTable”，并且它包含用于时间序列数据的名为“timestampcolumn”的列。

设置“external”: ”true”将告知数据工厂服务：数据集在数据工厂外部且不由数据工厂中的活动生成。

```JSON
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
**Azure Blob 输出数据集：**

数据将写入到新 blob，每隔一小时进行一次（频率：小时，间隔：1）。 根据正在处理的切片的开始时间，动态计算 blob 的文件夹路径。 文件夹路径使用开始时间的年、月、日和小时部分。

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**管道中使用 SqlDWSource 和 BlobSink 的复制活动：**

管道包含配置为使用输入和输出数据集、且计划每小时运行一次的复制活动。 在管道 JSON 定义中，**源**类型设置为 **SqlDWSource**，**接收器**类型设置为 **BlobSink**。 为 **SqlReaderQuery** 属性指定的 SQL 查询选择复制过去一小时的数据。

```JSON
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[  
      {
        "name": "AzureSQLDWtoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureSqlDWInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
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
      }
     ]
   }
}
```
> [!NOTE]
> 在本例中，为 SqlDWSource 指定了 **sqlReaderQuery**。 复制活动针对 Azure SQL 数据仓库源运行此查询以获取数据。
>
> 此外，也可以通过指定 **sqlReaderStoredProcedureName** 和 **storedProcedureParameters** 来指定存储过程（如果存储过程获取参数）。
>
> 如果不指定 sqlReaderQuery 或 sqlReaderStoredProcedureName，则使用在数据集 JSON 的结构部分定义的列来生成针对 Azure SQL 数据仓库运行的查询（从 mytable 选择 column1、column2）。 如果数据集定义不具备该结构，则从表中选择所有列。
>
>

### <a name="example-copy-data-from-azure-blob-to-azure-sql-data-warehouse"></a>示例：将数据从 Azure Blob 复制到 Azure SQL 数据仓库
此示例定义以下数据工厂实体：

1. [AzureSqlDW](#linked-service-properties) 类型的链接服务。
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) 类型的链接服务。
3. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)类型的输入[数据集](data-factory-create-datasets.md)。
4. [AzureSqlDWTable](#dataset-properties) 类型的输出[数据集](data-factory-create-datasets.md)。
5. 包含复制活动的[管道](data-factory-create-pipelines.md)，该复制活动使用 [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) 和 [SqlDWSink](#copy-activity-properties)。

示例每小时将时间序列数据（每小时、每天等）从 Azure Blob 复制到 Azure SQL 数据仓库中的表。 对于这些示例中使用的 JSON 属性，在示例后的部分对其进行描述。

**Azure SQL 数据仓库链接服务：**

```JSON
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
**Azure Blob 存储链接服务：**

```JSON
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
**Azure Blob 输入数据集：**

从新 blob 获取数据，每隔一小时进行一次（频率：小时，间隔：1）。 根据正在处理的切片的开始时间，将对 blob 的文件夹路径和文件名进行动态计算。 文件夹路径使用开始时间的年、月和日部分，文件名使用开始时间的小时部分。 “external”: ”true”设置将告知数据工厂服务：表在数据工厂外部且不由数据工厂中的活动生成。

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": "\n"
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
**Azure SQL 数据仓库输出数据集：**

此示例将数据复制到 Azure SQL 数据仓库中名为“MyTable”的表。 在 Azure SQL 数据仓库中创建表，其列数与 Blob CSV 文件要包含的列数相同。 每隔一小时会向表添加新行。

```JSON
{
  "name": "AzureSqlDWOutput",
  "properties": {
    "type": "AzureSqlDWTable",
    "linkedServiceName": "AzureSqlDWLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**管道中使用 BlobSource 和 SqlDWSink 的复制活动：**

管道包含配置为使用输入和输出数据集、且计划每小时运行一次的复制活动。 在管道 JSON 定义中，**源**类型设置为 **BlobSource**，**接收器**类型设置为 **SqlDWSink**。

```JSON
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[  
      {
        "name": "AzureBlobtoSQLDW",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSqlDWOutput"
          }
        ],
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
      }
      ]
   }
}
```
有关演练，请参阅 Azure SQL 数据仓库文档中的文章[使用 Azure 数据工厂在 15 分钟内将 1 TB 数据加载到 Azure SQL 数据仓库](data-factory-load-sql-data-warehouse.md)和[使用 Azure 数据工厂加载数据](../../sql-data-warehouse/sql-data-warehouse-get-started-load-with-azure-data-factory.md)。

## <a name="performance-and-tuning"></a>性能和优化
请参阅[复制活动性能和优化指南](data-factory-copy-activity-performance.md)，了解影响 Azure 数据工厂中数据移动（复制活动）性能的关键因素以及各种优化方法。

