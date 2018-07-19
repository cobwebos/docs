---
title: 使用 Azure 数据工厂从/向 Dynamics CRM 或 Dynamics 365 (Common Data Service) 复制数据 | Microsoft Docs
description: 了解如何通过在数据工厂管道中使用复制活动，将数据从 Microsoft Dynamics CRM 或 Microsoft Dynamics 365 (Common Data Service) 复制到支持的接收器数据存储，或者从支持的源数据存储复制到 Dynamics CRM 或 Dynamics 365。
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
ms.date: 05/02/2018
ms.author: jingwang
ms.openlocfilehash: 3f45f9337a5522f490c268bbdae3ef1a41205175
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2018
ms.locfileid: "37859368"
---
# <a name="copy-data-from-and-to-dynamics-365-common-data-service-or-dynamics-crm-by-using-azure-data-factory"></a>使用 Azure 数据工厂从/向 Dynamics 365 (Common Data Service) 或 Dynamics CRM 复制数据

本文概述如何使用 Azure 数据工厂中的复制活动从/向 Microsoft Dynamics 365 或 Microsoft Dynamics CRM 复制数据。 本文是根据总体概述复制活动的[复制活动概述](copy-activity-overview.md)一文编写的。

## <a name="supported-capabilities"></a>支持的功能

可以将数据从 Dynamics 365 (Common Data Service) 或 Dynamics CRM 复制到任何支持的接收器数据存储。 还可以将数据从任何支持的源数据存储复制到 Dynamics 365 (Common Data Service) 或 Dynamics CRM。 有关复制活动支持作为源或接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

此 Dynamics 连接器支持以下 Dynamics 版本和身份验证类型。 IFD 是“Internet Facing Deployment”（面向 Internet 的部署）的缩写。

