---
title: 使用 Azure 数据工厂（Beta 版本）从 Magento 复制数据 | Microsoft Docs
description: 了解如何通过在 Azure 数据工厂管道中使用复制活动，将数据从 Magento 复制到支持的接收器数据存储。
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
ms.openlocfilehash: 30a0a47086a36fcb57c4be5752a3716f11ca7a45
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34617733"
---
# <a name="copy-data-from-magento-using-azure-data-factory-beta"></a>使用 Azure 数据工厂（Beta 版本）从 Magento 复制数据

本文概述了如何使用 Azure 数据工厂中的复制活动从 Magento 复制数据。 它是基于概述复制活动总体的[复制活动概述](copy-activity-overview.md)一文。

> [!NOTE]
> 本文适用于目前处于预览版的数据工厂版本 2。 如果使用正式版 (GA) 1 版本的数据工厂服务，请参阅 [V1 中的复制活动](v1/data-factory-data-movement-activities.md)。

> [!IMPORTANT]
> 此连接器目前处于 Beta 版本。 欢迎试用并提供反馈。 请勿在生产环境中使用该版本。

## <a name="supported-capabilities"></a>支持的功能

可以将数据从 Magento 复制到任何支持的接收器数据存储。 有关复制活动支持作为源/接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

Azure 数据工厂提供内置的驱动程序用于启用连接，因此无需使用此连接器手动安装任何驱动程序。

## <a name="getting-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started-2](../../includes/data-factory-v2-connector-get-started-2.md)]

对于特定于 Magento 连接器的数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

Magento 链接的服务支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | type 属性必须设置为：“Magento” | 是 |
| host | Magento 实例的 URL。 （即 192.168.222.110/magento3）  | 是 |
| accessToken | 来自 Magento 的访问令牌。 将此字段标记为 SecureString 以安全地将其存储在数据工厂中或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 | 是 |
| useEncryptedEndpoints | 指定是否使用 HTTPS 加密数据源终结点。 默认值为 true。  | 否 |
| useHostVerification | 指定通过 SSL 连接时是否需要服务器证书中的主机名匹配服务器的主机名。 默认值为 true。  | 否 |
| usePeerVerification | 指定通过 SSL 连接时是否要验证服务器的标识。 默认值为 true。  | 否 |

**示例：**

```json
{
    "name": "MagentoLinkedService",
    "properties": {
        "type": "Magento",
        "typeProperties": {
            "host" : "192.168.222.110/magento3",
            "accessToken": {
                 "type": "SecureString",
                 "value": "<accessToken>"
            },
            "useEncryptedEndpoints" : true,
            "useHostVerification" : true,
            "usePeerVerification" : true
        }
    }
}
```

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 本部分提供 Magento 数据集支持的属性列表。

要从 Magento 复制数据，请将数据集的 type 属性设置为“MagentoObject”。 此类型的数据集中没有任何其他特定于类型的属性。

**示例**

```json
{
    "name": "MagentoDataset",
    "properties": {
        "type": "MagentoObject",
        "linkedServiceName": {
            "referenceName": "<Magento linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 Magento 源支持的属性列表。

### <a name="magentosource-as-source"></a>作为源的 MagentoSource

要从 Magento 复制数据，请将复制活动中的源类型设置为“MagentoSource”。 复制活动**源**部分支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动源的 type 属性必须设置为：MagentoSource | 是 |
| query | 使用自定义 SQL 查询读取数据。 例如：`"SELECT * FROM Customers"`。 | 是 |

**示例：**

```json
"activities":[
    {
        "name": "CopyFromMagento",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Magento input dataset name>",
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
                "type": "MagentoSource",
                "query": "SELECT * FROM Customers where Id > XXX"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>后续步骤
有关 Azure 数据工厂中复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。
