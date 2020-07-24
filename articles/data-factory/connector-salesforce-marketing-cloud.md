---
title: 从 Salesforce Marketing Cloud 复制数据
description: 了解如何通过在 Azure 数据工厂管道中使用复制活动，将数据从 Salesforce Marketing Cloud 复制到支持的接收器数据存储。
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/17/2020
ms.openlocfilehash: 1f0fb1ee8580c0c7f6eb30228b65e0a3780ef0a8
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87076803"
---
# <a name="copy-data-from-salesforce-marketing-cloud-using-azure-data-factory"></a>使用 Azure 数据工厂从 Salesforce Marketing Cloud 复制数据

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述了如何使用 Azure 数据工厂中的复制活动从 Salesforce Marketing Cloud 复制数据。 它是基于概述复制活动总体的[复制活动概述](copy-activity-overview.md)一文。

## <a name="supported-capabilities"></a>支持的功能

以下活动支持此 Salesforce Marketing Cloud 连接器：

- 带有[支持的源或接收器矩阵](copy-activity-overview.md)的[复制活动](copy-activity-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)

可以将数据从 Salesforce Marketing Cloud 复制到任何支持的接收器数据存储。 有关复制活动支持作为源/接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

Salesforce 营销云连接器支持 OAuth 2 身份验证，并且支持旧包类型和增强包类型。 此连接器基于[Salesforce 营销云 REST API](https://developer.salesforce.com/docs/atlas.en-us.mc-apis.meta/mc-apis/index-api.htm)。

>[!NOTE]
>此连接器不支持检索自定义对象或自定义数据扩展。

## <a name="getting-started"></a>入门

可以使用 .NET SDK、Python SDK、Azure PowerShell、REST API 或 Azure 资源管理器模板创建包含复制活动的管道。 有关创建包含复制活动的管道的分步说明，请参阅[复制活动教程](quickstart-create-data-factory-dot-net.md)。

对于特定于 Salesforce Marketing Cloud 连接器的数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

Salesforce Marketing Cloud 链接的服务支持以下属性：

| 属性 | 说明 | 必须 |
|:--- |:--- |:--- |
| type | type 属性必须设置为：**SalesforceMarketingCloud** | 是 |
| connectionProperties | 定义如何连接到 Salesforce 营销云的一组属性。 | 是 |
| 在 `connectionProperties` 下： | | |
| authenticationType | 指定要使用的身份验证方法。 允许的值为 `Enhanced sts OAuth 2.0` 或 `OAuth_2.0` 。<br><br>Salesforce 营销云旧包仅支持 `OAuth_2.0` ，同时增强包需求 `Enhanced sts OAuth 2.0` 。 <br>自2019年8月1日起，Salesforce 营销云已经消除了创建旧包的能力。 所有新包都是增强的包。 | 是 |
| host | 对于增强包，主机应是由以字母 "mc" 开头的28个字符的字符串表示的[子域](https://developer.salesforce.com/docs/atlas.en-us.mc-apis.meta/mc-apis/your-subdomain-tenant-specific-endpoints.htm)，例如 `mc563885gzs27c5t9-63k636ttgm` 。 <br>对于旧包，请指定 `www.exacttargetapis.com` 。 | 是 |
| clientId | 与 Salesforce Marketing Cloud 应用程序关联的客户端 ID。  | 是 |
| clientSecret | 与 Salesforce Marketing Cloud 应用程序关联的客户端密码。 你可以选择将此字段标记为 SecureString 以将其安全地存储在 ADF 中，或将机密存储在 Azure Key Vault 中，并让 ADF 复制活动在执行数据复制时从此处拉取-从[在 Key Vault 中存储凭据](store-credentials-in-key-vault.md)了解详细信息。 | 是 |
| useEncryptedEndpoints | 指定是否使用 HTTPS 加密数据源终结点。 默认值为 true。  | 否 |
| useHostVerification | 指定通过 TLS 进行连接时是否要求服务器证书中的主机名与服务器的主机名匹配。 默认值为 true。  | 否 |
| usePeerVerification | 指定通过 TLS 进行连接时是否要验证服务器的标识。 默认值为 true。  | 否 |

**示例：将增强型 STS OAuth 2 身份验证用于增强包** 

```json
{
    "name": "SalesforceMarketingCloudLinkedService",
    "properties": {
        "type": "SalesforceMarketingCloud",
        "typeProperties": {
            "connectionProperties": {
                "host": "<subdomain e.g. mc563885gzs27c5t9-63k636ttgm>",
                "authenticationType": "Enhanced sts OAuth 2.0",
                "clientId": "<clientId>",
                "clientSecret": {
                     "type": "SecureString",
                     "value": "<clientSecret>"
                },
                "useEncryptedEndpoints": true,
                "useHostVerification": true,
                "usePeerVerification": true
            }
        }
    }
}

```

**示例：对旧版包使用 OAuth 2 身份验证** 

```json
{
    "name": "SalesforceMarketingCloudLinkedService",
    "properties": {
        "type": "SalesforceMarketingCloud",
        "typeProperties": {
            "connectionProperties": {
                "host": "www.exacttargetapis.com",
                "authenticationType": "OAuth_2.0",
                "clientId": "<clientId>",
                "clientSecret": {
                     "type": "SecureString",
                     "value": "<clientSecret>"
                },
                "useEncryptedEndpoints": true,
                "useHostVerification": true,
                "usePeerVerification": true
            }
        }
    }
}

```

如果你使用的是具有以下有效负载的 Salesforce 营销云链接服务，则仍将支持它，但建议使用新的方法来添加增强的包支持。

```json
{
    "name": "SalesforceMarketingCloudLinkedService",
    "properties": {
        "type": "SalesforceMarketingCloud",
        "typeProperties": {
            "clientId": "<clientId>",
            "clientSecret": {
                 "type": "SecureString",
                 "value": "<clientSecret>"
            },
            "useEncryptedEndpoints": true,
            "useHostVerification": true,
            "usePeerVerification": true
        }
    }
}

```

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 本部分提供 Salesforce Marketing Cloud 数据集支持的属性列表。

要从 Salesforce Marketing Cloud 复制数据，请将数据集的 type 属性设置为 SalesforceMarketingCloudObject  。 支持以下属性：

| properties | 描述 | 必须 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为：**SalesforceMarketingCloudObject** | 是 |
| tableName | 表的名称。 | 否（如果指定了活动源中的“query”） |

**示例**

```json
{
    "name": "SalesforceMarketingCloudDataset",
    "properties": {
        "type": "SalesforceMarketingCloudObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SalesforceMarketingCloud linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 Salesforce Marketing Cloud 源支持的属性列表。

### <a name="salesforce-marketing-cloud-as-source"></a>Salesforce Marketing Cloud 作为源

要从 Salesforce Marketing Cloud 复制数据，请将复制活动中的源类型设置为 SalesforceMarketingCloudSource  。 复制活动**source**部分支持以下属性：

| properties | 描述 | 必须 |
|:--- |:--- |:--- |
| type | 复制活动 source 的 type 属性必须设置为：**SalesforceMarketingCloudSource** | 是 |
| query | 使用自定义 SQL 查询读取数据。 例如：`"SELECT * FROM MyTable"`。 | 否（如果指定了数据集中的“tableName”） |

**示例：**

```json
"activities":[
    {
        "name": "CopyFromSalesforceMarketingCloud",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SalesforceMarketingCloud input dataset name>",
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
                "type": "SalesforceMarketingCloudSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Lookup 活动属性

若要了解有关属性的详细信息，请查看 [Lookup 活动](control-flow-lookup-activity.md)。

## <a name="next-steps"></a>后续步骤
有关 Azure 数据工厂中复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。
