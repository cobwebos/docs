---
title: "使用 Azure 数据工厂（Beta 版本）从 Google BigQuery 复制数据 | Microsoft Docs"
description: "了解如何使用数据工厂管道中的复制活动，将数据从 Google BigQuery 复制到支持的接收器数据存储。"
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
ms.date: 01/05/2018
ms.author: jingwang
ms.openlocfilehash: 3b559e64f38727b1e390160515b7614ad1dfaa97
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2018
---
# <a name="copy-data-from-google-bigquery-by-using-azure-data-factory-beta"></a>使用 Azure 数据工厂（Beta 版本）从 Google BigQuery 复制数据

本文概述了如何使用 Azure 数据工厂中的复制活动从 Google BigQuery 复制数据。 本文基于总体概述复制活动的[复制活动概述](copy-activity-overview.md)一文。

> [!NOTE]
> 本文适用于目前处于预览状态的数据工厂版本 2。 如果使用已正式发布的版本 1 的数据工厂服务，请参阅[版本 1 中的复制活动](v1/data-factory-data-movement-activities.md)。

> [!IMPORTANT]
> 此连接器目前处于 Beta 版本。 欢迎试用并提供反馈。 请勿在生产环境中使用该版本。

## <a name="supported-capabilities"></a>支持的功能

可以将数据从 Google BigQuery 复制到任何支持的接收器数据存储。 有关复制活动支持作为源或接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

 数据工厂提供内置驱动程序以启用连接。 因此，无需要手动安装驱动程序即可使用此连接器。

## <a name="get-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started-2](../../includes/data-factory-v2-connector-get-started-2.md)]

对于特定于 Google BigQuery 连接器的数据工厂实体，以下部分提供了有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

Google BigQuery 链接服务支持以下属性。

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | type 属性必须设置为 **GoogleBigQuery**。 | 是 |
| project | 针对其查询的默认 BigQuery 项目的项目 ID。  | 是 |
| additionalProjects | 要访问的公共 BigQuery 项目的项目 ID 逗号分隔列表。  | 否 |
| requestGoogleDriveScope | 是否要请求访问 Google Drive。 允许 Google Drive 访问可支持将 BigQuery 数据与 Google Drive 中的数据组合的联合表。 默认值为 **false**。  | 否 |
| authenticationType | 用于身份验证的 OAuth 2.0 身份验证机制。 ServiceAuthentication 只能在自承载集成运行时上使用。 <br/>允许的值为 **ServiceAuthentication** 和 **UserAuthentication**。 | 是 |
| refreshToken | 从 Google 获得的刷新令牌，用于授权访问 BigQuery 以进行用户身份验证。 可以将此字段标记为 SecureString，以便安全地将其存储在数据工厂中。 还可以将密码存储在 Azure Key Vault 中，并让复制活动在执行数据复制时从该处拉取密码。 若要了解详细信息，请参阅[在 Key Vault 中存储凭据](store-credentials-in-key-vault.md)。 | 否 |
| 电子邮件 | 用于 ServiceAuthentication 的服务帐户电子邮件 ID。 它只能在自承载集成运行时上使用。  | 否 |
| keyFilePath | .p12 密钥文件的完整路径，该文件用于对服务帐户电子邮件地址进行身份验证。 它只能在自承载集成运行时上使用。  | 否 |
| trustedCertPath | 包含受信任 CA 证书（通过 SSL 进行连接时用于验证服务器）的 .pem 文件的完整路径。 仅当在自承载集成运行时上使用 SSL 时，才能设置此属性。 默认值是随集成运行时一起安装的 cacerts.pem 文件。  | 否 |
| useSystemTrustStore | 指定是使用系统信任存储中的 CA 证书还是使用指定 .pem 文件中的 CA 证书。 默认值为 **false**。  | 否 |

**示例：**

```json
{
    "name": "GoogleBigQueryLinkedService",
    "properties": {
        "type": "GoogleBigQuery",
        "typeProperties": {
            "project" : "<project ID>",
            "additionalProjects" : "<additional project IDs>",
            "requestGoogleDriveScope" : true,
            "authenticationType" : "UserAuthentication",
            "refreshToken": {
                 "type": "SecureString",
                 "value": "<refresh token>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各个部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 本部分提供 Google BigQuery 数据集支持的属性列表。

要从 Google BigQuery 复制数据，请将数据集的 type 属性设置为 **GoogleBigQueryObject**。 此类型的数据集中没有任何其他特定于类型的属性。

**示例**

```json
{
    "name": "GoogleBigQueryDataset",
    "properties": {
        "type": "GoogleBigQueryObject",
        "linkedServiceName": {
            "referenceName": "<GoogleBigQuery linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 Google BigQuery 源类型支持的属性列表。

### <a name="googlebigquerysource-as-a-source-type"></a>以 GoogleBigQuerySource 作为源类型

要从 Google BigQuery 复制数据，请将复制活动中的源类型设置为“GoogleBigQuerySource”。 复制活动**源**部分支持以下属性。

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动源的 type 属性必须设置为 **GoogleBigQuerySource**。 | 是 |
| query | 使用自定义 SQL 查询读取数据。 例如 `"SELECT * FROM MyTable"`。 | 是 |

**示例：**

```json
"activities":[
    {
        "name": "CopyFromGoogleBigQuery",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<GoogleBigQuery input dataset name>",
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
                "type": "GoogleBigQuerySource",
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
有关数据工厂中复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。
