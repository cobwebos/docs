---
title: "使用 Azure 数据工厂从/向 Dynamics CRM 和 365 复制数据 | Microsoft Docs"
description: "了解如何通过在 Azure 数据工厂管道中使用复制活动，将数据从 Dynamics CRM 和 365 复制到支持的接收器数据存储，或者从支持的源数据存储复制到 Dynamics CRM 和 365。"
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
ms.openlocfilehash: d577db2b2f14da61baccfb6230b0c6e03a62b9b1
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2018
---
# <a name="copy-data-fromto-dynamics-365dynamics-crm-using-azure-data-factory"></a>使用 Azure 数据工厂从/向 Dynamics 365/Dynamics CRM 复制数据

本文概述了如何使用 Azure 数据工厂中的复制活动从/向 Dynamics 365 和 Dynamics CRM 复制数据， 它是基于概述复制活动总体的[复制活动概述](copy-activity-overview.md)一文。

> [!NOTE]
> 本文适用于目前处于预览版的数据工厂版本 2。 如果使用正式版 (GA) 1 版本的数据工厂服务，请参阅 [V1 中的复制活动](v1/data-factory-data-movement-activities.md)。

## <a name="supported-capabilities"></a>支持的功能

可将数据从 Dynamics 365/Dynamics CRM 复制到任何受支持的接收器数据存储，或者将数据从任何受支持的源数据存储到 Dynamics 365/Dynamics CRM。 有关复制活动支持作为源/接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

此 Dynamics 连接器支持以下 Dynamics 版本和身份验证类型（*IFD 是“面向 Internet 的部署”的略写*）：

