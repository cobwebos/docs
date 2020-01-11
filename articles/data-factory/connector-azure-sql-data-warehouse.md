---
title: 在 Azure Synapse 分析中复制和转换数据
description: 了解如何使用数据工厂将数据复制到 Azure Synapse 分析，以及如何转换 Azure Synapse 分析中的数据。
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/12/2019
ms.openlocfilehash: f009b438cb0dc227289d65604d89c11fd382b675
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75892967"
---
# <a name="copy-and-transform-data-in-azure-synapse-analytics-formerly-azure-sql-data-warehouse-by-using-azure-data-factory"></a>使用 Azure 数据工厂复制和转换 Azure Synapse Analytics （以前称为 Azure SQL 数据仓库）中的数据 

> [!div class="op_single_selector" title1="选择要使用的数据工厂服务的版本："]
> * [Version1](v1/data-factory-azure-sql-data-warehouse-connector.md)
> * [当前版本](connector-azure-sql-data-warehouse.md)

本文概述如何使用 Azure 数据工厂中的复制活动将数据从和复制到 Azure Synapse Analytics，并使用数据流转换 Azure Data Lake Storage Gen2 中的数据。 若要了解 Azure 数据工厂，请阅读[介绍性文章](introduction.md)。

## <a name="supported-capabilities"></a>支持的功能

以下活动支持此 Azure Synapse Analytics 连接器：

- [复制活动](copy-activity-overview.md)与[支持的源/接收器矩阵](copy-activity-overview.md)表
- [映射数据流](concepts-data-flow-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)
- [GetMetadata 活动](control-flow-get-metadata-activity.md)

对于复制活动，此 Azure Synapse Analytics 连接器支持以下功能：

