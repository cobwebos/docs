---
title: "使用 Azure 数据工厂从 Dynamics CRM 和 365 复制数据 | Microsoft 文档"
description: "了解如何通过在 Azure 数据工厂管道中使用复制活动，将数据从 Dynamics CRM 和 365 复制到支持的接收器数据存储。"
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
ms.date: 11/02/2017
ms.author: jingwang
ms.openlocfilehash: 1af330596052a92237469aba4729474e7fe417aa
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2017
---
# <a name="copy-data-from-dynamics-365dynamics-crm-using-azure-data-factory"></a>使用 Azure 数据工厂从 Dynamics 365/Dynamics CRM 复制数据

本文概述了如何使用 Azure 数据工厂中的复制活动从 Dynamics 365/Dynamics CRM 复制数据， 它是基于概述复制活动总体的[复制活动概述](copy-activity-overview.md)一文。

> [!NOTE]
> 本文适用于目前处于预览状态的数据工厂版本 2。 如果使用正式版 (GA) 1 版本的数据工厂服务，请参阅 [V1 中的复制活动](v1/data-factory-data-movement-activities.md)。

## <a name="supported-capabilities"></a>支持的功能

可以将数据从 Dynamics 365/Dynamics CRM 复制到任何支持的接收器数据存储。 有关复制活动支持作为源/接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

具体来说，此 Dynamics 连接器支持以下 Dynamics 版本和身份验证类型：

