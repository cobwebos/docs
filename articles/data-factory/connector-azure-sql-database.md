---
title: 在 Azure SQL 数据库中复制和转换数据
description: 了解如何使用 Azure 数据工厂向/从 Azure SQL 数据库复制数据，以及如何在 Azure SQL 数据库中转换数据。
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/15/2020
ms.openlocfilehash: 424f858fff0ad050286122fcbbd03fdef78c11f6
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86497674"
---
# <a name="copy-and-transform-data-in-azure-sql-database-by-using-azure-data-factory"></a>使用 Azure 数据工厂在 Azure SQL 数据库中复制和转换数据

> [!div class="op_single_selector" title1="选择要使用的 Azure 数据工厂的版本："]
>
> - [版本 1](v1/data-factory-azure-sql-connector.md)
> - [当前版本](connector-azure-sql-database.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述如何使用 Azure 数据工厂中的复制活动将数据从和复制到 Azure SQL 数据库，并使用数据流转换 Azure SQL 数据库中的数据。 若要了解 Azure 数据工厂，请阅读[介绍性文章](introduction.md)。

## <a name="supported-capabilities"></a>支持的功能

以下活动支持此 Azure SQL 数据库连接器：

- 带有[支持的源或接收器矩阵](copy-activity-overview.md)表的[复制活动](copy-activity-overview.md)
- [映射数据流](concepts-data-flow-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)
- [GetMetadata 活动](control-flow-get-metadata-activity.md)

对于复制活动，此 Azure SQL 数据库连接器支持以下功能：

- 将 SQL 身份验证和 Azure Active Directory (Azure AD) 应用程序令牌身份验证与服务主体或 Azure 资源的托管标识配合使用来复制数据。
- 作为源，使用 SQL 查询或存储过程检索数据。
- 作为接收器，根据源架构自动创建目标表（如果不存在）；在复制过程中，将数据追加到表或使用自定义逻辑调用存储过程。

>[!NOTE]
> 目前此连接器不支持 Azure SQL 数据库 [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-current)。 为了解决此问题，可以通过自承载 Integration Runtime 使用[泛型 ODBC 连接器](connector-odbc.md)和 SQL Server ODBC 驱动程序。 从[使用 Always Encrypted](#using-always-encrypted) 部分了解更多信息。 

> [!IMPORTANT]
> 如果使用 Azure 集成运行时复制数据，请配置[服务器级防火墙规则](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)，以便 Azure 服务可以访问服务器。
> 如果使用自承载集成运行时复制数据，请将防火墙配置为允许合适的 IP 范围。 此范围包括用于连接 Azure SQL 数据库的计算机的 IP。

## <a name="get-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

对于特定于 Azure SQL 数据库连接器的 Azure 数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

Azure SQL 数据库链接服务支持以下属性：

| properties | 说明 | 必需 |
|:--- |:--- |:--- |
| type | type 属性必须设置为 AzureSqlDatabase 。 | 是 |
| connectionString | 为 connectionString 属性指定连接到 Azure SQL 数据库实例所需的信息。 <br/>还可以将密码或服务主体密钥放在 Azure Key Vault 中。 如果使用 SQL 身份验证，请从连接字符串中提取 `password` 配置。 有关详细信息，请参阅表格后面的 JSON 示例，以及[在 Azure Key Vault 中存储凭据](store-credentials-in-key-vault.md)。 | 是 |
| servicePrincipalId | 指定应用程序的客户端 ID。 | 是，将 Azure AD 身份验证与服务主体配合使用时是必需的 |
| servicePrincipalKey | 指定应用程序的密钥。 将此字段标记为 **SecureString**，以安全地将其存储在 Azure 数据工厂中或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 | 是，将 Azure AD 身份验证与服务主体配合使用时是必需的 |
| tenant | 指定应用程序所在的租户的信息（例如域名或租户 ID）。 将鼠标悬停在 Azure 门户右上角进行检索。 | 是，将 Azure AD 身份验证与服务主体配合使用时是必需的 |
| connectVia | 此[集成运行时](concepts-integration-runtime.md)用于连接到数据存储。 可使用 Azure Integration Runtime 或自承载集成运行时（如果数据存储位于专用网络）。 如果未指定，则使用默认 Azure Integration Runtime。 | 否 |

有关各种身份验证类型，请参阅关于先决条件和 JSON 示例的以下各部分：

- [SQL 身份验证](#sql-authentication)
- [Azure AD 应用程序令牌身份验证：服务主体](#service-principal-authentication)
- [Azure AD 应用程序令牌身份验证：Azure 资源的托管标识](#managed-identity)

>[!TIP]
>如果遇到错误（错误代码为“UserErrorFailedToConnectToSqlServer”，且消息如“数据库的会话限制为 XXX 且已达到。”），请将 `Pooling=false` 添加到连接字符串中，然后重试。

### <a name="sql-authentication"></a>SQL 身份验证

**示例：使用 SQL 身份验证**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**示例：中的密码 Azure Key Vault**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30",
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

1. 从 Azure 门户[创建 Azure Active Directory 应用程序](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)。 记下应用程序名称，以及以下定义链接服务的值：

    - 应用程序 ID
    - 应用程序密钥
    - 租户 ID

2. 为 Azure 门户上的服务器[预配 Azure Active Directory 管理员](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-database)（如果尚未这样做）。 Azure AD 管理员必须是 Azure AD 用户或 Azure AD 组，但不能是服务主体。 执行此步骤后，在下一步骤中便可使用 Azure AD 标识来为服务主体创建包含的数据库用户。

3. 为服务主体[创建包含的数据库用户](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)。 使用 SQL Server Management Studio 等工具和至少具有 ALTER ANY USER 权限的 Azure AD 标识连接到要向其/从中复制数据的数据库。 运行以下 T-SQL：
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. 像通常对 SQL 用户或其他用户所做的那样向服务主体授予所需的权限。 运行以下代码。 有关更多选项，请参阅[此文档](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017)。

    ```sql
    ALTER ROLE [role name] ADD MEMBER [your application name];
    ```

5. 在 Azure 数据工厂中配置 Azure SQL 数据库链接服务。

#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>使用服务主体身份验证的链接服务示例

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;Connection Timeout=30",
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

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a> Azure 资源的托管标识身份验证

可将数据工厂与代表此特定数据工厂的 [Azure 资源托管标识](data-factory-service-identity.md)相关联。 可将此托管标识用于 Azure SQL 数据库身份验证。 指定的工厂可以使用此标识访问数据库数据或从/向数据库复制数据。

若要使用托管标识身份验证，请执行以下步骤：

1. 为 Azure 门户上的服务器[预配 Azure Active Directory 管理员](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-database)（如果尚未这样做）。 Azure AD 管理员可以是 Azure AD 用户，也可以是 Azure AD 组。 如果授予包含托管标识的组管理员角色，则可跳过步骤 3 和步骤 4。 管理员拥有对数据库的完全访问权限。

2. 为 Azure 数据工厂托管标识[创建包含的数据库用户](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)。 使用 SQL Server Management Studio 等工具和至少具有 ALTER ANY USER 权限的 Azure AD 标识连接到要向其/从中复制数据的数据库。 运行以下 T-SQL：
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. 像通常对 SQL 用户和其他用户所做的那样向数据工厂托管标识授予所需的权限。 运行以下代码。 有关更多选项，请参阅[此文档](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017)。

    ```sql
    ALTER ROLE [role name] ADD MEMBER [your Data Factory name];
    ```

4. 在 Azure 数据工厂中配置 Azure SQL 数据库链接服务。

**示例**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;Connection Timeout=30"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各个节和属性的完整列表，请参阅[数据集](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services)。

Azure SQL 数据库数据集支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为 AzureSqlTable 。 | 是 |
| schema | 架构的名称。 |对于源为“No”，对于接收器为“Yes”  |
| 表 | 表/视图的名称。 |对于源为“No”，对于接收器为“Yes”  |
| tableName | 具有架构的表/视图的名称。 此属性支持后向兼容性。 对于新的工作负荷，请使用 `schema` 和 `table`。 | 对于源为“No”，对于接收器为“Yes” |

### <a name="dataset-properties-example"></a>数据集属性示例

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

若要从 Azure SQL 数据库复制数据，复制活动的 **source** 节需要支持以下属性：

| properties | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 复制活动源的 **type** 属性必须设置为 **AzureSqlSource**。 为了向后兼容，仍然支持“SqlSource”类型。 | 是 |
| sqlReaderQuery | 此属性使用自定义 SQL 查询来读取数据。 例如 `select * from MyTable`。 | 否 |
| sqlReaderStoredProcedureName | 从源表读取数据的存储过程的名称。 最后一个 SQL 语句必须是存储过程中的 SELECT 语句。 | 否 |
| storedProcedureParameters | 存储过程的参数。<br/>允许的值为名称或值对。 参数的名称和大小写必须与存储过程参数的名称和大小写匹配。 | 否 |
| isolationLevel | 指定 SQL 源的事务锁定行为。 允许的值为：**ReadCommitted**（默认值）、**ReadUncommitted**、**RepeatableRead**、**Serializable**、**Snapshot**。 请参阅[此文档](https://docs.microsoft.com/dotnet/api/system.data.isolationlevel)了解更多详细信息。 | 否 |

**需要注意的要点：**

- 如果为 **AzureSqlSource** 指定 **sqlReaderQuery**，则复制活动针对 Azure SQL 数据库源运行此查询可获取数据。 也可通过指定 sqlReaderStoredProcedureName 和 storedProcedureParameters 来指定存储过程，前提是存储过程使用参数 。
- 如果不指定 **sqlReaderQuery** 或 **sqlReaderStoredProcedureName**，则数据集 JSON 的“structure”节中定义的列用于构建查询。 查询 `select column1, column2 from mytable` 针对 Azure SQL 数据库运行。 如果数据集定义没有“structure”，则会从表中选择所有列。

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
> 若要详细了解支持的写入行为、配置和最佳做法，请参阅[有关将数据载入 Azure SQL 数据库的最佳做法](#best-practice-for-loading-data-into-azure-sql-database)。

将数据复制到 Azure SQL 数据库时，复制活动的 **sink** 节支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 复制活动接收器的 **type** 属性必须设置为 **AzureSqlSink**。 为了向后兼容，仍然支持“SqlSink”类型。 | 是 |
| preCopyScript | 将数据写入到 Azure SQL 数据库之前，指定复制活动要运行的 SQL 查询。 每次运行复制仅调用该查询一次。 使用此属性清理预加载的数据。 | 否 |
| tableOption | 指定是否根据源架构[自动创建接收器表](copy-activity-overview.md#auto-create-sink-tables)（如果不存在）。 <br>当接收器指定存储过程或在复制活动中配置了暂存复制时，不支持自动表创建。 <br>允许的值为：`none`（默认值）、`autoCreate`。 | 否 |
| sqlWriterStoredProcedureName | 定义如何将源数据应用于目标表的存储过程的名称。 <br/>此存储过程由每个批处理调用。 若要执行仅运行一次且与源数据无关的操作（例如删除或截断），请使用 `preCopyScript` 属性。<br>请参阅[调用 SQL 接收器的存储过程](#invoke-a-stored-procedure-from-a-sql-sink)中的示例。 | 否 |
| storedProcedureTableTypeParameterName |存储过程中指定的表类型的参数名称。  |否 |
| sqlWriterTableType |要在存储过程中使用的表类型名称。 通过复制活动，使移动数据在具备此表类型的临时表中可用。 然后，存储过程代码可合并复制数据和现有数据。 |否 |
| storedProcedureParameters |存储过程的参数。<br/>允许的值为名称和值对。 参数的名称和大小写必须与存储过程参数的名称和大小写匹配。 | 否 |
| writeBatchSize | 每批要插入到 SQL 表中的行数。<br/> 允许的值为 **integer**（行数）。 默认情况下，Azure 数据工厂会根据行大小动态确定适当的批大小。 | 否 |
| writeBatchTimeout | 超时前等待批插入操作完成的时间。<br/> 允许的值为 **timespan**。 例如“00:30:00”（30 分钟）。 | 否 |
| disableMetricsCollection | 数据工厂收集指标（如 Azure SQL 数据库 DTU），以获取复制性能优化和建议。 如果你担心此行为，请指定 `true` 将其关闭。 | 否（默认值为 `false`） |

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
                "type": "AzureSqlSink",
                "tableOption": "autoCreate",
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

## <a name="best-practice-for-loading-data-into-azure-sql-database"></a>有关将数据载入 Azure SQL 数据库的最佳做法

将数据复制到 Azure SQL 数据库时，可能需要不同的写入行为：

- [追加](#append-data)：我的源数据只包含新记录。
- [更新插入](#upsert-data)：我的源数据包含插入和更新内容。
- [覆盖](#overwrite-the-entire-table)：我需要每次都重新加载整个维度表。
- [使用自定义逻辑进行写入](#write-data-with-custom-logic)：在将数据最终插入目标表之前，我需要额外的处理。

有关如何在 Azure 数据工厂中进行配置和最佳做法，请参阅相应的部分。

### <a name="append-data"></a>追加数据

追加数据是此 Azure SQL 数据库接收器连接器的默认行为。 Azure 数据工厂执行批量插入，以有效地在表中写入数据。 可以相应地在复制活动中配置源和接收器。

### <a name="upsert-data"></a>更新插入数据

选项 1：当需要复制大量数据时，可以使用复制活动将所有记录大容量加载到一个临时表中，然后运行存储过程活动来一次性应用 [MERGE](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) 或 INSERT/UPDATE 语句。 

复制活动当前并非原生支持将数据加载到数据库临时表中。 有一种结合多种活动进行设置的高级方法，请参阅[优化 Azure SQL 数据库批量更新插入方案](https://github.com/scoriani/azuresqlbulkupsert)。 下面显示了使用永久表作为暂存的示例。

例如，在 Azure 数据工厂中，可以使用**复制活动**创建一个管道，并将其与**存储过程活动**相链接。 前者将数据从源存储复制到数据集中的 Azure SQL 数据库临时表（例如，表名为“UpsertStagingTable”的表）。 然后，后者调用一个存储过程，以将临时表中的源数据合并到目标表中，并清理临时表。

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

在数据库中使用 MERGE 逻辑定义一个存储过程（如以下示例所示），以便从上述存储过程活动指向该过程。 假设目标是包含三个列的 **Marketing** 表：**ProfileID**、**State** 和 **Category**。 根据 **ProfileID** 列执行更新插入。

```sql
CREATE PROCEDURE [dbo].[spMergeData]
AS
BEGIN
   MERGE TargetTable AS target
   USING UpsertStagingTable AS source
   ON (target.[ProfileID] = source.[ProfileID])
   WHEN MATCHED THEN
      UPDATE SET State = source.State
    WHEN NOT matched THEN
       INSERT ([ProfileID], [State], [Category])
      VALUES (source.ProfileID, source.State, source.Category);
    TRUNCATE TABLE UpsertStagingTable
END
```

选项 2：可选择[在复制活动中调用存储过程](#invoke-a-stored-procedure-from-a-sql-sink)。 这种方法运行源表中的每个批（由 `writeBatchSize` 属性控制），而不是在复制活动中使用批量插入作为默认方法。

**选项3：** 可以使用[映射数据流](#sink-transformation)，该数据流提供内置的 insert/upsert/update 方法。

### <a name="overwrite-the-entire-table"></a>覆盖整个表

可以在复制活动接收器中配置 **preCopyScript** 属性。 在此情况下，对于运行的每个复制活动，Azure 数据工厂会先运行脚本。 然后，运行复制来插入数据。 例如，若要使用最新数据覆盖整个表，请指定一个脚本，以先删除所有记录，然后从源批量加载新数据。

### <a name="write-data-with-custom-logic"></a>使用自定义逻辑写入数据

使用自定义逻辑写入数据的步骤与[更新插入数据](#upsert-data)部分中的描述类似。 如果需要在将源数据的最终插入目标表之前应用额外的处理，可以先将数据加载到临时表，然后调用存储过程活动，或在复制活动接收器中调用存储过程以应用数据，或使用映射数据流。

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a> 调用 SQL 接收器的存储过程

将数据复制到 Azure SQL 数据库中时，还可以通过对每批源表使用附加参数来配置和调用用户指定的存储过程。 存储过程功能利用[表值参数](https://msdn.microsoft.com/library/bb675163.aspx)。

当内置复制机制无法使用时，还可使用存储过程。 例如，在将源数据最终插入目标表之前应用额外的处理。 额外处理的示例包括合并列、查找其他值以及将数据插入多个表。

以下示例演示如何使用存储过程在 Azure SQL 数据库数据库中的表内执行 upsert。 假设输入数据和接收器 **Marketing** 表各有三列：**ProfileID**、**State** 和 **Category**。 基于 ProfileID 列执行更新插入，并仅将其应用于名为“ProductA”的特定类别。

1. 在数据库中，使用与 **sqlWriterTableType** 相同的名称定义表类型。 表类型的架构与输入数据返回的架构相同。

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL,
        [Category] [varchar](256) NOT NULL
    )
    ```

2. 在数据库中，使用与 **sqlWriterStoredProcedureName** 相同的名称定义存储过程。 它可处理来自指定源的输入数据，并将其合并到输出表中。 存储过程中的表类型的参数名称与数据集中定义的 **tableName** 相同。

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

3. 在 Azure 数据工厂中，在复制活动中定义 **SQL sink** 节，如下所示：

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

**输入：** 选择将源指向某个表（等效于 ```Select * from <table-name>```），还是输入自定义 SQL 查询。

**查询**：如果在“输入”字段中选择“查询”，请为源输入 SQL 查询。 此设置会替代在数据集中选择的任何表。 此处不支持 Order By 子句，但你可以设置完整的 SELECT FROM 语句。 还可以使用用户定义的表函数。 select * from udfGetData() 是 SQL 中可返回表的 UDF。 此查询将生成可以在数据流中使用的源表。 使用查询也是减少进行测试或查找的行的好方法。

- SQL 示例：```Select * from MyTable where customerId > 1000 and customerId < 2000```

**批大小**：输入批大小，以将大型数据分成多个读取操作。

**隔离级别：** 映射数据流中 SQL 源的默认设置为“读取未提交的内容”。 你可以将此处的隔离级别更改为以下值之一：

- 读取已提交的内容
- 读取未提交的内容
- 可重复的读取
- 可序列化
- 无（忽略隔离级别）

![隔离级别](media/data-flow/isolationlevel.png "隔离级别")

### <a name="sink-transformation"></a>接收器转换

特定于 Azure SQL 数据库的设置可在接收器转换的 "**设置**" 选项卡中找到。

**更新方法：** 确定数据库目标上允许哪些操作。 默认设置为仅允许插入。 若要更新、更新插入或删除行，需要进行 alter-row 转换才能标记这些操作的行。 对于更新、更新插入和删除操作，必须设置一个或多个键列，以确定要更改的行。

![键列](media/data-flow/keycolumn.png "键列")

ADF 将使用在此处选取为密钥的列名作为后续更新 upsert （删除）的一部分。 因此，你必须选择存在于接收器映射中的列。 如果您不希望将该值写入此键列，请单击 "跳过写入键列"。

**表操作：** 确定在写入之前是否从目标表重新创建或删除所有行。

- 无：不会对表进行任何操作。
- 重新创建：将删除表并重新创建表。 如果以动态方式创建表，则是必需的。
- 截断：将删除目标表中的所有行。

**批大小**：控制每个 Bucket 中写入的行数。 较大的批大小可提高压缩比并改进内存优化，但在缓存数据时可能会导致内存不足异常。

**预处理和后处理 SQL 脚本**：输入将在数据写入接收器数据库之前（预处理）和之后（后处理）执行的多行 SQL 脚本

![预处理和后处理 SQL 脚本](media/data-flow/prepost1.png "SQL 处理脚本")

## <a name="data-type-mapping-for-azure-sql-database"></a>Azure SQL 数据库的数据类型映射

从/向 Azure SQL 数据库复制数据时，以下映射用于从 Azure SQL 数据库数据类型映射到 Azure 数据工厂临时数据类型。 若要了解复制活动如何将源架构和数据类型映射到接收器，请参阅[架构和数据类型映射](copy-activity-schema-and-type-mapping.md)。

| Azure SQL 数据库数据类型 | Azure 数据工厂临时数据类型 |
|:--- |:--- |
| bigint |Int64 |
| binary |Byte[] |
| bit |布尔 |
| char |String, Char[] |
| date |DateTime |
| Datetime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| 小数 |小数 |
| FILESTREAM attribute (varbinary(max)) |Byte[] |
| Float |Double |
| image |Byte[] |
| int |Int32 |
| money |小数 |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numeric |Decimal |
| nvarchar |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |小数 |
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
> 对于映射到十进制临时类型的数据类型，目前复制活动支持的最大精度为 28。 如果有精度大于 28 的数据，请考虑在 SQL 查询中将其转换为字符串。

## <a name="lookup-activity-properties"></a>Lookup 活动属性

若要了解有关属性的详细信息，请查看 [Lookup 活动](control-flow-lookup-activity.md)。

## <a name="getmetadata-activity-properties"></a>GetMetadata 活动属性

若要了解有关属性的详细信息，请查看 [GetMetadata 活动](control-flow-get-metadata-activity.md)

## <a name="using-always-encrypted"></a>使用 Always Encrypted

使用 [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-current) 从/向 Azure SQL 数据库复制数据时，请通过自承载 Integration Runtime 使用[通用 ODBC 连接器](connector-odbc.md)和 SQL Server ODBC 驱动程序。 此 Azure SQL 数据库连接器目前不支持 Always Encrypted。 

更具体地说：

1. 安装自承载 Integration Runtime（如果没有）。 有关详细信息，请参阅[自承载集成运行时](create-self-hosted-integration-runtime.md)一文。

2. 从[此处](https://docs.microsoft.com/sql/connect/odbc/download-odbc-driver-for-sql-server?view=azuresqldb-current)下载适用于 SQL Server 的 64 位 ODBC 驱动程序，并将其安装在 Integration Runtime 计算机上。 若要详细了解此驱动程序的工作原理，请参阅[在适用于 SQL Server 的 ODBC 驱动程序中使用 Always Encrypted](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-current#using-the-azure-key-vault-provider)。

3. 若要创建 ODBC 类型的链接服务以连接到 SQL 数据库，请参阅以下示例：

    - 若要使用“SQL 身份验证”，请执行以下操作：如下所示指定 ODBC 连接字符串，并选择“基本”身份验证以设置用户名和密码。

        ```
        Driver={ODBC Driver 17 for SQL Server};Server=<serverName>;Database=<databaseName>;ColumnEncryption=Enabled;KeyStoreAuthentication=KeyVaultClientSecret;KeyStorePrincipalId=<servicePrincipalKey>;KeyStoreSecret=<servicePrincipalKey>
        ```

    - 若要使用“数据工厂托管标识身份验证”，请执行以下操作： 

        1. 按照相同的[先决条件](#managed-identity)为托管标识创建数据库用户，并在数据库中授予适当的角色。
        2. 在链接服务中，如下所示指定 ODBC 连接字符串，并选择“匿名”身份验证，因为连接字符串本身指示 `Authentication=ActiveDirectoryMsi`。

        ```
        Driver={ODBC Driver 17 for SQL Server};Server=<serverName>;Database=<databaseName>;ColumnEncryption=Enabled;KeyStoreAuthentication=KeyVaultClientSecret;KeyStorePrincipalId=<servicePrincipalKey>;KeyStoreSecret=<servicePrincipalKey>; Authentication=ActiveDirectoryMsi;
        ```

4. 相应地使用 ODBC 类型创建数据集和复制活动。 若要了解详细信息，请参阅 [ODBC 连接器](connector-odbc.md)一文。

## <a name="next-steps"></a>后续步骤

有关 Azure 数据工厂中复制活动支持用作源和接收器的数据存储的列表，请参阅[支持的数据存储和格式](copy-activity-overview.md#supported-data-stores-and-formats)。
