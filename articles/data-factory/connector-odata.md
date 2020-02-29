---
title: 使用 Azure 数据工厂从 OData 源复制数据
description: 了解如何通过在 Azure 数据工厂管道中使用复制活动，将数据从 OData 源复制到支持的接收器数据存储。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: 17b78e03e330e342e9d558dd3ca5d9071bcd3c2f
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2020
ms.locfileid: "78163924"
---
# <a name="copy-data-from-an-odata-source-by-using-azure-data-factory"></a>使用 Azure 数据工厂从 OData 源复制数据

> [!div class="op_single_selector" title1="选择所使用的数据工厂服务版本："]
> * [版本 1](v1/data-factory-odata-connector.md)
> * [当前版本](connector-odata.md)

本文概述了如何使用 Azure 数据工厂中的复制活动从 OData 源复制数据。 本文是根据总体概述复制活动的 [Azure 数据工厂中的复制活动](copy-activity-overview.md)编写的。

## <a name="supported-capabilities"></a>支持的功能

以下活动支持此 OData 连接器：

- [复制活动](copy-activity-overview.md)与[支持的源/接收器矩阵](copy-activity-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)

可以将数据从 OData 源复制到任何支持的接收器数据存储。 有关复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储和格式](copy-activity-overview.md#supported-data-stores-and-formats)。

具体而言，此 OData 连接器支持：

- OData 3.0 和 4.0 版。
- 使用以下身份验证之一复制数据：**匿名**、**基本**、 **Windows**和**AAD 服务主体**。

## <a name="prerequisites"></a>必备条件

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

对于特定于 OData 连接器的数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

OData 链接的服务支持以下属性：

| properties | 说明 | 必选 |
|:--- |:--- |:--- |
| type | type 属性必须设置为 OData。 |是 |
| url | OData 服务的根 URL。 |是 |
| authenticationType | 用于连接 OData 源的身份验证类型。 允许的值为**Anonymous**、 **Basic**、 **Windows**和**AadServicePrincipal**。 不支持基于用户的 OAuth。 | 是 |
| userName | 如果使用 Basic 或 Windows 身份验证，请指定用户名。 | 否 |
| password | 指定为 userName 指定的用户帐户的密码。 将此字段标记为 SecureString 类型，以便安全地将其存储在数据工厂中。 此外，还可以[引用 Azure Key Vault 中存储的机密](store-credentials-in-key-vault.md)。 | 否 |
| servicePrincipalId | 指定 Azure Active Directory 应用程序的客户端 ID。 | 否 |
| aadServicePrincipalCredentialType | 指定要用于服务主体身份验证的凭据类型。 允许值为：`ServicePrincipalKey` 或 `ServicePrincipalCert`。 | 否 |
| servicePrincipalKey | 指定 Azure Active Directory 应用程序的密钥。 将此字段标记为 **SecureString** 以安全地将其存储在数据工厂中或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 | 否 |
| servicePrincipalEmbeddedCert | 指定 Azure Active Directory 中注册的应用程序的 base64 编码证书。 将此字段标记为 **SecureString** 以安全地将其存储在数据工厂中或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 | 否 |
| servicePrincipalEmbeddedCertPassword | 如果使用密码保护证书，请指定证书的密码。 将此字段标记为 **SecureString** 以安全地将其存储在数据工厂中或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。  | 否|
| tenant | 指定应用程序的租户信息（域名或租户 ID）。 将鼠标悬停在 Azure 门户右上角进行检索。 | 否 |
| aadResourceId | 指定你请求授权的 AAD 资源。| 否 |
| connectVia | 用于连接到数据存储的 [ Integration Runtime](concepts-integration-runtime.md)。 从[必备组件](#prerequisites)部分了解详细信息。 如果未指定，则使用默认 Azure Integration Runtime。 |否 |

示例 1：使用 Anonymous 身份验证

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

示例 2：使用 Basic 身份验证

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "Basic",
            "userName": "<user name>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

示例 3：使用 Windows 身份验证

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "Windows",
            "userName": "<domain>\\<user>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**示例4：使用服务主体密钥身份验证**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "AadServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "aadServicePrincipalCredentialType": "ServicePrincipalKey",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource URL>"
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

**示例5：使用服务主体证书身份验证**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "AadServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "aadServicePrincipalCredentialType": "ServicePrincipalCert",
            "servicePrincipalEmbeddedCert": { 
                "type": "SecureString", 
                "value": "<base64 encoded string of (.pfx) certificate data>"
            },
            "servicePrincipalEmbeddedCertPassword": { 
                "type": "SecureString", 
                "value": "<password of your certificate>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource e.g. https://tenant.sharepoint.com>"
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

## <a name="dataset-properties"></a>数据集属性

本部分提供 OData 数据集支持的属性列表。

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集和链接服务](concepts-datasets-linked-services.md)。 

要从 OData 复制数据，请将数据集的 type 属性设置为“ODataResource”。 支持以下属性：

| properties | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为 ODataResource。 | 是 |
| 路径 | OData 资源的路径。 | 是 |

**示例**

```json
{
    "name": "ODataDataset",
    "properties":
    {
        "type": "ODataResource",
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<OData linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties":
        {
            "path": "Products"
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

本部分提供 OData 源支持的属性列表。

有关可用于定义活动的各个部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)。 

### <a name="odata-as-source"></a>以 OData 作为源

若要从 OData 复制数据，复制活动**源**部分支持以下属性：

| properties | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动源的**type**属性必须设置为**ODataSource**。 | 是 |
| query | 用于筛选数据的 OData 查询选项。 示例：`"$select=Name,Description&$top=5"`。<br/><br/>请注意，OData 连接器会从组合 URL 复制数据：`[URL specified in linked service]/[path specified in dataset]?[query specified in copy activity source]`。 有关详细信息，请参阅 [OData URL 组件](https://www.odata.org/documentation/odata-version-3-0/url-conventions/)。 | 否 |

**示例**

```json
"activities":[
    {
        "name": "CopyFromOData",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<OData input dataset name>",
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
                "type": "ODataSource",
                "query": "$select=Name,Description&$top=5"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

如果使用的是 `RelationalSource` 类型化源，则仍支持原样，但建议使用新的源。

## <a name="data-type-mapping-for-odata"></a>OData 的数据类型映射

从 OData 复制数据时，会在 OData 数据类型和 Azure 数据工厂临时数据类型之间使用以下映射。 要了解复制活动如何将源架构和数据类型映射到接收器，请参阅[架构和数据类型映射](copy-activity-schema-and-type-mapping.md)。

| OData 数据类型 | 数据工厂临时数据类型 |
|:--- |:--- |
| Edm.Binary | Byte[] |
| Edm.Boolean | Bool |
| Edm.Byte | Byte[] |
| Edm.DateTime | DateTime |
| Edm.Decimal | Decimal |
| Edm.Double | Double |
| Edm.Single | Single |
| Edm.Guid | Guid |
| Edm.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | String |
| Edm.Time | TimeSpan |
| Edm.DateTimeOffset | DateTimeOffset |

> [!NOTE]
> 不支持 OData 复杂数据类型，例如对象。


## <a name="lookup-activity-properties"></a>查找活动属性

若要了解有关属性的详细信息，请检查[查找活动](control-flow-lookup-activity.md)。

## <a name="next-steps"></a>后续步骤

有关 Azure 数据工厂中复制活动支持用作源和接收器的数据存储的列表，请参阅[支持的数据存储和格式](copy-activity-overview.md#supported-data-stores-and-formats)。