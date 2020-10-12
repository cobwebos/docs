---
title: 从 Square 复制数据（预览版）
description: 了解如何通过在 Azure 数据工厂管道中使用复制活动，将数据从 Square 复制到支持的接收器数据存储。
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
ms.date: 08/03/2020
ms.openlocfilehash: 2bfe9115f38c79618924379837dda8014ee31ed5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87529358"
---
# <a name="copy-data-from-square-using-azure-data-factory-preview"></a>使用 Azure 数据工厂（预览版）从 Square 复制数据
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述了如何使用 Azure 数据工厂中的复制活动从 Square 复制数据。 它是基于概述复制活动总体的[复制活动概述](copy-activity-overview.md)一文。

> [!IMPORTANT]
> 此连接器目前提供预览版。 欢迎试用并提供反馈。 若要在解决方案中使用预览版连接器的依赖项，请联系 [Azure 客户支持](https://azure.microsoft.com/support/)。

## <a name="supported-capabilities"></a>支持的功能

以下活动支持此 Square 连接器：

- 带有[支持的源或接收器矩阵](copy-activity-overview.md)的[复制活动](copy-activity-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)

可以将数据从 Square 复制到任何支持的接收器数据存储。 有关复制活动支持作为源/接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

Azure 数据工厂提供内置的驱动程序用于启用连接，因此无需使用此连接器手动安装任何驱动程序。

## <a name="getting-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

对于特定于 Square 连接器的数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

Square 链接的服务支持以下属性：

| 属性 | 说明 | 必须 |
|:--- |:--- |:--- |
| type | type 属性必须设置为：**Square** | 是 |
| connectionProperties | 定义如何连接到 Square 的一组属性。 | 是 |
| 在 `connectionProperties` 下： | | |
| host | Square 实例的 URL。 （即 mystore.mysquare.com）  | 是 |
| clientId | 与 Square 应用程序关联的客户端 ID。  | 是 |
| clientSecret | 与 Square 应用程序关联的客户端密码。 将此字段标记为 SecureString 以安全地将其存储在数据工厂中或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 | 是 |
| accessToken | 从 Square 获得的访问令牌。 通过向经过身份验证的用户请求显式权限，授予对 Square 帐户的有限访问权限。 OAuth 访问令牌在发布后 30 天过期，但刷新令牌不会过期。 刷新令牌可以刷新访问令牌。<br>将此字段标记为 SecureString 以安全地将其存储在数据工厂中或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。  | 是 |
| refreshToken | 从 Square 获得的刷新令牌。 用于在当前的访问令牌过期时获取新的访问令牌。<br>将此字段标记为 SecureString 以安全地将其存储在数据工厂中或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 | 否 |
| useEncryptedEndpoints | 指定是否使用 HTTPS 加密数据源终结点。 默认值为 true。  | 否 |
| useHostVerification | 指定通过 TLS 进行连接时是否要求服务器证书中的主机名与服务器的主机名匹配。 默认值为 true。  | 否 |
| usePeerVerification | 指定通过 TLS 进行连接时是否要验证服务器的标识。 默认值为 true。  | 否 |

Square 支持两种类型的访问令牌：个人和 OAuth 。

- 个人访问令牌用于对自己 Square 帐户中的资源进行无限制的 Connect API 访问。
- OAuth 访问令牌用于对任何 Square 帐户进行经过身份验证且在范围内的 Connect API 访问。 当应用以帐户所有者名义访问其他 Square 帐户中的资源时，请使用它们。 OAuth 访问令牌还可用于访问自己 Square 帐户中的资源。

在数据工厂中，通过个人访问令牌进行身份验证只需使用 `accessToken`，而通过 OAuth 进行身份验证则需要 `accessToken` 和 `refreshToken`。 在[此处](https://developer.squareup.com/docs/build-basics/access-tokens)了解如何获取访问令牌。

**示例：**

```json
{
    "name": "SquareLinkedService",
    "properties": {
        "type": "Square",
        "typeProperties": {
            "connectionProperties": {
                "host": "<e.g. mystore.mysquare.com>", 
                "clientId": "<client ID>", 
                "clientSecrect": {
                    "type": "SecureString",
                    "value": "<clientSecret>"
                }, 
                "accessToken": {
                    "type": "SecureString",
                    "value": "<access token>"
                }, 
                "refreshToken": {
                    "type": "SecureString",
                    "value": "<refresh token>"
                }, 
                "useEncryptedEndpoints": true, 
                "useHostVerification": true, 
                "usePeerVerification": true 
            }
        }
    }
}
```

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 本部分提供 Square 数据集支持的属性列表。

要从 Square 复制数据，请将数据集的 type 属性设置为“SquareObject”  。 支持以下属性：

| properties | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为：**SquareObject** | 是 |
| tableName | 表的名称。 | 否（如果指定了活动源中的“query”） |

**示例**

```json
{
    "name": "SquareDataset",
    "properties": {
        "type": "SquareObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Square linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 Square 源支持的属性列表。

### <a name="square-as-source"></a>Square 作为源

要从 Square 复制数据，请将复制活动中的源类型设置为“SquareSource”  。 复制活动**source**部分支持以下属性：

| properties | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 复制活动 source 的 type 属性必须设置为：**SquareSource** | 是 |
| 查询 | 使用自定义 SQL 查询读取数据。 例如：`"SELECT * FROM Business"`。 | 否（如果指定了数据集中的“tableName”） |

**示例：**

```json
"activities":[
    {
        "name": "CopyFromSquare",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Square input dataset name>",
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
                "type": "SquareSource",
                "query": "SELECT * FROM Business"
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
