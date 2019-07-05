---
title: 使用数据工厂向/从 Azure SQL 数据库复制数据 | Microsoft Docs
description: 了解如何使用数据工厂将数据从支持的源数据存储复制到 Azure SQL 数据库，或从 SQL 数据库复制到支持的接收器数据存储。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/14/2019
ms.author: jingwang
ms.openlocfilehash: 5dbd739070b1f66fe5ff04f319a3818269d0bdaa
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449610"
---
# <a name="copy-data-to-or-from-azure-sql-database-by-using-azure-data-factory"></a>使用 Azure 数据工厂向/从 Azure SQL 数据库复制数据
> [!div class="op_single_selector" title1="选择要使用的 Azure 数据工厂的版本："]
> * [版本 1](v1/data-factory-azure-sql-connector.md)
> * [当前版本](connector-azure-sql-database.md)

本文概述了如何将数据复制到 Azure SQL 数据库和从 Azure SQL 数据库复制数据。 若要了解 Azure 数据工厂，请阅读[介绍性文章](introduction.md)。

## <a name="supported-capabilities"></a>支持的功能

此 Azure SQL 数据库连接器支持以下活动：

- [复制活动](copy-activity-overview.md)与[支持源/接收器矩阵](copy-activity-overview.md)表
- [映射数据流](concepts-data-flow-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)
- [GetMetadata 活动](control-flow-get-metadata-activity.md)

具体而言，此 Azure SQL 数据库连接器支持以下函数：

- 将数据复制的 Azure 资源与服务主体或托管标识使用 SQL 身份验证和 Azure Active Directory (Azure AD) 应用程序令牌身份验证。
- 作为源，通过使用 SQL 查询或存储的过程检索数据。
- 作为接收器，将数据追加到目标表中或在复制期间调用带有自定义逻辑的存储的过程。

>[!NOTE]
>Azure SQL 数据库[Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-current)现在不支持此连接器。 若要解决，可以使用[泛型 ODBC 连接器](connector-odbc.md)和通过自承载的集成运行时的 SQL Server ODBC 驱动程序。 请按照[本指南](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-current)使用 ODBC 驱动程序下载和连接字符串配置。

> [!IMPORTANT]
> 如果使用 Azure 数据工厂 integration runtime 复制数据，请配置[Azure SQL Server 防火墙](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure)，以便 Azure 服务可以访问服务器。
> 如果使用自承载的 integration runtime 复制数据，请配置 Azure SQL Server 防火墙以允许适当的 IP 范围。 此范围还包括用于连接到 Azure SQL 数据库的计算机的 IP。

## <a name="get-started"></a>开始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

以下部分提供有关用于定义于 Azure SQL 数据库连接器特定的 Azure 数据工厂实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

Azure SQL 数据库链接服务支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | type 属性必须设置为 AzureSqlDatabase   。 | 是 |
| connectionString | 为 connectionString 属性指定连接到 Azure SQL 数据库实例所需的信息  。 <br/>将为此字段标记**SecureString**安全地将其存储在 Azure 数据工厂。 此外可以在 Azure 密钥保管库中将密码或服务主体键。 如果它是 SQL 身份验证，请将拉`password`带连接字符串配置。 有关详细信息，请参阅下表的 JSON 示例和[在 Azure 密钥保管库中存储凭据](store-credentials-in-key-vault.md)。 | 是 |
| servicePrincipalId | 指定应用程序的客户端 ID。 | 是的当使用 Azure AD 身份验证和服务主体 |
| servicePrincipalKey | 指定应用程序的密钥。 将为此字段标记**SecureString**以将其安全地存储在 Azure 数据工厂中或[引用 Azure Key Vault 中存储的机密](store-credentials-in-key-vault.md)。 | 是的当使用 Azure AD 身份验证和服务主体 |
| tenant | 指定租户信息，如的域名或租户的 ID，你的应用程序所在。 通过将鼠标悬停鼠标在 Azure 门户的右上角进行检索。 | 是的当使用 Azure AD 身份验证和服务主体 |
| connectVia | 此[集成运行时](concepts-integration-runtime.md)用于连接到数据存储。 如果数据存储位于专用网络中，可以使用 Azure 集成运行时或自承载的集成运行时。 如果未指定，则使用默认 Azure 集成运行时。 | 否 |

