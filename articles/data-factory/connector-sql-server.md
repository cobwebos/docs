---
title: 使用 Azure 数据工厂向 / 从 SQL Server 复制数据 |Microsoft Docs
description: 了解有关如何使用 Azure 数据工厂将数据移到和从本地 SQL Server 数据库或 Azure VM 中。
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
ms.openlocfilehash: a6767c7c8931898c44fd748dbe4299b8ed23eb9c
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443288"
---
# <a name="copy-data-to-and-from-sql-server-by-using-azure-data-factory"></a>使用 Azure 数据工厂向 / 从 SQL Server 复制数据
> [!div class="op_single_selector" title1="选择要使用的 Azure 数据工厂的版本："]
> * [版本 1](v1/data-factory-sqlserver-connector.md)
> * [当前版本](connector-sql-server.md)

本文概述了如何使用 Azure 数据工厂中复制活动从 / 向 SQL Server 数据库复制数据。 它基于[复制活动概述](copy-activity-overview.md)一文，其中总体概述复制活动。

## <a name="supported-capabilities"></a>支持的功能

从 / 向 SQL Server 数据库到任何支持的接收器数据存储，你可以复制数据。 或者，可以将数据从任一支持的源数据存储复制到 SQL Server 数据库。 有关复制活动支持作为源或接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

具体而言，此 SQL Server 连接器支持：

- SQL Server 版本 2016年、 2014年、 2012年、 2008 R2、 2008年和 2005年。
- 使用 SQL 或 Windows 身份验证复制数据。
- 作为源，通过使用 SQL 查询或存储的过程检索数据。
- 作为接收器，在复制期间将数据追加到目标表，或调用带有自定义逻辑的存储过程。

>[!NOTE]
>SQL Server [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017)现在不支持此连接器。 若要解决，可以使用[泛型 ODBC 连接器](connector-odbc.md)和 SQL Server ODBC 驱动程序。 请按照[本指南](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=sql-server-2017)使用 ODBC 驱动程序下载和连接字符串配置。

## <a name="prerequisites"></a>必备组件

若要使用从不可公开访问的 SQL Server 数据库复制数据，需要设置自承载的集成运行时。 有关详细信息，请参阅[自承载集成运行时](create-self-hosted-integration-runtime.md)。 集成运行时提供内置的 SQL Server 数据库驱动程序。 您不必手动安装任何驱动程序时从 / 向 SQL Server 数据库复制数据。

## <a name="get-started"></a>开始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

以下部分提供有关用于定义数据工厂实体的特定于 SQL Server 数据库连接器的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

SQL Server 链接服务支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | type 属性必须设置为 **SqlServer**。 | 是 |
| connectionString |指定**connectionString**使用 SQL 身份验证或 Windows 身份验证连接到 SQL Server 数据库所需的信息。 请参阅以下示例。<br/>将为此字段标记**SecureString**安全地将其存储在 Azure 数据工厂。 此外可以将密码放在 Azure 密钥保管库中。 如果它是 SQL 身份验证，请将拉`password`带连接字符串配置。 有关详细信息，请参阅下表的 JSON 示例和[在 Azure 密钥保管库中存储凭据](store-credentials-in-key-vault.md)。 |是 |
| userName |如果使用 Windows 身份验证，请指定用户名。 例如，**domainname\\username**。 |否 |
| password |指定用户名称为指定的用户帐户的密码。 将为此字段标记**SecureString**安全地将其存储在 Azure 数据工厂。 或者，你可以[引用 Azure Key Vault 中存储的机密](store-credentials-in-key-vault.md)。 |否 |
| connectVia | 此[集成运行时](concepts-integration-runtime.md)用于连接到数据存储。 如果数据存储区是可公开访问，可以使用自承载的集成运行时或 Azure 集成运行时。 如果未指定，则使用默认 Azure 集成运行时。 |否 |

>[!TIP]
>如果达到，错误代码"UserErrorFailedToConnectToSqlServer"错误和消息，如"数据库会话数限制为 XXX 和已达到"，添加`Pooling=false`到连接字符串和重试。

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

**示例 2：使用 SQL 身份验证和 Azure 密钥保管库中的密码**

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

有关可用于定义数据集的各部分和属性的完整列表，请参阅数据集一文。 本部分提供 SQL Server 数据集支持的属性的列表。

