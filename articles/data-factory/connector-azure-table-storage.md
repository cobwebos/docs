---
title: 使用数据工厂向/从 Azure 表存储复制数据 | Microsoft Docs
description: 了解如何使用数据工厂将数据从支持的源存储复制到 Azure 表存储，或从表存储复制到支持的接收器存储。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/14/2018
ms.author: jingwang
ms.openlocfilehash: 24954cfc128834313bf13a1917e67d5c1812cf66
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37055299"
---
# <a name="copy-data-to-and-from-azure-table-storage-by-using-azure-data-factory"></a>使用 Azure 数据工厂向/从 Azure 表存储复制数据
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [第 1 版](v1/data-factory-azure-table-connector.md)
> * [当前版本](connector-azure-table-storage.md)

本文概述如何使用 Azure 数据工厂中的复制活动向/从 Azure 表存储复制数据。 本文是根据总体概述复制活动的[复制活动概述](copy-activity-overview.md)一文编写的。

## <a name="supported-capabilities"></a>支持的功能

可将数据从任一支持的源数据存储复制到表存储。 也可以将数据从表存储复制到任一支持的接收器数据存储。 有关复制活动支持作为源或接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

具体而言，此 Azure 表连接器支持使用帐户密钥和服务共享访问签名身份验证复制数据。

## <a name="get-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

对于特定于表存储的数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

### <a name="use-an-account-key"></a>使用帐户密钥

可以使用帐户密钥创建 Azure 存储链接服务。 该链接服务将存储的全局访问权限提供给数据工厂。 支持以下属性。

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | type 属性必须设置为 **AzureStorage**。 |是 |
| connectionString | 为 connectionString 属性指定连接到存储所需的信息。 将此字段标记为 SecureString 以安全地将其存储在数据工厂中或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 |是 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如果数据存储位于专用网络，则可以使用 Azure 集成运行时或自承载集成运行时。 如果未指定，则使用默认 Azure 集成运行时。 |否 |

**示例：**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="use-service-shared-access-signature-authentication"></a>使用服务共享访问签名身份验证

还可以使用共享访问签名创建存储链接服务。 这样，便可以将存储中所有/特定资源的受限/限时访问权限提供给数据工厂。

共享访问签名对存储帐户中的资源提供委托访问。 可以使用共享访问签名授权客户端在指定时间内，以一组指定权限有限访问存储帐户中的对象。 无需共享帐户访问密钥。 共享访问签名是一个 URI，在其查询参数中包含对存储资源已验证访问所需的所有信息。 若要使用共享访问签名访问存储资源，客户端只需将共享访问签名传入到相应的构造函数或方法。 有关共享访问签名的详细信息，请参阅[共享访问签名：了解共享访问签名模型](../storage/common/storage-dotnet-shared-access-signature-part-1.md)。

> [!NOTE]
> 数据工厂现在同时支持服务共享访问签名和帐户共享访问签名。 有关这两种类型及其构建方式的详细信息，请参阅[共享访问签名的类型](../storage/common/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures)。 

