---
title: 在 Azure SQL 数据库中复制和转换数据
description: 了解如何使用 Azure 数据工厂将数据复制到 Azure SQL 数据库，以及如何转换 Azure SQL 数据库中的数据。
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/13/2019
ms.openlocfilehash: 7ef28933dc7d10817982690aa3c7bc866c33eb03
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75440698"
---
# <a name="copy-and-transform-data-in-azure-sql-database-by-using-azure-data-factory"></a>使用 Azure 数据工厂在 Azure SQL 数据库中复制和转换数据

> [!div class="op_single_selector" title1="选择要使用的 Azure 数据工厂的版本："]
> * [版本 1](v1/data-factory-azure-sql-connector.md)
> * [当前版本](connector-azure-sql-database.md)

本文概述如何使用 Azure 数据工厂中的复制活动将数据从和复制到 Azure SQL 数据库，并使用数据流转换 Azure SQL 数据库中的数据。 若要了解 Azure 数据工厂，请阅读[介绍性文章](introduction.md)。

## <a name="supported-capabilities"></a>支持的功能

此 Azure SQL 数据库连接器支持以下活动：

- [复制活动](copy-activity-overview.md)与[支持的源/接收器矩阵](copy-activity-overview.md)表
- [映射数据流](concepts-data-flow-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)
- [GetMetadata 活动](control-flow-get-metadata-activity.md)

对于复制活动，此 Azure SQL 数据库连接器支持以下功能：

- 使用 SQL 身份验证和 Azure Active Directory （Azure AD）应用程序令牌身份验证通过 Azure 资源的服务主体或托管标识复制数据。
- 作为源，使用 SQL 查询或存储过程检索数据。
- 作为接收器，在复制过程中将数据追加到目标表或使用自定义逻辑调用存储过程。

>[!NOTE]
>此连接器目前不支持 Azure SQL 数据库[Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-current) 。 若要解决此情况，可以通过自承载集成运行时使用[泛型 odbc 连接器](connector-odbc.md)和 SQL Server ODBC 驱动程序。 按照[此指南](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-current)进行 ODBC 驱动程序下载和连接字符串配置。

> [!IMPORTANT]
> 如果使用 Azure 数据工厂 integration runtime 复制数据，请配置[azure SQL Server 防火墙](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure)，使 azure 服务可以访问服务器。
> 如果使用自承载 integration runtime 复制数据，请将 Azure SQL Server 防火墙配置为允许适当的 IP 范围。 此范围包括用于连接到 Azure SQL 数据库的计算机 IP。

## <a name="get-started"></a>开始体验

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

对于特定于 Azure SQL 数据库连接器的 Azure 数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

Azure SQL 数据库链接服务支持以下属性：

| 属性 | Description | 需要 |
|:--- |:--- |:--- |
| type | type 属性必须设置为 AzureSqlDatabase。 | 是 |
| connectionString | 为 connectionString 属性指定连接到 Azure SQL 数据库实例所需的信息。 <br/>你还可以将密码或服务主体密钥放在 Azure Key Vault 中。 如果它是 SQL 身份验证，请从连接字符串中请求 `password` 配置。 有关详细信息，请参阅表后面的 JSON 示例，并[在 Azure Key Vault 中存储凭据](store-credentials-in-key-vault.md)。 | 是 |
| servicePrincipalId | 指定应用程序的客户端 ID。 | 是，当你将 Azure AD 身份验证用于服务主体时 |
| servicePrincipalKey | 指定应用程序的密钥。 将此字段标记为**SecureString**以将其安全地存储在 Azure 数据工厂中或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 | 是，当你将 Azure AD 身份验证用于服务主体时 |
| tenant | 指定应用程序所驻留的租户信息，例如域名或租户 ID。 通过将鼠标悬停在 Azure 门户的右上角来检索它。 | 是，当你将 Azure AD 身份验证用于服务主体时 |
| connectVia | 此[集成运行时](concepts-integration-runtime.md)用于连接到数据存储。 如果数据存储位于专用网络，则可以使用 Azure 集成运行时或自承载集成运行时。 如果未指定，则使用默认的 Azure 集成运行时。 | 否 |

有关各种身份验证类型，请参阅关于先决条件和 JSON 示例的以下各部分：

