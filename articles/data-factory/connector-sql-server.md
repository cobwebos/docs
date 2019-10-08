---
title: 使用 Azure 数据工厂向/从 SQL Server 复制数据 | Microsoft Docs
description: 了解如何使用 Azure 数据工厂将数据移入和移出本地或 Azure VM 中的 SQL Server 数据库。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: jingwang
ms.openlocfilehash: b87676e773c4b7714a3b5ef21a6be703e0e3761a
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2019
ms.locfileid: "72001377"
---
# <a name="copy-data-to-and-from-sql-server-by-using-azure-data-factory"></a>使用 Azure 数据工厂向/从 SQL Server 复制数据
> [!div class="op_single_selector" title1="选择要使用的 Azure 数据工厂的版本："]
> * [版本 1](v1/data-factory-sqlserver-connector.md)
> * [当前版本](connector-sql-server.md)

本文概述了如何使用 Azure 数据工厂中的复制活动从/向 SQL Server 数据库复制数据。 本文基于总体概述复制活动的[复制活动概述](copy-activity-overview.md)一文。

## <a name="supported-capabilities"></a>支持的功能

以下活动支持此 SQL Server 连接器：

- 带有[支持的源或接收器矩阵](copy-activity-overview.md)的[复制活动](copy-activity-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)
- [GetMetadata 活动](control-flow-get-metadata-activity.md)

可以将 SQL Server 数据库中的数据复制到任何支持的接收器数据存储。 或者，可将数据从任何支持的源数据存储复制到 SQL Server 数据库。 有关复制活动支持作为源或接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

具体而言，此 SQL Server 连接器支持：

- SQL Server 版本 2016、2014、2012、2008 R2、2008 和 2005。
- 使用 SQL 或 Windows 身份验证复制数据。
- 作为源，使用 SQL 查询或存储过程检索数据。
- 作为接收器，在复制期间将数据追加到目标表，或调用带有自定义逻辑的存储过程。

不支持[SQL Server Express LocalDB](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-express-localdb?view=sql-server-2017) 。

>[!NOTE]
>此连接器目前不支持 SQL Server [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017)。 为了解决此问题，可以使用[通用 ODBC 连接器](connector-odbc.md)和 SQL Server ODBC 驱动程序。 按照[此指南](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=sql-server-2017)完成 ODBC 驱动程序下载和连接字符串配置。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

对于特定于 SQL Server 数据库连接器的数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

SQL Server 链接服务支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | type 属性必须设置为 **SqlServer**。 | 是 |
| connectionString |指定使用 SQL 身份验证或 Windows 身份验证连接到 SQL Server 数据库时所需的 **connectionString** 信息。 请参阅以下示例。<br/>将此字段标记为 **SecureString**，以便安全地将其存储在 Azure 数据工厂中。 还可以在 Azure Key Vault 中输入密码。 如果使用 SQL 身份验证，请从连接字符串中提取 `password` 配置。 有关详细信息，请参阅表格后面的 JSON 示例，以及[在 Azure Key Vault 中存储凭据](store-credentials-in-key-vault.md)。 |是 |
| userName |如果使用 Windows 身份验证，请指定用户名。 例如，**domainname\\username**。 |否 |
| password |指定为用户名指定的用户帐户的密码。 将此字段标记为 **SecureString**，以便安全地将其存储在 Azure 数据工厂中。 或者，可以[引用 Azure Key Vault 中存储的机密](store-credentials-in-key-vault.md)。 |否 |
| connectVia | 此[集成运行时](concepts-integration-runtime.md)用于连接到数据存储。 从[必备组件](#prerequisites)部分了解详细信息。 如果未指定，则使用默认 Azure Integration Runtime。 |否 |

>[!TIP]
>如果遇到错误（错误代码为“UserErrorFailedToConnectToSqlServer”，且消息如“数据库的会话限制为 XXX 且已达到。”），请将 `Pooling=false` 添加到连接字符串中，然后重试。

**示例 1：使用 SQL 身份验证**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**示例 2：将 SQL 身份验证与 Azure Key Vault 中的密码结合使用**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;"
            },
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**示例 3：使用 Windows 身份验证**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=True;"
            },
            "userName": "<domain\\username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
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

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 本部分提供 SQL Server 数据集支持的属性列表。

从/向 SQL Server 数据库复制数据时支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为 SqlServerTable。 | 是 |
| schema | 架构的名称。 |对于源为“No”，对于接收器为“Yes”  |
| table | 表/视图的名称。 |对于源为“No”，对于接收器为“Yes”  |
| tableName | 具有架构的表/视图的名称。 支持此属性是为了向后兼容。 对于新工作负荷，请使用 `schema` 并 `table`。 | 对于源为“No”，对于接收器为“Yes” |

