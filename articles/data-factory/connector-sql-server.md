---
title: 使用 Azure 数据工厂将数据复制到 SQL Server 和从中复制数据 |Microsoft Docs
description: 了解如何使用 Azure 数据工厂将数据移入或移出本地或 Azure 虚拟机中的 SQL Server 数据库。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: jingwang
ms.openlocfilehash: 5dcbb2c25511277eaf46d6c9f4afc007a180f8a6
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68827872"
---
# <a name="copy-data-to-and-from-sql-server-by-using-azure-data-factory"></a>使用 Azure 数据工厂将数据复制到 SQL Server
> [!div class="op_single_selector" title1="选择要使用的 Azure 数据工厂的版本:"]
> * [版本 1](v1/data-factory-sqlserver-connector.md)
> * [当前版本](connector-sql-server.md)

本文概述如何使用 Azure 数据工厂中的复制活动将数据从和复制到 SQL Server 数据库。 它基于提供复制活动的一般概述的[复制活动概述](copy-activity-overview.md)一文。

## <a name="supported-capabilities"></a>支持的功能

您可以将数据从 SQL Server 数据库复制到任何支持的接收器数据存储。 也可以将数据从任何支持的源数据存储复制到 SQL Server 数据库。 有关复制活动支持作为源或接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

具体而言，此 SQL Server 连接器支持：

- SQL Server 版本2016、2014、2012、2008 R2、2008和2005。
- 使用 SQL 或 Windows 身份验证复制数据。
- 作为源, 使用 SQL 查询或存储过程检索数据。
- 作为接收器，在复制期间将数据追加到目标表，或调用带有自定义逻辑的存储过程。

不支持[SQL Server Express LocalDB](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-express-localdb?view=sql-server-2017) 。

>[!NOTE]
>此连接器目前不支持 SQL Server [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017) 。 若要解决此情况, 可以使用[泛型 odbc 连接器](connector-odbc.md)和 SQL Server ODBC 驱动程序。 按照[此指南](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=sql-server-2017)完成 ODBC 驱动程序下载和连接字符串配置。

## <a name="prerequisites"></a>先决条件

若要使用从不可公开访问的 SQL Server 数据库复制数据, 需要设置自承载集成运行时。 有关详细信息，请参阅[自承载集成运行时](create-self-hosted-integration-runtime.md)。 集成运行时提供内置 SQL Server 数据库驱动程序。 将数据从或复制到 SQL Server 数据库时, 无需手动安装任何驱动程序。

## <a name="get-started"></a>开始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

对于特定于 SQL Server 数据库连接器的数据工厂实体, 以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

SQL Server 链接服务支持以下属性:

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | type 属性必须设置为 **SqlServer**。 | 是 |
| connectionString |使用 SQL 身份验证或 Windows 身份验证来指定连接到 SQL Server 数据库所需的**connectionString**信息。 请参阅以下示例。<br/>将此字段标记为**SecureString**以将其安全地存储在 Azure 数据工厂中。 还可以在 Azure Key Vault 中输入密码。 如果它是 SQL 身份验证, 请`password`将配置从连接字符串中提取出来。 有关详细信息, 请参阅表后面的 JSON 示例, 并[在 Azure Key Vault 中存储凭据](store-credentials-in-key-vault.md)。 |是 |
| userName |如果使用 Windows 身份验证, 请指定用户名。 例如，**domainname\\username**。 |否 |
| password |指定为 "用户名" 指定的用户帐户的密码。 将此字段标记为**SecureString**以将其安全地存储在 Azure 数据工厂中。 或者，可以[引用 Azure Key Vault 中存储的机密](store-credentials-in-key-vault.md)。 |否 |
| connectVia | 此[集成运行时](concepts-integration-runtime.md)用于连接到数据存储。 如果数据存储可公开访问, 则可以使用自承载集成运行时或 Azure 集成运行时。 如果未指定，则使用默认 Azure Integration Runtime。 |否 |

>[!TIP]
>如果遇到错误代码为 "UserErrorFailedToConnectToSqlServer" 的错误和消息, 如 "数据库的会话限制为 XXX 并已达到", 请将添加`Pooling=false`到连接字符串, 然后重试。

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

**示例 2：在 Azure Key Vault 中使用带密码的 SQL 身份验证**

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

有关可用于定义数据集的各部分和属性的完整列表，请参阅数据集一文。 本部分提供 SQL Server 数据集支持的属性列表。

若要将数据从和复制到 SQL Server 数据库, 支持以下属性:

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为 SqlServerTable。 | 是 |
| tableName |此属性是链接的服务引用的 SQL Server 数据库实例中的表或视图的名称。 | 对于源为“No”，对于接收器为“Yes” |

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

有关可用于定义活动的节和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 SQL Server 源和接收器支持的属性列表。

### <a name="sql-server-as-a-source"></a>SQL Server 为源

