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
ms.devlang: na
ms.topic: article
ms.date: 05/05/2018
ms.author: jingwang
ms.openlocfilehash: 0503b355089fe6bbcc7632ac93fd21e71f268032
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2018
---
# <a name="copy-data-to-or-from-azure-sql-database-by-using-azure-data-factory"></a>使用 Azure 数据工厂向/从 Azure SQL 数据库复制数据
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [版本 1 - 正式版](v1/data-factory-azure-sql-connector.md)
> * [版本 2 - 预览版](connector-azure-sql-database.md)

本文概述了如何使用 Azure 数据工厂中的复制活动从/向 Azure SQL 数据库复制数据。 它是基于概述复制活动总体的[复制活动概述](copy-activity-overview.md)一文。

> [!NOTE]
> 本文适用于目前处于预览版的数据工厂版本 2。 如果使用数据工厂服务第 1 版（已正式推出 (GA)），请参阅 [V1 中的 Azure SQL 数据库连接器](v1/data-factory-azure-sql-connector.md)。

## <a name="supported-capabilities"></a>支持的功能

可将数据从 Azure SQL 数据库复制到任一支持的接收器数据存储，或从任一支持的源数据存储复制到 Azure SQL 数据库。 有关复制活动支持作为源/接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

具体而言，此 Azure SQL 数据库连接器支持：

- 将 **SQL 身份验证**和 **Azure Active Directory 应用程序令牌身份验证**与服务主体或托管的服务标识 (MSI) 配合使用来复制数据。
- 作为源，使用 SQL 查询或存储过程检索数据。
- 作为接收器，在复制期间将数据追加到目标表，或调用带有自定义逻辑的存储过程。

> [!IMPORTANT]
> 如果使用 Azure Integration Runtime 复制数据，请将 [Azure SQL Server 防火墙](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure)配置为[允许 Azure 服务访问服务器](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure)。 如果使用自我托管的集成运行时复制数据，请将 Azure SQL Server 防火墙配置为允许合适的 IP 范围，包括用来连接到 Azure SQL 数据库的计算机 IP。

## <a name="getting-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

对于特定于 Azure SQL 数据库连接器的数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

Azure SQL 数据库链接的服务支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 类型属性必须设置为：**AzureSqlDatabase** | 是 |
| connectionString |为 connectionString 属性指定连接到 Azure SQL 数据库实例所需的信息。 将此字段标记为 SecureString 以安全地将其存储在数据工厂中或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 |是 |
| servicePrincipalId | 指定应用程序的客户端 ID。 | 将 AAD 身份验证与服务主体配合使用时是必需的。 |
| servicePrincipalKey | 指定应用程序的密钥。 将此字段标记为 SecureString 以安全地将其存储在数据工厂中或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 | 将 AAD 身份验证与服务主体配合使用时是必需的。 |
| tenant | 指定应用程序的租户信息（域名或租户 ID）。 可将鼠标悬停在 Azure 门户右上角进行检索。 | 将 AAD 身份验证与服务主体配合使用时是必需的。 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如果数据存储位于专用网络，则可以使用 Azure 集成运行时或自承载集成运行时。 如果未指定，则使用默认 Azure 集成运行时。 |否 |

有关各种身份验证类型，请参阅分别关于先决条件和 JSON 示例的以下各部分：

