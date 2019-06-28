---
title: 使用 Azure 数据工厂向/从 Azure SQL 数据库托管实例复制数据 | Microsoft Docs
description: 了解如何使用 Azure 数据工厂将数据移入和移出 Azure SQL 数据库托管实例。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: jingwang
ms.openlocfilehash: 3e1978c761c365125ac94a1ecbef5f9ac7375eba
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/24/2019
ms.locfileid: "67338608"
---
# <a name="copy-data-to-and-from-azure-sql-database-managed-instance-by-using-azure-data-factory"></a>使用 Azure 数据工厂向/从 Azure SQL 数据库托管实例复制数据

本文概述了如何使用 Azure 数据工厂中的复制活动从/向 Azure SQL 数据库托管实例复制数据。 本文是根据总体概述复制活动的[复制活动概述](copy-activity-overview.md)一文编写的。

## <a name="supported-capabilities"></a>支持的功能

可将数据从 Azure SQL 数据库托管实例复制到任何支持的接收器数据存储。 还可以将数据从任何支持的源数据存储复制到托管实例。 有关复制活动支持作为源和接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

具体而言，此 Azure SQL 数据库托管实例连接器支持：

- 使用 SQL 身份验证复制数据。
- 作为源，通过使用 SQL 查询或存储的过程检索数据。
- 作为接收器，在复制期间将数据追加到目标表，或调用带有自定义逻辑的存储过程。

>[!NOTE]
>Azure SQL 数据库托管实例[Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-mi-current)现在不支持此连接器。 若要解决，可以使用[泛型 ODBC 连接器](connector-odbc.md)和通过自承载的集成运行时的 SQL Server ODBC 驱动程序。 请按照[本指南](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-mi-current)使用 ODBC 驱动程序下载和连接字符串配置。

>[!NOTE]
>服务主体和托管标识身份验证目前不支持此连接器。 若要解决，请选择 Azure SQL 数据库连接器，并手动指定你的托管实例的服务器。

## <a name="prerequisites"></a>必备组件

若要访问 Azure SQL 数据库托管实例[公共终结点](../sql-database/sql-database-managed-instance-public-endpoint-securely.md)，可以使用 Azure 数据工厂托管 Azure 集成运行时。 请确保启用的公共终结点，并且还允许公共终结点上的流量的网络安全组，以便 Azure 数据工厂可以连接到你的数据库。 有关详细信息，请参阅[本指南](../sql-database/sql-database-managed-instance-public-endpoint-configure.md)。

若要访问 Azure SQL 数据库托管实例的专用终结点，设置[自我托管集成运行时](create-self-hosted-integration-runtime.md)，可以访问数据库。 如果预配你的托管实例所在的虚拟网络中的自承载的集成运行时，请确保在集成运行时计算机比你的托管实例的不同子网。 如果你比你的托管实例设置不同的虚拟网络中自承载的集成运行时，可以使用虚拟网络对等互连或虚拟网络连接到虚拟网络。 有关详细信息，请参阅[将应用程序连接到 Azure SQL 数据库托管实例](../sql-database/sql-database-managed-instance-connect-app.md)。

## <a name="get-started"></a>开始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

以下部分提供有关用于定义于 Azure SQL 数据库托管实例连接器特定的 Azure 数据工厂实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

Azure SQL 数据库托管实例链接的服务支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | type 属性必须设置为 **SqlServer**。 | 是 |
| connectionString |此属性指定**connectionString**使用 SQL 身份验证连接到托管实例所需的信息。 有关详细信息，请参阅以下示例。 <br/>将为此字段标记**SecureString**安全地将其存储在 Azure 数据工厂。 此外可以将密码放在 Azure 密钥保管库中。 如果它是 SQL 身份验证，请将拉`password`带连接字符串配置。 有关详细信息，请参阅下表的 JSON 示例和[在 Azure 密钥保管库中存储凭据](store-credentials-in-key-vault.md)。 |是 |
| connectVia | 此[集成运行时](concepts-integration-runtime.md)用于连接到数据存储。 如果你的托管的实例具有一个公共终结点和 Azure 数据工厂可以访问它，可以使用自承载的集成运行时或 Azure 集成运行时。 如果未指定，则使用默认 Azure 集成运行时。 |是 |

