---
title: 使用 Azure 数据工厂将数据复制到搜索索引 | Microsoft Docs
description: 了解如何使用 Azure 数据工厂管道中的复制活动将数据推送或复制到 Azure 搜索索引。
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
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: 280c91d6a871984959d587e9895166853e89cc45
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34615676"
---
# <a name="copy-data-to-an-azure-search-index-using-azure-data-factory"></a>使用 Azure 数据工厂将数据复制到 Azure 搜索索引

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [版本 1 - 正式版](v1/data-factory-azure-search-connector.md)
> * [版本 2 - 预览版](connector-azure-search.md)

本文概述了如何使用 Azure 数据工厂中的复制活动将数据复制到 Azure 搜索索引。 它是基于概述复制活动总体的[复制活动概述](copy-activity-overview.md)一文。

> [!NOTE]
> 本文适用于目前处于预览版的数据工厂版本 2。 如果使用数据工厂服务版本 1（即正式版 (GA)），请参阅 [V1 中的 Azure 搜索连接器](v1/data-factory-azure-search-connector.md)。

## <a name="supported-capabilities"></a>支持的功能

可以将数据从任何支持的源数据存储复制到 Azure 搜索索引。 有关复制活动支持作为源/接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

## <a name="getting-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started-2](../../includes/data-factory-v2-connector-get-started-2.md)]

对于特定于 Azure 搜索连接器的数据工厂实体，以下部分提供了有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

Azure 搜索链接的服务支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | type 属性必须设置为：AzureSearch | 是 |
| url | Azure 搜索服务的 URL。 | 是 |
| key | Azure 搜索服务的管理密钥。 将此字段标记为 SecureString 以安全地将其存储在数据工厂中或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 | 是 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如果数据存储位于专用网络，则可以使用 Azure 集成运行时或自承载集成运行时。 如果未指定，则使用默认 Azure 集成运行时。 |否 |

> [!IMPORTANT]
> 将数据从云数据存储复制到 Azure 搜索索引时，需要使用 connectVia 中的显式区域在 Azure 搜索链接服务中引用 Azure 集成运行时。 将此区域设置为 Azure 搜索所在的位置。 从 [Azure Integration Runtime](concepts-integration-runtime.md#azure-integration-runtime) 了解更多信息。

**示例：**

```json
{
    "name": "AzureSearchLinkedService",
    "properties": {
        "type": "AzureSearch",
        "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": {
                "type": "SecureString",
                "value": "<AdminKey>"
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

有关可用于定义数据集的各个部分和属性的完整列表，请参阅数据集一文。 本部分提供 Azure 搜索数据集支持的属性列表。

要将数据复制到 Azure 搜索，请将数据集的 type 属性设置为“RelationalTable”。 支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为：AzureSearchIndex | 是 |
| indexName | Azure 搜索索引的名称。 数据工厂不创建索引。 索引必须存在于 Azure 搜索中。 | 是 |

**示例：**

```json
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "linkedServiceName": {
            "referenceName": "<Azure Search linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties" : {
            "indexName": "products"
        }
   }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 Azure 搜索源支持的属性列表。

### <a name="azure-search-as-sink"></a>Azure 搜索作为接收器

要将数据复制到 Azure 搜索，请将复制活动中的源类型设置为“AzureSearchIndexSink”。 复制活动接收器部分中支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动源的 type 属性必须设置为：AzureSearchIndexSink | 是 |
| writeBehavior | 指定索引中已存在文档时要合并还是替换该文档。 请参阅 [WriteBehavior 属性](#writebehavior-property)。<br/><br/>允许的值为：**Merge**（默认）和**Upload**。 | 否 |
| writeBatchSize | 缓冲区大小达到 writeBatchSize 时会数据上传到 Azure 搜索索引。 有关详细信息，请参阅 [WriteBatchSize 属性](#writebatchsize-property)。<br/><br/>允许的值为：整数 1 到 1,000；默认值为 1000。 | 否 |

### <a name="writebehavior-property"></a>WriteBehavior 属性

AzureSearchSink 在写入数据时执行 upsert 操作。 换言之，编写文档时，如果 Azure 搜索索引中已存在文档键，则 Azure 搜索会更新现有文档，而不引发冲突异常。

AzureSearchSink（通过使用 AzureSearch SDK）提供以下两种 upsert 行为：

- **合并**：合并新文档和现有文档中的所有列。 对于新文档中具有 null 值的列，会在现有文档列中保留该值。
- **上传**：新文档替换现有文档。 对于未在新文档中指定的列，无论现有文档中是否存在非 null 值，均将该值设置为 null。

默认行为是**合并**。

### <a name="writebatchsize-property"></a>WriteBatchSize 属性

Azure 搜索服务支持成批编写文档。 每批次可包含 1 到 1,000 个操作。 每个操作处理一个文档以执行上传/合并操作。

**示例：**

```json
"activities":[
    {
        "name": "CopyToAzureSearch",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Search output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureSearchIndexSink",
                "writeBehavior": "Merge"
            }
        }
    }
]
```

### <a name="data-type-support"></a>数据类型支持

下表指定是否支持某个 Azure 搜索数据类型。

| Azure 搜索数据类型 | 在 Azure 搜索接收器中受到支持 |
| ---------------------- | ------------------------------ |
| String | Y |
| Int32 | Y |
| Int64 | Y |
| Double | Y |
| 布尔 | Y |
| DataTimeOffset | Y |
| String Array | N |
| GeographyPoint | N |

## <a name="next-steps"></a>后续步骤
有关 Azure 数据工厂中复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md##supported-data-stores-and-formats)。
