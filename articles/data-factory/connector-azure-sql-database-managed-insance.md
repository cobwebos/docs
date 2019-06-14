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
ms.openlocfilehash: e68b522d5a0fe7c359d83fc436aa7a1fd2159198
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67048587"
---
# <a name="copy-data-to-and-from-azure-sql-database-managed-instance-by-using-azure-data-factory"></a>使用 Azure 数据工厂向/从 Azure SQL 数据库托管实例复制数据

本文概述了如何使用 Azure 数据工厂中的复制活动从/向 Azure SQL 数据库托管实例复制数据。 本文是根据总体概述复制活动的[复制活动概述](copy-activity-overview.md)一文编写的。

## <a name="supported-capabilities"></a>支持的功能

可将数据从 Azure SQL 数据库托管实例复制到任何支持的接收器数据存储。 还可以将数据从任何支持的源数据存储复制到托管实例。 有关复制活动支持作为源和接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

具体而言，此 Azure SQL 数据库托管实例连接器支持：

- 使用 SQL 身份验证复制数据。
- 作为源，使用 SQL 查询或存储过程检索数据。
- 作为接收器，在复制期间将数据追加到目标表，或调用带有自定义逻辑的存储过程。

目前不支持 SQL Server [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017)。 

## <a name="prerequisites"></a>必备组件

若要访问 Azure SQL 数据库托管实例 **[公共终结点](../sql-database/sql-database-managed-instance-public-endpoint-securely.md)** ，可以使用 ADF 托管 Azure ir。 请确保不只启用公共终结点，但还允许公共终结点流量网络安全组，以便能够连接到数据库，按照 ADF[本指南](../sql-database/sql-database-managed-instance-public-endpoint-configure.md)。

若要访问 Azure SQL 数据库托管实例**专用终结点**、 设置[自承载集成运行时](create-self-hosted-integration-runtime.md)，可以访问数据库。 如果预配你的托管实例所在的虚拟网络中的自承载的集成运行时，请确保在集成运行时计算机比你的托管实例的不同子网。 如果预配的自承载集成运行时与托管实例位于不同的虚拟网络中，则可使用虚拟网络对等互连或虚拟网络间的连接。 有关详细信息，请参阅[将应用程序连接到 Azure SQL 数据库托管实例](../sql-database/sql-database-managed-instance-connect-app.md)。

## <a name="get-started"></a>开始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

对于特定于 Azure SQL 数据库托管实例连接器的数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

Azure SQL 数据库托管实例链接的服务支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | type 属性必须设置为 **SqlServer**。 | 是的。 |
| connectionString |此属性指定使用 SQL 身份验证连接到托管实例所需的 connectionString 信息。 有关详细信息，请参阅以下示例。 <br/>将此字段标记为 SecureString，以便安全地将其存储在数据工厂中。 还可以在 Azure 密钥保管库中输入密码，如果是 SQL 身份验证，则从连接字符串中拉取 `password` 配置。 有关更多详细信息，请参阅下表的 JSON 示例和[在密钥保管库中存储凭据](store-credentials-in-key-vault.md)一文。 |是的。 |
| connectVia | 此[集成运行时](concepts-integration-runtime.md)用于连接到数据存储。 （如果你的托管的实例具有公共终结点，并允许访问的 ADF），可以使用自承载集成运行时或 Azure 集成运行时。 如果未指定，则使用默认 Azure Integration Runtime。 |是的。 |

**示例 1：使用 SQL 身份验证**

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername:port>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**示例 2：将 SQL 身份验证与 Azure 密钥保管库中的密码结合使用**

