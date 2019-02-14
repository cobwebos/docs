---
title: 使用 Azure 数据工厂向/从 Azure 数据资源管理器复制数据 | Microsoft Docs
description: 了解如何通过在 Azure 数据工厂管道中使用复制活动来向/从 Azure 数据资源管理器复制数据。
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
ms.date: 02/01/2019
ms.author: orspod
ms.openlocfilehash: 8f2a7a953ce2964645c281d9454a73b0cf1a8ff6
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2019
ms.locfileid: "55747182"
---
# <a name="copy-data-to-or-from-azure-data-explorer-using-azure-data-factory"></a>使用 Azure 数据工厂向/从 Azure 数据资源管理器复制数据

本文概述了如何在 Azure 数据工厂中使用复制活动向/从 [Azure 数据资源管理器](../data-explorer/data-explorer-overview.md)复制数据。 它是基于概述复制活动总体的[复制活动概述](copy-activity-overview.md)一文。

## <a name="supported-capabilities"></a>支持的功能

可以将数据从任何受支持的源数据存储复制到 Azure 数据资源管理器。 可以将数据从 Azure 数据资源管理器复制到任何受支持的接收器数据存储。 有关复制活动支持作为源或接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md)表。

>[!NOTE]
>目前，尚不支持使用自承载集成运行时在 Azure 数据资源管理器与本地数据存储之间复制数据。

Azure 数据资源管理器连接器可用来执行以下操作：

* 将 Azure Active Directory (Azure AD) 应用程序令牌身份验证与**服务主体**配合使用来复制数据。
* 作为源时，请使用 KQL (Kusto) 查询来检索数据。
* 作为接收器时，请将数据追加到目标表。

## <a name="getting-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

对于特定于 Azure 数据资源管理器连接器的数据工厂实体，以下部分提供了有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

Azure 数据资源管理器链接服务支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | **type** 属性必须设置为 **AzureDataExplorer** | 是 |
| endpoint | Azure 数据资源管理器群集的终结点 URL，格式为 `https://<clusterName>.<regionName>.kusto.windows.net `。 | 是 |
| database | 数据库的名称。 | 是 |
| tenant | 指定应用程序的租户信息（域名或租户 ID）。 可以将鼠标指针悬停在 Azure 门户右上角来检索它。 | 是 |
| servicePrincipalId | 指定应用程序的客户端 ID。 | 是 |
| servicePrincipalKey | 指定应用程序的密钥。 将此字段标记为 **SecureString** 以安全地将其存储在数据工厂中或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 | 是 |

**链接服务属性示例：**

```json
{
    "name": "AzureDataExplorerLinkedService",
    "properties": {
        "type": "AzureDataExplorer",
        "typeProperties": {
            "endpoint": "https://<clusterName>.<regionName>.kusto.windows.net ",
            "database": "<database name>",
            "tenant": "<tenant name/id e.g. microsoft.onmicrosoft.com>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 本部分提供了 Azure 数据资源管理器数据集支持的属性列表。

若要将数据复制到 Azure 数据资源管理器，请将数据集的 type 属性设置为 **AzureDataExplorerTable**。

支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | **type** 属性必须设置为 **AzureDataExplorerTable** | 是 |
| table | 链接服务引用的表的名称。 | 对于接收器为必需的，对于源不是必需的 |

**数据集属性示例**

```json
{
   "name": "AzureDataExplorerDataset",
    "properties": {
        "type": "AzureDataExplorerTable",
        "linkedServiceName": {
            "referenceName": "<Azure Data Explorer linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "table": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供了 Azure 数据资源管理器源和接收器支持的属性列表。

### <a name="azure-data-explorer-as-source"></a>Azure 数据资源管理器作为源

若要从 Azure 数据资源管理器复制数据，请将复制活动源中的 **type** 属性设置为 **AzureDataExplorerSource**。 复制活动源部分支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 复制活动源的 **type** 属性必须设置为：**AzureDataExplorerSource** | 是 |
| query | 以 [KQL 格式](/azure/kusto/query/)指定的只读请求。 使用自定义 KQL 查询作为参考。 | 是 |
| queryTimeout | 查询请求超时前的等待时间。默认值是 10 分钟 (00:10:00)；允许的最大值是 1 小时 (01:00:00)。 | 否 |

**示例：**

```json
"activities":[
    {
        "name": "CopyFromAzureDataExplorer",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "AzureDataExplorerSource",
                "query": "TestTable1 | take 10",
                "queryTimeout": "00:10:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        },
        "inputs": [
            {
                "referenceName": "<Azure Data Explorer input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

### <a name="azure-data-explorer-as-sink"></a>Azure 数据资源管理器作为接收器

若要将数据复制到 Azure 数据资源管理器，请将复制活动接收器中的 type 属性设置为 **AzureDataExplorerSink**。 复制活动接收器部分中支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 复制活动接收器的 **type** 属性必须设置为：**AzureDataExplorerSink** | 是 |
| ingestionMappingName | 基于 Kusto 表预先创建的 [csv 映射](/azure/kusto/management/mappings#csv-mapping)的名称。 若要将源中的列映射到 Azure 数据资源管理器，还可以使用复制活动[列映射](copy-activity-schema-and-type-mapping.md)。 | 否 |

**示例：**

```json
"activities":[
    {
        "name": "CopyToAzureDataExplorer",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDataExplorerSink",
                "ingestionMappingName": "<optional csv mapping name>"
            }
        },
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Data Explorer output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="next-steps"></a>后续步骤
有关 Azure 数据工厂中复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。