| Dynamics 版本 | 身份验证类型 | 链接的服务示例 |
|:--- |:--- |:--- |
| Dynamics 365 联机 <br> Dynamics CRM 联机 | Office365 | [Dynamics 联机 + Office365 身份验证](#dynamics-365-and-dynamics-crm-online) |
| 带有 IFD 的本地 Dynamics 365 <br> 带有 IFD 的本地 Dynamics CRM 2016 <br> 带有 IFD 的本地 Dynamics CRM 2015 | IFD | [带有 IFD 的本地 Dynamics + IFD 身份验证](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

具体而言，对于 Dynamics 365，支持以下应用程序类型：

- Dynamics 365 for Sales
- Dynamics 365 for Customer Service
- Dynamics 365 for Field Service
- Dynamics 365 for Project Service Automation
- Dynamics 365 for Marketing

> [!NOTE]
> 若要使用 Dynamics 连接器，请将密码存储在 Azure Key Vault 中，并在执行数据复制时允许从中拉取复制活动。 请参阅如何在[链接的服务属性](#linked-service-properties)部分进行配置。

## <a name="getting-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started-2](../../includes/data-factory-v2-connector-get-started-2.md)]

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
| password | 指定为用户名指定的用户帐户的密码。 必须将密码存放在 Azure Key Vault 中，并将密码配置为“AzureKeyVaultSecret”。 在[在 Key Vault 中存储凭据](store-credentials-in-key-vault.md)中了解详细信息。 | 是 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如果未指定，则使用默认 Azure 集成运行时。 | 对于源为“否”，对于接收器为“是”（如果源链接服务没有 IR） |

>[!IMPORTANT]
>将数据复制**到** Dynamics 时，不能使用默认 Azure 集成运行时执行复制。 换句话说，如果源链接服务未指定 IR，请使用接近 Dynamics 的位置显式[创建 Azure IR](create-azure-integration-runtime.md#create-azure-ir)，并按以下示例所示关联 Dynamics 链接服务。

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
                "secretName": "<secret name in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
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
| password | 指定为用户名指定的用户帐户的密码。 请注意，你必须将密码存放在 Azure Key Vault 中，并将密码配置为“AzureKeyVaultSecret”。 在[在 Key Vault 中存储凭据](store-credentials-in-key-vault.md)中了解详细信息。 | 是 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如果未指定，则使用默认 Azure 集成运行时。 | 对于源为“No”，对于接收器为“Yes” |

>[!IMPORTANT]
>若要将数据复制到 Dynamics，请使用接近 Dynamics 的位置显式[创建 Azure IR](create-azure-integration-runtime.md#create-azure-ir)，并关联如下面示例中的链接服务。

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
                "secretName": "<secret name in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
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

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 本部分提供 Dynamics 数据集支持的属性列表。

要从/向 Dynamics 复制数据，请将数据集的类型属性设置为“DynamicsEntity”。 支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 数据集的类型属性必须设置为：DynamicsEntity |是 |
| entityName | 要检索的实体的逻辑名称。 | 源为否（如果指定了活动源中的“query”），接收器为是 |

> [!IMPORTANT]
>- **从 Dynamics 复制数据时，需要 Dynamics 数据集中的“structure”节**，它定义要复制的 Dynamics 数据的列名和数据类型。 请参阅[数据集结构](concepts-datasets-linked-services.md#dataset-structure)和 [Dynamics 的数据类型映射](#data-type-mapping-for-dynamics)，了解详细信息。
>- **向 Dynamics 复制数据时，Dynamics 数据集中的“structure”节是可选的**。 要复制到哪些列由源数据架构确定。 如果源是 CSV 文件没有标头，与列的名称和数据类型映射到 CSV 文件逐个顺序中的字段中输入数据集，指定“结构”。

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

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 Dynamics 源和接收器支持的属性列表。

### <a name="dynamics-as-source"></a>作为源的 Dynamics

要从 Dynamics 复制数据，请将复制活动中的源类型设置为“DynamicsSource”。 复制活动**源**部分支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动源的 type 属性必须设置为：**DynamicsSource**  | 是 |
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

### <a name="dynamics-as-sink"></a>作为接收器的 Dynamics

要向 Dynamics 复制数据，请将复制活动中的接收器类型设置为“DynamicsSink”。 复制活动接收器部分中支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动接收器的 type 属性必须设置为：**DynamicsSink**  | 是 |
| writeBehavior | 操作的写入行为。<br/>允许的值为：**"Upsert"**。 | 是 |
| writeBatchSize | 每批中写入到 Dynamics 的数据行计数。 | 否（默认值为 10） |
| ignoreNullValues | 指示是否忽略 null 值从输入数据（键字段除外）期间写入操作。<br/>允许的值为：true 和 false。<br>- true：保留目标中的数据对象时进行更新插入/更新操作保持不变，并插入在执行插入操作时定义默认值。<br/>- false：执行 upsert/update 操作时为 NULL 更新目标对象中的数据和执行插入操作时插入 NULL 值。  | 否（默认值为 false） |

>[!NOTE]
>接收器 writeBatchSize 和复制活动的默认值 [parallelCopies](copy-activity-performance.md#parallel-copy) 为 Dynamics 接收器是这两个 10，这意味着同时提交给 Dynamics 100 条记录。

**示例：**

```json
"activities":[
    {
        "name": "CopyToDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Dynamics output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "DynamicsSink",
                "writeBehavior": "Upsert",
                "writeBatchSize": 10,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="data-type-mapping-for-dynamics"></a>Dynamics 的数据类型映射

从 Dynamics 复制数据时，以下映射用于从 Dynamics 数据类型映射到 Azure 数据工厂临时数据类型。 若要了解复制活动如何将源架构和数据类型映射到接收器，请参阅[架构和数据类型映射](copy-activity-schema-and-type-mapping.md)。

使用以下映射表，根据源 Dynamics 数据类型在数据集结构中配置相应的数据工厂数据类型：

| Dynamics 数据类型 | 数据工厂临时数据类型 | 支持用作源 | 支持用作接收器 |
|:--- |:--- |:--- |:--- |
| AttributeTypeCode.BigInt | Long | ✓ | ✓ |
| AttributeTypeCode.Boolean | 布尔 | ✓ | ✓ |
| AttributeType.Customer | Guid | ✓ |  |
| AttributeType.DateTime | Datetime | ✓ | ✓ |
| AttributeType.Decimal | 小数 | ✓ | ✓ |
| AttributeType.Double | Double | ✓ | ✓ |
| AttributeType.EntityName | String | ✓ | ✓ |
| AttributeType.Integer | Int32 | ✓ | ✓ |
| AttributeType.Lookup | Guid | ✓ |  |
| AttributeType.ManagedProperty | 布尔 | ✓ |  |
| AttributeType.Memo | String | ✓ | ✓ |
| AttributeType.Money | 小数 | ✓ | ✓ |
| AttributeType.Owner | Guid | ✓ | |
| AttributeType.Picklist | Int32 | ✓ | ✓ |
| AttributeType.Uniqueidentifier | Guid | ✓ | ✓ |
| AttributeType.String | String | ✓ | ✓ |
| AttributeType.State | Int32 | ✓ | ✓ |
| AttributeType.Status | Int32 | ✓ | ✓ |


> [!NOTE]
> Dynamics 数据类型 AttributeType.CalendarRules 和 AttributeType.PartyList 不受支持。

## <a name="next-steps"></a>后续步骤
有关 Azure 数据工厂中复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。