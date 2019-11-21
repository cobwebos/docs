---
title: Copy data to and from Azure SQL Database Managed Instance
description: 了解如何使用 Azure 数据工厂将数据移入和移出 Azure SQL 数据库托管实例。
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.custom: seo-lt-2019
ms.date: 09/09/2019
ms.openlocfilehash: 9eedd8c1ad740f7393da47eac7a20cb5b58ad8d3
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74218774"
---
# <a name="copy-data-to-and-from-azure-sql-database-managed-instance-by-using-azure-data-factory"></a>使用 Azure 数据工厂向/从 Azure SQL 数据库托管实例复制数据

本文概述了如何使用 Azure 数据工厂中的复制活动从/向 Azure SQL 数据库托管实例复制数据。 本文是根据总体概述复制活动的[复制活动概述](copy-activity-overview.md)一文编写的。

## <a name="supported-capabilities"></a>支持的功能

This Azure SQL Database Managed Instance connector is supported for the following activities:

- [Copy activity](copy-activity-overview.md) with [supported source/sink matrix](copy-activity-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)
- [GetMetadata 活动](control-flow-get-metadata-activity.md)

可将数据从 Azure SQL 数据库托管实例复制到任何支持的接收器数据存储。 还可以将数据从任何支持的源数据存储复制到托管实例。 有关复制活动支持作为源和接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

具体而言，此 Azure SQL 数据库托管实例连接器支持：

- Copying data by using SQL authentication and Azure Active Directory (Azure AD) Application token authentication with a service principal or managed identities for Azure resources.
- As a source, retrieving data by using a SQL query or a stored procedure.
- 作为接收器，在复制期间将数据追加到目标表，或调用带有自定义逻辑的存储过程。

>[!NOTE]
>Azure SQL Database Managed Instance [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-mi-current) isn't supported by this connector now. To work around, you can use a [generic ODBC connector](connector-odbc.md) and a SQL Server ODBC driver via a self-hosted integration runtime. Follow [this guidance](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-mi-current) with ODBC driver download and connection string configurations.

>[!NOTE]
>Service principal and managed identity authentications currently aren't supported by this connector. To work around, choose an Azure SQL Database connector and manually specify the server of your managed instance.

## <a name="prerequisites"></a>必备组件

To access the Azure SQL Database Managed Instance [public endpoint](../sql-database/sql-database-managed-instance-public-endpoint-securely.md), you can use an Azure Data Factory managed Azure integration runtime. Make sure that you enable the public endpoint and also allow public endpoint traffic on the network security group so that Azure Data Factory can connect to your database. For more information, see [this guidance](../sql-database/sql-database-managed-instance-public-endpoint-configure.md).

To access the Azure SQL Database Managed Instance private endpoint, set up a [self-hosted integration runtime](create-self-hosted-integration-runtime.md) that can access the database. If you provision the self-hosted integration runtime in the same virtual network as your managed instance, make sure that your integration runtime machine is in a different subnet than your managed instance. If you provision your self-hosted integration runtime in a different virtual network than your managed instance, you can use either a virtual network peering or a virtual network to virtual network connection. 有关详细信息，请参阅[将应用程序连接到 Azure SQL 数据库托管实例](../sql-database/sql-database-managed-instance-connect-app.md)。

## <a name="get-started"></a>开始体验

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

The following sections provide details about properties that are used to define Azure Data Factory entities specific to the Azure SQL Database Managed Instance connector.

## <a name="linked-service-properties"></a>链接服务属性

Azure SQL 数据库托管实例链接的服务支持以下属性：

| properties | 描述 | 需要 |
|:--- |:--- |:--- |
| type | The type property must be set to **AzureSqlMI**. | 是 |
| connectionString |This property specifies the **connectionString** information that's needed to connect to the managed instance by using SQL authentication. 有关详细信息，请参阅以下示例。 <br/>The default port is 1433. If you're using Azure SQL Database Managed Instance with a public endpoint, explicitly specify port 3342.<br>Mark this field as **SecureString** to store it securely in Azure Data Factory. You also can put a password in Azure Key Vault. If it's SQL authentication, pull the `password` configuration out of the connection string. For more information, see the JSON example following the table and [Store credentials in Azure Key Vault](store-credentials-in-key-vault.md). |是 |
| servicePrincipalId | 指定应用程序的客户端 ID。 | Yes, when you use Azure AD authentication with a service principal |
| servicePrincipalKey | 指定应用程序的密钥。 Mark this field as **SecureString** to store it securely in Azure Data Factory or [reference a secret stored in Azure Key Vault](store-credentials-in-key-vault.md). | Yes, when you use Azure AD authentication with a service principal |
| tenant | Specify the tenant information, like the domain name or tenant ID, under which your application resides. Retrieve it by hovering the mouse in the upper-right corner of the Azure portal. | Yes, when you use Azure AD authentication with a service principal |
| connectVia | 此[集成运行时](concepts-integration-runtime.md)用于连接到数据存储。 You can use a self-hosted integration runtime or an Azure integration runtime if your managed instance has a public endpoint and allows Azure Data Factory to access it. If not specified, the default Azure integration runtime is used. |是 |

