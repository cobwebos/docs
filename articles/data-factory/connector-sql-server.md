---
title: 使用 Azure 数据工厂向/从 SQL Server 复制数据 | Microsoft Docs
description: 了解如何使用 Azure 数据工厂将数据移入/移出本地或 Azure VM 中的 SQL Server 数据库。
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
ms.openlocfilehash: 230fe94820a00c276238a7f5ff189ecc817f3f96
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67074048"
---
# <a name="copy-data-to-and-from-sql-server-using-azure-data-factory"></a>使用 Azure 数据工厂向/从 SQL Server 复制数据
> [!div class="op_single_selector" title1="选择在使用数据工厂服务版本："]
> * [版本 1](v1/data-factory-sqlserver-connector.md)
> * [当前版本](connector-sql-server.md)

本文概述了如何使用 Azure 数据工厂中的复制活动从/向 SQL Server 数据库复制数据。 它是基于概述复制活动总体的[复制活动概述](copy-activity-overview.md)一文。

## <a name="supported-capabilities"></a>支持的功能

可以将 SQL Server 数据库中的数据复制到任意受支持的接收器数据存储，或将任意受支持的接收器数据存储中的数据复制到 SQL Server 数据库。 有关复制活动支持作为源/接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

具体而言，此 SQL Server 连接器支持：

- SQL Server 版本 2016、2014、2012、2008 R2、2008 和 2005
- 使用 SQL 或 Windows 身份验证复制数据   。
- 作为源，使用 SQL 查询或存储过程检索数据。
- 作为接收器，在复制期间将数据追加到目标表，或调用带有自定义逻辑的存储过程。

目前不支持 SQL Server [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017)。

## <a name="prerequisites"></a>必备组件

要从不可公开访问的 SQL Server 数据库复制数据，需要设置自承载集成运行时。 有关详细信息，请参阅[自承载集成运行时](create-self-hosted-integration-runtime.md)一文。 集成运行时提供内置 SQL Server 数据库驱动程序，因此从/向 SQL Server 数据库复制数据时，无需手动安装任何驱动程序。

## <a name="getting-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

对于特定于 SQL Server 数据库连接器的数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

SQL Server 链接服务支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | type 属性必须设置为：**SqlServer** | 是 |
| connectionString |指定使用 SQL 身份验证或 Windows 身份验证连接到 SQL Server 数据库时所需的 connectionString 信息。 请参阅以下示例。<br/>将此字段标记为 SecureString，以便安全地将其存储在数据工厂中。 还可以在 Azure 密钥保管库中输入密码，如果是 SQL 身份验证，则从连接字符串中拉取 `password` 配置。 有关更多详细信息，请参阅下表的 JSON 示例和[在 Azure 密钥保管库中存储凭据](store-credentials-in-key-vault.md)一文。 |是 |
| userName |如果使用的是 Windows 身份验证，请指定用户名。 示例：域名\\用户名  。 |否 |
| password |指定为 userName 指定的用户帐户的密码。 将此字段标记为 SecureString 以安全地将其存储在数据工厂中或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 |否 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如果可以公开访问数据存储，则可以使用自承载集成运行时或 Azure Integration Runtime 时。 如果未指定，则使用默认 Azure Integration Runtime。 |否 |

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

**示例 2：在 Azure 密钥保管库中使用带有密码的 SQL 身份验证**

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

若要从 SQL Server 数据库复制数据/将数据复制到 SQL Server 数据库，需要支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为：**SqlServerTable** | 是 |
| tableName |链接服务所引用的 SQL Server 数据库实例中的表或视图的名称。 | 对于源为“No”，对于接收器为“Yes” |

**示例：**

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

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 SQL Server 源和接收器支持的属性列表。

### <a name="sql-server-as-source"></a>SQL Server 作为源

要从 SQL Server 复制数据，请将复制活动中的源类型设置为 SqlSource  。 复制活动源部分支持以下属性  ：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动源的 type 属性必须设置为：**SqlSource** | 是 |
| sqlReaderQuery |使用自定义 SQL 查询读取数据。 示例：`select * from MyTable`。 |否 |
| sqlReaderStoredProcedureName |从源表读取数据的存储过程的名称。 最后一个 SQL 语句必须是存储过程中的 SELECT 语句。 |否 |
| storedProcedureParameters |存储过程的参数。<br/>允许的值为：名称/值对。 参数的名称和大小写必须与存储过程参数的名称和大小写匹配。 |否 |