**示例**

```json
{
    "name": "SQLServerDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<SQL Server linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "schema": "<schema_name>",
            "table": "<table_name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的节和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 SQL Server 源和接收器支持的属性列表。

### <a name="sql-server-as-a-source"></a>SQL Server 作为源

要从 SQL Server 复制数据，请将复制活动中的源类型设置为 SqlSource。 复制活动的 source 节支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动 source 节的 type 属性必须设置为 SqlSource。 | 是 |
| sqlReaderQuery |使用自定义 SQL 查询读取数据。 例如 `select * from MyTable`。 |否 |
| sqlReaderStoredProcedureName |此属性是从源表读取数据的存储过程的名称。 最后一个 SQL 语句必须是存储过程中的 SELECT 语句。 |否 |
| storedProcedureParameters |这些参数用于存储过程。<br/>允许的值为名称或值对。 参数的名称和大小写必须与存储过程参数的名称和大小写匹配。 |否 |

**需要注意的要点：**

- 如果为 **SqlSource** 指定 **sqlReaderQuery**，则复制活动针对 SQL Server 源运行此查询以获取数据。 也可通过指定 sqlReaderStoredProcedureName 和 storedProcedureParameters 来指定存储过程，前提是存储过程使用参数。
- 如果不指定 **sqlReaderQuery** 或 **sqlReaderStoredProcedureName**，则数据集 JSON 的“structure”节中定义的列用于构建查询。 查询 `select column1, column2 from mytable` 针对 SQL Server 运行。 如果数据集定义没有“structure”，则会从表中选择所有列。

示例：**使用 SQL 查询**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Server input dataset name>",
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
                "type": "SqlSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

示例：**使用存储过程**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Server input dataset name>",
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
                "type": "SqlSource",
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

**存储过程定义**

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

### <a name="sql-server-as-a-sink"></a>SQL Server 作为接收器

> [!TIP]
> 若要详细了解支持的写入行为、配置和最佳做法，请参阅[将数据加载到 SQL Server 中的最佳做法](#best-practice-for-loading-data-into-sql-server)。

要向 SQL Server 复制数据，请将复制活动中的接收器类型设置为 SqlSink。 复制活动的 sink 节支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动的 sink 的 type 属性必须设置为 SqlSink。 | 是 |
| writeBatchSize |每批要插入到 SQL 表中的行数。<br/>允许的值为表示行数的整数。 默认情况下，Azure 数据工厂会根据行大小动态确定适当的批大小。 |否 |
| writeBatchTimeout |此属性指定超时前等待批插入操作完成的时间。<br/>允许的值是指时间跨度。 例如，“00:30:00”表示 30 分钟。 如果未指定任何值，则超时默认为 "02:00:00"。 |否 |
| preCopyScript |此属性指定将数据写入到 SQL Server 中之前复制活动要运行的 SQL 查询。 每次运行复制仅调用该查询一次。 可以使用此属性清除预加载的数据。 |否 |
| sqlWriterStoredProcedureName | 定义如何将源数据应用于目标表的存储过程的名称。 <br/>此存储过程由每个批处理调用。 若要执行仅运行一次且与源数据无关的操作（例如删除或截断），请使用 `preCopyScript` 属性。 | 否 |
| storedProcedureTableTypeParameterName |存储过程中指定的表类型的参数名称。  |否 |
| sqlWriterTableType |要在存储过程中使用的表类型名称。 通过复制活动，使移动数据在具备此表类型的临时表中可用。 然后，存储过程代码可合并复制数据和现有数据。 |否 |
| storedProcedureParameters |存储过程的参数。<br/>允许的值为名称和值对。 参数的名称和大小写必须与存储过程参数的名称和大小写匹配。 | 否 |
| tableOption | 指定是否根据源架构自动创建接收器表（如果不存在）。 在复制活动中配置了 sink 指定存储过程或暂存副本时，不支持创建自动表。 允许的值为： `none` （默认值） @no__t 为-1。 |否 |

**示例 1：追加数据**

```json
"activities":[
    {
        "name": "CopyToSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Server output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "writeBatchSize": 100000,
                "tableOption": "autoCreate"
            }
        }
    }
]
```

**示例 2：在复制过程中调用存储过程**

请参阅[调用 SQL 接收器的存储过程](#invoke-a-stored-procedure-from-a-sql-sink)，了解更多详细信息。

```json
"activities":[
    {
        "name": "CopyToSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Server output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
                "storedProcedureTableTypeParameterName": "MyTable",
                "sqlWriterTableType": "MyTableType",
                "storedProcedureParameters": {
                    "identifier": { "value": "1", "type": "Int" },
                    "stringData": { "value": "str1" }
                }
            }
        }
    }
]
```

## <a name="best-practice-for-loading-data-into-sql-server"></a>将数据加载到 SQL Server 中的最佳做法

将数据复制到 SQL Server 中时，可能需要不同的写入行为：

- [追加](#append-data)：我的源数据只包含新记录。
- [更新插入](#upsert-data)：我的源数据包含插入和更新内容。
- [覆盖](#overwrite-the-entire-table)：我需要每次都重新加载整个维度表。
- [使用自定义逻辑进行写入](#write-data-with-custom-logic)：在将数据最终插入目标表之前，我需要额外的处理。

有关如何在 Azure 数据工厂中进行配置和最佳做法，请参阅相应的部分。

### <a name="append-data"></a>追加数据

追加数据是此 SQL Server 接收器连接器的默认行为。 Azure 数据工厂执行批量插入，以有效地在表中写入数据。 可以相应地在复制活动中配置源和接收器。

### <a name="upsert-data"></a>更新插入数据

**选项 1：** 如果有大量的数据需要复制，请使用以下方法执行更新插入： 

- 首先，使用[临时表](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables)通过复制活动批量加载所有记录。 由于系统不记录对临时表执行的操作，你可以在数秒内加载数百万条记录。
- 在 Azure 数据工厂中运行存储过程活动，以应用 [MERGE](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) 或 INSERT/UPDATE 语句。 使用临时表作为源，以单个事务的形式执行所有更新或插入操作。 这样可以减少往返次数和日志操作次数。 在存储过程活动结束时，可以截断临时表，使之可以用于下一更新插入循环。

例如，在 Azure 数据工厂中，可以使用**复制活动**创建一个管道，并将其与**存储过程活动**相链接。 前者将数据从源存储复制到数据集中的数据库临时表（例如，表名为 **##UpsertTempTable** 的表）。 然后，后者调用一个存储过程，以将临时表中的源数据合并到目标表中，并清理临时表。

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

在数据库中使用 MERGE 逻辑定义一个存储过程（如以下示例所示），以便从上述存储过程活动指向该过程。 假设目标是包含三个列的 **Marketing** 表：**ProfileID**、**State** 和 **Category**。 根据 **ProfileID** 列执行更新插入。

```sql
CREATE PROCEDURE [dbo].[spMergeData]
AS
BEGIN
    MERGE TargetTable AS target
    USING ##UpsertTempTable AS source
    ON (target.[ProfileID] = source.[ProfileID])
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT matched THEN
        INSERT ([ProfileID], [State], [Category])
      VALUES (source.ProfileID, source.State, source.Category);
    
    TRUNCATE TABLE ##UpsertTempTable