```json
{
    "name": "AzureSqlMILinkedService",
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

## <a name="dataset-properties"></a>数据集属性

如需可用于定义数据集的节和属性的完整列表，请参阅有关数据集的文章。 本节提供 Azure SQL 数据库托管实例数据集支持的属性列表。

若要向 / 从 Azure SQL 数据库托管实例，请复制数据，支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为 SqlServerTable  。 | 是的。 |
| tableName |此属性是链接服务引用的数据库实例中的表名称或视图名称。 | 对于源为“No”， 对于接收器为“Yes”。 |

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
| type | 复制活动 source 节的 type 属性必须设置为 SqlSource  。 | 是的。 |
| sqlReaderQuery |此属性使用自定义 SQL 查询来读取数据。 例如 `select * from MyTable`。 |不。 |
| sqlReaderStoredProcedureName |此属性是从源表读取数据的存储过程的名称。 最后一个 SQL 语句必须是存储过程中的 SELECT 语句。 |不。 |
| storedProcedureParameters |这些参数用于存储过程。<br/>允许的值为名称或值对。 参数的名称和大小写必须与存储过程参数的名称和大小写匹配。 |不。 |

请注意以下几点：

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
> 了解有关受支持的写入行为、 配置和最佳做法是从[最佳做法，用于将数据加载到 Azure SQL 数据库托管实例](#best-practice-for-loading-data-into-azure-sql-database-managed-instance)。

若要向 Azure SQL 数据库托管实例复制数据，请将复制活动中的接收器类型设置为“SqlSink”  。 复制活动的 sink 节支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动的 sink 的 type 属性必须设置为 SqlSink  。 | 是的。 |
| writeBatchSize |插入 SQL 表的行数**每个批处理**。<br/>允许的值为表示行数的整数。 默认情况下，数据工厂动态确定基于行大小的合适的批大小。  |否 |
| writeBatchTimeout |此属性指定超时前等待批插入操作完成的时间。<br/>允许的值表示时间跨度。 例如，“00:30:00”表示 30 分钟。 |不。 |
| preCopyScript |此属性指定将数据写入到托管实例中之前复制活动要执行的 SQL 查询。 每次运行复制仅调用该查询一次。 可以使用此属性清除预加载的数据。 |不。 |
| sqlWriterStoredProcedureName |此名称所适用的存储过程定义如何将源数据应用于目标表。 <br/>此存储过程由每个批处理调用  。 若要执行仅运行一次且与源数据无关的操作（例如删除或截断），请使用 `preCopyScript` 属性。 |不。 |
| storedProcedureParameters |这些参数用于存储过程。<br/>允许的值为名称或值对。 参数的名称和大小写必须与存储过程参数的名称和大小写匹配。 |不。 |
| sqlWriterTableType |此属性指定要在存储过程中使用的表类型名称。 通过复制活动，使移动数据在具备此表类型的临时表中可用。 然后，存储过程代码可合并复制数据和现有数据。 |不。 |

**示例 1： 追加数据**

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

**示例 2： 在复制过程中调用存储的过程**

请参阅[调用 SQL 接收器的存储过程](#invoking-stored-procedure-for-sql-sink)，了解更多详细信息。

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

当将数据复制到 Azure SQL 数据库托管实例时，可能需要不同的写入行为：

- **[追加](#append-data)** ： 我的源数据只有新记录;
- **[Upsert](#upsert-data)** ： 我的源数据已插入和更新;
- **[覆盖](#overwrite-entire-table)** :我想要重新加载整个维度表每次;
- **[使用自定义逻辑编写](#write-data-with-custom-logic)** :我需要额外的处理之前最后一次插入到目标表。

请参阅如何在 ADF 和最佳实践配置分别部分。

### <a name="append-data"></a>追加数据

这是此 Azure SQL 数据库托管实例接收器连接器的默认行为，执行 ADF**大容量插入**要高效率地写入到表。 只需配置的源，并相应地接收器复制活动中。

### <a name="upsert-data"></a>更新插入数据

**选项我**（建议尤其是当具有要复制的大型数据）：**大多数的高性能方法**执行 upsert 是以下： 

- 首先，利用[临时表](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables)要大容量加载使用复制活动的所有记录。 不记录对临时表执行的操作时，您可以在数秒内加载数百万条记录。
- 在 ADF 应用中执行存储过程活动[合并](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current)（或插入/更新） 语句，并使用 temp 表减少往返时间量，如源上执行所有更新或插入作为单个事务和记录操作。 在存储过程活动结束时，可以准备就绪可供下一步的 upsert 周期而被截断临时表。 

例如，在 Azure 数据工厂中，您可以创建管道，其中包含**的复制活动**与关联**存储过程活动**成功。 前者将数据从复制的源存储到临时表，说" **##UpsertTempTable**"作为表中数据集的名称，然后后者调用存储过程来合并到目标表的临时表中的源数据和清理临时表。

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

在数据库中，定义带有合并逻辑，如下所示，从更高版本的存储过程活动所指向的存储过程。 假设目标**市场营销**的三个列的表：**ProfileID**，**状态**，和**类别**，并执行基于 upsert **ProfileID**列。

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

**选项 II:** 或者，您可以选择[调用存储的过程中复制活动](#invoking-stored-procedure-for-sql-sink)，同时请注意这种方法执行而不是利用大容量的源表中的每一行插入作为默认方法因此在复制活动中，它不适合于较大的比例 upsert。

### <a name="overwrite-entire-table"></a>覆盖整个表

你可以配置**preCopyScript**接收器的复制活动中的属性，将在这种情况下对于每个复制活动运行，ADF 首先执行，该脚本，然后运行插入数据的副本。 例如，若要使用最新数据覆盖整个表，可指定一个脚本，先删除所有记录，再从源大容量加载新数据。

### <a name="write-data-with-custom-logic"></a>写入自定义逻辑数据。

如中所述类似[Upsert 数据](#upsert-data)部分中，需要进行额外处理之前最后一次插入的源数据插入目标表中，应用时可以) 的较大的比例，加载到临时表，然后调用文件存储过程中或 b） 在复制期间调用的存储的过程。

## <a name="invoking-stored-procedure-for-sql-sink"></a> 调用 SQL 接收器的存储过程

当将数据复制到 Azure SQL 数据库托管实例时，还可以配置并调用使用其他参数的用户指定的存储的过程。

> [!TIP]
> 调用存储的过程处理数据的行而不是大容量操作，不建议在较大的比例副本。 了解详细信息[最佳做法，用于将数据加载到 Azure SQL 数据库托管实例](#best-practice-for-loading-data-into-azure-sql-database-managed-instance)。

您可以使用存储的过程时内置复制机制并不适合用用途，例如应用之前最后一次插入的源数据插入目标表的额外处理。 额外处理包括合并列、查找其他值以及插入多个表等。

以下示例演示如何使用存储过程，在 SQL Server 数据库中的表内执行 upsert。 假设输入数据和接收器“Marketing”  表各具有三列：**ProfileID**、**State** 和 **Category**。 基于 ProfileID 列执行 upsert，并仅将其应用于特定类别  。

**输出数据集：** "tableName"应为你的存储过程 （请参阅下面的存储的过程脚本） 中的同一个表类型参数名称。

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

定义**SQL 接收器**部分复制活动中，如下所示。

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

在数据库中，使用与 SqlWriterStoredProcedureName 相同的名称定义存储过程  。 它可处理来自指定源的输入数据，并将其合并到输出表中。 存储过程中的表类型的参数名称应与数据集中定义的 **tableName** 相同。

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

在数据库中，使用与 SqlWriterTableType 相同的名称定义表类型。 表类型的架构与输入数据返回的架构相同。

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
