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
ms.date: 08/06/2019
ms.author: jingwang
ms.openlocfilehash: 1baa28dd1c9cc323e3dc7ca6fc5fbe2eac54652a
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68829146"
---
# <a name="copy-data-to-and-from-azure-sql-database-managed-instance-by-using-azure-data-factory"></a>使用 Azure 数据工厂向/从 Azure SQL 数据库托管实例复制数据

本文概述了如何使用 Azure 数据工厂中的复制活动从/向 Azure SQL 数据库托管实例复制数据。 本文是根据总体概述复制活动的[复制活动概述](copy-activity-overview.md)一文编写的。

## <a name="supported-capabilities"></a>支持的功能

可将数据从 Azure SQL 数据库托管实例复制到任何支持的接收器数据存储。 还可以将数据从任何支持的源数据存储复制到托管实例。 有关复制活动支持作为源和接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

具体而言，此 Azure SQL 数据库托管实例连接器支持：

- 使用 SQL 身份验证和 Azure Active Directory (Azure AD) 应用程序令牌身份验证通过 Azure 资源的服务主体或托管标识复制数据。
- 作为源, 使用 SQL 查询或存储过程检索数据。
- 作为接收器，在复制期间将数据追加到目标表，或调用带有自定义逻辑的存储过程。

>[!NOTE]
>此连接器目前不支持 Azure SQL 数据库托管实例[Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-mi-current) 。 若要解决此情况, 可以通过自承载集成运行时使用[泛型 odbc 连接器](connector-odbc.md)和 SQL Server ODBC 驱动程序。 按照[此指南](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-mi-current)完成 ODBC 驱动程序下载和连接字符串配置。

>[!NOTE]
>此连接器目前不支持服务主体和托管标识身份验证。 若要解决此操作, 请选择 Azure SQL 数据库连接器, 并手动指定托管实例的服务器。

## <a name="prerequisites"></a>先决条件

若要访问 Azure SQL 数据库托管实例[公共终结点](../sql-database/sql-database-managed-instance-public-endpoint-securely.md), 可以使用 Azure 数据工厂托管的 azure 集成运行时。 确保启用公共终结点, 并且还允许网络安全组上的公共终结点流量, 使 Azure 数据工厂能够连接到数据库。 有关详细信息, 请参阅[此指南](../sql-database/sql-database-managed-instance-public-endpoint-configure.md)。

若要访问 Azure SQL 数据库托管实例专用终结点, 请设置可访问数据库的[自承载集成运行时](create-self-hosted-integration-runtime.md)。 如果预配托管实例所在的虚拟网络中的自承载集成运行时，请确保集成运行时计算机与托管实例位于不同的子网中。 如果在与托管实例不同的虚拟网络中预配了自承载集成运行时, 则可以使用虚拟网络对等互连或虚拟网络到虚拟网络连接。 有关详细信息，请参阅[将应用程序连接到 Azure SQL 数据库托管实例](../sql-database/sql-database-managed-instance-connect-app.md)。

## <a name="get-started"></a>开始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

对于特定于 Azure SQL 数据库托管实例连接器的 Azure 数据工厂实体, 以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