END
```

**选项 2：** 也可选择[在复制活动中调用存储过程](#invoke-a-stored-procedure-from-a-sql-sink)。 此方法针对源表中的每个行运行，而不是在复制活动中使用批量插入作为默认方法，因此它不适用于大规模更新插入。

### <a name="overwrite-the-entire-table"></a>覆盖整个表

可以在复制活动接收器中配置 **preCopyScript** 属性。 在此情况下，对于运行的每个复制活动，Azure 数据工厂会先运行脚本。 然后，运行复制来插入数据。 例如，若要使用最新数据覆盖整个表，请指定一个脚本，以先删除所有记录，然后从源批量加载新数据。

### <a name="write-data-with-custom-logic"></a>使用自定义逻辑写入数据

使用自定义逻辑写入数据的步骤与[更新插入数据](#upsert-data)部分中的描述类似。 如果在将源数据大规模地最终插入目标表之前需要应用额外的处理，可执行以下两项操作之一： 

- 先加载到临时表，然后调用存储过程。 
- 在复制过程中调用存储过程。

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a> 调用 SQL 接收器的存储过程

将数据复制到 SQL Server 数据库时，还可通过其他参数配置并调用某个用户指定的存储过程。 存储过程功能利用[表值参数](https://msdn.microsoft.com/library/bb675163.aspx)。

> [!TIP]
> 调用存储过程时，会逐行处理数据，而不使用批量操作，我们不建议将批量操作用于大规模复制。 从[将数据加载到 SQL Server 中的最佳做法](#best-practice-for-loading-data-into-sql-server)中了解更多信息。

当内置复制机制无法使用时，还可使用存储过程。 例如，在将源数据最终插入目标表之前应用额外的处理。 额外处理的示例包括合并列、查找其他值以及将数据插入多个表。

以下示例演示如何使用存储过程，在 SQL Server 数据库中的表内执行 upsert。 假设输入数据和接收器 **Marketing** 表各有三列：**ProfileID**、**State** 和 **Category**。 根据**配置文件 id**列执行 upsert，并仅将其应用于名为 "ProductA" 的特定类别。

1. 在数据库中，使用与 **sqlWriterTableType** 相同的名称定义表类型。 表类型的架构与输入数据返回的架构相同。

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL，
        [Category] [varchar](256) NOT NULL
    )
    ```