- 将 SQL 身份验证和 Azure Active Directory (Azure AD) 应用程序令牌身份验证与服务主体或 Azure 资源的托管标识配合使用来复制数据。
- 作为源，使用 SQL 查询或存储过程检索数据。
- 作为接收器，使用[PolyBase](#use-polybase-to-load-data-into-azure-sql-data-warehouse)或[COPY 语句](#use-copy-statement)（预览版）或大容量插入来加载数据。 对于更好的复制性能，建议 PolyBase 或 COPY 语句（预览）。

> [!IMPORTANT]
> 如果使用 Azure 数据工厂集成运行时复制数据，请将 [Azure SQL Server 防火墙](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure)配置为允许 Azure 服务访问服务器。
> 如果使用自承载集成运行时复制数据，请将 Azure SQL Server 防火墙配置为允许合适的 IP 范围。 此范围包括用于连接到 Azure Synapse Analytics 的计算机 IP。

## <a name="get-started"></a>开始体验

> [!TIP]
> 若要实现最佳性能，请使用 PolyBase 将数据加载到 Azure Synapse Analytics 中。 [使用 PolyBase 将数据加载到 Azure Synapse Analytics](#use-polybase-to-load-data-into-azure-sql-data-warehouse)部分提供详细信息。 有关用例的演练，请参阅[使用 Azure 数据工厂在15分钟内将 1 TB 加载到 Azure Synapse Analytics 中](load-azure-sql-data-warehouse.md)。

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

以下各节提供了有关定义特定于 Azure Synapse Analytics 连接器的数据工厂实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

Azure Synapse Analytics 链接服务支持以下属性：

| 属性            | Description                                                  | 需要                                                     |
| :------------------ | :----------------------------------------------------------- | :----------------------------------------------------------- |
| type                | type 属性必须设置为 **AzureSqlDW**。             | 是                                                          |
| connectionString    | 为**connectionString**属性指定连接到 Azure Synapse Analytics 实例所需的信息。 <br/>将此字段标记为 SecureString，以便安全地将其存储在数据工厂中。 还可以将密码/服务主体密钥放在 Azure 密钥保管库中，如果是 SQL 身份验证，则从连接字符串中拉取 `password` 配置。 有关更多详细信息，请参阅表下方的 JSON 示例和[将凭据存储在 Azure 密钥保管库中](store-credentials-in-key-vault.md)一文。 | 是                                                          |
| servicePrincipalId  | 指定应用程序的客户端 ID。                         | 是，将 Azure AD 身份验证与服务主体配合使用时是必需的。 |
| servicePrincipalKey | 指定应用程序的密钥。 将此字段标记为 SecureString 以安全地将其存储在数据工厂中或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 | 是，将 Azure AD 身份验证与服务主体配合使用时是必需的。 |
| tenant              | 指定应用程序的租户信息（域名或租户 ID）。 可将鼠标悬停在 Azure 门户右上角进行检索。 | 是，将 Azure AD 身份验证与服务主体配合使用时是必需的。 |
| connectVia          | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 可使用 Azure 集成运行时或自承载集成运行时（如果数据存储位于专用网络）。 如果未指定，则使用默认 Azure Integration Runtime。 | 否                                                           |

有关各种身份验证类型，请参阅关于先决条件和 JSON 示例的以下各部分：

- [SQL 身份验证](#sql-authentication)
- Azure AD 应用程序令牌身份验证：[服务主体](#service-principal-authentication)
- Azure AD 应用程序令牌身份验证：[Azure 资源的托管标识](#managed-identity)

>[!TIP]
>如果遇到错误（错误代码为“UserErrorFailedToConnectToSqlServer”，且消息如“数据库的会话限制为 XXX 且已达到。”），请将 `Pooling=false` 添加到连接字符串中，然后重试。

### <a name="sql-authentication"></a>SQL 身份验证

#### <a name="linked-service-example-that-uses-sql-authentication"></a>使用 SQL 身份验证的链接服务示例

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
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

**Azure 密钥保管库中的密码：**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
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

若要使用基于服务主体的 Azure AD 应用程序令牌身份验证，请执行以下步骤：

1. 从 Azure 门户 **[创建 Azure Active Directory 应用程序](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)** 。 记下应用程序名称，以及以下定义链接服务的值：

    - 应用程序 ID
    - 应用程序密钥
    - 租户 ID

2. 在 Azure 门户上为 Azure SQL 服务器[预配 Azure Active Directory 管理员](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)（如果尚未执行该操作）。 Azure AD 管理员可以是 Azure AD 用户或 Azure AD 组。 如果为具有托管标识的组授予管理员角色，请跳过步骤 3 和 4。 管理员将拥有对数据库的完全访问权限。

3. 为服务主体 **[创建包含的数据库用户](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)** 。 使用 SSMS 等工具连接到要从中复制数据或要将数据复制到其中的数据仓库，其 Azure AD 标识至少具有 ALTER ANY USER 权限。 运行以下 T-SQL：
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. 像通常对 SQL 用户或其他用户所做的那样**向服务主体授予所需的权限**。 运行以下代码，或[在此处](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017)参考更多选项。 如果要使用 PolyBase 加载数据，请了解[所需的数据库权限](#required-database-permission)。

    ```sql
    EXEC sp_addrolemember db_owner, [your application name];
    ```

5. 在 Azure 数据工厂中**配置 Azure Synapse Analytics 链接服务**。


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>使用服务主体身份验证的链接服务示例

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
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

可将数据工厂与代表此特定工厂的 [Azure 资源托管标识](data-factory-service-identity.md)相关联。 可以将此托管标识用于 Azure Synapse Analytics 身份验证。 指定工厂可使用此标识访问数据仓库数据并从或向其中复制数据。

若要使用托管标识身份验证，请执行以下步骤：

1. 在 Azure 门户上为 Azure SQL 服务器[预配 Azure Active Directory 管理员](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)（如果尚未执行该操作）。 Azure AD 管理员可以是 Azure AD 用户或 Azure AD 组。 如果为具有托管标识的组授予管理员角色，请跳过步骤 3 和 4。 管理员将拥有对数据库的完全访问权限。

2. 为数据工厂托管标识 **[创建包含的数据库用户](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)** 。 使用 SSMS 等工具连接到要从中复制数据或要将数据复制到其中的数据仓库，其 Azure AD 标识至少具有 ALTER ANY USER 权限。 运行以下 T-SQL。 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. **向数据工厂托管标识授予**对 SQL 用户和其他用户通常所需的权限。 运行以下代码，或[在此处](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017)参考更多选项。 如果要使用 PolyBase 加载数据，请了解[所需的数据库权限](#required-database-permission)。

    ```sql
    EXEC sp_addrolemember db_owner, [your Data Factory name];
    ```

5. 在 Azure 数据工厂中**配置 Azure Synapse Analytics 链接服务**。

**示例：**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
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

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 

Azure Synapse Analytics 数据集支持以下属性：

| 属性  | Description                                                  | 需要                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| type      | 数据集的 **type** 属性必须设置为 **AzureSqlDWTable**。 | 是                         |
| 架构 | 架构的名称。 |对于源为“No”，对于接收器为“Yes”  |
| 表 | 表/视图的名称。 |对于源为“No”，对于接收器为“Yes”  |
| tableName | 具有架构的表/视图的名称。 支持此属性是为了向后兼容。 对于新工作负荷，请使用 `schema` 和 `table`。 | 对于源为“No”，对于接收器为“Yes” |

#### <a name="dataset-properties-example"></a>数据集属性示例

```json
{
    "name": "AzureSQLDWDataset",
    "properties":
    {
        "type": "AzureSqlDWTable",
        "linkedServiceName": {
            "referenceName": "<Azure Synapse Analytics linked service name>",
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

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 Azure Synapse Analytics 源和接收器支持的属性列表。

### <a name="azure-synapse-analytics-as-the-source"></a>作为源的 Azure Synapse 分析

若要从 Azure Synapse Analytics 复制数据，请将复制活动源中的**type**属性设置为 " **SqlDWSource**"。 复制活动 **source** 节支持以下属性：

| 属性                     | Description                                                  | 需要 |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| type                         | 复制活动源的 **type** 属性必须设置为 **SqlDWSource**。 | 是      |
| sqlReaderQuery               | 使用自定义 SQL 查询读取数据。 示例：`select * from MyTable`。 | 否       |
| sqlReaderStoredProcedureName | 从源表读取数据的存储过程的名称。 最后一个 SQL 语句必须是存储过程中的 SELECT 语句。 | 否       |
| storedProcedureParameters    | 存储过程的参数。<br/>允许的值为名称或值对。 参数的名称和大小写必须与存储过程参数的名称和大小写匹配。 | 否       |

**示例：使用 SQL 查询**

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
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
                "type": "SqlDWSource",
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
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
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
                "type": "SqlDWSource",
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

**示例存储过程：**

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

### <a name="azure-sql-data-warehouse-as-sink"></a>作为接收器的 Azure Synapse 分析

Azure 数据工厂支持三种方法将数据加载到 SQL 数据仓库。

![SQL DW 接收器复制选项](./media/connector-azure-sql-data-warehouse/sql-dw-sink-copy-options.png)

- [使用 PolyBase](#use-polybase-to-load-data-into-azure-sql-data-warehouse) 
- [使用 COPY 语句（预览）](#use-copy-statement)
- 使用大容量插入

通过[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)或[COPY 语句](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest)（预览），可以最快、最灵活的方式加载数据。

要向 Azure SQL 数据仓库复制数据，请将复制活动中的接收器类型设置为 **SqlDWSink**。 复制活动 **sink** 节支持以下属性：

| 属性          | Description                                                  | 需要                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| type              | 复制活动接收器的 **type** 属性必须设置为 **SqlDWSink**。 | 是                                           |
| allowPolyBase     | 指示是否使用 PolyBase 将数据加载到 SQL 数据仓库。 `allowCopyCommand` 和 `allowPolyBase` 不能同时为 true。 <br/><br/>有关约束和详细信息，请参阅[使用 PolyBase 将数据加载到 Azure SQL 数据仓库](#use-polybase-to-load-data-into-azure-sql-data-warehouse)部分。<br/><br/>允许的值为 **True** 和 **False**（默认值）。 | 不。<br/>使用 PolyBase 时应用。     |
| polyBaseSettings  | 一组可在 `allowPolybase` 属性设置为**true**时指定的属性。 | 不。<br/>使用 PolyBase 时应用。 |
| allowCopyCommand | 指示是否使用[COPY 语句](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest)（预览版）将数据加载到 SQL 数据仓库。 `allowCopyCommand` 和 `allowPolyBase` 不能同时为 true。 <br/><br/>有关约束和详细信息，请参阅[USE COPY 语句将数据加载到 AZURE SQL 数据仓库](#use-copy-statement)部分。<br/><br/>允许的值为 **True** 和 **False**（默认值）。 | 不。<br>使用复制时应用。 |
| copyCommandSettings | `allowCopyCommand` 属性设置为 TRUE 时可指定的一组属性。 | 不。<br/>使用复制时应用。 |
| writeBatchSize    | **每批**在 SQL 表中插入的行数。<br/><br/>允许的值为 **integer**（行数）。 默认情况下，数据工厂根据行大小动态确定适当的批大小。 | 不。<br/>使用 bulk insert 时应用。     |
| writeBatchTimeout | 超时前等待批量插入操作完成的时间。<br/><br/>允许的值为 **timespan**。 示例：“00:30:00”（30 分钟）。 | 不。<br/>使用 bulk insert 时应用。        |
| preCopyScript     | 每次运行时，将数据写入到 Azure SQL 数据仓库之前，指定复制活动要运行的 SQL 查询。 使用此属性清理预加载的数据。 | 否                                            |
| tableOption | 指定是否根据源架构自动创建接收器表（如果不存在）。 如果在复制活动中配置了暂存复制，则不支持创建自动表。 允许的值为： `none` （默认值），`autoCreate`。 |否 |
| disableMetricsCollection | 数据工厂收集用于复制性能优化和建议的指标，例如 SQL 数据仓库 Dwu。 如果你担心此行为，请指定 `true` 将其关闭。 | 否（默认值为 `false`） |

#### <a name="sql-data-warehouse-sink-example"></a>SQL 数据仓库接收器示例

```json
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

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>使用 PolyBase 将数据加载到 Azure SQL 数据仓库

使用[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)是将大量数据加载到高吞吐量的 Azure Synapse 分析的有效方法。 使用 PolyBase 而非默认 BULKINSERT 机制可以实现吞吐量的巨大增加。 有关用例的演练，请参阅将[1 TB 负载到 Azure Synapse Analytics 中](v1/data-factory-load-sql-data-warehouse.md)。

* 如果源数据位于**Azure Blob 中、Azure Data Lake Storage Gen1 或 Azure Data Lake Storage Gen2**并且**格式与 PolyBase 兼容**，则可以使用复制活动直接调用 POLYBASE，使 Azure SQL 数据仓库从源中提取数据。 有关详细信息，请参阅 **[使用 PolyBase 直接复制](#direct-copy-by-using-polybase)** 。
* 如果 PolyBase 最初不支持源数据存储和格式，请改用 **[使用 PolyBase 的暂存复制](#staged-copy-by-using-polybase)** 功能。 暂存复制功能也能提供更高的吞吐量。 它自动将数据转换为 PolyBase 兼容的格式，并将数据存储在 Azure Blob 存储中，然后调用 PolyBase 将数据加载到 SQL 数据仓库。

>[!TIP]
>了解[有关使用 PolyBase 的最佳实践的](#best-practices-for-using-polybase)详细信息。

在复制活动的 `polyBaseSettings` 下支持以下 PolyBase 设置：

| 属性          | Description                                                  | 需要                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| rejectValue       | 指定在查询失败之前可以拒绝的行数或百分比。<br/><br/>有关 PolyBase 的拒绝选项的详细信息，请参阅 [CREATE EXTERNAL TABLE (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx) 的“参数”部分。 <br/><br/>允许的值为 0（默认值）、1、2 等。 | 否                                            |
| rejectType        | 指定 **rejectValue** 选项是文本值还是百分比。<br/><br/>允许的值为 **Value**（默认值）和 **Percentage**。 | 否                                            |
| rejectSampleValue | 确定在 PolyBase 重新计算被拒绝行的百分比之前要检索的行数。<br/><br/>允许的值为 1、2 等。 | 如果 **rejectType** 是 **percentage**，则为“是” |
| useTypeDefault    | 指定在 PolyBase 从文本文件中检索数据时如何处理带分隔符的文本文件中的缺失值。<br/><br/>有关此属性的详细信息，请参阅[创建外部文件格式 (Transact SQL)](https://msdn.microsoft.com/library/dn935026.aspx) 中的参数部分。<br/><br/>允许的值为 **True** 和 **False**（默认值）。<br><br> | 否                                            |

### <a name="direct-copy-by-using-polybase"></a>使用 PolyBase 直接复制

SQL 数据仓库 PolyBase 直接支持 Azure Blob、Azure Data Lake Storage Gen1 和 Azure Data Lake Storage Gen2。 如果源数据满足此部分中所述的条件，则使用 PolyBase 直接从源数据存储复制到 Azure Synapse Analytics。 否则，请改用[使用 PolyBase 的暂存复制](#staged-copy-by-using-polybase)。

> [!TIP]
> 若要有效地将数据复制到 SQL 数据仓库，请从[Azure 数据工厂了解更多，在将 Data Lake Store 与 SQL 数据仓库结合使用时，可以更轻松、更方便地从数据中发现见解](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/)。

如果不满足要求，Azure 数据工厂会检查设置，并自动回退到 BULKINSERT 机制以进行数据移动。

1. **源链接服务**具有以下类型和身份验证方法：

    | 支持的源数据存储类型                             | 支持的源身份验证类型                        |
    | :----------------------------------------------------------- | :---------------------------------------------------------- |
    | [Azure Blob](connector-azure-blob-storage.md)                | 帐户密钥身份验证，托管标识身份验证 |
    | [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | 服务主体身份验证                            |
    | [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | 帐户密钥身份验证，托管标识身份验证 |

    >[!IMPORTANT]
    >如果 Azure 存储配置了 VNet 服务终结点，则必须使用托管标识身份验证-请参阅将[VNet 服务终结点与 Azure 存储配合使用的影响](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage)。 分别从[Azure Blob-托管标识身份验证](connector-azure-blob-storage.md#managed-identity)和[Azure Data Lake Storage Gen2 托管标识身份验证](connector-azure-data-lake-storage.md#managed-identity)部分了解数据工厂中所需的配置。

2. **源数据格式**为**Parquet**、 **ORC**或**带分隔符的文本**，具有以下配置：

   1. 文件夹路径不包含通配符筛选器。
   2. 文件名为空，或指向单个文件。 如果在复制活动中指定通配符文件名，则只能 `*` 或 `*.*`。
   3. `rowDelimiter` 为**default**、 **\n**、 **\r\n**或 **\r**。
   4. `nullValue` 保留为默认值或设置为**空字符串**（""），并且 `treatEmptyAsNull` 保留为默认值或设置为 true。
   5. `encodingName` 保留为默认值或设置为**utf-8**。
   6. 未指定 `quoteChar`、`escapeChar`和 `skipLineCount`。 PolyBase 支持跳过标题行，可在 ADF 中将其配置为 `firstRowAsHeader`。
   7. `compression` 可为**无压缩**、**GZip** 或 **Deflate**。

3. 如果源是文件夹，则必须将复制活动中的 `recursive` 设置为 true。

4. 未指定 `wildcardFolderPath`、`wildcardFilename`、`modifiedDateTimeStart`和 `modifiedDateTimeEnd`。

>[!NOTE]
>如果源是文件夹，请注意，PolyBase 将从该文件夹及其所有子文件夹中检索文件，并且不会从文件中以下划线（_）或句点（.）开头的文件中检索数据，如[此处](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest#arguments-2)所述。

```json
"activities":[
    {
        "name": "CopyFromAzureBlobToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "ParquetDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "ParquetSource",
                "storeSettings":{
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            }
        }
    }
]
```

### <a name="staged-copy-by-using-polybase"></a>使用 PolyBase 的暂存复制

如果源数据与 PolyBase 不是本机兼容的，请启用通过临时暂存 Azure Blob 存储实例（不能是 Azure 高级存储）进行的数据复制。 在这种情况下，Azure 数据工厂会自动转换数据以满足 PolyBase 的数据格式要求。 然后，它会调用 PolyBase 将数据加载到 SQL 数据仓库。 最后，它会从 Blob 存储中清理临时数据。 有关通过暂存 Azure Blob 存储实例复制数据的详细信息，请参阅[暂存复制](copy-activity-performance.md#staged-copy)。

若要使用此功能，请创建一个[Azure Blob 存储链接服务](connector-azure-blob-storage.md#linked-service-properties)，该服务引用具有临时 Blob 存储的 azure 存储帐户。 然后，为复制活动指定 `enableStaging` 和 `stagingSettings` 属性，如以下代码所示。

>[!IMPORTANT]
>如果临时 Azure 存储配置了 VNet 服务终结点，则必须使用托管标识身份验证-请参阅将[VNet 服务终结点与 Azure 存储配合使用的影响](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage)。 了解[Azure Blob-托管标识身份验证](connector-azure-blob-storage.md#managed-identity)中数据工厂所需的配置。

```json
"activities":[
    {
        "name": "CopyFromSQLServerToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "SQLServerDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
]
```

### <a name="best-practices-for-using-polybase"></a>有关使用 PolyBase 的最佳做法

以下各部分提供[了有关 Azure Synapse 分析的最佳实践](../sql-data-warehouse/sql-data-warehouse-best-practices.md)中所述的最佳实践。

#### <a name="required-database-permission"></a>所需数据库权限

若要使用 PolyBase，将数据加载到 SQL 数据仓库的用户必须对目标数据库拥有[“CONTROL”权限](https://msdn.microsoft.com/library/ms191291.aspx)。 一种实现方法是将该用户添加为 **db_owner** 角色的成员。 在 [SQL 数据仓库概述](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization)中了解如何执行该操作。

#### <a name="row-size-and-data-type-limits"></a>行大小和数据类型限制

PolyBase 负载限制为小于 1 MB 的行。 它不能用于加载到 VARCHR （MAX）、NVARCHAR （MAX）或 VARBINARY （MAX）。 有关详细信息，请参阅 [SQL 数据仓库服务容量限制](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads)。

如果数据源中的行大于 1 MB，可能需要将源表垂直拆分为多个小型表。 确保每行的最大大小不超过该限制。 然后，可以使用 PolyBase 加载较小的表，并将它们合并到 Azure Synapse Analytics 中。

或者，对于具有此类大列的数据，可以使用非 PolyBase 来使用 ADF 加载数据，方法是关闭 "允许 PolyBase" 设置。

#### <a name="sql-data-warehouse-resource-class"></a>SQL 数据仓库资源类

若要实现最佳吞吐量，请将更大的资源类分配给通过 PolyBase 在 SQL 数据仓库中加载数据的用户。

#### <a name="polybase-troubleshooting"></a>PolyBase 故障排除

**正在加载到十进制列**

如果源数据是文本格式或其他非 PolyBase 兼容存储（使用暂存复制和 PolyBase），并且它包含要加载到 SQL 数据仓库 Decimal 列中的空值，则可能会遇到以下错误：

```
ErrorCode=FailedDbOperation, ......HadoopSqlException: Error converting data type VARCHAR to DECIMAL.....Detailed Message=Empty string can't be converted to DECIMAL.....
```

解决方案是在复制活动接收器-> PolyBase 设置中取消选择 "**使用类型默认值**" 选项（为 false）。 "[USE_TYPE_DEFAULT](https://docs.microsoft.com/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest#arguments
)" 是一个 polybase 本机配置，它指定当 PolyBase 从文本文件中检索数据时如何处理分隔文本文件中的缺失值。 

**Azure Synapse 分析中的 `tableName`**

下表举例说明如何在 JSON 数据集中指定 **tableName** 属性。 其中显示了架构和表名称的多个组合。

| DB 架构 | 表名称 | **tableName** JSON 属性               |
| --------- | ---------- | ----------------------------------------- |
| dbo       | MyTable    | MyTable 或 dbo.MyTable 或 [dbo].[MyTable] |
| dbo1      | MyTable    | dbo1.MyTable 或 [dbo1].[MyTable]          |
| dbo       | My.Table   | [My.Table] 或 [dbo].[My.Table]            |
| dbo1      | My.Table   | [dbo1].[My.Table]                         |

如果看到以下错误，问题可能与为 **tableName** 属性指定的值有关。 有关为 **tableName** JSON 属性指定值的正确方法，请参阅上表。

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

**具有默认值的列**

目前，数据工厂中的 PolyBase 功能只接受与目标表中相同数量的列。 例如，某个表包含四列，其中一列定义了默认值。 输入数据仍需包含四列。 包含三列的输入数据集生成类似于以下消息的错误：

```
All columns of the table must be specified in the INSERT BULK statement.
```

NULL 值是特殊形式的默认值。 如果列可为 null，则该列的 Blob 中的输入数据可能为空。 但输入数据集中不能缺少该数据。 PolyBase 在 Azure Synapse 分析中为缺失值插入 NULL。

## <a name="use-copy-statement"></a>使用 COPY 语句将数据加载到 Azure SQL 数据仓库（预览版）

SQL 数据仓库[COPY 语句](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest)（预览版）直接支持从**Azure Blob 和 Azure Data Lake Storage Gen2**加载数据。 如果源数据满足此部分中所述的条件，则可以选择使用 ADF 中的 COPY 语句将数据加载到 Azure SQL 数据仓库。 Azure 数据工厂检查设置，如果不满足条件，则复制活动运行失败。

>[!NOTE]
>目前，数据工厂仅支持从以下提到的复制语句兼容的源复制。

使用 COPY 语句支持以下配置：

1. **源链接服务和格式**具有以下类型和身份验证方法：

    | 支持的源数据存储类型                             | 支持的格式           | 支持的源身份验证类型                         |
    | :----------------------------------------------------------- | -------------------------- | :----------------------------------------------------------- |
    | [Azure Blob](connector-azure-blob-storage.md)                | [分隔文本](format-delimited-text.md)             | 帐户密钥身份验证，共享访问签名身份验证，服务主体身份验证，托管标识身份验证 |
    | &nbsp;                                                       | [Parquet](format-parquet.md)                    | 帐户密钥身份验证，共享访问签名身份验证 |
    | &nbsp;                                                       | [ORC](format-orc.md)                        | 帐户密钥身份验证，共享访问签名身份验证 |
    | [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | [分隔文本](format-delimited-text.md)<br/>[Parquet](format-parquet.md)<br/>[ORC](format-orc.md) | 帐户密钥身份验证，服务主体身份验证，托管标识身份验证 |

    >[!IMPORTANT]
    >如果 Azure 存储配置了 VNet 服务终结点，则必须使用托管标识身份验证-请参阅将[VNet 服务终结点与 Azure 存储配合使用的影响](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage)。 分别从[Azure Blob-托管标识身份验证](connector-azure-blob-storage.md#managed-identity)和[Azure Data Lake Storage Gen2 托管标识身份验证](connector-azure-data-lake-storage.md#managed-identity)部分了解数据工厂中所需的配置。

2. 格式设置如下所示：

   1. 对于**Parquet**： `compression` 不能为**compression**、 **Snappy**或**GZip**。
   2. 对于**ORC**： `compression` 不能为**compression**、 **zlib**或**Snappy**。
   3. 对于**带分隔符的文本**：
      1. `rowDelimiter` 显式设置为**单个字符**或 " **\r\n**"，则不支持默认值。
      2. `nullValue` 保留为默认值或设置为**空字符串**（""）。
      3. `encodingName` 保留为默认值或设置为**utf-8 或 utf-16**。
      4. `escapeChar` 必须与 `quoteChar`相同，并且不为空。
      5. `skipLineCount` 保留为默认值或设置为0。
      6. `compression` 不能为**压缩**或**GZip**。

3. 如果源是文件夹，则必须将复制活动中的 `recursive` 设置为 true。

4. 未指定 `wildcardFolderPath`、`wildcardFilename`、`modifiedDateTimeStart`和 `modifiedDateTimeEnd`。

复制活动中 `allowCopyCommand` 支持以下 COPY 语句设置：

| 属性          | Description                                                  | 需要                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| 默认 | 为 SQL DW 中的每个目标列指定默认值。  属性中的默认值将覆盖数据仓库中设置的默认约束，并且标识列不能有默认值。 | 否 |
| 其他 | 将直接在[COPY 语句](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest)的 "With" 子句中传递到 SQL DW COPY 语句的其他选项。 根据需要将值括起来，以符合 COPY 语句要求。 | 否 |

```json
"activities":[
    {
        "name": "CopyFromAzureBlobToSQLDataWarehouseViaCOPY",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "ParquetDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "ParquetSource",
                "storeSettings":{
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "SqlDWSink",
                "allowCopyCommand": true, 
                "copyCommandSettings": {
                    "defaultValues": [ 
                        { 
                            "columnName": "col_string", 
                            "defaultValue": "DefaultStringValue" 
                        }
                    ],
                    "additionalOptions": { 
                        "MAXERRORS": "10000", 
                        "DATEFORMAT": "'ymd'" 
                    }
                }
            },
            "enableSkipIncompatibleRow": true
        }
    }
]
```


## <a name="lookup-activity-properties"></a>查找活动属性

若要了解有关属性的详细信息，请检查[查找活动](control-flow-lookup-activity.md)。

## <a name="getmetadata-activity-properties"></a>GetMetadata 活动属性

若要了解有关属性的详细信息，请查看[GetMetadata 活动](control-flow-get-metadata-activity.md) 

## <a name="data-type-mapping-for-azure-sql-data-warehouse"></a>Azure SQL 数据仓库的数据类型映射

## <a name="mapping-data-flow-properties"></a>映射数据流属性

在映射数据流中转换数据时，可以从 Azure Synapse Analytics 读取和写入表。 有关详细信息，请参阅映射数据流中的[源转换](data-flow-source.md)和[接收器转换](data-flow-sink.md)。

### <a name="source-transformation"></a>源转换

源转换的 "**源选项**" 选项卡中提供了特定于 Azure Synapse Analytics 的设置。 

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

特定于 Azure Synapse Analytics 的设置可在接收器转换的 "**设置**" 选项卡中找到。

**更新方法：** 确定对数据库目标允许哪些操作。 默认值为仅允许插入。 若要更新、upsert 或删除行，需要更改行转换才能标记这些操作的行。 对于更新、upsert 和删除，必须设置一个或多个键列，以确定要更改的行。

**表操作：** 确定在写入之前是否在目标表中重新创建或删除所有行。
* None：不会对表执行任何操作。
* 重新创建：该表将被删除并重新创建。 如果动态创建新表，则为必需。
* 截断：目标表中的所有行都将被删除。

**启用暂存：** 确定在写入 Azure Synapse 分析时是否使用[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide?view=sql-server-ver15)

**批大小**：控制正在每个存储桶中写入的行数。 较大的批处理大小会提高压缩和内存优化，但在缓存数据时可能会导致内存不足异常。

**Pre 和 POST sql 脚本**：输入将在之前（预处理）和之后（后处理）数据写入接收器数据库时执行的多行 sql 脚本

![pre 和 post SQL 处理脚本](media/data-flow/prepost1.png "SQL 处理脚本")

## <a name="data-type-mapping-for-azure-synapse-analytics"></a>Azure Synapse Analytics 的数据类型映射

在将数据从或复制到 Azure Synapse Analytics 时，以下映射用于从 Azure Synapse Analytics 数据类型映射到 Azure 数据工厂临时数据类型。 若要了解复制活动如何将源架构和数据类型映射到接收器，请参阅[架构和数据类型映射](copy-activity-schema-and-type-mapping.md)。

>[!TIP]
>有关支持的数据类型和不支持的数据类型的解决方法，请参阅[Azure Synapse Analytics 中的表数据类型](../sql-data-warehouse/sql-data-warehouse-tables-data-types.md)。

| Azure Synapse Analytics 数据类型    | 数据工厂临时数据类型 |
| :------------------------------------ | :----------------------------- |
| bigint                                | Int64                          |
| binary                                | Byte[]                         |
| bit                                   | Boolean                        |
| char                                  | String, Char[]                 |
| date                                  | 日期/时间                       |
| Datetime                              | 日期/时间                       |
| datetime2                             | 日期/时间                       |
| Datetimeoffset                        | DateTimeOffset                 |
| Decimal                               | Decimal                        |
| FILESTREAM attribute (varbinary(max)) | Byte[]                         |
| Float                                 | Double                         |
| image                                 | Byte[]                         |
| int                                   | Int32                          |
| money                                 | Decimal                        |
| nchar                                 | String, Char[]                 |
| numeric                               | Decimal                        |
| nvarchar                              | String, Char[]                 |
| real                                  | 单一                         |
| rowversion                            | Byte[]                         |
| smalldatetime                         | 日期/时间                       |
| smallint                              | Int16                          |
| smallmoney                            | Decimal                        |
| time                                  | TimeSpan                       |
| tinyint                               | Byte                           |
| uniqueidentifier                      | GUID                           |
| varbinary                             | Byte[]                         |
| varchar                               | String, Char[]                 |

## <a name="next-steps"></a>后续步骤
有关 Azure 数据工厂中复制活动支持用作源和接收器的数据存储的列表，请参阅[支持的数据存储和格式](copy-activity-overview.md#supported-data-stores-and-formats)。