有关各种身份验证类型，请参阅关于先决条件和 JSON 示例的以下各部分：

- [SQL 身份验证](#sql-authentication)
- [Azure AD 应用程序令牌身份验证：服务主体](#service-principal-authentication)
- [Azure AD 应用程序令牌身份验证：Azure 资源的托管标识](#managed-identity)

>[!TIP]
>如果达到，错误代码"UserErrorFailedToConnectToSqlServer"错误和消息，如"数据库会话数限制为 XXX 和已达到"，添加`Pooling=false`到连接字符串和重试。

### <a name="sql-authentication"></a>SQL 身份验证

#### <a name="linked-service-example-that-uses-sql-authentication"></a>使用 SQL 身份验证的链接服务示例

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
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

**Azure 密钥保管库中的密码** 

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
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

### <a name="service-principal-authentication"></a>服务主体身份验证

要使用基于服务主体的 Azure AD 应用程序令牌身份验证，请执行以下步骤：

1. [创建 Azure Active Directory 应用程序](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)从 Azure 门户。 记下应用程序名称，以及以下定义链接服务的值：

    - 应用程序 ID
    - 应用程序密钥
    - 租户 ID

2. [预配 Azure Active Directory 管理员](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)为 Azure SQL Server 在 Azure 门户中，如果尚未这样做。 Azure AD 管理员必须是 Azure AD 用户或 Azure AD 组，但不能是服务主体。 执行此步骤后，在下一步骤中便可使用 Azure AD 标识来为服务主体创建包含的数据库用户。

3. [创建包含的数据库用户](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)为服务主体。 连接到从数据库或想要通过至少有一个 Azure AD 标识使用 SQL Server Management Studio 等工具将数据复制 ALTER ANY USER 权限。 运行以下 T-SQL： 
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. 授予服务主体所需的权限按照通常的 SQL 用户或其他人的方式。 运行下面的代码。 有关更多选项，请参阅[本文档](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017)。

    ```sql
    EXEC sp_addrolemember [role name], [your application name];
    ```

5. 在 Azure 数据工厂中配置 Azure SQL 数据库链接服务。


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>使用服务主体身份验证的链接服务示例

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
            },
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identity"></a> Azure 资源的托管标识身份验证

可将数据工厂与代表此特定数据工厂的 [Azure 资源托管标识](data-factory-service-identity.md)相关联。 可将此托管标识用于 Azure SQL 数据库身份验证。 指定工厂可使用此标识访问数据库数据并向其/从中复制数据。

若要使用托管的标识身份验证，请执行以下步骤。

1. [预配 Azure Active Directory 管理员](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)为 Azure SQL Server 在 Azure 门户中，如果尚未这样做。 Azure AD 管理员可以是 Azure AD 用户或 Azure AD 组。 如果授予包含托管标识的组管理员角色，则可跳过步骤 3 和步骤 4。 管理员具有完全访问权限的数据库。

2. [创建包含的数据库用户](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)用于 Azure 数据工厂托管标识。 连接到从数据库或想要通过至少有一个 Azure AD 标识使用 SQL Server Management Studio 等工具将数据复制 ALTER ANY USER 权限。 运行以下 T-SQL： 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. 授予数据工厂托管标识需要与你的权限通常情况下进行的 SQL 用户和其他用户。 运行下面的代码。 有关更多选项，请参阅[本文档](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017)。

    ```sql
    EXEC sp_addrolemember [role name], [your Data Factory name];
    ```

4. 在 Azure 数据工厂中配置 Azure SQL 数据库链接服务。

**示例**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
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

有关的各部分和属性可用于定义数据集的完整列表，请参阅[数据集](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services)。 本部分提供 Azure SQL 数据库数据集支持的属性列表。

若要从 Azure SQL 数据库复制数据或将数据复制到 Azure SQL 数据库，需要支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为 AzureSqlTable   。 | 是 |
| tableName | 链接服务引用的 Azure SQL 数据库实例中的表名称或视图名称。 | 对于源为“No”，对于接收器为“Yes” |

#### <a name="dataset-properties-example"></a>数据集属性示例

```json
{
    "name": "AzureSQLDbDataset",
    "properties":
    {
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Database linked service name>",
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

有关可用于定义活动的各个部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)。 本部分提供 Azure SQL 数据库源和接收器支持的属性列表。

### <a name="azure-sql-database-as-the-source"></a>Azure SQL 数据库作为源

若要从 Azure SQL 数据库复制数据，设置**类型**到复制活动源中的属性**SqlSource**。 复制活动源  部分支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | **类型**的复制活动源的属性必须设置为**SqlSource**。 | 是 |
| sqlReaderQuery | 此属性使用自定义 SQL 查询来读取数据。 例如 `select * from MyTable`。 | 否 |
| sqlReaderStoredProcedureName | 从源表读取数据的存储过程的名称。 最后一个 SQL 语句必须是存储过程中的 SELECT 语句。 | 否 |
| storedProcedureParameters | 存储过程的参数。<br/>允许的值为名称或值对。 名称和参数的大小写必须与匹配的名称和存储的过程参数的大小写。 | 否 |

**需要注意的要点：**

- 如果**sqlReaderQuery**为指定**SqlSource**，复制活动针对 Azure SQL 数据库源获取的数据运行此查询。 也可通过指定 sqlReaderStoredProcedureName 和 storedProcedureParameters 来指定存储过程，前提是存储过程使用参数   。
- 如果未指定任一**sqlReaderQuery**或**sqlReaderStoredProcedureName**，数据集 JSON 的"structure"节中定义的列用于构造查询。 查询`select column1, column2 from mytable`针对 Azure SQL 数据库运行。 如果数据集定义没有“structure”，则会从表中选择所有列。

#### <a name="sql-query-example"></a>SQL 查询示例

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
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

#### <a name="stored-procedure-example"></a>存储过程示例

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
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

### <a name="stored-procedure-definition"></a>存储过程定义

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

### <a name="azure-sql-database-as-the-sink"></a>Azure SQL 数据库作为接收器

> [!TIP]
> 了解有关受支持的写入行为、 配置和最佳实践的详细信息[最佳做法，用于将数据加载到 Azure SQL 数据库](#best-practice-for-loading-data-into-azure-sql-database)。

若要将数据复制到 Azure SQL 数据库，设置**类型**到接收器的复制活动中的属性**SqlSink**。 复制活动接收器部分中支持以下属性  ：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | **类型**的复制活动接收器的属性必须设置为**SqlSink**。 | 是 |
| writeBatchSize | 要插入 SQL 表的行数*每个批处理*。<br/> 允许的值为 **integer**（行数）。 默认情况下，Azure 数据工厂动态确定基于行大小的合适的批大小。 | 否 |
| writeBatchTimeout | 超时前等待批插入操作完成的时间。<br/> 允许的值为 **timespan**。 一个示例是"00: 30:00"（30 分钟）。 | 否 |
| preCopyScript | 指定复制活动将数据写入到 Azure SQL 数据库之前运行的 SQL 查询。 每次运行复制仅调用该查询一次。 使用此属性清理预加载的数据。 | 否 |
| sqlWriterStoredProcedureName | 定义如何将源数据应用于目标表的存储过程的名称。 <br/>此存储过程由每个批处理调用  。 对于仅运行一次，毫无执行与源数据，例如，删除或截断的操作，使用`preCopyScript`属性。 | 否 |
| storedProcedureParameters |存储过程的参数。<br/>允许的值为名称和值对。 参数的名称和大小写必须与存储过程参数的名称和大小写匹配。 | 否 |
| sqlWriterTableType | 指定要在存储过程中使用的表类型名称。 复制活动，使要移动的数据拥有此表类型的临时表中可用。 然后，存储过程代码可合并复制数据和现有数据。 | 否 |

**示例 1：追加数据**

```json
"activities":[
    {
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
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
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
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

## <a name="best-practice-for-loading-data-into-azure-sql-database"></a>将数据加载到 Azure SQL 数据库的最佳做法

在将数据复制到 Azure SQL 数据库时，可能需要不同的写入行为：

- [追加](#append-data):我的源数据具有新的记录。
- [更新插入](#upsert-data):我的源数据已插入和更新。
- [覆盖](#overwrite-the-entire-table):我想要每次重新加载整个维度表。
- [使用自定义逻辑编写](#write-data-with-custom-logic):我需要额外的处理之前最后一次插入到目标表。

请参阅有关如何在 Azure 数据工厂和最佳实践中配置的相应部分。

### <a name="append-data"></a>追加数据

将数据追加为此 Azure SQL 数据库接收器连接器的默认行为。 Azure 数据工厂执行大容量插入以高效率地写入到表。 可以配置源和接收器相应地将复制活动中。

### <a name="upsert-data"></a>更新插入数据

**选项 1：** 当您有大量的数据复制，请使用以下方法来执行 upsert: 

- 首先，使用[数据库范围的临时表](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current#database-scoped-global-temporary-tables-azure-sql-database)使用复制活动的所有记录的大容量加载。 因为对数据库作用域的临时表执行的操作而不记录，可以在数秒内加载数百万条记录。
- 在 Azure 数据工厂应用中运行存储的过程活动[合并](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current)或插入/更新语句。 使用临时表的源上执行所有更新或插入作为单个事务。 在这种方式，减少往返和日志操作的数量。 在存储的过程活动结束时，可以准备就绪可供下一步的 upsert 周期而被截断临时表。

例如，在 Azure 数据工厂中，您可以创建管道，其中包含**的复制活动**与关联**存储过程活动**。 前者将数据从复制的源存储在 Azure SQL 数据库临时表，例如， **##UpsertTempTable**，作为在数据集中的表名称。 然后后者调用存储的过程来合并到目标表的临时表中的源数据和清理临时表。

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

- 加载到一个数据库作用域内的临时表，然后调用存储的过程。 
- 在复制过程中调用存储的过程。

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a> 调用 SQL 接收器的存储过程

当将数据复制到 Azure SQL 数据库时，还可以配置和调用使用其他参数的用户指定的存储的过程。

> [!TIP]
> 调用存储的过程处理的数据行的行而不是通过使用大容量操作，我们不建议用于大规模复制。 了解详细信息[最佳做法，用于将数据加载到 Azure SQL 数据库](#best-practice-for-loading-data-into-azure-sql-database)。

当内置复制机制无法使用时，还可使用存储过程。 例如，当你想要应用之前最后一次插入的源数据插入目标表的额外处理。 一些额外的处理示例是当你想要合并列、 查找其他值，并将插入到多个表。

以下示例演示如何使用存储过程在 Azure SQL 数据库数据库中的表内执行 upsert。 假设输入的数据和接收器**市场营销**每个表具有三个列：**ProfileID**、**State** 和 **Category**。 基于 ProfileID 列执行 upsert，并仅将其应用于特定类别  。

**输出数据集：** "TableName"是表类型参数中的同名存储过程，如以下存储的过程脚本中所示：

```json
{
    "name": "AzureSQLDbDataset",
    "properties":
    {
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Database linked service name>",
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
    [State] [varchar](256) NOT NULL,
    [Category] [varchar](256) NOT NULL
)
```

存储过程功能利用[表值参数](https://msdn.microsoft.com/library/bb675163.aspx)。

## <a name="mapping-data-flow-properties"></a>映射数据流属性

详细信息请参阅[源转换](data-flow-source.md)并[接收器转换](data-flow-sink.md)中映射的数据流。

## <a name="data-type-mapping-for-azure-sql-database"></a>Azure SQL 数据库的数据类型映射

从 / 向 Azure SQL 数据库复制数据时，以下映射用于从 Azure SQL 数据库数据类型到 Azure 数据工厂临时数据类型。 若要了解复制活动如何将源架构和数据类型映射到接收器，请参阅[架构和数据类型映射](copy-activity-schema-and-type-mapping.md)。

| Azure SQL 数据库数据类型 | Azure 数据工厂临时数据类型 |
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
| tinyint |Byte |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |Xml |

>[!NOTE]
> 对于映射到十进制临时类型的数据类型，目前 Azure 数据工厂支持的最大精度为 28。 如果您的精度大于 28 的数据，请考虑将转换为 SQL 查询中的字符串。

## <a name="next-steps"></a>后续步骤
通过 Azure 数据工厂中的复制活动支持作为源和接收器数据存储的列表，请参阅[支持的数据存储和格式](copy-activity-overview.md##supported-data-stores-and-formats)。
