---
title: 使用 Azure 数据工厂从/向 Salesforce 复制数据 | Microsoft Docs
description: 了解如何通过在数据工厂管道中使用复制活动，将数据从 Salesforce 复制到支持的接收器数据存储，或者从支持的源数据存储复制到 Salesforce。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/19/2019
ms.author: jingwang
ms.openlocfilehash: 6056df9aa9079887bfb06ca20ad564eb52baff38
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60546523"
---
# <a name="copy-data-from-and-to-salesforce-by-using-azure-data-factory"></a>使用 Azure 数据工厂从/向 Salesforce 复制数据
> [!div class="op_single_selector" title1="选择在使用数据工厂服务版本："]
> * [版本 1](v1/data-factory-salesforce-connector.md)
> * [当前版本](connector-salesforce.md)

本文概述如何使用 Azure 数据工厂中的复制活动从/向 Salesforce 复制数据。 本文基于总体概述复制活动的[复制活动概述](copy-activity-overview.md)一文。

## <a name="supported-capabilities"></a>支持的功能

可以将数据从 Salesforce 复制到任何支持的接收器数据存储。 还可以将数据从任何支持的源数据存储复制到 Salesforce。 有关复制活动支持作为源或接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

具体而言，Salesforce 连接器支持：

- Salesforce 开发人员版、专业版、企业版或不受限制版。
- 从/向 Salesforce 生产、沙盒和自定义域复制数据。