Azure SQL 数据库托管实例链接的服务支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | Type 属性必须设置为**AzureSqlMI**。 | 是 |
| connectionString |此属性指定使用 SQL 身份验证连接到托管实例所需的**connectionString**信息。 有关详细信息，请参阅以下示例。 <br/>默认端口为1433。 如果将 Azure SQL 数据库托管实例与公共终结点一起使用, 请显式指定端口3342。<br>将此字段标记为**SecureString**以将其安全地存储在 Azure 数据工厂中。 还可以在 Azure Key Vault 中输入密码。 如果它是 SQL 身份验证, 请`password`将配置从连接字符串中提取出来。 有关详细信息, 请参阅表后面的 JSON 示例, 并[在 Azure Key Vault 中存储凭据](store-credentials-in-key-vault.md)。 |是 |
| servicePrincipalId | 指定应用程序的客户端 ID。 | 是, 当你将 Azure AD 身份验证用于服务主体时 |
| servicePrincipalKey | 指定应用程序的密钥。 将此字段标记为**SecureString**以将其安全地存储在 Azure 数据工厂中或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 | 是, 当你将 Azure AD 身份验证用于服务主体时 |
| 租户 | 指定应用程序所驻留的租户信息, 例如域名或租户 ID。 将鼠标悬停在 Azure 门户右上角进行检索。 | 是, 当你将 Azure AD 身份验证用于服务主体时 |
| connectVia | 此[集成运行时](concepts-integration-runtime.md)用于连接到数据存储。 如果托管实例有一个公共终结点, 并允许 Azure 数据工厂访问它, 则可以使用自承载集成运行时或 Azure 集成运行时。 如果未指定，则使用默认 Azure Integration Runtime。 |是 |

有关各种身份验证类型，请参阅关于先决条件和 JSON 示例的以下各部分：