- [使用 SQL 身份验证](#using-sql-authentication)
- [使用 AAD 应用程序令牌身份验证 - 服务主体](#using-service-principal-authentication)
- [使用 AAD 应用程序令牌身份验证 - 托管的服务标识](#using-managed-service-identity-authentication)

### <a name="using-sql-authentication"></a>使用 SQL 身份验证

**使用 SQL 身份验证的链接服务示例：**

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

### <a name="using-service-principal-authentication"></a>使用服务主体身份验证

若要使用基于服务主体的 AAD 应用程序令牌身份验证，请执行以下步骤：

1. **[从 Azure 门户创建 Azure Active Directory 应用程序](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application)。**  记下应用程序名称，以及用来定义链接服务的以下值：

    - 应用程序 ID
    - 应用程序密钥
    - 租户 ID

2. 使用 Azure 门户为 Azure SQL Server **[预配 Azure Active Directory 管理员](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)**（如果尚未这样做）。 AAD 管理员必须是 AAD 用户或 AAD 组，但不能是服务主体。 执行此步骤后，在后续步骤中，可以使用 AAD 标识来为服务主体创建包含的数据库用户。

3. **为服务主体创建包含的数据库用户**：使用至少具有 ALTER ANY USER 权限的 AAD 标识，通过 SSMS 之类的工具连接到要从中复制或要将数据复制到的数据库，并执行以下 T-SQL。 可从[此处](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)了解有关包含的数据库用户的详细信息。
    
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. 像通常对 SQL 用户所做的那样**向服务主体授予所需的权限**，例如，通过执行以下命令：

    ```sql
    EXEC sp_addrolemember [role name], [your application name];
    ```

5. 在 ADF 中，配置 Azure SQL 数据库链接服务。


**使用服务主体身份验证的链接服务示例：**

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

### <a name="using-managed-service-identity-authentication"></a>使用托管服务标识身份验证

可将数据工厂与代表此特定数据工厂的[托管服务标识 (MSI)](data-factory-service-identity.md) 相关联。 可以使用此服务标识进行 Azure SQL 数据库身份验证，这允许此指定的工厂访问数据库并从中复制数据或将数据复制到其中。

若要使用基于 MSI 的 AAD 应用程序令牌身份验证，请执行以下步骤：

1. **在 Azure AD 中创建一个组，并使工厂 MSI 成为该组的成员**。

    a. 从 Azure 门户中找到数据工厂服务标识。 转到你的数据工厂 ->“属性”-> 复制**服务标识 ID**。

    b. 安装 [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) 模块，使用 `Connect-AzureAD` 命令进行登录，运行以下命令来创建组并将数据工厂 MSI 添加为成员。
    ```powershell
    $Group = New-AzureADGroup -DisplayName "<your group name>" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId "<your data factory service identity ID>"
    ```

2. 使用 Azure 门户为 Azure SQL Server **[预配 Azure Active Directory 管理员](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)**（如果尚未这样做）。 AAD 管理员可以是 AAD 用户或 AAD 组。 如果你向具有 MSI 的组授予了管理员角色，请跳过下面的步骤 3 和 4，因为管理员对 DB 具有完全访问权限。

3. **为 AAD 组创建包含的数据库用户**：使用至少具有 ALTER ANY USER 权限的 AAD 标识，通过 SSMS 之类的工具连接到要从中复制或要将数据复制到的数据库，并执行以下 T-SQL。 可从[此处](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)了解有关包含的数据库用户的详细信息。
    
    ```sql
    CREATE USER [your AAD group name] FROM EXTERNAL PROVIDER;
    ```

4. 像通常对 SQL 用户所做的那样**向 AAD 组授予所需的权限**，例如，通过执行以下命令：

    ```sql
    EXEC sp_addrolemember [role name], [your AAD group name];
    ```

5. 在 ADF 中，配置 Azure SQL 数据库链接服务。

**使用 MSI 身份验证的链接服务示例：**

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

有关可用于定义数据集的各个部分和属性的完整列表，请参阅数据集一文。 本部分提供 Azure SQL 数据库数据集支持的属性列表。

要从/向 Azure SQL 数据库复制数据，请将数据集的 type 属性设置为“AzureSqlTable”。 支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为：**AzureSqlTable** | 是 |
| tableName |链接服务引用的 Azure SQL 数据库实例中的表名称或视图名称。 | 是 |

**示例：**

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
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 Azure SQL 数据库源和接收器支持的属性列表。

### <a name="azure-sql-database-as-source"></a>Azure SQL 数据库作为源

要从 Azure SQL 数据库复制数据，请将复制活动中的源类型设置为“SqlSource”。 复制活动**源**部分支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动源的 type 属性必须设置为：SqlSource | 是 |
| sqlReaderQuery |使用自定义 SQL 查询读取数据。 示例：`select * from MyTable`。 |否 |
| sqlReaderStoredProcedureName |从源表读取数据的存储过程的名称。 存储过程中的最后一条 SQL 语句必须是 SELECT 语句。 |否 |
| storedProcedureParameters |存储过程的参数。<br/>允许的值为：名称/值对。 参数的名称和大小写必须与存储过程参数的名称和大小写匹配。 |否 |

**需要注意的要点：**

- 如果为 SqlSource 指定 **sqlReaderQuery**，则复制活动针对 Azure SQL 数据库源运行此查询可获取数据。 此外，也可以通过指定 **sqlReaderStoredProcedureName** 和 **storedProcedureParameters** 来指定存储过程（如果存储过程使用参数）。
- 如果不指定“sqlReaderQuery”或“sqlReaderStoredProcedureName”，则使用在数据集 JSON 的“结构”部分定义的列，构建针对 Azure SQL 数据库运行的查询 (`select column1, column2 from mytable`)。 如果数据集定义不具备该“结构”，则从表中选择所有列。
- 使用 sqlReaderStoredProcedureName 时，仍需指定数据集 JSON 中虚拟的 tableName属性。

**示例：使用 SQL 查询**

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

**示例：使用存储过程**

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

### <a name="azure-sql-database-as-sink"></a>Azure SQL 数据库作为接收器

要向 Azure SQL 数据库复制数据，请将复制活动中的接收器类型设置为“SqlSink”。 复制活动接收器部分中支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动接收器的 type 属性必须设置为：**SqlSink** | 是 |
| writeBatchSize |缓冲区大小达到 writeBatchSize 时会数据插入 SQL 表。<br/>允许的值为：整数（行数）。 |否（默认值为 10000） |
| writeBatchTimeout |超时之前等待批插入操作完成时的等待时间。<br/>允许的值为：timespan。 示例：“00:30:00”（30 分钟）。 |否 |
| preCopyScript |将数据写入到 Azure SQL 数据库之前，指定复制活动要执行的 SQL 查询。 每次运行复制仅调用该查询一次。 此属性可用于清理预先加载的数据。 |否 |
| sqlWriterStoredProcedureName |存储过程的名称，该存储过程定义如何将源数据应用到目标表，例如使用你自己的业务逻辑执行更新插入或转换。 <br/><br/>请注意，此存储过程将由每个批处理调用。 如果要执行仅运行一次且与源数据无关的操作（例如删除/截断），请使用 `preCopyScript` 属性。 |否 |
| storedProcedureParameters |存储过程的参数。<br/>允许的值为：名称/值对。 参数的名称和大小写必须与存储过程参数的名称和大小写匹配。 |否 |
| sqlWriterTableType |指定要在存储过程中使用的表类型名称。 通过复制活动，使移动数据在具备此表类型的临时表中可用。 然后，存储过程代码可合并复制数据和现有数据。 |否 |

> [!TIP]
> 将数据复制到 Azure SQL 数据库时，复制活动默认将数据追加到接收器表后面。 要执行 UPSERT 或其他业务逻辑，请在 SqlSink 中使用存储过程。 参阅[调用 SQL 接收器的存储过程](#invoking-stored-procedure-for-sql-sink)，了解更多详细信息。

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

**示例 2：在 upsert 的复制过程中调用存储过程**

参阅[调用 SQL 接收器的存储过程](#invoking-stored-procedure-for-sql-sink)，了解更多详细信息。

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

## <a name="identity-columns-in-the-target-database"></a>目标数据库中的标识列

本部分举例说明了如何将数据从不含标识列的源表复制到含标识列的目标表。

**源表：**

```sql
create table dbo.SourceTbl
(
       name varchar(100),
       age int
)
```

**目标表：**

```sql
create table dbo.TargetTbl
(
       identifier int identity(1,1),
       name varchar(100),
       age int
)
```

请注意，目标表包含标识列。

**源数据集 JSON 定义**

```json
{
    "name": "SampleSource",
    "properties": {
        "type": " AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "TestIdentitySQL",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTbl"
        }
    }
}
```

**数据集 JSON 定义**

```json
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "TestIdentitySQL",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "TargetTbl"
        }
    }
}
```

请注意，源表和目标表具有不同架构（目标表具有一个额外标识列）。 在本方案中，需在不包含标识列的目标数据集定义中指定 **structure** 属性。

## <a name="invoking-stored-procedure-for-sql-sink"></a> 调用 SQL 接收器的存储过程

将数据复制到 Azure SQL 数据库时，可以配置某个用户指定的存储过程并配合其他参数进行调用。

内置的复制机制无法使用时，可使用存储过程。 需要在最后一次将源数据插入目标表前需要完成 upsert（插入+更新）或额外处理（合并列、查找其他值、插入到多个表等）时，通常使用此过程。

以下示例演示如何使用存储过程，在 Azure SQL 数据库数据库中的表内执行 upsert。 假设输入数据和接收器“Marketing”表中各具有三列：ProfileID、State 和 Category。 基于“ProfileID”列执行 upsert，仅应用于特定类别。

**输出数据集**

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

如下所示，在复制活动中定义“SqlSink”节。

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

在数据库中，使用与“SqlWriterStoredProcedureName”相同的名称定义存储过程。 它处理来自指定源的输入数据，并合并到输出表。 请注意，存储过程的参数名称应与数据集中定义的“tableName”相同。

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
      VALUES (source.ProfileID, source.State, source.Category)
END
```

在数据库中，使用与 sqlWriterTableType 相同的名称定义表类型。 请注意，表类型的架构应与输入数据返回的架构相同。

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL，
    [Category] [varchar](256) NOT NULL，
)
```

存储过程功能利用[表值参数](https://msdn.microsoft.com/library/bb675163.aspx)。

## <a name="data-type-mapping-for-azure-sql-database"></a>Azure SQL 数据库的数据类型映射

从/向 Azure SQL 数据库复制数据时，以下映射用于从 Azure SQL 数据库数据类型映射到 Azure 数据工厂临时数据类型。 若要了解复制活动如何将源架构和数据类型映射到接收器，请参阅[架构和数据类型映射](copy-activity-schema-and-type-mapping.md)。

| Azure SQL 数据库数据类型 | 数据工厂临时数据类型 |
|:--- |:--- |
| bigint |Int64 |
| binary |Byte[] |
| bit |布尔 |
| char |String, Char[] |
| 日期 |DateTime |
| Datetime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| 小数 |小数 |
| FILESTREAM 特性 (varbinary(max)) |Byte[] |
| Float |Double |
| 图像 |Byte[] |
| int |Int32 |
| money |小数 |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numeric |小数 |
| nvarchar |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |小数 |
| sql_variant |对象 * |
| text |String, Char[] |
| time |TimeSpan |
| timestamp |Byte[] |
| tinyint |Byte |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |Xml |

## <a name="next-steps"></a>后续步骤
有关 Azure 数据工厂中复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md##supported-data-stores-and-formats)。