要从 SQL Server 复制数据，请将复制活动中的源类型设置为 SqlSource。 复制活动的 source 节支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动 source 节的 type 属性必须设置为 SqlSource。 | 是 |
| sqlReaderQuery |使用自定义 SQL 查询读取数据。 例如 `select * from MyTable`。 |否 |
| sqlReaderStoredProcedureName |此属性是从源表读取数据的存储过程的名称。 最后一个 SQL 语句必须是存储过程中的 SELECT 语句。 |否 |
| storedProcedureParameters |这些参数用于存储过程。<br/>允许的值为名称或值对。 参数的名称和大小写必须与存储过程参数的名称和大小写相匹配。 |否 |

**需要注意的要点：**

- 如果为**SqlSource**指定**sqlReaderQuery** , 则复制活动对 SQL Server 源运行此查询以获取数据。 也可通过指定 sqlReaderStoredProcedureName 和 storedProcedureParameters 来指定存储过程，前提是存储过程使用参数。
- 如果未指定**sqlReaderQuery**或**sqlReaderStoredProcedureName**, 则使用在数据集 JSON 的 "结构" 部分定义的列来构造查询。 查询`select column1, column2 from mytable`针对 SQL Server 运行。 如果数据集定义没有“structure”，则会从表中选择所有列。

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

### <a name="sql-server-as-a-sink"></a>作为接收器 SQL Server