| Dynamics 版本 | 身份验证类型 | 链接的服务示例 |
|:--- |:--- |:--- |
| Dynamics 365 联机 <br> Dynamics CRM 联机 | Office365 | [Dynamics 联机 + Office365 身份验证](#dynamics-365-and-dynamics-crm-online) |
| 带有 IFD 的本地 Dynamics 365 <br> 带有 IFD 的本地 Dynamics CRM 2016 <br> 带有 IFD 的本地 Dynamics CRM 2015 | IFD | [带有 IFD 的本地 Dynamics + IFD 身份验证](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

*IFD 是“Internet Facing Deployment”（面向 Internet 的部署）的缩写。*

> [!NOTE]
> 要使用 Dynamics 连接器，请将密码存储在 Azure Key Vault 中，并在执行数据复制时允许从中拉取 ADF 复制活动。 请参阅如何在[链接的服务属性](#linked-service-properties)部分进行配置。

## <a name="getting-started"></a>入门

可以使用 .NET SDK、Python SDK、Azure PowerShell、REST API 或 Azure 资源管理器模板创建包含复制活动的管道。 有关创建包含复制活动的管道的分步说明，请参阅[复制活动教程](quickstart-create-data-factory-dot-net.md)。

以下部分提供了有关各属性的详细信息，这些属性用于定义特定于 Dynamics 的数据工厂实体。

## <a name="linked-service-properties"></a>链接服务属性

Dynamics 链接的服务支持以下属性：

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 和 Dynamics CRM Online

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 类型属性必须设置为：Dynamics | 是 |
| deploymentType | Dynamics 实例的部署类型。 Dynamics 联机必须为“联机”。 | 是 |
| organizationName | Dynamics 实例的组织名称。 | 否，应指定何时与用户关联多个 Dynamics 实例。 |
| authenticationType | 要连接到 Dynamics 服务器的身份验证类型。 为 Dynamics 联机指定“Office365”。 | 是 |
| username | 指定要连接到 Dynamics 的用户名。 | 是 |
| password | 指定为用户名指定的用户帐户的密码。 必须将密码存放在 Azure Key Vault 中，并将密码配置为“AzureKeyVaultSecret”。 有关详细信息，请参阅[在 Key Vault 中存储凭据](store-credentials-in-key-vault.md)一文。 | 是 |

示例：使用 Office365 身份验证的 Dynamics 联机

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "description": "Dynamics online linked service using Office365 authentication",
        "typeProperties": {
            "deploymentType": "Online",
            "organizationName": "orga02d9c75",
            "authenticationType": "Office365",
            "username": "test@contoso.onmicrosoft.com",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "mySecret",
                "store":{
                    "linkedServiceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
}
```

### <a name="dynamics-365-and-dynamics-crm-on-premises-with-ifd"></a>带有 IFD 的本地 Dynamics 365 和 Dynamics CRM

与 Dynamics 联机进行对比的其他属性是“hostName”和“port”。

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 类型属性必须设置为：Dynamics | 是 |
| deploymentType | Dynamics 实例的部署类型。 带有 IFD 的本地 Dynamics 必须为“OnPremisesWithIfd”| 是 |
| hostName | 本地 Dynamics 服务器的主机名称。 | 是 |
| **port** | 本地 Dynamics 服务器的端口。 | 否，默认端口为 443 |
| organizationName | Dynamics 实例的组织名称。 | 是 |
| authenticationType | 要连接到 Dynamics 服务器的身份验证类型。 为带有 IFD 的本地 Dynamics 指定“Ifd” | 是 |
| username | 指定要连接到 Dynamics 的用户名。 | 是 |
| password | 指定为用户名指定的用户帐户的密码。 请注意，你必须将密码存放在 Azure Key Vault 中，并将密码配置为“AzureKeyVaultSecret”。 有关详细信息，请参阅[在 Key Vault 中存储凭据](store-credentials-in-key-vault.md)一文。 | 是 |

示例：带有 IFD、使用 IFD 身份验证的本地 Dynamics

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "description": "Dynamics on-premises with IFD linked service using IFD authentication",
        "typeProperties": {
            "deploymentType": "OnPremisesWithIFD",
            "hostName": "contosodynamicsserver.contoso.com",
            "port": 443,
            "organizationName": "admsDynamicsTest",
            "authenticationType": "Ifd",
            "username": "test@contoso.onmicrosoft.com",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "mySecret",
                "store":{
                    "linkedServiceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
}
```

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 本部分提供 Dynamics 数据集支持的属性列表。

要从 Dynamics 复制数据，请将数据集的类型属性设置为“DynamicsEntity”。 支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 数据集的类型属性必须设置为：DynamicsEntity |是 |
| entityName | 要检索的实体的逻辑名称。 | 否（如果指定了活动源中的“query”） |

> [!IMPORTANT]
> Dynamics 需要数据集中的“structure”部分，它定义要复制的 Dynamics 数据的列名和数据类型。 请参阅[数据集结构](concepts-datasets-linked-services.md#dataset-structure)和 [Dynamics 的数据类型映射](#data-type-mapping-for-dynamics)，了解详细信息。

**示例：**

```json
{
    "name": "DynamicsDataset",
    "properties": {
        "type": "DynamicsEntity",
        "structure": [
            {
                "name": "accountid",
                "type": "Guid"
            },
            {
                "name": "name",
                "type": "String"
            },
            {
                "name": "marketingonly",
                "type": "Boolean"
            },
            {
                "name": "modifiedon",
                "type": "Datetime"
            }
        ],
        "typePoperties": {
            "entityName": "account"
        },
        "linkedServiceName": {
            "referenceName": "<Dynamics linked service name>",
            "type": "linkedservicereference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各个部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 Dynamics 源支持的属性列表。

### <a name="dynamics-as-source"></a>作为源的 Dynamics

要从 Dynamics 复制数据，请将复制活动中的源类型设置为“DynamicsSource”。 复制活动**源**部分支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 类型属性必须设置为：DynamicsSource  | 是 |
| query  | FetchXML 是在 Microsoft Dynamics（联机和本地）中使用的专有查询语言。 请参阅下面的示例，并从[使用 FeachXML 生成查询](https://msdn.microsoft.com/en-us/library/gg328332.aspx)了解详细信息。 | 否（如果指定了数据集中的“entityName”）  |

**示例：**

```json
"activities":[
    {
        "name": "CopyFromDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Dynamics input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DynamicsSource",
                "query": "<FetchXML Query>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="sample-fetchxml-query"></a>示例 FetchXML 查询

```xml
<fetch>
  <entity name="account">
    <attribute name="accountid" />
    <attribute name="name" />
    <attribute name="marketingonly" />
    <attribute name="modifiedon" />
    <order attribute="modifiedon" descending="false" />
    <filter type="and">
      <condition attribute ="modifiedon" operator="between">
        <value>2017-03-10 18:40:00z</value>
        <value>2017-03-12 20:40:00z</value>
      </condition>
    </filter>
  </entity>
</fetch>
```

## <a name="data-type-mapping-for-dynamics"></a>Dynamics 的数据类型映射

从 Dynamics 复制数据时，以下映射用于从 Dynamics 数据类型映射到 Azure 数据工厂临时数据类型。 若要了解复制活动如何将源架构和数据类型映射到接收器，请参阅[架构和数据类型映射](copy-activity-schema-and-type-mapping.md)。

使用以下映射表，根据源 Dynamics 数据类型在数据集结构中配置相应的 ADF 数据类型：

| Dynamics 数据类型 | 数据工厂临时数据类型 |
|:--- |:--- |
| AttributeTypeCode.BigInt | Long |
| AttributeTypeCode.Boolean | 布尔 |
| AttributeType.Customer | Guid |
| AttributeType.DateTime | Datetime |
| AttributeType.Decimal | 小数 |
| AttributeType.Double | Double |
| AttributeType.EntityName | String |
| AttributeType.Integer | Int32 |
| AttributeType.Lookup | Guid |
| AttributeType.ManagedProperty | 布尔 |
| AttributeType.Memo | String |
| AttributeType.Money | 小数 |
| AttributeType.Owner | Guid |
| AttributeType.Picklist | Int32 |
| AttributeType.Uniqueidentifier | Guid |
| AttributeType.String | String |
| AttributeType.State | Int32 |
| AttributeType.Status | Int32 |

> [!NOTE]
> Dynamics 数据类型 AttributeType.CalendarRules 和 AttributeType.PartyList 不受支持。


## <a name="next-steps"></a>后续步骤
有关 Azure 数据工厂中复制活动支持作为源和接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md##supported-data-stores-and-formats)。