**示例 1：使用 SQL 身份验证**

默认端口为 1433年。 如果你使用 Azure SQL 数据库托管实例与公共终结点，显式指定端口 3342。

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<hostname,port>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**示例 2：使用 SQL 身份验证和 Azure 密钥保管库中的密码**

默认端口为 1433年。 如果你使用 Azure SQL 数据库托管实例与公共终结点，显式指定端口 3342。

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<hostname,port>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;"
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

## <a name="dataset-properties"></a>数据集属性

如需可用于定义数据集的节和属性的完整列表，请参阅有关数据集的文章。 本节提供 Azure SQL 数据库托管实例数据集支持的属性列表。

若要向 / 从 Azure SQL 数据库托管实例，请复制数据，支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为 SqlServerTable  。 | 是 |
| tableName |此属性是链接服务引用的数据库实例中的表名称或视图名称。 | 对于源为“No”，对于接收器为“Yes” |

**示例**

```json
{
    "name": "AzureSqlMIDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<Managed Instance linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的节和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 此节提供 Azure SQL 数据库托管实例源和和接收器支持的属性列表。

### <a name="azure-sql-database-managed-instance-as-a-source"></a>将 Azure SQL 数据库托管实例作为源

若要从 Azure SQL 数据库托管实例复制数据，请将复制活动中的源类型设置为“SqlSource”  。 复制活动的 source 节支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动 source 节的 type 属性必须设置为 SqlSource  。 | 是 |
| sqlReaderQuery |此属性使用自定义 SQL 查询来读取数据。 例如 `select * from MyTable`。 |否 |
| sqlReaderStoredProcedureName |此属性是从源表读取数据的存储过程的名称。 存储过程中的最后一条 SQL 语句必须是 SELECT 语句。 |否 |
| storedProcedureParameters |这些参数用于存储过程。<br/>允许的值为名称或值对。 参数的名称和大小写必须与存储过程参数的名称和大小写匹配。 |否 |

**请注意以下几点：**

- 如果为 **SqlSource** 指定 sqlReaderQuery  ，则复制活动针对托管实例源运行此查询以获取数据。 也可通过指定 sqlReaderStoredProcedureName 和 storedProcedureParameters 来指定存储过程，前提是存储过程使用参数   。
- 如果不指定 **sqlReaderQuery** 或 **sqlReaderStoredProcedureName** 属性，则数据集 JSON 的“structure”节中定义的列用于构建查询。 查询 `select column1, column2 from mytable` 针对托管实例运行。 如果数据集定义没有“structure”，则会从表中选择所有列。

示例：**使用 SQL 查询**

```json
"activities":[
    {
        "name": "CopyFromAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Managed Instance input dataset name>",
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
        "name": "CopyFromAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Managed Instance input dataset name>",
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

### <a name="azure-sql-database-managed-instance-as-a-sink"></a>将 Azure SQL 数据库托管实例作为接收器

> [!TIP]
> 了解有关受支持的写入行为、 配置和最佳实践的详细信息[最佳做法，用于将数据加载到 Azure SQL 数据库托管实例](#best-practice-for-loading-data-into-azure-sql-database-managed-instance)。

若要向 Azure SQL 数据库托管实例复制数据，请将复制活动中的接收器类型设置为“SqlSink”  。 复制活动的 sink 节支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动的 sink 的 type 属性必须设置为 SqlSink  。 | 是 |
| writeBatchSize |要插入 SQL 表的行数*每个批处理*。<br/>允许的值为表示行数的整数。 默认情况下，Azure 数据工厂动态确定基于行大小的合适的批大小。  |否 |
| writeBatchTimeout |此属性指定超时前等待批插入操作完成的时间。<br/>允许的值适用时间跨度。 例如，“00:30:00”表示 30 分钟。 |否 |
| preCopyScript |此属性指定复制活动将数据写入到托管实例之前运行的 SQL 查询。 每次运行复制仅调用该查询一次。 可以使用此属性清除预加载的数据。 |否 |
| sqlWriterStoredProcedureName |此名称所适用的存储过程定义如何将源数据应用于目标表。 <br/>此存储过程由每个批处理调用  。 若要执行仅运行一次且与源数据无关的操作（例如删除或截断），请使用 `preCopyScript` 属性。 |否 |
| storedProcedureParameters |这些参数用于存储过程。<br/>允许的值为名称或值对。 参数的名称和大小写必须与存储过程参数的名称和大小写匹配。 |否 |
| sqlWriterTableType |此属性指定要在存储过程中使用的表类型名称。 通过复制活动，使移动数据在具备此表类型的临时表中可用。 然后，存储过程代码可合并复制数据和现有数据。 |否 |

**示例 1：追加数据**

```json
"activities":[
    {
        "name": "CopyToAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Managed Instance output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "writeBatchSize": 100000
            }
        }
    }
]
```

**示例 2：在复制过程中调用存储的过程**

请参阅[调用 SQL 接收器的存储过程](#invoke-a-stored-procedure-from-a-sql-sink)，了解更多详细信息。

```json
"activities":[
    {
        "name": "CopyToAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Managed Instance output dataset name>",
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
                "sqlWriterTableType": "CopyTestTableType",
                "storedProcedureParameters": {
                    "identifier": { "value": "1", "type": "Int" },
                    "stringData": { "value": "str1" }
                }
            }
        }
    }
]
```

## <a name="best-practice-for-loading-data-into-azure-sql-database-managed-instance"></a>将数据加载到 Azure SQL 数据库托管实例的最佳做法

在将数据复制到 Azure SQL 数据库托管实例时，可能需要不同的写入行为：

- [追加](#append-data):我的源数据具有新的记录。
- [更新插入](#upsert-data):我的源数据已插入和更新。
- [覆盖](#overwrite-the-entire-table):我想要每次重新加载整个维度表。
- [使用自定义逻辑编写](#write-data-with-custom-logic):我需要额外的处理之前最后一次插入到目标表。 

请参阅如何在 Azure 数据工厂和最佳实践中配置的相应部分。

### <a name="append-data"></a>追加数据

将数据追加为此 Azure SQL 数据库托管实例接收器连接器的默认行为。 Azure 数据工厂执行大容量插入以高效率地写入到表。 可以配置源和接收器相应地将复制活动中。

### <a name="upsert-data"></a>更新插入数据

**选项 1：** 当您有大量的数据复制，请使用以下方法来执行 upsert: 

- 首先，使用[临时表](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables)使用复制活动的所有记录的大容量加载。 对临时表执行的操作而不记录，因为您可以在数秒内加载数百万条记录。
- 在 Azure 数据工厂应用中运行存储的过程活动[合并](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current)或插入/更新语句。 使用临时表的源上执行所有更新或插入作为单个事务。 在这种方式，减少往返和日志操作的数量。 在存储的过程活动结束时，可以准备就绪可供下一步的 upsert 周期而被截断临时表。

例如，在 Azure 数据工厂中，您可以创建管道，其中包含**的复制活动**与关联**存储过程活动**。 前者将数据从复制的源存储到临时表，例如， **##UpsertTempTable**，作为在数据集中的表名称。 然后后者调用存储的过程来合并到目标表的临时表中的源数据和清理临时表。

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

在数据库中，定义合并逻辑，如以下示例前, 一个存储的过程活动从指向的存储的过程。 假设目标**市场营销**的三个列的表：**ProfileID**、**State** 和 **Category**。 执行基于 upsert **ProfileID**列。

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

**选项 2：** 你也可以选择[调用的复制活动中的存储的过程](#invoke-a-stored-procedure-from-a-sql-sink)。 这种方法运行而不是作为默认的方法并不适合大规模 upsert 的复制活动中使用大容量插入源表中的每个行。

### <a name="overwrite-the-entire-table"></a>覆盖整个表

你可以配置**preCopyScript**中复制活动接收器的属性。 在这种情况下，对于每个复制活动运行，Azure 数据工厂运行该脚本第一次。 然后它将运行以插入数据的副本。 例如，若要使用最新数据覆盖整个表，请指定用于大容量之前先删除所有记录的脚本从源加载新数据。

### <a name="write-data-with-custom-logic"></a>写入自定义逻辑数据。

编写使用自定义逻辑的数据的步骤是类似于中所述[Upsert 数据](#upsert-data)部分。 你需要将应用额外处理之前最后一次插入的源数据插入目标表，对于较大的比例，您可以执行两个条件之一操作： 

- 加载到临时表，然后调用存储的过程。
- 在复制过程中调用存储的过程。

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a> 调用 SQL 接收器的存储过程

当将数据复制到 Azure SQL 数据库托管实例时，还可以配置和调用使用其他参数的用户指定的存储的过程。

> [!TIP]
> 调用存储的过程处理的数据行的行而不是通过使用大容量操作，我们不建议用于大规模复制。 了解详细信息[最佳做法，用于将数据加载到 Azure SQL 数据库托管实例](#best-practice-for-loading-data-into-azure-sql-database-managed-instance)。

当内置复制机制无法使用时，还可使用存储过程。 例如，当你想要应用之前最后一次插入的源数据插入目标表的额外处理。 一些额外的处理示例是当你想要合并列、 查找其他值，并将数据插入到多个表。

以下示例演示如何使用存储过程，在 SQL Server 数据库中的表内执行 upsert。 假设输入的数据和接收器**市场营销**每个表具有三个列：**ProfileID**、**State** 和 **Category**。 基于 ProfileID 列执行 upsert，并仅将其应用于特定类别  。

**输出数据集：** "TableName"是表类型参数中的同名存储过程，如以下存储的过程脚本中所示：

```json
{
    "name": "AzureSqlMIDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<Managed Instance linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "Marketing"
        }
    }
}
```

定义**SQL 接收器**部分将复制活动中，如下所示：

```json
"sink": {
    "type": "SqlSink",
    "SqlWriterTableType": "MarketingType",
    "SqlWriterStoredProcedureName": "spOverwriteMarketing",
    "storedProcedureParameters": {
        "category": {
            "value": "ProductA"
        }
    }
}
```

在数据库中，使用与 **SqlWriterStoredProcedureName** 相同的名称定义存储过程。 它可处理来自指定源的输入数据，并将其合并到输出表中。 存储过程中的表类型的参数名称等同于**tableName**中数据集定义。

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

在数据库中，定义具有相同的名称的表类型**sqlWriterTableType**。 表类型的架构与输入数据返回的架构相同。

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL，
    [Category] [varchar](256) NOT NULL
)
```

存储过程功能利用[表值参数](https://msdn.microsoft.com/library/bb675163.aspx)。

## <a name="data-type-mapping-for-azure-sql-database-managed-instance"></a>Azure SQL 数据库托管实例的数据类型映射

从/向 Azure SQL 数据库托管实例复制数据时，以下映射用于从 Azure SQL 数据库托管实例数据类型映射到 Azure 数据工厂临时数据类型。 若要了解复制活动如何从源架构和数据类型映射到接收器，请参阅[架构和数据类型映射](copy-activity-schema-and-type-mapping.md)。

| Azure SQL 数据库托管实例数据类型 | Azure 数据工厂临时数据类型 |
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

## <a name="next-steps"></a>后续步骤
有关 Azure 数据工厂中复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md##supported-data-stores-and-formats)。