**需要注意的要点：**

- 如果为 SqlSource 指定 sqlReaderQuery  ，则复制活动针对 SQL Server 源运行此查询以获取数据。 此外，也可以通过指定 sqlReaderStoredProcedureName 和 storedProcedureParameters 来指定存储过程（如果存储过程使用参数）   。
- 如果不指定“sqlReaderQuery”或“sqlReaderStoredProcedureName”，则使用在数据集 JSON 的“结构”部分定义的列，构建针对 SQL Server 运行的查询 (`select column1, column2 from mytable`)。 如果数据集定义不具备该“结构”，则从表中选择所有列。

**示例：使用 SQL 查询**

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

**示例：使用存储过程**

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

### <a name="sql-server-as-sink"></a>SQL Server 作为接收器

> [!TIP]
> 了解有关受支持的写入行为、 配置和最佳做法是从[最佳做法，用于将数据加载到 SQL Server](#best-practice-for-loading-data-into-sql-server)。

要向 SQL Server 复制数据，请将复制活动中的接收器类型设置为 SqlSink  。 复制活动接收器部分中支持以下属性  ：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动接收器的 type 属性必须设置为：**SqlSink** | 是 |
| writeBatchSize |**每批**要插入到 SQL 表中的行数。<br/>允许的值为：整数（行数）。 默认情况下，数据工厂会根据行大小动态确定适当的批大小。 |否 |
| writeBatchTimeout |超时之前等待批插入操作完成时的等待时间。<br/>允许的值为：timespan。 示例：“00:30:00”（30 分钟）。 |否 |
| preCopyScript |将数据写入到 SQL Server 之前，指定复制活动要执行的 SQL 查询。 每次运行复制仅调用该查询一次。 此属性可用于清理预先加载的数据。 |否 |
| sqlWriterStoredProcedureName |定义如何将源数据应用到目标表的存储过程的名称。<br/>请注意，此存储过程将由每个批处理调用  。 如果要执行仅运行一次且与源数据无关的操作（例如删除/截断），请使用 `preCopyScript` 属性。 |否 |
| storedProcedureParameters |存储过程的参数。<br/>允许的值为：名称/值对。 参数的名称和大小写必须与存储过程参数的名称和大小写匹配。 |否 |
| sqlWriterTableType |指定要在存储过程中使用的表类型名称。 通过复制活动，使移动数据在具备此表类型的临时表中可用。 然后，存储过程代码可合并复制数据和现有数据。 |否 |

**示例 1： 追加数据**

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

**示例 2： 在复制过程中调用存储的过程**

参阅[调用 SQL 接收器的存储过程](#invoking-stored-procedure-for-sql-sink)，了解更多详细信息。

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

当将数据复制到 SQL Server 时，可能需要不同的写入行为：

- **[追加](#append-data)** ： 我的源数据只有新记录;
- **[Upsert](#upsert-data)** ： 我的源数据已插入和更新;
- **[覆盖](#overwrite-entire-table)** :我想要重新加载整个维度表每次;
- **[使用自定义逻辑编写](#write-data-with-custom-logic)** :我需要额外的处理之前最后一次插入到目标表。

请参阅如何在 ADF 和最佳实践配置分别部分。

### <a name="append-data"></a>追加数据

这是此 SQL Server 接收器连接器的默认行为，执行 ADF**大容量插入**要高效率地写入到表。 只需配置的源，并相应地接收器复制活动中。

### <a name="upsert-data"></a>更新插入数据

**选项我**（建议尤其是当具有要复制的大型数据）：**大多数的高性能方法**执行 upsert 是以下： 

- 首先，利用[临时表](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables)要大容量加载使用复制活动的所有记录。 不记录对临时表执行的操作时，您可以在数秒内加载数百万条记录。
- 在 ADF 应用中执行存储过程活动[合并](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current)（或插入/更新） 语句，并使用 temp 表减少往返时间量，如源上执行所有更新或插入作为单个事务和记录操作。 在存储过程活动结束时，可以准备就绪可供下一步的 upsert 周期而被截断临时表。 

例如，在 Azure 数据工厂中，您可以创建管道，其中包含**的复制活动**与关联**存储过程活动**成功。 前者将数据从复制的源存储在数据库临时表，说" **##UpsertTempTable**"作为表中数据集的名称，然后后者调用存储过程将源数据从临时表合并到目标表中，并清除临时表。

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

将数据复制到 SQL Server 数据库，还可以配置和调用使用其他参数的用户指定的存储的过程。

> [!TIP]
> 调用存储的过程处理数据的行而不是大容量操作，不建议在较大的比例副本。 了解详细信息[最佳做法，用于将数据加载到 SQL Server](#best-practice-for-loading-data-into-sql-server)。

您可以使用存储的过程时内置复制机制并不适合用用途，例如应用之前最后一次插入的源数据插入目标表的额外处理。 额外处理包括合并列、查找其他值以及插入多个表等。

以下示例演示如何使用存储过程，在 SQL Server 数据库中的表内执行 upsert。 假设输入数据和接收器“Marketing”  表各具有三列：**ProfileID**、**State** 和 **Category**。 基于 ProfileID 列执行 upsert，并仅将其应用于特定类别  。

**输出数据集：** “tableName”应该是存储过程中相同的表类型参数名（请参见下面的存储过程脚本）。

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

在复制活动中定义“SQL 接收器”  部分，如下所示。

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

在数据库中，使用与 SqlWriterTableType 相同的名称定义表类型。 请注意，表类型的架构应与输入数据返回的架构相同。

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL，
    [Category] [varchar](256) NOT NULL
)
```

存储过程功能利用[表值参数](https://msdn.microsoft.com/library/bb675163.aspx)。

## <a name="data-type-mapping-for-sql-server"></a>SQL Server 的数据类型映射

从/向 SQL Server 复制数据时，以下映射用于从 SQL Server 数据类型映射到 Azure 数据工厂临时数据类型。 若要了解复制活动如何将源架构和数据类型映射到接收器，请参阅[架构和数据类型映射](copy-activity-schema-and-type-mapping.md)。

| SQL Server 数据类型 | 数据工厂临时数据类型 |
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
> 对于映射到十进制临时类型的数据类型，目前 ADF 支持的最大精度为 28。 如果有精度大于 28 的数据，请考虑在 SQL 查询中将其转换为字符串。

## <a name="troubleshooting-connection-issues"></a>连接问题故障排除

1. 将 SQL Server 配置为接受远程连接。 启动 SQL Server Management Studio，右键单击“服务器”，并单击“属性”    。 从列表选择“连接”，并选中“允许远程连接到此服务器”   。

    ![启用远程连接](media/copy-data-to-from-sql-server/AllowRemoteConnections.png)

    有关详细步骤，请参阅[配置远程访问服务器配置选项](https://msdn.microsoft.com/library/ms191464.aspx)。

2. 启动“SQL Server 配置管理器”  。 针对所需实例展开“SQL Server 网络配置”，并选择“MSSQLSERVER 的协议”   。 应会在右侧窗格中看到协议。 右键单击“TCP/IP”，并单击“启用”，可启用 TCP/IP   。

    ![启用 TCP/IP](./media/copy-data-to-from-sql-server/EnableTCPProptocol.png)

    有关详细信息和启用 TCP/IP 协议的其他方法，请参阅[启用或禁用服务器网络协议](https://msdn.microsoft.com/library/ms191294.aspx)。

3. 在同一窗口中，双击“TCP/IP”以启动“TCP/IP 属性”窗口   。
4. 切换到“IP 地址”选项卡  。向下滚动以查看 IPAll 部分  。 记下 TCP 端口（默认值是 1433）   。
5. 在计算机上创建 Windows 防火墙规则，以便允许通过此端口传入流量  。  
6. **验证连接**：若要使用完全限定名称连接到 SQL Server，请从另一台计算机使用 SQL Server Management Studio。 例如：`"<machine>.<domain>.corp.<company>.com,1433"`。

## <a name="next-steps"></a>后续步骤
有关 Azure 数据工厂中复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md##supported-data-stores-and-formats)。