- [SQL 身份验证](#sql-authentication)
- [Azure AD 应用程序令牌身份验证：服务主体](#service-principal-authentication)
- [Azure AD 应用程序令牌身份验证：Azure 资源的托管标识](#managed-identity)

>[!TIP]
>如果遇到错误代码为 "UserErrorFailedToConnectToSqlServer" 的错误和消息，如 "数据库的会话限制为 XXX 并已达到"，请 "将 `Pooling=false` 添加到连接字符串，然后重试。

### <a name="sql-authentication"></a>SQL 身份验证

#### <a name="linked-service-example-that-uses-sql-authentication"></a>使用 SQL 身份验证的链接服务示例

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Azure Key Vault 中的密码** 

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30",
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

1. 从 Azure 门户[创建 Azure Active Directory 应用程序](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)。 记下应用程序名称，以及以下定义链接服务的值：

    - 应用程序 ID
    - 应用程序密钥
    - 租户 ID

2. 为 Azure Azure 门户 SQL Server[预配 Azure Active Directory 管理员](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)（如果尚未这样做）。 Azure AD 管理员必须是 Azure AD 用户或 Azure AD 组，但不能是服务主体。 执行此步骤后，在下一步骤中便可使用 Azure AD 标识来为服务主体创建包含的数据库用户。

3. 为服务主体[创建包含的数据库用户](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)。 使用 SQL Server Management Studio 之类的工具连接到要从中复制数据的数据库，并使用至少具有 ALTER ANY USER 权限的 Azure AD 标识连接到该数据库。 运行以下 T-SQL： 
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. 向服务主体授予对 SQL 用户或其他用户通常所需的权限。 运行下面的代码。 有关更多选项，请参阅[此文档](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017)。

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
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30",
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

可将数据工厂与代表此特定数据工厂的 [Azure 资源托管标识](data-factory-service-identity.md)相关联。 可以将此托管标识用于 Azure SQL Database 身份验证。 指定的工厂可以使用此标识访问数据库数据或从/向数据库复制数据。

若要使用托管标识身份验证，请执行以下步骤。

1. 为 Azure Azure 门户 SQL Server[预配 Azure Active Directory 管理员](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)（如果尚未这样做）。 Azure AD 管理员可以是 Azure AD 用户或 Azure AD 组。 如果为具有托管标识的组授予管理员角色，请跳过步骤 3 和 4。 管理员对数据库具有完全访问权限。

2. 为 Azure 数据工厂托管标识[创建包含的数据库用户](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)。 使用 SQL Server Management Studio 之类的工具连接到要从中复制数据的数据库，并使用至少具有 ALTER ANY USER 权限的 Azure AD 标识连接到该数据库。 运行以下 T-SQL： 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. 向数据工厂托管标识授予对 SQL 用户和其他用户通常所需的权限。 运行下面的代码。 有关更多选项，请参阅[此文档](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017)。

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
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各节和属性的完整列表，请参阅[数据集](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services)。 

Azure SQL 数据库数据集支持以下属性：

| 属性 | Description | 需要 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为 AzureSqlTable。 | 是 |
| 架构 | 架构的名称。 |对于源为“No”，对于接收器为“Yes”  |
| 表 | 表/视图的名称。 |对于源为“No”，对于接收器为“Yes”  |
| tableName | 具有架构的表/视图的名称。 支持此属性是为了向后兼容。 对于新工作负荷，请使用 `schema` 和 `table`。 | 对于源为“No”，对于接收器为“Yes” |

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
            "schema": "<schema_name>",
            "table": "<table_name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各个部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)。 本部分提供 Azure SQL 数据库源和接收器支持的属性列表。

### <a name="azure-sql-database-as-the-source"></a>Azure SQL 数据库作为源

若要从 Azure SQL 数据库复制数据，复制活动**源**部分支持以下属性：

| 属性 | Description | 需要 |
|:--- |:--- |:--- |
| type | 复制活动源的**type**属性必须设置为**AzureSqlSource**。 对于向后兼容性，仍支持 "SqlSource" 类型。 | 是 |
| sqlReaderQuery | 此属性使用自定义 SQL 查询来读取数据。 示例为 `select * from MyTable`。 | 否 |
| sqlReaderStoredProcedureName | 从源表读取数据的存储过程的名称。 最后一个 SQL 语句必须是存储过程中的 SELECT 语句。 | 否 |
| storedProcedureParameters | 存储过程的参数。<br/>允许的值为名称或值对。 参数的名称和大小写必须与存储过程参数的名称和大小写相匹配。 | 否 |

**需要注意的要点：**

- 如果为**AzureSqlSource**指定**sqlReaderQuery** ，则复制活动对 Azure SQL 数据库源运行此查询以获取数据。 也可通过指定 sqlReaderStoredProcedureName 和 storedProcedureParameters 来指定存储过程，前提是存储过程使用参数。
- 如果未指定**sqlReaderQuery**或**sqlReaderStoredProcedureName**，则使用在数据集 JSON 的 "结构" 部分定义的列来构造查询。 `select column1, column2 from mytable` 对 Azure SQL 数据库运行查询。 如果数据集定义没有“structure”，则会从表中选择所有列。

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
                "type": "AzureSqlSource",
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
                "type": "AzureSqlSource",
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
> 详细了解将[数据加载到 AZURE SQL 数据库的最佳做法](#best-practice-for-loading-data-into-azure-sql-database)中所支持的写入行为、配置和最佳实践。

若要将数据复制到 Azure SQL 数据库，请在复制活动**接收器**部分中支持以下属性：

| 属性 | Description | 需要 |
|:--- |:--- |:--- |
| type | 复制活动接收器的**type**属性必须设置为**AzureSqlSink**。 对于向后兼容性，仍支持 "SqlSink" 类型。 | 是 |
| writeBatchSize | *每批*插入到 SQL 表中的行数。<br/> 允许的值为 **integer**（行数）。 默认情况下，Azure 数据工厂会根据行大小动态确定相应的批大小。 | 否 |
| writeBatchTimeout | 超时前等待批插入操作完成的时间。<br/> 允许的值为 **timespan**。 例如，"00:30:00" （30分钟）。 | 否 |
| preCopyScript | 指定在将数据写入 Azure SQL 数据库之前要运行的复制活动的 SQL 查询。 每次运行复制仅调用该查询一次。 使用此属性清理预加载的数据。 | 否 |
| sqlWriterStoredProcedureName | 定义如何将源数据应用于目标表的存储过程的名称。 <br/>此存储过程由每个批处理调用。 对于仅运行一次并且与源数据不执行任何操作（例如，删除或截断）的操作，请使用 `preCopyScript` 属性。 | 否 |
| storedProcedureTableTypeParameterName |存储过程中指定的表类型的参数名称。  |否 |
| sqlWriterTableType |要在存储过程中使用的表类型名称。 通过复制活动，使移动数据在具备此表类型的临时表中可用。 然后，存储过程代码可合并复制数据和现有数据。 |否 |
| storedProcedureParameters |存储过程的参数。<br/>允许的值为名称和值对。 参数的名称和大小写必须与存储过程参数的名称和大小写匹配。 | 否 |
| tableOption | 指定是否根据源架构自动创建接收器表（如果不存在）。 在复制活动中配置了 sink 指定存储过程或暂存副本时，不支持创建自动表。 允许的值为： `none` （默认值），`autoCreate`。 |否 |
| disableMetricsCollection | 数据工厂收集指标（如 Azure SQL 数据库 Dtu），以获取复制性能优化和建议。 如果你担心此行为，请指定 `true` 将其关闭。 | 否（默认值为 `false`） |

**示例1：追加数据**

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
                "type": "AzureSqlSink",
                "writeBatchSize": 100000,
                "tableOption": "autoCreate"
            }
        }
    }
]
```

**示例2：在复制过程中调用存储过程**

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
                "type": "AzureSqlSink",
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

## <a name="best-practice-for-loading-data-into-azure-sql-database"></a>将数据加载到 Azure SQL 数据库的最佳做法

将数据复制到 Azure SQL 数据库时，可能需要不同的写入行为：

- [追加](#append-data)：我的源数据只有新记录。
- [Upsert](#upsert-data)：我的源数据同时包含插入和更新。
- [覆盖](#overwrite-the-entire-table)：我想要每次重新加载整个维度表。
- [用自定义逻辑编写](#write-data-with-custom-logic)：需要额外处理才能在目标表中执行最终插入操作。

有关如何在 Azure 数据工厂中进行配置和最佳实践，请参阅相应的部分。

### <a name="append-data"></a>追加数据

此 Azure SQL 数据库接收器连接器的默认行为是追加数据。 Azure 数据工厂执行大容量插入来有效地写入表。 可以在复制活动中相应地配置源和接收器。

### <a name="upsert-data"></a>更新插入数据

**选项1：** 如果要复制大量数据，请使用以下方法执行 upsert： 

- 首先，使用[数据库范围内的临时表](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current#database-scoped-global-temporary-tables-azure-sql-database)，通过使用复制活动大容量加载所有记录。 由于不记录对数据库作用域的临时表的操作，因此你可以在数秒内加载数百万条记录。
- 在 Azure 数据工厂中运行存储过程活动，以应用[MERGE](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current)或 INSERT/UPDATE 语句。 使用临时表作为作为单个事务执行所有更新或插入的源。 通过这种方式，可减少往返次数和日志操作。 在存储过程活动结束时，可以截断临时表，以便在下一个 upsert 周期中做好准备。

例如，在 Azure 数据工厂中，可以创建一个管道，其中包含与**存储过程活动**链接的**复制活动**。 前者将源存储中的数据复制到 Azure SQL 数据库临时表中，例如，使用 **# #UpsertTempTable**作为数据集中的表名。 然后，后者将调用一个存储过程，用于将临时表中的源数据合并到目标表中，并清理临时表。

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

在数据库中，定义包含合并逻辑的存储过程，如下例所示，它指向以前的存储过程活动。 假定目标为包含三列的**营销**表：**配置文件 id**、 **State**和**Category**。 根据**配置文件 id**列来执行 upsert。

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

**选项2：** 您还可以选择在[复制活动中调用存储过程](#invoke-a-stored-procedure-from-a-sql-sink)。 此方法运行源表中的每行，而不是使用 bulk insert 作为复制活动中的默认方法，这不适用于大规模 upsert。

### <a name="overwrite-the-entire-table"></a>覆盖整个表

可以在复制活动接收器中配置**preCopyScript**属性。 在这种情况下，对于运行的每个复制活动，Azure 数据工厂首先运行脚本。 然后，它将运行复制以插入数据。 例如，若要使用最新数据覆盖整个表，请在从源中大容量加载新数据之前，指定脚本以首先删除所有记录。

### <a name="write-data-with-custom-logic"></a>用自定义逻辑写入数据

用自定义逻辑写入数据的步骤类似于[Upsert data](#upsert-data)节中所述的步骤。 如果需要在将源数据最终插入目标表之前应用额外的处理，以进行大规模调整，则可以执行以下两个操作之一：

- 加载到数据库范围内的临时表，然后调用存储过程。 
- 在复制过程中调用存储过程。

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a> 调用 SQL 接收器的存储过程

将数据复制到 Azure SQL 数据库时，还可以使用其他参数配置和调用用户指定的存储过程。 存储过程功能利用[表值参数](https://msdn.microsoft.com/library/bb675163.aspx)。

> [!TIP]
> 调用存储过程时，将按行而不是使用大容量操作来处理数据行，但我们不建议使用大容量复制。 了解有关将[数据加载到 AZURE SQL 数据库的最佳实践的](#best-practice-for-loading-data-into-azure-sql-database)详细信息。

当内置复制机制无法使用时，还可使用存储过程。 例如，当你想要在将源数据的最终插入目标表之前应用额外处理时。 一些额外的处理示例是要合并列、查找其他值并插入多个表。

以下示例演示如何使用存储过程在 Azure SQL 数据库数据库中的表内执行 upsert。 假设输入数据和接收器**营销**表各有三列：**配置文件 id**、 **State**和**Category**。 根据**配置文件 id**列执行 upsert，并仅将其应用于名为 "ProductA" 的特定类别。

1. 在数据库中，用与**sqlWriterTableType**相同的名称定义表类型。 表类型的架构与输入数据返回的架构相同。

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL，
        [Category] [varchar](256) NOT NULL
    )
    ```

