---
title: "使用 Azure 数据工厂（Beta 版本）从 HubSpot 复制数据 | Microsoft Docs"
description: "了解如何通过在 Azure 数据工厂管道中使用复制活动，将数据从 HubSpot 复制到支持的接收器数据存储。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/30/2017
ms.author: jingwang
ms.openlocfilehash: 6fbc4644ca7d42f40024739b48c12968e67c7fee
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/12/2017
---
# <a name="copy-data-from-hubspot-using-azure-data-factory-beta"></a>使用 Azure 数据工厂（Beta 版本）从 HubSpot 复制数据

本文概述了如何使用 Azure 数据工厂中的复制活动从 HubSpot 复制数据。 它是基于概述复制活动总体的[复制活动概述](copy-activity-overview.md)一文。

> [!NOTE]
> 本文适用于目前处于预览状态的数据工厂版本 2。 如果使用正式版 (GA) 1 版本的数据工厂服务，请参阅 [V1 中的复制活动](v1/data-factory-data-movement-activities.md)。

> [!IMPORTANT]
> 此连接器目前处于 Beta 版本。 可以进行试用并向我们提供反馈。 不要在生产环境中使用它。

## <a name="supported-capabilities"></a>支持的功能

可以将数据从 HubSpot 复制到任何支持的接收器数据存储。 有关复制活动支持作为源/接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

Azure 数据工厂提供内置的驱动程序用于启用连接，因此无需使用此连接器手动安装任何驱动程序。

## <a name="getting-started"></a>入门

可以使用 .NET SDK、Python SDK、Azure PowerShell、REST API 或 Azure 资源管理器模板创建包含复制活动的管道。 有关创建包含复制活动的管道的分步说明，请参阅[复制活动教程](quickstart-create-data-factory-dot-net.md)。

对于特定于 HubSpot 连接器的数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

HubSpot 链接服务支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | type 属性必须设置为：**Hubspot** | 是 |
| clientId | 与 Hubspot 应用程序关联的客户端 ID。  | 是 |
| clientSecret | 与 Hubspot 应用程序关联的客户端密码。 可以选择将此字段标记为 SecureString 以将其安全地存储在 ADF 中，或在 Azure Key Vault 中存储密码，并允许复制活动在执行数据复制时从此处拉取 - 从[在 Key Vault 中存储凭据](store-credentials-in-key-vault.md)了解详细信息。 | 是 |
| accessToken | 最初进行 OAuth 集成身份验证时获得的访问令牌。 可以选择将此字段标记为 SecureString 以将其安全地存储在 ADF 中，或在 Azure Key Vault 中存储密码，并允许复制活动在执行数据复制时从此处拉取 - 从[在 Key Vault 中存储凭据](store-credentials-in-key-vault.md)了解详细信息。 | 是 |
| refreshToken | 最初进行 OAuth 集成身份验证时获得的刷新令牌。 可以选择将此字段标记为 SecureString 以将其安全地存储在 ADF 中，或在 Azure Key Vault 中存储密码，并允许复制活动在执行数据复制时从此处拉取 - 从[在 Key Vault 中存储凭据](store-credentials-in-key-vault.md)了解详细信息。 | 是 |
| useEncryptedEndpoints | 指定是否使用 HTTPS 加密数据源终结点。 默认值为 true。  | 否 |
| useHostVerification | 指定通过 SSL 连接时是否需要服务器证书中的主机名匹配服务器的主机名。 默认值为 true。  | 否 |
| usePeerVerification | 指定通过 SSL 连接时是否要验证服务器的标识。 默认值为 true。  | 否 |

**示例：**

```json
{
    "name": "HubspotLinkedService",
    "properties": {
        "type": "Hubspot",
        "typeProperties": {
            "clientId" : "<clientId>",
            "clientSecret": {
                 "type": "SecureString",
                 "value": "<clientSecret>"
            },
            "accessToken": {
                 "type": "SecureString",
                 "value": "<accessToken>"
            },
            "refreshToken": {
                 "type": "SecureString",
                 "value": "<refreshToken>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 本部分提供 HubSpot 数据集支持的属性列表。

要从 HubSpot 复制数据，请将数据集的 type 属性设置为“HubspotObject”。 在此类型的数据集中没有任何其他特定于类型的属性。

**示例**

```json
{
    "name": "HubspotDataset",
    "properties": {
        "type": "HubspotObject",
        "linkedServiceName": {
            "referenceName": "<Hubspot linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各个部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 HubSpot 数据源支持的属性列表。

### <a name="hubspotsource-as-source"></a>以 HubspotSource 作为源

要从 HubSpot 复制数据，请将复制活动中的源类型设置为“HubspotSource”。 复制活动**源**部分支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动源的 type 属性必须设置为：**HubspotSource** | 是 |
| query | 使用自定义 SQL 查询读取数据。 例如：`"SELECT * FROM Companies where Company_Id = xxx"`。 | 是 |

**示例：**

```json
"activities":[
    {
        "name": "CopyFromHubspot",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Hubspot input dataset name>",
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
                "type": "HubspotSource",
                "query": "SELECT * FROM Companies where Company_Id = xxx"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>后续步骤
有关 Azure 数据工厂中复制活动支持作为源和接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。