若要从 / 向 SQL Server 数据库复制数据，支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为 SqlServerTable  。 | 是 |
| tableName |此属性是表或视图中的链接的服务引用的 SQL Server 数据库实例的名称。 | 对于源为“No”，对于接收器为“Yes” |

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
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的节和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 SQL Server 源和接收器支持的属性的列表。

### <a name="sql-server-as-a-source"></a>SQL Server 作为源

要从 SQL Server 复制数据，请将复制活动中的源类型设置为 SqlSource  。 复制活动的 source 节支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动 source 节的 type 属性必须设置为 SqlSource  。 | 是 |
| sqlReaderQuery |使用自定义 SQL 查询读取数据。 例如 `select * from MyTable`。 |否 |
| sqlReaderStoredProcedureName |此属性是从源表读取数据的存储过程的名称。 存储过程中的最后一条 SQL 语句必须是 SELECT 语句。 |否 |
| storedProcedureParameters |这些参数用于存储过程。<br/>允许的值为名称或值对。 名称和参数的大小写必须与匹配的名称和存储的过程参数的大小写。 |否 |

**需要注意的要点：**

- 如果**sqlReaderQuery**为指定**SqlSource**，复制活动针对 SQL Server 源获取的数据运行此查询。 也可通过指定 sqlReaderStoredProcedureName 和 storedProcedureParameters 来指定存储过程，前提是存储过程使用参数   。
- 如果未指定任一**sqlReaderQuery**或**sqlReaderStoredProcedureName**，数据集 JSON 的"structure"节中定义的列用于构造查询。 查询`select column1, column2 from mytable`SQL 服务器上运行。 如果数据集定义没有“structure”，则会从表中选择所有列。

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
> 了解有关受支持的写入行为、 配置和最佳实践的详细信息[最佳做法，用于将数据加载到 SQL Server](#best-practice-for-loading-data-into-sql-server)。

要向 SQL Server 复制数据，请将复制活动中的接收器类型设置为 SqlSink  。 复制活动的 sink 节支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动的 sink 的 type 属性必须设置为 SqlSink  。 | 是 |
| writeBatchSize |要插入 SQL 表的行数*每个批处理*。<br/>允许的值为表示行数的整数。 默认情况下，Azure 数据工厂动态确定基于行大小的合适的批大小。 |否 |
| writeBatchTimeout |此属性指定超时前等待批插入操作完成的时间。<br/>允许的值适用时间跨度。 一个示例是"00: 30:00"30 分钟。 |否 |
| preCopyScript |此属性指定复制活动将数据写入到 SQL Server 之前运行的 SQL 查询。 每次运行复制仅调用该查询一次。 可以使用此属性清除预加载的数据。 |否 |
| sqlWriterStoredProcedureName |此名称所适用的存储过程定义如何将源数据应用于目标表。<br/>此存储过程由每个批处理调用  。 若要执行仅运行一次且与源数据无关的操作（例如删除或截断），请使用 `preCopyScript` 属性。 |否 |
| storedProcedureParameters |这些参数用于存储过程。<br/>允许的值为名称或值对。 参数的名称和大小写必须与存储过程参数的名称和大小写匹配。 |否 |
| sqlWriterTableType |此属性指定要在存储过程中使用的表类型名称。 通过复制活动，使移动数据在具备此表类型的临时表中可用。 然后，存储过程代码可合并复制数据和现有数据。 |否 |

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

## <a name="best-practice-for-loading-data-into-sql-server"></a>将数据加载到 SQL Server 的最佳做法

在将数据复制到 SQL Server，可能需要不同的写入行为：

- [追加](#append-data):我的源数据具有新的记录。
- [更新插入](#upsert-data):我的源数据已插入和更新。
- [覆盖](#overwrite-the-entire-table):我想要每次重新加载整个维度表。
- [使用自定义逻辑编写](#write-data-with-custom-logic):我需要额外的处理之前最后一次插入到目标表。

请参阅如何在 Azure 数据工厂和最佳实践中配置的相应部分。

### <a name="append-data"></a>追加数据

将数据追加是此 SQL Server 接收器连接器的默认行为。 Azure 数据工厂执行大容量插入以高效率地写入到表。 可以配置源和接收器相应地将复制活动中。

### <a name="upsert-data"></a>更新插入数据

**选项 1：** 当您有大量的数据复制，请使用以下方法来执行 upsert: 

- 首先，使用[临时表](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables)使用复制活动的所有记录的大容量加载。 对临时表执行的操作而不记录，因为您可以在数秒内加载数百万条记录。
- 在 Azure 数据工厂应用中运行存储的过程活动[合并](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current)或插入/更新语句。 使用临时表的源上执行所有更新或插入作为单个事务。 在这种方式，减少往返和日志操作的数量。 在存储的过程活动结束时，可以准备就绪可供下一步的 upsert 周期而被截断临时表。

例如，在 Azure 数据工厂中，您可以创建管道，其中包含**的复制活动**与关联**存储过程活动**。 前者将数据从复制的源存储到数据库的临时表，例如， **##UpsertTempTable**，作为在数据集中的表名称。 然后后者调用存储的过程来合并到目标表的临时表中的源数据和清理临时表。

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

当将数据复制到 SQL Server 数据库时，还可以配置和调用使用其他参数的用户指定的存储的过程。

> [!TIP]
> 调用存储的过程处理的数据行的行而不是通过使用大容量操作，我们不建议用于大规模复制。 了解详细信息[最佳做法，用于将数据加载到 SQL Server](#best-practice-for-loading-data-into-sql-server)。

当内置复制机制无法使用时，还可使用存储过程。 例如，当你想要应用之前最后一次插入的源数据插入目标表的额外处理。 一些额外的处理示例是当你想要合并列、 查找其他值，并将数据插入到多个表。

以下示例演示如何使用存储过程，在 SQL Server 数据库中的表内执行 upsert。 假设输入的数据和接收器**市场营销**每个表具有三个列：**ProfileID**、**State** 和 **Category**。 基于 ProfileID 列执行 upsert，并仅将其应用于特定类别  。

**输出数据集：** "TableName"是你的存储过程中的同一个表类型参数名称，以下存储的过程脚本中所示：

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

## <a name="data-type-mapping-for-sql-server"></a>SQL Server 的数据类型映射

当从 / 向 SQL Server 复制数据时，以下映射用于从 SQL Server 数据类型到 Azure 数据工厂临时数据类型。 若要了解复制活动如何将源架构和数据类型映射到接收器，请参阅[架构和数据类型映射](copy-activity-schema-and-type-mapping.md)。

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

## <a name="troubleshoot-connection-issues"></a>排查连接问题

1. 配置 SQL Server 实例，以接受远程连接。 启动**SQL Server Management Studio**，右键单击**服务器**，然后选择**属性**。 选择**连接**从该列表中，然后选择**允许远程连接到此服务器**复选框。

    ![启用远程连接](media/copy-data-to-from-sql-server/AllowRemoteConnections.png)

    有关详细步骤，请参阅[配置远程访问服务器配置选项](https://msdn.microsoft.com/library/ms191464.aspx)。

2. 启动**SQL Server 配置管理器**。 针对所需实例展开“SQL Server 网络配置”，并选择“MSSQLSERVER 的协议”   。 在右窗格中显示的协议。 通过右键单击启用 TCP/IP **TCP/IP** ，然后选择**启用**。

    ![启用 TCP/IP](./media/copy-data-to-from-sql-server/EnableTCPProptocol.png)

    有关详细信息和启用 TCP/IP 协议的备用方式，请参阅[启用或禁用服务器网络协议](https://msdn.microsoft.com/library/ms191294.aspx)。

3. 在同一窗口中，双击**TCP/IP**以启动**TCP/IP 属性**窗口。
4. 切换到“IP 地址”选项卡  。向下滚动到，请参阅**IPAll**部分。 记下**TCP 端口**。 默认值是**1433年**。
5. 在计算机上创建 Windows 防火墙规则，以便允许通过此端口传入流量  。 
6. **验证连接**：若要使用完全限定的名称连接到 SQL Server，请从另一台计算机使用 SQL Server Management Studio。 例如 `"<machine>.<domain>.corp.<company>.com,1433"`。

## <a name="next-steps"></a>后续步骤
有关 Azure 数据工厂中复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md##supported-data-stores-and-formats)。