> [!TIP]
> 若要为存储帐户生成服务共享访问签名，可以执行以下 PowerShell 命令。 请替换占位符并授予所需的权限。
> `$context = New-AzureStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzureStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

若要使用服务共享访问签名身份验证，需支持以下属性。

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | type 属性必须设置为 **AzureStorage**。 |是 |
| sasUri | 指定存储资源（例如 Blob、容器或表）的共享访问签名 URI。 将此字段标记为 SecureString 以安全地将其存储在数据工厂中或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 |是 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如果数据存储位于专用网络，则可以使用 Azure 集成运行时或自承载集成运行时。 如果未指定，则使用默认 Azure 集成运行时。 |否 |

**示例：**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

在创建共享访问签名 URI 时，请注意以下几点：

- 根据链接服务（读取、写入、读/写）在数据工厂中的用法，设置针对对象的适当读/写权限。
- 根据需要设置“到期时间”。 确保存储对象的访问权限不会在管道的活动期限内过期。
- 应该根据需要在正确的表级别创建 URI。

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 本部分提供 Azure 表数据集支持的属性列表。

要向/从 Azure 表复制数据，请将数据集的 type 属性设置为 **AzureTable**。 支持以下属性。

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为 **AzureTable**。 |是 |
| tableName |链接服务引用的表存储数据库实例中表的名称。 |是 |

**示例：**

```json
{
    "name": "AzureTableDataset",
    "properties":
    {
        "type": "AzureTable",
        "linkedServiceName": {
            "referenceName": "<Azure Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

### <a name="schema-by-data-factory"></a>数据工厂的构架

对于无架构的数据存储（如 Azure 表），数据工厂将使用下列方式之一推断架构：

* 如果使用数据集定义中的**结构**属性指定数据的结构，数据工厂会将此结构作为架构。 在这种情况下，如果行不包含列的值，则会为其提供 null 值。
* 如果不使用数据集定义中的**结构**属性指定数据结构，数据工厂将通过使用数据中的第一行来推断架构。 在这种情况下，如果第一行不包含完整架构，则复制操作的结果中会丢失部分列。

对于无架构的数据源，最佳做法是使用**结构**属性指定数据的结构。

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 Azure 表源和接收器支持的属性列表。

### <a name="azure-table-as-a-source-type"></a>将 Azure 表用作源类型

要从 Azure 表复制数据，请将复制活动中的源类型设置为“AzureTableSource”。 复制活动的 **source** 节支持以下属性。

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动源的 type 属性必须设置为 **AzureTableSource**。 |是 |
| azureTableSourceQuery |使用自定义表存储查询读取数据。 请参阅以下部分中的示例。 |否 |
| azureTableSourceIgnoreTableNotFound |指示是否允许存在忽略表异常。<br/>允许的值为 **True** 和 **False**（默认值）。 |否 |

### <a name="azuretablesourcequery-examples"></a>azureTableSourceQuery 示例

如果 Azure 表列为 datetime 类型：

```json
"azureTableSourceQuery": "LastModifiedTime gt datetime'2017-10-01T00:00:00' and LastModifiedTime le datetime'2017-10-02T00:00:00'"
```

如果 Azure 表列为 string 类型：

```json
"azureTableSourceQuery": "LastModifiedTime ge '201710010000_0000' and LastModifiedTime le '201710010000_9999'"
```

如果使用的是管道参数，请按照上面的示例将日期时间值强制转换为适当格式。

### <a name="azure-table-as-a-sink-type"></a>将 Azure 表用作接收器类型

若要将数据复制到 Azure 表，请将复制活动中的接收器类型设置为“AzureTableSink”。 复制活动 **sink** 节支持以下属性。

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动接收器的 type 属性必须设置为 **AzureTableSink**。 |是 |
| azureTableDefaultPartitionKeyValue |接收器可以使用的默认分区键值。 |否 |
| azureTablePartitionKeyName |指定列名称，使用列值作为分区键。 如果未指定，则使用“AzureTableDefaultPartitionKeyValue”作为分区键。 |否 |
| azureTableRowKeyName |指定列名称，使用列值作为行键。 如果未指定，对每一行使用 GUID。 |否 |
| azureTableInsertType |将数据插入 Azure 表的模式。 此属性控制输出表中具有匹配的分区键和行键的现有行是否替换或合并其值。 <br/><br/>允许的值为 **merge**（默认值）和 **replace**。 <br/><br> 此设置在行级别而不是表级别进行应用。 并且两个选项都不会删除输入中不存在的输出表中的行。 若要了解合并和替换设置的工作原理，请参阅[插入或合并实体](https://msdn.microsoft.com/library/azure/hh452241.aspx)和[插入或替换实体](https://msdn.microsoft.com/library/azure/hh452242.aspx)。 |否 |
| writeBatchSize |writeBatchSize 或 writeBatchTimeout 命中时，将数据插入 Azure 表。<br/>允许的值为 integer（行数）。 |否（默认值为 10,000） |
| writeBatchTimeout |writeBatchSize 或 writeBatchTimeout 命中时，将数据插入 Azure 表。<br/>允许的值为 timespan。 例如“00:20:00”（20 分钟）。 |否（默认值为 90 秒，即存储客户端的默认超时） |

**示例：**

```json
"activities":[
    {
        "name": "CopyToAzureTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Table output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureTableSink",
                "azureTablePartitionKeyName": "<column name>",
                "azureTableRowKeyName": "<column name>"
            }
        }
    }
]
```

### <a name="azuretablepartitionkeyname"></a>azureTablePartitionKeyName

使用 **"translator"** 属性将源列映射到目标列后，才能将目标列用作 azureTablePartitionKeyName。

在以下示例中，源列 DivisionID 映射到目标列 DivisionID：

```json
"translator": {
    "type": "TabularTranslator",
    "columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
}
```

“DivisionID”指定为分区键。

```json
"sink": {
    "type": "AzureTableSink",
    "azureTablePartitionKeyName": "DivisionID"
}
```

## <a name="data-type-mapping-for-azure-table"></a>Azure 表的数据类型映射

从/向 Azure 表复制数据时，以下映射用于从 Azure 表数据类型映射到数据工厂临时数据类型。 若要了解复制活动如何将源架构和数据类型映射到接收器，请参阅[架构和数据类型映射](copy-activity-schema-and-type-mapping.md)。

将数据移入和移出 Azure 表时，[Azure 表定义的以下映射](https://msdn.microsoft.com/library/azure/dd179338.aspx)将从 Azure 表 OData 类型转换为 .NET 类型，反之亦然。

| Azure 表数据类型 | 数据工厂临时数据类型 | 详细信息 |
|:--- |:--- |:--- |
| Edm.Binary |byte[] |一个字节数组，最大 64 KB。 |
| Edm.Boolean |bool |布尔值。 |
| Edm.DateTime |DateTime |一个 64 位值，用协调世界时 (UTC) 表示。 支持的 DateTime 范围从 UTC 公元 (C.E.) 1601 年 1 月 1 日 午夜 12:00 开始。 该范围到 9999 年 12 月 31 日结束。 |
| Edm.Double |double |64 位浮点值。 |
| Edm.Guid |Guid |128 位全局唯一标识符。 |
| Edm.Int32 |Int32 |32 位整数。 |
| Edm.Int64 |Int64 |64 位整数。 |
| Edm.String |String |UTF-16 编码值。 字符串值最大可以为 64 KB。 |

## <a name="next-steps"></a>后续步骤
有关数据工厂中复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。
