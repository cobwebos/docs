---
title: 使用 Azure 数据工厂从 Google AdWords 复制数据
description: 了解如何通过在 Azure 数据工厂管道中使用复制活动，将数据从 Google AdWords 复制到支持的接收器数据存储。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: jingwang
ms.openlocfilehash: cc620e4dd7a84a85fb8d7bc8466a7a3fd116416e
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680950"
---
# <a name="copy-data-from-google-adwords-using-azure-data-factory"></a>使用 Azure 数据工厂从 Google AdWords 复制数据

本文概述了如何使用 Azure 数据工厂中的复制活动从 Google AdWords 复制数据。 本文基于概述复制活动总体的[复制活动概述](copy-activity-overview.md)一文。

## <a name="supported-capabilities"></a>支持的功能

以下活动支持此 Google AdWords 连接器：

- 带有[支持的源或接收器矩阵](copy-activity-overview.md)的[复制活动](copy-activity-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)


可以将数据从 Google AdWords 复制到任何支持的接收器数据存储。 有关复制活动支持作为源/接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

Azure 数据工厂提供内置的驱动程序用于启用连接，因此无需使用此连接器手动安装任何驱动程序。

## <a name="getting-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

对于特定于 Google AdWords 连接器的数据工厂实体，以下部分提供了有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

Google AdWords 链接服务支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | type 属性必须设置为 **GoogleAdWords**。 | 是 |
| clientCustomerID | 需提取其报表数据的 AdWords 帐户的客户端客户 ID。  | 是 |
| developerToken | 与管理员帐户相关联的开发人员令牌，该帐户用来授予对 AdWords API 的访问权限。  可选择将此字段标记为 SecureString，将其安全地存储在 ADF 中，或在 Azure Key Vault 中存储密码，并允许 ADF 复制活动在执行数据复制时从此处拉取（请参阅[在 Key Vault 中存储凭据](store-credentials-in-key-vault.md)了解详细信息）。 | 是 |
| authenticationType | 用于身份验证的 OAuth 2.0 身份验证机制。 ServiceAuthentication 只能在自托管 IR 上使用。 <br/>允许的值为：**ServiceAuthentication**、**UserAuthentication** | 是 |
| refreshToken | 从 Google 获得的刷新令牌，用于授权访问 AdWords 以进行用户身份验证。 可选择将此字段标记为 SecureString，将其安全地存储在 ADF 中，或在 Azure Key Vault 中存储密码，并允许 ADF 复制活动在执行数据复制时从此处拉取（请参阅[在 Key Vault 中存储凭据](store-credentials-in-key-vault.md)了解详细信息）。 | 否 |
| clientId | 用于获取刷新令牌的 Google 应用程序的客户端 ID。 可选择将此字段标记为 SecureString，将其安全地存储在 ADF 中，或在 Azure Key Vault 中存储密码，并允许 ADF 复制活动在执行数据复制时从此处拉取（请参阅[在 Key Vault 中存储凭据](store-credentials-in-key-vault.md)了解详细信息）。 | 否 |
| clientSecret | Google 应用程序的客户端机密，用户获取刷新令牌。 可选择将此字段标记为 SecureString，将其安全地存储在 ADF 中，或在 Azure Key Vault 中存储密码，并允许 ADF 复制活动在执行数据复制时从此处拉取（请参阅[在 Key Vault 中存储凭据](store-credentials-in-key-vault.md)了解详细信息）。 | 否 |
| email | 用于 ServiceAuthentication 的服务帐户电子邮件 ID，只能在自托管 IR 上使用。  | 否 |
| keyFilePath | .p12 密钥文件的完整路径，该文件用于对服务帐户电子邮件地址进行身份验证，只能在自托管 IR 上使用。  | 否 |
| trustedCertPath | 包含受信任 CA 证书（通过 SSL 进行连接时用于验证服务器）的 .pem 文件的完整路径。 只有在自托管 IR 上使用 SSL 时才能设置此属性。 默认值是随 IR 一起安装的 cacerts.pem 文件。  | 否 |
| useSystemTrustStore | 指定是使用系统信任存储中的 CA 证书还是使用指定 PEM 文件中的 CA 证书。 默认值为 false。  | 否 |

**示例：**

```json
{
    "name": "GoogleAdWordsLinkedService",
    "properties": {
        "type": "GoogleAdWords",
        "typeProperties": {
            "clientCustomerID" : "<clientCustomerID>",
            "developerToken": {
                "type": "SecureString",
                "value": "<developerToken>"
            },
            "authenticationType" : "ServiceAuthentication",
            "refreshToken": {
                "type": "SecureString",
                "value": "<refreshToken>"
            },
            "clientId": {
                "type": "SecureString",
                "value": "<clientId>"
            },
            "clientSecret": {
                "type": "SecureString",
                "value": "<clientSecret>"
            },
            "email" : "<email>",
            "keyFilePath" : "<keyFilePath>",
            "trustedCertPath" : "<trustedCertPath>",
            "useSystemTrustStore" : true,
        }
    }
}

```

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 本部分提供 Google AdWords 数据集支持的属性列表。

若要从 Google AdWords 复制数据，请将数据集的 type 属性设置为 **GoogleAdWordsObject**。 支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为： **GoogleAdWordsObject** | 是 |
| tableName | 表名称。 | 否（如果指定了活动源中的“query”） |

**示例**

```json
{
    "name": "GoogleAdWordsDataset",
    "properties": {
        "type": "GoogleAdWordsObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<GoogleAdWords linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}

```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各节和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 Google AdWords 数据源支持的属性列表。

### <a name="google-adwords-as-source"></a>充当源的 Google AdWords

若要从 Google AdWords 复制数据，请将复制活动中的源类型设置为“GoogleAdWordsSource”。 复制活动**source**部分支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动源的 type 属性必须设置为 **GoogleAdWordsSource** | 是 |
| 查询 | 使用自定义 SQL 查询读取数据。 例如：`"SELECT * FROM MyTable"`。 | 否（如果指定了数据集中的“tableName”） |

**示例：**

```json
"activities":[
    {
        "name": "CopyFromGoogleAdWords",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<GoogleAdWords input dataset name>",
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
                "type": "GoogleAdWordsSource",
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
有关 Azure 数据工厂中复制活动支持作为源和接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。