- [SQL 身份验证](#sql-authentication)
- [Azure AD 应用程序令牌身份验证：服务主体](#service-principal-authentication)
- [Azure AD 应用程序令牌身份验证：Azure 资源的托管标识](#managed-identity)

### <a name="sql-authentication"></a>SQL 身份验证

**示例 1: 使用 SQL 身份验证**

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "AzureSqlMI",
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

**示例 2: 在 Azure Key Vault 中使用带密码的 SQL 身份验证**

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "AzureSqlMI",
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

### <a name="service-principal-authentication"></a>服务主体身份验证

要使用基于服务主体的 Azure AD 应用程序令牌身份验证，请执行以下步骤：

1. 从 Azure 门户[创建 Azure Active Directory 应用程序](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)。 记下应用程序名称，以及以下定义链接服务的值：

    - 应用程序 ID
    - 应用程序密钥
    - 租户 ID

2. 为 Azure 数据工厂托管标识[创建登录名](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current)。 在 SQL Server Management Studio (SSMS) 中, 使用作为**sysadmin**的 SQL Server 帐户连接到托管实例。 在**master**数据库中, 运行以下 t-sql:

    ```sql
    CREATE LOGIN [your application name] FROM EXTERNAL PROVIDER
    ```

2. 为 Azure 数据工厂托管标识[创建包含的数据库用户](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)。 从或连接到要复制数据的数据库, 运行以下 T-sql: 
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER
    ```

3. 向数据工厂托管标识授予对 SQL 用户和其他用户通常所需的权限。 运行下面的代码。 有关更多选项, 请参阅[此文档](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql?view=azuresqldb-mi-current)。

    ```sql
    ALTER ROLE [role name e.g. db_owner] ADD MEMBER [your application name]
    ```

4. 在 Azure 数据工厂中配置 Azure SQL 数据库托管实例链接的服务。

**示例: 使用服务主体身份验证**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<hostname,port>;Initial Catalog=<databasename>;"
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

可将数据工厂与代表此特定数据工厂的 [Azure 资源托管标识](data-factory-service-identity.md)相关联。 你可以使用此托管标识进行 Azure SQL 数据库托管实例身份验证。 指定工厂可使用此标识访问数据库数据并向其/从中复制数据。

若要使用托管标识身份验证, 请执行以下步骤。

1. 为 Azure 数据工厂托管标识[创建登录名](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current)。 在 SQL Server Management Studio (SSMS) 中, 使用作为**sysadmin**的 SQL Server 帐户连接到托管实例。 在**master**数据库中, 运行以下 t-sql:

    ```sql
    CREATE LOGIN [your Data Factory name] FROM EXTERNAL PROVIDER
    ```

2. 为 Azure 数据工厂托管标识[创建包含的数据库用户](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)。 从或连接到要复制数据的数据库, 运行以下 T-sql: 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER
    ```

3. 向数据工厂托管标识授予对 SQL 用户和其他用户通常所需的权限。 运行下面的代码。 有关更多选项, 请参阅[此文档](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql?view=azuresqldb-mi-current)。

    ```sql
    ALTER ROLE [role name e.g. db_owner] ADD MEMBER [your Data Factory name]
    ```

4. 在 Azure 数据工厂中配置 Azure SQL 数据库托管实例链接的服务。

**示例: 使用托管标识身份验证**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<hostname,port>;Initial Catalog=<databasename>;"
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

在将数据复制到 Azure SQL 数据库托管实例或从其复制数据时，支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为**AzureSqlMITable**。 | 是 |
| tableName |此属性是链接服务引用的数据库实例中的表名称或视图名称。 | 对于源为“No”，对于接收器为“Yes” |

**示例**

```json
{
    "name": "AzureSqlMIDataset",
    "properties":
    {
        "type": "AzureSqlMITable",
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

若要从 Azure SQL 数据库托管实例复制数据, 复制活动源部分支持以下属性:

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动源的 type 属性必须设置为**SqlMISource**。 | 是 |
| sqlReaderQuery |此属性使用自定义 SQL 查询来读取数据。 例如 `select * from MyTable`。 |否 |
| sqlReaderStoredProcedureName |此属性是从源表读取数据的存储过程的名称。 最后一个 SQL 语句必须是存储过程中的 SELECT 语句。 |否 |
| storedProcedureParameters |这些参数用于存储过程。<br/>允许的值为名称或值对。 参数的名称和大小写必须与存储过程参数的名称和大小写匹配。 |否 |

**请注意以下几点：**

- 如果为**SqlMISource**指定**sqlReaderQuery** , 则复制活动对托管实例源运行此查询以获取数据。 也可通过指定 sqlReaderStoredProcedureName 和 storedProcedureParameters 来指定存储过程，前提是存储过程使用参数。
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
                "type": "SqlMISource",
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
                "type": "SqlMISource",
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
> 从将[数据加载到 Azure SQL 数据库托管实例中的最佳做法](#best-practice-for-loading-data-into-azure-sql-database-managed-instance), 了解有关支持的写入行为、配置和最佳实践的详细信息。

若要将数据复制到 Azure SQL 数据库托管实例, 请在复制活动接收器部分中支持以下属性:

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动接收器的 type 属性必须设置为**SqlMISink**。 | 是 |
| writeBatchSize |*每批*插入到 SQL 表中的行数。<br/>允许的值为表示行数的整数。 默认情况下, Azure 数据工厂会根据行大小动态确定相应的批大小。  |否 |
| writeBatchTimeout |此属性指定超时前等待批插入操作完成的时间。<br/>允许的值为 timespan。 例如，“00:30:00”表示 30 分钟。 |否 |
| preCopyScript |此属性指定在将数据写入到托管实例之前要运行的复制活动的 SQL 查询。 每次运行复制仅调用该查询一次。 可以使用此属性清除预加载的数据。 |否 |
| sqlWriterStoredProcedureName | 定义如何将源数据应用于目标表的存储过程的名称。 <br/>此存储过程由每个批处理调用。 对于仅运行一次并且与源数据不执行任何操作 (例如, 删除或截断) 的操作, 请`preCopyScript`使用属性。 | 否 |
| storedProcedureTableTypeParameterName |存储过程中指定的表类型的参数名称。  |否 |
| sqlWriterTableType |要在存储过程中使用的表类型名称。 通过复制活动，使移动数据在具备此表类型的临时表中可用。 然后，存储过程代码可合并复制数据和现有数据。 |否 |
| storedProcedureParameters |存储过程的参数。<br/>允许的值为名称和值对。 参数的名称和大小写必须与存储过程参数的名称和大小写匹配。 | 否 |

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
                "type": "SqlMISink",
                "writeBatchSize": 100000
            }
        }
    }
]
```

**示例 2：在复制过程中调用存储过程**

从[SQL MI 接收器调用存储过程](#invoke-a-stored-procedure-from-a-sql-sink)了解更多详细信息。

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
                "type": "SqlMISink",
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

## <a name="best-practice-for-loading-data-into-azure-sql-database-managed-instance"></a>将数据加载到 Azure SQL 数据库托管实例中的最佳做法

将数据复制到 Azure SQL 数据库托管实例时, 可能需要不同的写入行为:

- [追加](#append-data):我的源数据只有新记录。
- [Upsert](#upsert-data):我的源数据同时包含插入和更新。
- [覆盖](#overwrite-the-entire-table):我想每次都重新加载整个维度表。
- [用自定义逻辑编写](#write-data-with-custom-logic):在将数据最终插入目标表之前，我需要额外的处理。 

有关如何在 Azure 数据工厂中进行配置和最佳实践, 请参阅相应的部分。

### <a name="append-data"></a>追加数据

追加数据是此 Azure SQL 数据库托管实例接收器连接器的默认行为。 Azure 数据工厂执行大容量插入来有效地写入表。 可以在复制活动中相应地配置源和接收器。

### <a name="upsert-data"></a>更新插入数据

**选项 1：** 如果要复制大量数据, 请使用以下方法执行 upsert: 

- 首先, 使用[临时表](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables), 通过使用复制活动大容量加载所有记录。 由于不会记录对临时表的操作, 因此你可以在数秒内加载数百万条记录。
- 在 Azure 数据工厂中运行存储过程活动, 以应用[MERGE](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current)或 INSERT/UPDATE 语句。 使用临时表作为作为单个事务执行所有更新或插入的源。 通过这种方式, 可减少往返次数和日志操作。 在存储过程活动结束时, 可以截断临时表, 以便在下一个 upsert 周期中做好准备。

例如, 在 Azure 数据工厂中, 可以创建一个管道, 其中包含与**存储过程活动**链接的**复制活动**。 前者将源存储中的数据复制到临时表 (例如, **# #UpsertTempTable**), 作为数据集中的表名。 然后, 后者将调用一个存储过程, 用于将临时表中的源数据合并到目标表中, 并清理临时表。

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

**选项 2：** 您还可以选择[在复制活动中调用存储过程](#invoke-a-stored-procedure-from-a-sql-sink)。 此方法运行源表中的每行, 而不是使用 bulk insert 作为复制活动中的默认方法, 这不适用于大规模 upsert。

### <a name="overwrite-the-entire-table"></a>覆盖整个表

可以在复制活动接收器中配置**preCopyScript**属性。 在这种情况下, 对于运行的每个复制活动, Azure 数据工厂首先运行脚本。 然后, 它将运行复制以插入数据。 例如, 若要使用最新数据覆盖整个表, 请在从源中大容量加载新数据之前, 指定脚本以首先删除所有记录。

### <a name="write-data-with-custom-logic"></a>使用自定义逻辑写入数据

用自定义逻辑写入数据的步骤类似于[Upsert data](#upsert-data)节中所述的步骤。 如果需要在将源数据最终插入目标表之前应用额外的处理, 以进行大规模调整, 则可以执行以下两个操作之一: 

- 加载到临时表, 然后调用存储过程。
- 在复制过程中调用存储过程。

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a> 调用 SQL 接收器的存储过程

将数据复制到 Azure SQL 数据库托管实例时, 还可以使用其他参数配置和调用用户指定的存储过程。 存储过程功能利用[表值参数](https://msdn.microsoft.com/library/bb675163.aspx)。

> [!TIP]
> 调用存储过程时, 将按行而不是使用大容量操作来处理数据行, 但我们不建议使用大容量复制。 从[将数据加载到 Azure SQL 数据库托管实例中的最佳做法](#best-practice-for-loading-data-into-azure-sql-database-managed-instance)一文了解详细信息。

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

3. 在 Azure 数据工厂中, 在复制活动中定义**SQL MI 接收器**节, 如下所示:

    ```json
    "sink": {
        "type": "SqlMISink",
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