| Dynamics 版本 | 身份验证类型 | 链接的服务示例 |
|:--- |:--- |:--- |
| Dynamics 365 联机 <br> Dynamics CRM Online | Office365 | [Dynamics Online + Office365 身份验证](#dynamics-365-and-dynamics-crm-online) |
| 带有 IFD 的本地 Dynamics 365 <br> 带有 IFD 的本地 Dynamics CRM 2016 <br> 带有 IFD 的本地 Dynamics CRM 2015 | IFD | [带有 IFD 的本地 Dynamics + IFD 身份验证](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

具体而言，对于 Dynamics 365，支持以下应用程序类型：

- Dynamics 365 for Sales
- Dynamics 365 for Customer Service
- Dynamics 365 for Field Service
- Dynamics 365 for Project Service Automation
- Dynamics 365 for Marketing

不支持其他应用程序类型，例如，Operations and Finance、Talent 等。

## <a name="get-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

以下部分提供了有关各属性的详细信息，这些属性用于定义特定于 Dynamics 的数据工厂实体。

## <a name="linked-service-properties"></a>链接服务属性

Dynamics 链接服务支持以下属性。

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 和 Dynamics CRM Online

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 类型属性必须设置为 **Dynamics**。 | 是 |
| deploymentType | Dynamics 实例的部署类型。 Dynamics Online 必须为 **"Online"**。 | 是 |
| serviceUri | 你的 Dynamics 实例的服务 URL，例如 `https://adfdynamics.crm.dynamics.com`。 | 是 |
| authenticationType | 要连接到 Dynamics 服务器的身份验证类型。 为 Dynamics Online 指定 **"Office365"**。 | 是 |
| username | 指定用于连接到 Dynamics 的用户名。 | 是 |
| password | 指定为 username 指定的用户帐户的密码。 将此字段标记为 SecureString 以安全地将其存储在数据工厂中或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 | 是 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如果未指定，则使用默认 Azure 集成运行时。 | 对于源为“否”，对于接收器为“是”（如果源链接服务没有集成运行时） |

>[!IMPORTANT]
>将数据复制到 Dynamics 时，不能使用默认 Azure 集成运行时执行复制。 换而言之，如果源链接服务未指定集成运行时，请使用靠近 Dynamics 实例的位置显式[创建 Azure 集成运行时](create-azure-integration-runtime.md#create-azure-ir)。 按如下示例所示在 Dynamics 链接服务中关联该运行时。

>[!NOTE]
>Dynamics 连接器使用可选的“organizationName”属性来标识 Dynamics CRM/365 Online 实例。 虽然它保持正常工作，但建议改为指定新的“serviceUri”属性来获得更好的实例发现性能。

示例：使用 Office365 身份验证的 Dynamics 联机

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "description": "Dynamics online linked service using Office365 authentication",
        "typeProperties": {
            "deploymentType": "Online",
            "serviceUri": "https://adfdynamics.crm.dynamics.com",
            "authenticationType": "Office365",
            "username": "test@contoso.onmicrosoft.com",
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

### <a name="dynamics-365-and-dynamics-crm-on-premises-with-ifd"></a>带有 IFD 的本地 Dynamics 365 和 Dynamics CRM

与 Dynamics 联机进行对比的其他属性是“hostName”和“port”。

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 类型属性必须设置为 **Dynamics**。 | 是 |
| deploymentType | Dynamics 实例的部署类型。 带有 IFD 的本地 Dynamics 必须为 **"OnPremisesWithIfd"**。| 是 |
| hostName | 本地 Dynamics 服务器的主机名称。 | 是 |
| port | 本地 Dynamics 服务器的端口。 | 否，默认端口为 443 |
| organizationName | Dynamics 实例的组织名称。 | 是 |
| authenticationType | 要连接到 Dynamics 服务器的身份验证类型。 为带有 IFD 的本地 Dynamics 指定“Ifd” | 是 |
| username | 指定用于连接到 Dynamics 的用户名。 | 是 |
| password | 指定为 username 指定的用户帐户的密码。 可选择将此字段标记为 SecureString，将其安全地存储在 ADF 中，或在 Azure Key Vault 中存储密码，并允许复制活动在执行数据复制时从此处拉取（请参阅[在 Key Vault 中存储凭据](store-credentials-in-key-vault.md)了解详细信息）。 | 是 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如果未指定，则使用默认 Azure 集成运行时。 | 对于源为“No”，对于接收器为“Yes” |

>[!IMPORTANT]
>若要将数据复制到 Dynamics，请使用靠近 Dynamics 实例的位置显式[创建 Azure 运行时](create-azure-integration-runtime.md#create-azure-ir)。 按如下示例所示在链接服务中关联该运行时。

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

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 本部分提供 Dynamics 数据集支持的属性列表。

要从/向 Dynamics 复制数据，请将数据集的类型属性设置为 **DynamicsEntity**。 支持以下属性。

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 数据集的类型属性必须设置为 **DynamicsEntity**。 |是 |
| entityName | 要检索的实体的逻辑名称。 | 源为“No”（如果指定了活动源中的“query”），接收器为“Yes” |

> [!IMPORTANT]
>- 从 Dynamics 复制数据时，需要 Dynamics 数据集中的“structure”节。 该节定义要复制的 Dynamics 数据的列名和数据类型。 有关详细信息，请参阅[数据集结构](concepts-datasets-linked-services.md#dataset-structure)和 [Dynamics 的数据类型映射](#data-type-mapping-for-dynamics)。
>- 向 Dynamics 复制数据时，Dynamics 数据集中的“structure”节是可选的。 要复制到哪些列由源数据架构确定。 如果源是不包含标头的 CSV 文件，请在输入数据集中指定包含列名称和数据类型的“结构”。 这些值将按顺序逐个映射到 CSV 文件中的字段。

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
        "typeProperties": {
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

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 Dynamics 源和接收器类型支持的属性列表。

### <a name="dynamics-as-a-source-type"></a>将 Dynamics 用作源类型

要从 Dynamics 复制数据，请将复制活动中的源类型设置为“DynamicsSource”。 复制活动的 **source** 节支持以下属性。

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动源的 type 属性必须设置为 **DynamicsSource**。 | 是 |
| query | FetchXML 是在 Dynamics（联机和本地）中使用的专属查询语言。 请参阅以下示例。 有关详细信息，请参阅[使用 FeachXML 生成查询](https://msdn.microsoft.com/library/gg328332.aspx)。 | 否（如果指定了数据集中的“entityName”） |

>[!NOTE]
>即使在 FetchXML 查询中配置的列投影不包含 PK 列，也将始终复制该列。

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

### <a name="dynamics-as-a-sink-type"></a>将 Dynamics 用作接收器类型

要向 Dynamics 复制数据，请将复制活动中的接收器类型设置为“DynamicsSink”。 复制活动 **sink** 节支持以下属性。

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动接收器的 type 属性必须设置为 **DynamicsSink**。 | 是 |
| writeBehavior | 操作的写入行为。<br/>允许的值为 **"Upsert"**。 | 是 |
| writeBatchSize | 每批中写入到 Dynamics 的数据行计数。 | 否（默认值为 10） |
| ignoreNullValues | 指示是否忽略 null 值从输入数据（键字段除外）期间写入操作。<br/>允许的值为 **true** 和 **false**。<br>- **True**：保留目标中的数据对象时进行更新插入/更新操作保持不变。 插入在执行插入操作时定义的默认值。<br/>- **False**：执行更新插入/更新操作时为 NULL 更新目标对象中的数据。 执行插入操作时插入 NULL 值。 | 否（默认值为 false） |

>[!NOTE]
>接收器“writeBatchSize”和 Dynamics 接收器的复制活动“[parallelCopies](copy-activity-performance.md#parallel-copy)” 的默认值都是 10。 因此，会将 100 条记录同时提交到 Dynamics。

对于 Dynamics 365（联机版），存在[每个组织进行 2 次并发批量调用](https://msdn.microsoft.com/en-us/library/jj863631.aspx#Run-time%20limitations)的限制。 如果超出此限制，则会在执行第一个请求之前引发“服务器忙”错误。 保持“writeBatchSize”小于或等于 10 可避免这种并发调用的限制。

“writeBatchSize”和“parallelCopies”的最佳组合取决于实体的架构，例如列数、行大小、与这些调用挂钩的插件/工作流/工作流活动的数量等。10 writeBatchSize * 10 parallelCopies 的默认设置是基于 Dynamics 服务提供的建议设置，该服务可用于大多数 Dynamics 实体，但可能无法获得最佳性能。 你可以通过在复制活动设置中调整组合来调整性能。

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

从 Dynamics 复制数据时，以下映射用于从 Dynamics 数据类型映射到数据工厂临时数据类型。 若要了解复制活动如何将源架构和数据类型映射到接收器，请参阅[架构和数据类型映射](copy-activity-schema-and-type-mapping.md)。

使用以下映射表，根据源 Dynamics 数据类型在数据集结构中配置相应的数据工厂数据类型。

| Dynamics 数据类型 | 数据工厂临时数据类型 | 支持用作源 | 支持用作接收器 |
|:--- |:--- |:--- |:--- |
| AttributeTypeCode.BigInt | Long | ✓ | ✓ |
| AttributeTypeCode.Boolean | 布尔 | ✓ | ✓ |
| AttributeType.Customer | Guid | ✓ | | 
| AttributeType.DateTime | Datetime | ✓ | ✓ |
| AttributeType.Decimal | 小数 | ✓ | ✓ |
| AttributeType.Double | Double | ✓ | ✓ |
| AttributeType.EntityName | String | ✓ | ✓ |
| AttributeType.Integer | Int32 | ✓ | ✓ |
| AttributeType.Lookup | Guid | ✓ | ✓（关联单个类型） |
| AttributeType.ManagedProperty | 布尔 | ✓ | |
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
有关数据工厂中复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。