> [!TIP]
> 从将[数据加载到 SQL Server 中的最佳做法](#best-practice-for-loading-data-into-sql-server), 了解有关支持的写入行为、配置和最佳实践的详细信息。

要向 SQL Server 复制数据，请将复制活动中的接收器类型设置为 SqlSink。 复制活动的 sink 节支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动的 sink 的 type 属性必须设置为 SqlSink。 | 是 |
| writeBatchSize |*每批*插入到 SQL 表中的行数。<br/>允许的值为表示行数的整数。 默认情况下, Azure 数据工厂会根据行大小动态确定相应的批大小。 |否 |
| writeBatchTimeout |此属性指定超时前等待批插入操作完成的时间。<br/>允许的值为 timespan。 例如, "00:30:00" 表示30分钟。 |否 |
| preCopyScript |此属性指定在将数据写入 SQL Server 之前要运行的复制活动的 SQL 查询。 每次运行复制仅调用该查询一次。 可以使用此属性清除预加载的数据。 |否 |
| sqlWriterStoredProcedureName | 定义如何将源数据应用于目标表的存储过程的名称。 <br/>此存储过程由每个批处理调用。 对于仅运行一次并且与源数据不执行任何操作 (例如, 删除或截断) 的操作, 请`preCopyScript`使用属性。 | 否 |
| storedProcedureTableTypeParameterName |存储过程中指定的表类型的参数名称。  |否 |
| sqlWriterTableType |要在存储过程中使用的表类型名称。 通过复制活动，使移动数据在具备此表类型的临时表中可用。 然后，存储过程代码可合并复制数据和现有数据。 |否 |
| storedProcedureParameters |存储过程的参数。<br/>允许的值为名称和值对。 参数的名称和大小写必须与存储过程参数的名称和大小写匹配。 | 否 |

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

将数据复制到 SQL Server 时, 可能需要不同的写入行为:

- [追加](#append-data):我的源数据只有新记录。
- [Upsert](#upsert-data):我的源数据同时包含插入和更新。
- [覆盖](#overwrite-the-entire-table):我想每次都重新加载整个维度表。
- [用自定义逻辑编写](#write-data-with-custom-logic):在将数据最终插入目标表之前，我需要额外的处理。

有关如何在 Azure 数据工厂中进行配置和最佳实践, 请参阅相应的部分。

### <a name="append-data"></a>追加数据

追加数据是此 SQL Server 接收器连接器的默认行为。 Azure 数据工厂执行大容量插入来有效地写入表。 可以在复制活动中相应地配置源和接收器。

### <a name="upsert-data"></a>更新插入数据

**选项 1：** 如果要复制大量数据, 请使用以下方法执行 upsert: 

- 首先, 使用[临时表](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables), 通过使用复制活动大容量加载所有记录。 由于不会记录对临时表的操作, 因此你可以在数秒内加载数百万条记录。
- 在 Azure 数据工厂中运行存储过程活动, 以应用[MERGE](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current)或 INSERT/UPDATE 语句。 使用临时表作为源来执行所有更新或插入作为单个事务。 通过这种方式, 可减少往返次数和日志操作。 在存储过程活动结束时, 可以截断临时表, 以便在下一个 upsert 周期中做好准备。

例如, 在 Azure 数据工厂中, 可以创建一个管道, 其中包含与**存储过程活动**链接的**复制活动**。 前者将源存储中的数据复制到数据库临时表中, 例如, 使用 **# #UpsertTempTable**作为数据集中的表名。 然后, 后者将调用一个存储过程, 用于将临时表中的源数据合并到目标表中, 并清理临时表。

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

在数据库中, 定义包含合并逻辑的存储过程, 如下例所示, 它指向以前的存储过程活动。 假定目标为包含三列的**营销**表:**ProfileID**、**State** 和 **Category**。 根据**配置文件 id**列来执行 upsert。

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

**选项 2：** 您还可以选择在[复制活动中调用存储过程](#invoke-a-stored-procedure-from-a-sql-sink)。 此方法运行源表中的每行, 而不是使用 bulk insert 作为复制活动中的默认方法, 这不适用于大规模 upsert。

### <a name="overwrite-the-entire-table"></a>覆盖整个表

可以在复制活动接收器中配置**preCopyScript**属性。 在这种情况下, 对于运行的每个复制活动, Azure 数据工厂首先运行脚本。 然后, 它将运行复制以插入数据。 例如, 若要使用最新数据覆盖整个表, 请在从源中大容量加载新数据之前, 指定脚本以首先删除所有记录。

### <a name="write-data-with-custom-logic"></a>使用自定义逻辑写入数据

用自定义逻辑写入数据的步骤类似于[Upsert data](#upsert-data)节中所述的步骤。 如果需要在将源数据最终插入目标表之前应用额外的处理, 以进行大规模调整, 则可以执行以下两个操作之一: 

- 加载到临时表, 然后调用存储过程。 
- 在复制过程中调用存储过程。

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a> 调用 SQL 接收器的存储过程

将数据复制到 SQL Server 数据库中时, 还可以使用其他参数配置和调用用户指定的存储过程。 存储过程功能利用[表值参数](https://msdn.microsoft.com/library/bb675163.aspx)。

> [!TIP]
> 调用存储过程时, 将按行而不是使用大容量操作来处理数据行, 但我们不建议使用大容量复制。 从[将数据加载到 SQL Server 中的最佳做法](#best-practice-for-loading-data-into-sql-server)中了解更多信息。

当内置复制机制无法使用时，还可使用存储过程。 例如, 当你想要在将源数据的最终插入目标表之前应用额外处理时。 一些额外的处理示例是要合并列、查找其他值和将数据插入多个表中。

以下示例演示如何使用存储过程，在 SQL Server 数据库中的表内执行 upsert。 假设输入数据和接收器**营销**表各有三列:**ProfileID**、**State** 和 **Category**。 根据**配置文件 id**列执行 upsert, 并仅将其应用于名为 "ProductA" 的特定类别。

1. 在数据库中, 用与**sqlWriterTableType**相同的名称定义表类型。 表类型的架构与输入数据返回的架构相同。

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL，
        [Category] [varchar](256) NOT NULL
    )
    ```

2. 在数据库中，使用与 **SqlWriterStoredProcedureName** 相同的名称定义存储过程。 它可处理来自指定源的输入数据，并将其合并到输出表中。 存储过程中表类型的参数名称与数据集中定义的**tableName**相同。

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

3. 在 Azure 数据工厂中, 在复制活动中定义**SQL 接收器**部分, 如下所示:

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

在将数据从和复制到 SQL Server 时, 以下映射用于从 SQL Server 数据类型映射到 Azure 数据工厂临时数据类型。 若要了解复制活动如何将源架构和数据类型映射到接收器，请参阅[架构和数据类型映射](copy-activity-schema-and-type-mapping.md)。

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

1. 将 SQL Server 实例配置为接受远程连接。 开始**SQL Server Management Studio**, 右键单击 "**服务器**", 然后选择 "**属性**"。 从列表中选择 "**连接**", 然后选中 "**允许远程连接到此服务器**" 复选框。

    ![启用远程连接](media/copy-data-to-from-sql-server/AllowRemoteConnections.png)

    有关详细步骤, 请参阅[配置远程访问服务器配置选项](https://msdn.microsoft.com/library/ms191464.aspx)。

2. 开始**SQL Server 配置管理器**。 针对所需实例展开“SQL Server 网络配置”，并选择“MSSQLSERVER 的协议”。 协议将显示在右窗格中。 右键单击 " **tcp/ip** ", 然后选择 "**启用**", 启用 tcp/ip。

    ![启用 TCP/IP](./media/copy-data-to-from-sql-server/EnableTCPProptocol.png)

    有关启用 TCP/IP 协议的详细信息和备用方法, 请参阅[启用或禁用服务器网络协议](https://msdn.microsoft.com/library/ms191294.aspx)。

3. 在同一窗口中, 双击 " **tcp/ip** " 以启动 " **tcp/ip 属性**" 窗口。
4. 切换到“IP 地址”选项卡。向下滚动以查看**IPAll**部分。 记下**TCP 端口**。 默认值为**1433**。
5. 在计算机上创建 Windows 防火墙规则，以便允许通过此端口传入流量。 
6. **验证连接**：若要使用完全限定名称连接到 SQL Server, 请使用来自其他计算机的 SQL Server Management Studio。 例如 `"<machine>.<domain>.corp.<company>.com,1433"`。

## <a name="next-steps"></a>后续步骤
有关 Azure 数据工厂中复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md##supported-data-stores-and-formats)。