2. 在数据库中，使用与 **SqlWriterStoredProcedureName** 相同的名称定义存储过程。 它可处理来自指定源的输入数据，并将其合并到输出表中。 存储过程中的表类型的参数名称与数据集中定义的 **tableName** 相同。

    ```sql
    CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @category varchar(256)
    AS
    BEGIN
    MERGE [dbo].[Marketing] AS target
    USING @Marketing AS source
    ON (target.ProfileID = source.ProfileID and target.Category = @category)
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT MATCHED THEN
        INSERT (ProfileID, State, Category)
        VALUES (source.ProfileID, source.State, source.Category);
    END
    ```

3. 在 Azure 数据工厂中，在复制活动中定义**SQL 接收器**部分，如下所示：

    ```json
    "sink": {
        "type": "SqlSink",
        "SqlWriterStoredProcedureName": "spOverwriteMarketing",
        "storedProcedureTableTypeParameterName": "Marketing",
        "SqlWriterTableType": "MarketingType",
        "storedProcedureParameters": {
            "category": {
                "value": "ProductA"
            }
        }
    }
    ```

## <a name="data-type-mapping-for-sql-server"></a>SQL Server 的数据类型映射

从/向 SQL Server 复制数据时，以下映射用于从 SQL Server 数据类型映射到 Azure 数据工厂临时数据类型。 若要了解复制活动如何将源架构和数据类型映射到接收器，请参阅[架构和数据类型映射](copy-activity-schema-and-type-mapping.md)。

| SQL Server 数据类型 | Azure 数据工厂临时数据类型 |
|:--- |:--- |
| bigint |Int64 |
| binary |Byte[] |
| bit |Boolean |
| char |String, Char[] |
| date |DateTime |
| Datetime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM attribute (varbinary(max)) |Byte[] |
| Float |Double |
| image |Byte[] |
| int |Int32 |
| money |Decimal |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numeric |Decimal |
| nvarchar |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Object |
| text |String, Char[] |
| time |TimeSpan |
| timestamp |Byte[] |
| tinyint |Int16 |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |Xml |

>[!NOTE]
> 对于映射到十进制临时类型的数据类型，目前 Azure 数据工厂支持的最大精度为 28。 如果数据需要的精度大于 28，请考虑在 SQL 查询中将其转换为字符串。

## <a name="lookup-activity-properties"></a>查找活动属性

若要了解有关属性的详细信息，请检查[查找活动](control-flow-lookup-activity.md)。

## <a name="getmetadata-activity-properties"></a>GetMetadata 活动属性

若要了解有关属性的详细信息，请查看[GetMetadata 活动](control-flow-get-metadata-activity.md) 

## <a name="troubleshoot-connection-issues"></a>排查连接问题

1. 将 SQL Server 实例配置为接受远程连接。 启动“SQL Server Management Studio”，右键单击“服务器”并选择“属性”。 从列表中选择“连接”，并选中“允许远程连接到此服务器”复选框。

    ![启用远程连接](media/copy-data-to-from-sql-server/AllowRemoteConnections.png)

    有关详细步骤，请参阅[配置远程访问服务器配置选项](https://msdn.microsoft.com/library/ms191464.aspx)。

2. 启动“SQL Server 配置管理器”。 针对所需实例展开“SQL Server 网络配置”，并选择“MSSQLSERVER 的协议”。 协议将显示在右窗格中。 右键单击“TCP/IP”并选择“启用”以启用 TCP/IP。

    ![启用 TCP/IP](./media/copy-data-to-from-sql-server/EnableTCPProptocol.png)

    有关详细信息和启用 TCP/IP 协议的其他方法，请参阅[启用或禁用服务器网络协议](https://msdn.microsoft.com/library/ms191294.aspx)。

3. 在同一窗口中，双击“TCP/IP”以启动“TCP/IP 属性”窗口。
4. 切换到“IP 地址”选项卡。向下滚动到“IPAll”部分。 记下“TCP 端口”的值。 默认值为 **1433**。
5. 在计算机上创建 Windows 防火墙规则，以便允许通过此端口传入流量。 
6. **验证连接**：若要使用完全限定名称连接到 SQL Server，请从另一台计算机使用 SQL Server Management Studio。 例如 `"<machine>.<domain>.corp.<company>.com,1433"`。

## <a name="next-steps"></a>后续步骤
有关 Azure 数据工厂中复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md##supported-data-stores-and-formats)。