Salesforce 连接器具有基于 Salesforce REST/大容量 API， [v45](https://developer.salesforce.com/docs/atlas.en-us.218.0.api_rest.meta/api_rest/dome_versions.htm)有关的数据复制并[v40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api_asynch.meta/api_asynch/asynch_api_intro.htm)用于将数据复制到。

## <a name="prerequisites"></a>必备组件

在 Salesforce 中，必须启用 API 权限。 有关详细信息，请参阅[通过权限集启用 Salesforce 中的 API 访问权限](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)

## <a name="salesforce-request-limits"></a>Salesforce 请求限制

Salesforce 对 API 请求总数和并发 API 请求均有限制。 请注意以下几点：

- 如果并发请求数超过限制，则将进行限制并且会看到随机失败。
- 如果请求总数超过限制，会阻止 Salesforce 帐户 24 小时。

在这两种情况下，还可能会收到“REQUEST_LIMIT_EXCEEDED”错误消息。 有关详细信息，请参阅 [Salesforce 开发人员限制](https://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf)中的“API 请求限制”部分。

## <a name="get-started"></a>开始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

对于特定于 Salesforce 连接器的数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

Salesforce 链接服务支持以下属性。

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type |类型属性必须设置为 **Salesforce**。 |是 |
| environmentUrl | 指定 Salesforce 实例的 URL。 <br> - 默认为 `"https://login.salesforce.com"`。 <br> - 要从沙盒复制数据，请指定 `"https://test.salesforce.com"`。 <br> - 要从自定义域复制数据，请指定 `"https://[domain].my.salesforce.com"`（以此为例）。 |否 |
| username |为用户帐户指定用户名。 |是 |
| password |指定用户帐户的密码。<br/><br/>将此字段标记为 SecureString 以安全地将其存储在数据工厂中或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 |是 |
| securityToken |为用户帐户指定安全令牌。 有关如何重置和获取安全令牌的说明，请参阅[获取安全令牌](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm)。 若要了解有关安全令牌的一般信息，请参阅 [Security and the API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm)（安全性和 API）。<br/><br/>将此字段标记为 SecureString 以安全地将其存储在数据工厂中或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 |是 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如果未指定，则使用默认 Azure Integration Runtime。 | 对于源为“否”，对于接收器为“是”（如果源链接服务没有集成运行时） |

>[!IMPORTANT]
>将数据复制到 Salesforce 时，不能使用默认 Azure 集成运行时执行复制。 换而言之，如果源链接服务未指定集成运行时，请使用靠近 Salesforce 实例的位置显式[创建 Azure 集成运行时](create-azure-integration-runtime.md#create-azure-ir)。 按如下示例所示关联 Salesforce 链接服务。

示例：**在数据工厂中存储凭据**

```json
{
    "name": "SalesforceLinkedService",
    "properties": {
        "type": "Salesforce",
        "typeProperties": {
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            },
            "securityToken": {
                "type": "SecureString",
                "value": "<security token>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

示例：**在密钥保管库中存储凭据**

```json
{
    "name": "SalesforceLinkedService",
    "properties": {
        "type": "Salesforce",
        "typeProperties": {
            "username": "<username>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name of password in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            },
            "securityToken": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name of security token in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
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

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 本部分提供 Salesforce 数据集支持的属性列表。

要从/向 Salesforce 复制数据，请将数据集的 type 属性设置为 **SalesforceObject**。 支持以下属性。

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | type 属性必须设置为 **SalesforceObject**。  | 是 |
| objectApiName | 要从中检索数据的 Salesforce 对象名称。 | 对于源为“No”，对于接收器为“Yes” |

> [!IMPORTANT]
> 任何自定义对象均需要 **API 名称**的“__c”部分。

![数据工厂 Salesforce 连接 API 名称](media/copy-data-from-salesforce/data-factory-salesforce-api-name.png)

**示例：**

```json
{
    "name": "SalesforceDataset",
    "properties": {
        "type": "SalesforceObject",
        "linkedServiceName": {
            "referenceName": "<Salesforce linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "objectApiName": "MyTable__c"
        }
    }
}
```

>[!NOTE]
>为了向后兼容：从 Salesforce 复制数据时，如果使用以前的“RelationalTable”类型数据集，它会在你看到切换到新的“SalesforceObject”类型的建议时继续工作。

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为 **RelationalTable**。 | 是 |
| tableName | 在 Salesforce 中表的名称。 | 否（如果指定了活动源中的“query”） |

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 Salesforce 源和接收器支持的属性列表。

### <a name="salesforce-as-a-source-type"></a>将 Salesforce 用作源类型

要从 Salesforce 复制数据，请将复制活动中的源类型设置为“SalesforceSource”  。 复制活动的 **source** 节支持以下属性。

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动源的 type 属性必须设置为 **SalesforceSource**。 | 是 |
| query |使用自定义查询读取数据。 可以使用 [Salesforce 对象查询语言 (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) 查询或 SQL-92 查询。 请在[查询提示](#query-tips)部分中查看更多提示。 如果未指定查询，将检索在数据集的“objectApiName”中指定的 Salesforce 对象的所有数据。 | 否（如果指定了数据集中的“objectApiName”） |
| readBehavior | 指示是查询现有记录，还是查询包括已删除记录在内的所有记录。 如果未指定，默认行为是前者。 <br>允许的值：**query**（默认值）、**queryAll**。  | 否 |

> [!IMPORTANT]
> 任何自定义对象均需要 **API 名称**的“__c”部分。

![数据工厂 Salesforce 连接 API 名称列表](media/copy-data-from-salesforce/data-factory-salesforce-api-name-2.png)

**示例：**

```json
"activities":[
    {
        "name": "CopyFromSalesforce",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Salesforce input dataset name>",
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
                "type": "SalesforceSource",
                "query": "SELECT Col_Currency__c, Col_Date__c, Col_Email__c FROM AllDataType__c"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

>[!NOTE]
>为了向后兼容：从 Salesforce 复制数据时，如果使用以前的“RelationalSource”类型副本，则当你看到切换到新的“SalesforceSource”类型的建议时，该源会继续工作。

### <a name="salesforce-as-a-sink-type"></a>将 Salesforce 用作接收器类型

要向 Salesforce 复制数据，请将复制活动中的接收器类型设置为“SalesforceSink”  。 复制活动 **sink** 节支持以下属性。

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动接收器的 type 属性必须设置为 **SalesforceSink**。 | 是 |
| writeBehavior | 操作写入行为。<br/>允许的值为 **Insert** 和 **Upsert**。 | 否（默认值为 Insert） |
| externalIdFieldName | 更新插入操作的外部的 ID 字段名称。 指定的字段必须在 Salesforce 对象中定义为“外部 Id 字段”。 它相应的输入数据中不能有 NULL 值。 | 对于“Upsert”是必需的 |
| writeBatchSize | 每批中写入到 Salesforce 的数据行计数。 | 否（默认值为5,000） |
| ignoreNullValues | 指示是否忽略 NULL 值从输入数据期间写入操作。<br/>允许的值为 **true** 和 **false**。<br>- **True**：执行更新插入或更新操作时，保持目标对象中的数据不变。 插入在执行插入操作时定义的默认值。<br/>- **False**：执行更新插入或更新操作时，将目标对象中的数据更新为 NULL。 执行插入操作时插入 NULL 值。 | 否（默认值为 false） |

示例：**复制活动中的 Salesforce 接收器**

```json
"activities":[
    {
        "name": "CopyToSalesforce",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Salesforce output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SalesforceSink",
                "writeBehavior": "Upsert",
                "externalIdFieldName": "CustomerId__c",
                "writeBatchSize": 10000,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="query-tips"></a>查询提示

### <a name="retrieve-data-from-a-salesforce-report"></a>从 Salesforce 报表检索数据

可通过将查询指定为 `{call "<report name>"}` 从 Salesforce 报表检索数据。 例如 `"query": "{call \"TestReport\"}"`。

### <a name="retrieve-deleted-records-from-the-salesforce-recycle-bin"></a>从 Salesforce 回收站中检索删除的记录

若要从 Salesforce 回收站中查询软删除的记录，需要将 `readBehavior` 指定为 `queryAll`。 

### <a name="difference-between-soql-and-sql-query-syntax"></a>SOQL 与 SQL 查询语法之间的差异

从 Salesforce 中复制数据时，可以使用 SOQL 查询或 SQL 查询。 请注意，这两者具有不同的语法和功能支持，不要混用。 建议使用 Salesforce 原本就支持的 SOQL 查询。 下表列出了主要差异：

| 语法 | SOQL 模式 | SQL 模式 |
|:--- |:--- |:--- |
| 列选择 | 需要枚举要在查询中，例如复制的字段 `SELECT field1, filed2 FROM objectname` | 除了列选择之外，还支持 `SELECT *`。 |
| 引号 | 字段/对象名称不能用引号引起来。 | 字段/对象名称可以用引号引起来，例如 `SELECT "id" FROM "Account"` |
| 日期时间格式 |  请参考[此处的](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql_select_dateformats.htm)详细信息和下一部分中的示例。 | 请参考[此处的](https://docs.microsoft.com/sql/odbc/reference/develop-app/date-time-and-timestamp-literals?view=sql-server-2017)详细信息和下一部分中的示例。 |
| 布尔值 | 表示为 `False` 和 `True`，例如 `SELECT … WHERE IsDeleted=True`。 | 表示为 0 或 1，例如 `SELECT … WHERE IsDeleted=1`。 |
| 列重命名 | 不支持。 | 支持，例如：`SELECT a AS b FROM …`。 |
| 关系 | 支持，例如 `Account_vod__r.nvs_Country__c`。 | 不支持。 |

### <a name="retrieve-data-by-using-a-where-clause-on-the-datetime-column"></a>使用 DateTime 列上的 where 子句检索数据

当指定 SOQL 或 SQL 查询时，请注意 DateTime 的格式差异。 例如：

* **SOQL 示例**：`SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= @{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-ddTHH:mm:ssZ')} AND LastModifiedDate < @{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-ddTHH:mm:ssZ')}`
* **SQL 示例**：`SELECT * FROM Account WHERE LastModifiedDate >= {ts'@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}'} AND LastModifiedDate < {ts'@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'}`

### <a name="error-of-malformedquerytruncated"></a>错误 MALFORMED_QUERY： 截断

如果您遇到的错误"MALFORMED_QUERY:截断"，通常是由于在数据中具有 JunctionIdList 类型列和 Salesforce 有上支持此类数据与大量的行的限制。 若要缓解问题，请尝试 JunctionIdList 列中排除或限制要复制的行数 （可以分区到多个复制活动运行）。

## <a name="data-type-mapping-for-salesforce"></a>Salesforce 的数据类型映射

从 Salesforce 复制数据时，以下映射用于从 Salesforce 数据类型映射到数据工厂临时数据类型。 若要了解复制活动如何将源架构和数据类型映射到接收器，请参阅[架构和数据类型映射](copy-activity-schema-and-type-mapping.md)。

| Salesforce 数据类型 | 数据工厂临时数据类型 |
|:--- |:--- |
| Auto Number |String |
| Checkbox |Boolean |
| Currency |Decimal |
| Date |DateTime |
| Date/Time |DateTime |
| Email |String |
| Id |String |
| Lookup Relationship |String |
| Multi-Select Picklist |String |
| Number |Decimal |
| Percent |Decimal |
| Phone |String |
| Picklist |String |
| Text |String |
| Text Area |String |
| Text Area (Long) |String |
| Text Area (Rich) |String |
| Text (Encrypted) |String |
| URL |String |

## <a name="next-steps"></a>后续步骤
有关数据工厂中复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。