2. 在数据库中，用与**sqlWriterStoredProcedureName**相同的名称定义存储过程。 它可处理来自指定源的输入数据，并将其合并到输出表中。 存储过程中表类型的参数名称与数据集中定义的**tableName**相同。

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
        "type": "AzureSqlSink",
        "sqlWriterStoredProcedureName": "spOverwriteMarketing",
        "storedProcedureTableTypeParameterName": "Marketing",
        "sqlWriterTableType": "MarketingType",
        "storedProcedureParameters": {
            "category": {
                "value": "ProductA"
            }
        }
    }
    ```

## <a name="mapping-data-flow-properties"></a>映射数据流属性

在映射数据流中转换数据时，可以在 Azure SQL 数据库中读取和写入表。 有关详细信息，请参阅映射数据流中的[源转换](data-flow-source.md)和[接收器转换](data-flow-sink.md)。

### <a name="source-transformation"></a>源转换

源转换的 "**源选项**" 选项卡中提供了特定于 Azure SQL 数据库的设置。 

**输入：** 选择是将源指向某个表（等效于 ```Select * from <table-name>```）还是输入自定义 SQL 查询。

**查询**：如果在输入字段中选择 "查询"，则输入源的 SQL 查询。 此设置将重写您在数据集中选择的任何表。 此处不支持**Order By**子句，但你可以设置完整的 SELECT FROM 语句。 你还可以使用用户定义的表函数。 **select * From udfGetData （）** 是返回表的 SQL 中的 UDF。 此查询将生成可以在数据流中使用的源表。 使用查询也是减少用于测试或查找的行的好方法。 

* SQL 示例： ```Select * from MyTable where customerId > 1000 and customerId < 2000```

**批大小**：输入用于将大型数据拆分为读取的批大小。

**隔离级别**：映射数据流中 SQL 源的默认值为 "未提交读"。 可以将此处的隔离级别更改为以下值之一：
* 已提交读
* 未提交读
* 可重复的读取
* 可序列化
* 无（忽略隔离级别）

![隔离级别](media/data-flow/isolationlevel.png "隔离级别")

### <a name="sink-transformation"></a>接收器转换

特定于 Azure SQL 数据库的设置可在接收器转换的 "**设置**" 选项卡中找到。

**更新方法：** 确定对数据库目标允许哪些操作。 默认值为仅允许插入。 若要更新、upsert 或删除行，需要更改行转换才能标记这些操作的行。 对于更新、upsert 和删除，必须设置一个或多个键列，以确定要更改的行。

**表操作：** 确定在写入之前是否在目标表中重新创建或删除所有行。
* None：不会对表执行任何操作。
* 重新创建：该表将被删除并重新创建。 如果动态创建新表，则为必需。
* 截断：目标表中的所有行都将被删除。

**批大小**：控制正在每个存储桶中写入的行数。 较大的批处理大小会提高压缩和内存优化，但在缓存数据时可能会导致内存不足异常。

**Pre 和 POST sql 脚本**：输入将在之前（预处理）和之后（后处理）数据写入接收器数据库时执行的多行 sql 脚本

![pre 和 post SQL 处理脚本](media/data-flow/prepost1.png "SQL 处理脚本")

## <a name="data-type-mapping-for-azure-sql-database"></a>Azure SQL 数据库的数据类型映射

从或向 Azure SQL 数据库复制数据时，以下映射用于从 Azure SQL 数据库数据类型到 Azure 数据工厂临时数据类型。 若要了解复制活动如何将源架构和数据类型映射到接收器，请参阅[架构和数据类型映射](copy-activity-schema-and-type-mapping.md)。

| Azure SQL 数据库数据类型 | Azure 数据工厂临时数据类型 |
|:--- |:--- |
| bigint |Int64 |
| binary |Byte[] |
| bit |Boolean |
| char |String, Char[] |
| date |日期/时间 |
| Datetime |日期/时间 |
| datetime2 |日期/时间 |
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
| real |单一 |
| rowversion |Byte[] |
| smalldatetime |日期/时间 |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |对象 |
| text |String, Char[] |
| time |TimeSpan |
| timestamp |Byte[] |
| tinyint |Byte |
| uniqueidentifier |GUID |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |Xml |

>[!NOTE]
> 对于映射到十进制临时类型的数据类型，目前 Azure 数据工厂支持的最大精度为 28。 如果数据的精度大于28，请考虑在 SQL 查询中转换为字符串。

## <a name="lookup-activity-properties"></a>查找活动属性

若要了解有关属性的详细信息，请检查[查找活动](control-flow-lookup-activity.md)。

## <a name="getmetadata-activity-properties"></a>GetMetadata 活动属性

若要了解有关属性的详细信息，请查看[GetMetadata 活动](control-flow-get-metadata-activity.md) 

## <a name="next-steps"></a>后续步骤
有关 Azure 数据工厂中的复制活动支持作为源和接收器的数据存储列表，请参阅[支持的数据存储和格式](copy-activity-overview.md##supported-data-stores-and-formats)。
