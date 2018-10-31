---
title: 使用 Azure 数据工厂从 Netezza 复制数据 | Microsoft Docs
description: 了解如何通过在 Azure 数据工厂管道中使用复制活动，将数据从 Netezza 复制到支持的接收器数据存储。
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
ms.date: 06/15/2018
ms.author: jingwang
ms.openlocfilehash: 1b7499990a049f276bf1af9e31b639ea4944d8f7
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2018
ms.locfileid: "49167556"
---
# <a name="copy-data-from-netezza-by-using-azure-data-factory"></a>使用 Azure 数据工厂从 Netezza 复制数据 

本文概述了如何使用 Azure 数据工厂中的复制活动从 Netezza 复制数据。 本文是根据总体概述复制活动的 [Azure 数据工厂中的复制活动](copy-activity-overview.md)编写的。

## <a name="supported-capabilities"></a>支持的功能

可以将数据从 Netezza 复制到任何支持的接收器数据存储。 有关复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储和格式](copy-activity-overview.md#supported-data-stores-and-formats)。

Azure 数据工厂提供内置驱动程序以启用连接。 无需要手动安装任何驱动程序即可使用此连接器。

## <a name="get-started"></a>入门

可以通过使用 .NET SDK、Python SDK、Azure PowerShell、REST API 或 Azure 资源管理器模板创建使用复制活动的管道。 有关如何创建包含复制活动的管道的分步说明，请参阅[复制活动教程](quickstart-create-data-factory-dot-net.md)。

对于特定于 Netezza 连接器的数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

Netezza 链接服务支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | “type”属性必须设置为“Netezza”。 | 是 |
| connectionString | 用于连接到 Netezza 的 ODBC 连接字符串。 将此字段标记为 SecureString 类型，以便安全地将其存储在数据工厂中。 此外，还可以[引用 Azure Key Vault 中存储的机密](store-credentials-in-key-vault.md)。 | 是 |
| connectVia | 用于连接到数据存储的 [ Integration Runtime](concepts-integration-runtime.md)。 如果可以公开访问数据存储，则可以选择自承载集成运行时或 Azure Integration Runtime。 如果未指定，则使用默认 Azure Integration Runtime。 |否 |

典型的连接字符串为 `Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>`。 下表介绍了更多可以设置的属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| SecurityLevel | 驱动程序用于连接到数据存储区的安全级别 (SSL/TLS)。 示例：`SecurityLevel=preferredSecured`。 支持的值是：<br/>-  仅不安全 (onlyUnSecured)：驱动程序不使用 SSL。<br/>-  首选不安全 (preferredUnSecured)（默认）：如果服务器提供了选择，则驱动程序不使用 SSL。 <br/>-  首选安全 (preferredSecured)：如果服务器提供了选择，则驱动程序使用 SSL。 <br/>-  仅安全 (onlySecured)：除非有 SSL 连接，否则驱动程序不会连接。 | 否 |
| CaCertFile | 服务器使用的 SSL 证书的完整路径。 示例： `CaCertFile=<cert path>;`| 是，如果启用了 SSL |

**示例**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": {
                 "type": "SecureString",
                 "value": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>"
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

本部分提供 Netezza 数据集支持的属性列表。

若要查看可用于定义数据集的各部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)。 

若要从 Netezza 复制数据，请将数据集的 type 属性设置为“NetezzaTable”。 此类型的数据集中没有任何其他特定于类型的属性。

**示例**

```json
{
    "name": "NetezzaDataset",
    "properties": {
        "type": "NetezzaTable",
        "linkedServiceName": {
            "referenceName": "<Netezza linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

本部分提供 Netezza 源支持的属性列表。

有关可用于定义活动的各个部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)。 

### <a name="netezza-as-source"></a>以 Netezza 作为源

若要从 Netezza 复制数据，请将复制活动中的 source 类型设置为“NetezzaSource”。 复制活动 **source** 节支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 必须将复制活动源的 type 属性设置为“NetezzaSource”。 | 是 |
| query | 使用自定义 SQL 查询读取数据。 示例： `"SELECT * FROM MyTable"` | 是 |

**示例：**

```json
"activities":[
    {
        "name": "CopyFromNetezza",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Netezza input dataset name>",
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
                "type": "NetezzaSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>后续步骤

有关 Azure 数据工厂中复制活动支持用作源和接收器的数据存储的列表，请参阅[支持的数据存储和格式](copy-activity-overview.md#supported-data-stores-and-formats)。