有关各种身份验证类型，请参阅关于先决条件和 JSON 示例的以下各部分：

- [SQL 身份验证](#sql-authentication)
- [Azure AD 应用程序令牌身份验证：服务主体](#service-principal-authentication)
- [Azure AD 应用程序令牌身份验证：Azure 资源的托管标识](#managed-identity)

### <a name="sql-authentication"></a>SQL 身份验证

**Example 1: use SQL authentication**

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

**Example 2: use SQL authentication with a password in Azure Key Vault**

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

1. Follow the steps to [Provision an Azure Active Directory administrator for your Managed Instance](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance).

2. [Create an Azure Active Directory application](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) from the Azure portal. 记下应用程序名称，以及以下定义链接服务的值：

    - 应用程序 ID
    - 应用程序密钥
    - 租户 ID

3. [Create logins](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) for the Azure Data Factory managed identity. In SQL Server Management Studio (SSMS), connect to your Managed Instance using a SQL Server account that is a **sysadmin**. In **master** database, run the following T-SQL:

    ```sql
    CREATE LOGIN [your application name] FROM EXTERNAL PROVIDER
    ```

4. [Create contained database users](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) for the Azure Data Factory managed identity. Connect to the database from or to which you want to copy data, run the following T-SQL: 
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER
    ```

5. Grant the Data Factory managed identity needed permissions as you normally do for SQL users and others. Run the following code. For more options, see [this document](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql?view=azuresqldb-mi-current).

    ```sql
    ALTER ROLE [role name e.g. db_owner] ADD MEMBER [your application name]
    ```

6. Configure an Azure SQL Database Managed Instance linked service in Azure Data Factory.

**Example: use service principal authentication**

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

可将数据工厂与代表此特定数据工厂的 [Azure 资源托管标识](data-factory-service-identity.md)相关联。 You can use this managed identity for Azure SQL Database Managed Instance authentication. 指定工厂可使用此标识访问数据库数据并向其/从中复制数据。

To use managed identity authentication, follow these steps.

1. Follow the steps to [Provision an Azure Active Directory administrator for your Managed Instance](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance).

2. [Create logins](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) for the Azure Data Factory managed identity. In SQL Server Management Studio (SSMS), connect to your Managed Instance using a SQL Server account that is a **sysadmin**. In **master** database, run the following T-SQL:

    ```sql
    CREATE LOGIN [your Data Factory name] FROM EXTERNAL PROVIDER
    ```

3. [Create contained database users](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) for the Azure Data Factory managed identity. Connect to the database from or to which you want to copy data, run the following T-SQL: 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER
    ```

4. Grant the Data Factory managed identity needed permissions as you normally do for SQL users and others. Run the following code. For more options, see [this document](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql?view=azuresqldb-mi-current).

    ```sql
    ALTER ROLE [role name e.g. db_owner] ADD MEMBER [your Data Factory name]
    ```

5. Configure an Azure SQL Database Managed Instance linked service in Azure Data Factory.

**Example: uses managed identity authentication**

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

To copy data to and from Azure SQL Database Managed Instance, the following properties are supported:

| properties | 描述 | 需要 |
|:--- |:--- |:--- |
| type | The type property of the dataset must be set to **AzureSqlMITable**. | 是 |
| schema | Name of the schema. |对于源为“No”，对于接收器为“Yes”  |
| 表 | Name of the table/view. |对于源为“No”，对于接收器为“Yes”  |
| tableName | Name of the table/view with schema. This property is supported for backward compatibility. For new workload, use `schema` and `table`. | 对于源为“No”，对于接收器为“Yes” |

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
            "schema": "<schema_name>",
            "table": "<table_name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的节和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 此节提供 Azure SQL 数据库托管实例源和和接收器支持的属性列表。

### <a name="azure-sql-database-managed-instance-as-a-source"></a>将 Azure SQL 数据库托管实例作为源

To copy data from Azure SQL Database Managed Instance, the following properties are supported in the copy activity source section:

| properties | 描述 | 需要 |
|:--- |:--- |:--- |
| type | The type property of the copy activity source must be set to **SqlMISource**. | 是 |
| sqlReaderQuery |此属性使用自定义 SQL 查询来读取数据。 例如 `select * from MyTable`。 |No |
| sqlReaderStoredProcedureName |此属性是从源表读取数据的存储过程的名称。 最后一个 SQL 语句必须是存储过程中的 SELECT 语句。 |No |
| storedProcedureParameters |这些参数用于存储过程。<br/>允许的值为名称或值对。 参数的名称和大小写必须与存储过程参数的名称和大小写匹配。 |No |

**请注意以下几点：**

- If **sqlReaderQuery** is specified for **SqlMISource**, the copy activity runs this query against the managed instance source to get the data. 也可通过指定 sqlReaderStoredProcedureName 和 storedProcedureParameters 来指定存储过程，前提是存储过程使用参数。
- 如果不指定 **sqlReaderQuery** 或 **sqlReaderStoredProcedureName** 属性，则数据集 JSON 的“structure”节中定义的列用于构建查询。 查询 `select column1, column2 from mytable` 针对托管实例运行。 如果数据集定义没有“structure”，则会从表中选择所有列。

**Example: Use a SQL query**

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

**Example: Use a stored procedure**

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
> Learn more about the supported write behaviors, configurations, and best practices from [Best practice for loading data into Azure SQL Database Managed Instance](#best-practice-for-loading-data-into-azure-sql-database-managed-instance).

To copy data to Azure SQL Database Managed Instance, the following properties are supported in the copy activity sink section:

| properties | 描述 | 需要 |
|:--- |:--- |:--- |
| type | The type property of the copy activity sink must be set to **SqlMISink**. | 是 |
| writeBatchSize |Number of rows to insert into the SQL table *per batch*.<br/>允许的值为表示行数的整数。 By default, Azure Data Factory dynamically determines the appropriate batch size based on the row size.  |No |
| writeBatchTimeout |此属性指定超时前等待批插入操作完成的时间。<br/>Allowed values are for the timespan. 例如，“00:30:00”表示 30 分钟。 |No |
| preCopyScript |This property specifies a SQL query for the copy activity to run before writing data into the managed instance. 每次运行复制仅调用该查询一次。 可以使用此属性清除预加载的数据。 |No |
| sqlWriterStoredProcedureName | 定义如何将源数据应用于目标表的存储过程的名称。 <br/>此存储过程由每个批处理调用。 For operations that run only once and have nothing to do with source data, for example, delete or truncate, use the `preCopyScript` property. | No |
| storedProcedureTableTypeParameterName |The parameter name of the table type specified in the stored procedure.  |No |
| sqlWriterTableType |The table type name to be used in the stored procedure. 通过复制活动，使移动数据在具备此表类型的临时表中可用。 然后，存储过程代码可合并复制数据和现有数据。 |No |
| storedProcedureParameters |存储过程的参数。<br/>允许的值为名称和值对。 参数的名称和大小写必须与存储过程参数的名称和大小写匹配。 | No |
| tableOption | Specifies whether to automatically create the sink table if not exists based on the source schema. Auto table creation is not supported when sink specifies stored procedure or staged copy is configured in copy activity. Allowed values are: `none` (default), `autoCreate`. |No |

**Example 1: Append data**

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
                "writeBatchSize": 100000,
                "tableOption": "autoCreate"
            }
        }
    }
]
```

**Example 2: Invoke a stored procedure during copy**

Learn more details from [Invoke a stored procedure from a SQL MI sink](#invoke-a-stored-procedure-from-a-sql-sink).

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

## <a name="best-practice-for-loading-data-into-azure-sql-database-managed-instance"></a>Best practice for loading data into Azure SQL Database Managed Instance

When you copy data into Azure SQL Database Managed Instance, you might require different write behavior:

- [Append](#append-data): My source data has only new records.
- [Upsert](#upsert-data): My source data has both inserts and updates.
- [Overwrite](#overwrite-the-entire-table): I want to reload the entire dimension table each time.
- [Write with custom logic](#write-data-with-custom-logic): I need extra processing before the final insertion into the destination table. 

See the respective sections for how to configure in Azure Data Factory and best practices.

### <a name="append-data"></a>Append data

Appending data is the default behavior of this Azure SQL Database Managed Instance sink connector. Azure Data Factory does a bulk insert to write to your table efficiently. You can configure the source and sink accordingly in the copy activity.

### <a name="upsert-data"></a>更新插入数据

**Option 1:** When you have a large amount of data to copy, use the following approach to do an upsert: 

- First, use a [temporary table](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables) to bulk load all records by using the copy activity. Because operations against temporary tables aren't logged, you can load millions of records in seconds.
- Run a stored procedure activity in Azure Data Factory to apply a [MERGE](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) or INSERT/UPDATE statement. Use the temp table as the source to perform all updates or inserts as a single transaction. In this way, the number of round trips and log operations is reduced. At the end of the stored procedure activity, the temp table can be truncated to be ready for the next upsert cycle.

As an example, in Azure Data Factory, you can create a pipeline with a **Copy activity** chained with a **Stored Procedure activity**. The former copies data from your source store into a temporary table, for example, **##UpsertTempTable**, as the table name in the dataset. Then the latter invokes a stored procedure to merge source data from the temp table into the target table and clean up the temp table.

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

In your database, define a stored procedure with MERGE logic, like the following example, which is pointed to from the previous stored procedure activity. Assume that the target is the **Marketing** table with three columns: **ProfileID**, **State**, and **Category**. Do the upsert based on the **ProfileID** column.

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

**Option 2:** You also can choose to [invoke a stored procedure within a copy activity](#invoke-a-stored-procedure-from-a-sql-sink). This approach runs each row in the source table instead of using bulk insert as the default approach in the copy activity, which isn't appropriate for large-scale upsert.

### <a name="overwrite-the-entire-table"></a>Overwrite the entire table

You can configure the **preCopyScript** property in a copy activity sink. In this case, for each copy activity that runs, Azure Data Factory runs the script first. Then it runs the copy to insert the data. For example, to overwrite the entire table with the latest data, specify a script to first delete all the records before you bulk load the new data from the source.

### <a name="write-data-with-custom-logic"></a>Write data with custom logic

The steps to write data with custom logic are similar to those described in the [Upsert data](#upsert-data) section. When you need to apply extra processing before the final insertion of source data into the destination table, for large scale, you can do one of two things: 

- Load to a temporary table and then invoke a stored procedure.
- Invoke a stored procedure during copy.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a> 调用 SQL 接收器的存储过程

When you copy data into Azure SQL Database Managed Instance, you also can configure and invoke a user-specified stored procedure with additional parameters. 存储过程功能利用[表值参数](https://msdn.microsoft.com/library/bb675163.aspx)。

> [!TIP]
> Invoking a stored procedure processes the data row by row instead of by using a bulk operation, which we don't recommend for large-scale copy. Learn more from [Best practice for loading data into Azure SQL Database Managed Instance](#best-practice-for-loading-data-into-azure-sql-database-managed-instance).

当内置复制机制无法使用时，还可使用存储过程。 An example is when you want to apply extra processing before the final insertion of source data into the destination table. Some extra processing examples are when you want to merge columns, look up additional values, and insert data into more than one table.

以下示例演示如何使用存储过程，在 SQL Server 数据库中的表内执行 upsert。 Assume that the input data and the sink **Marketing** table each have three columns: **ProfileID**, **State**, and **Category**. Do the upsert based on the **ProfileID** column, and only apply it for a specific category called "ProductA".

1. In your database, define the table type with the same name as **sqlWriterTableType**. 表类型的架构与输入数据返回的架构相同。

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL，
        [Category] [varchar](256) NOT NULL
    )
    ```

2. In your database, define the stored procedure with the same name as **sqlWriterStoredProcedureName**. 它可处理来自指定源的输入数据，并将其合并到输出表中。 The parameter name of the table type in the stored procedure is the same as **tableName** defined in the dataset.

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

3. In Azure Data Factory, define the **SQL MI sink** section in the copy activity as follows:

    ```json
    "sink": {
        "type": "SqlMISink",
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

## <a name="data-type-mapping-for-azure-sql-database-managed-instance"></a>Azure SQL 数据库托管实例的数据类型映射

从/向 Azure SQL 数据库托管实例复制数据时，以下映射用于从 Azure SQL 数据库托管实例数据类型映射到 Azure 数据工厂临时数据类型。 若要了解复制活动如何从源架构和数据类型映射到接收器，请参阅[架构和数据类型映射](copy-activity-schema-and-type-mapping.md)。

| Azure SQL 数据库托管实例数据类型 | Azure 数据工厂临时数据类型 |
|:--- |:--- |
| bigint |Int64 |
| binary |Byte[] |
| bit |布尔 |
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
| tinyint |Int16 |
| uniqueidentifier |GUID |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |Xml |

>[!NOTE]
> 对于映射到十进制临时类型的数据类型，目前 Azure 数据工厂支持的最大精度为 28。 如果数据需要的精度大于 28，请考虑在 SQL 查询中将其转换为字符串。

## <a name="lookup-activity-properties"></a>Lookup activity properties

To learn details about the properties, check [Lookup activity](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>GetMetadata activity properties

To learn details about the properties, check [GetMetadata activity](control-flow-get-metadata-activity.md) 

## <a name="next-steps"></a>后续步骤
有关 Azure 数据工厂中复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md##supported-data-stores-and-formats)。
