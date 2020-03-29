---
title: 在 Dynamics (Common Data Service) 中复制数据
description: 了解如何通过在数据工厂管道中使用复制活动，将数据从 Microsoft Dynamics CRM 或 Microsoft Dynamics 365 (Common Data Service) 复制到支持的接收器数据存储，或者从支持的源数据存储复制到 Dynamics CRM 或 Dynamics 365。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.custom: seo-lt-2019
ms.date: 11/20/2019
ms.openlocfilehash: d065439839ba5db479305ae81c61892cb5cf5e70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74929454"
---
# <a name="copy-data-from-and-to-dynamics-365-common-data-service-or-dynamics-crm-by-using-azure-data-factory"></a>使用 Azure 数据工厂从/向 Dynamics 365 (Common Data Service) 或 Dynamics CRM 复制数据

本文概述如何使用 Azure 数据工厂中的复制活动从/向 Microsoft Dynamics 365 或 Microsoft Dynamics CRM 复制数据。 它基于["复制活动概述](copy-activity-overview.md)"一文，其中介绍了复制活动的一般概述。

## <a name="supported-capabilities"></a>支持的功能

以下活动支持此连接器：

- 带有[支持的源或接收器矩阵](copy-activity-overview.md)的[复制活动](copy-activity-overview.md)
- [查找活动](control-flow-lookup-activity.md)

可以将数据从 Dynamics 365 (Common Data Service) 或 Dynamics CRM 复制到任何支持的接收器数据存储。 还可以将数据从任何支持的源数据存储复制到 Dynamics 365 (Common Data Service) 或 Dynamics CRM。 有关复制活动支持作为源或接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

此 Dynamics 连接器支持联机或本地的 Dynamics 版本 7.x 到 9.x。 更具体地说，

- 版本 7.x 映射到 Dynamics CRM 2015
- 版本 8.x 映射到 Dynamics CRM 2016 和 Dynamics 365 的早期版本
- 版本 9.x 映射到 Dynamics 365 的更高版本

请参阅下表，了解各种 Dynamics 版本/产品支持的身份验证类型和配置。 IFD 是“Internet Facing Deployment”（面向 Internet 的部署）的缩写。

| Dynamics 版本 | 身份验证类型 | 链接的服务示例 |
|:--- |:--- |:--- |
| Common Data Service <br> Dynamics 365 联机 <br> Dynamics CRM Online | AAD 服务主体 <br> Office365 | [Dynamics Online + AAD 服务主体或 Office365 身份验证](#dynamics-365-and-dynamics-crm-online) |
| 带有 IFD 的本地 Dynamics 365 <br> 带有 IFD 的本地 Dynamics CRM 2016 <br> 带有 IFD 的本地 Dynamics CRM 2015 | IFD | [与 IFD 和 IFD auth 的本地动态](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

具体而言，对于 Dynamics 365，支持以下应用程序类型：

- Dynamics 365 for Sales
- Dynamics 365 for Customer Service
- Dynamics 365 for Field Service
- Dynamics 365 for Project Service Automation
- Dynamics 365 for Marketing

此连接器不支持其他应用程序类型，如 Finance and Operations、Talent 等。

此 Dynamics 连接器基于 [Dynamics XRM 工具](https://docs.microsoft.com/dynamics365/customer-engagement/developer/build-windows-client-applications-xrm-tools)构建。

>[!TIP]
>若要从 **Dynamics 365 Finance and Operations** 复制数据，可以使用 [Dynamics AX 连接器](connector-dynamics-ax.md)。

## <a name="get-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

以下部分提供了有关各属性的详细信息，这些属性用于定义特定于 Dynamics 的数据工厂实体。

## <a name="linked-service-properties"></a>链接服务属性

Dynamics 链接服务支持以下属性。

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 和 Dynamics CRM Online

| properties | 描述 | 必选 |
|:--- |:--- |:--- |
| type | 类型属性必须设置为 **Dynamics**、**DynamicsCrm** 或 **CommonDataServiceForApps**。 | 是 |
| deploymentType | Dynamics 实例的部署类型。 Dynamics Online 必须为 **"Online"**。 | 是 |
| serviceUri | 你的 Dynamics 实例的服务 URL，例如 `https://adfdynamics.crm.dynamics.com`。 | 是 |
| authenticationType | 要连接到 Dynamics 服务器的身份验证类型。 允许的值是 **：AADService 原则**或 **"Office365"。** | 是 |
| servicePrincipalId | 指定 Azure Active Directory 应用程序的客户端 ID。 | 在使用 `AADServicePrincipal` 身份验证时为“是” |
| servicePrincipalCredentialType | 指定要用于服务主体身份验证的凭据类型。 允许的值是：**服务主密钥**或服务**主体证书**。 | 在使用 `AADServicePrincipal` 身份验证时为“是” |
| servicePrincipalCredential | 指定服务主体凭据。 <br>使用 `ServicePrincipalKey` 作为凭据类型时，`servicePrincipalCredential` 可以是字符串（ADF 会在链接服务部署的基础上对其加密），也可以是对 AKV 中机密的引用。 <br>使用 `ServicePrincipalCert` 作为凭据时，`servicePrincipalCredential` 应该是对 AKV 中证书的引用。 | 在使用 `AADServicePrincipal` 身份验证时为“是” | 
| username | 指定用于连接到 Dynamics 的用户名。 | 在使用 `Office365` 身份验证时为“是” |
| password | 指定为 username 指定的用户帐户的密码。 将此字段标记为 SecureString 以安全地将其存储在数据工厂中或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 | 在使用 `Office365` 身份验证时为“是” |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如果未指定，则使用默认 Azure Integration Runtime。 | 对于源为“否”，对于接收器为“是”（如果源链接服务没有集成运行时） |

>[!NOTE]
>Dynamics 连接器使用可选的“organizationName”属性来标识 Dynamics CRM/365 Online 实例。 虽然它保持正常工作，但建议改为指定新的“serviceUri”属性来获得更好的实例发现性能。

**示例：使用 AAD 服务主体和密钥身份验证在线动态**

```json
{  
    "name": "DynamicsLinkedService",  
    "properties": {  
        "type": "Dynamics",  
        "typeProperties": {  
            "deploymentType": "Online",  
            "serviceUri": "https://adfdynamics.crm.dynamics.com",  
            "authenticationType": "AADServicePrincipal",  
            "servicePrincipalId": "<service principal id>",  
            "servicePrincipalCredentialType": "ServicePrincipalKey",  
            "servicePrincipalCredential": "<service principal key>"
        },  
        "connectVia": {  
            "referenceName": "<name of Integration Runtime>",  
            "type": "IntegrationRuntimeReference"  
        }  
    }  
}  
```
**示例：使用 AAD 服务主体和证书身份验证联机动态**

```json
{ 
    "name": "DynamicsLinkedService", 
    "properties": { 
        "type": "Dynamics", 
        "typeProperties": { 
            "deploymentType": "Online", 
            "serviceUri": "https://adfdynamics.crm.dynamics.com", 
            "authenticationType": "AADServicePrincipal", 
            "servicePrincipalId": "<service principal id>", 
            "servicePrincipalCredentialType": "ServicePrincipalCert", 
            "servicePrincipalCredential": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<AKV reference>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<certificate name in AKV>" 
            } 
        }, 
        "connectVia": { 
            "referenceName": "<name of Integration Runtime>", 
            "type": "IntegrationRuntimeReference" 
        } 
    } 
} 
```

示例：使用 Office365 身份验证的 Dynamics 联机****

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
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

与 Dynamics 联机进行对比的其他属性是“hostName”和“port”。**

| properties | 描述 | 必选 |
|:--- |:--- |:--- |
| type | 类型属性必须设置为 **Dynamics**、**DynamicsCrm** 或 **CommonDataServiceForApps**。 | 是 |
| deploymentType | Dynamics 实例的部署类型。 带有 IFD 的本地 Dynamics 必须为 **"OnPremisesWithIfd"**。| 是 |
| hostName | 本地 Dynamics 服务器的主机名称。 | 是 |
| port | 本地 Dynamics 服务器的端口。 | 否，默认端口为 443 |
| organizationName | Dynamics 实例的组织名称。 | 是 |
| authenticationType | 要连接到 Dynamics 服务器的身份验证类型。 为带有 IFD 的本地 Dynamics 指定“Ifd”**** | 是 |
| username | 指定用于连接到 Dynamics 的用户名。 | 是 |
| password | 指定为 username 指定的用户帐户的密码。 可选择将此字段标记为 SecureString，将其安全地存储在 ADF 中，或在 Azure Key Vault 中存储密码，并允许复制活动在执行数据复制时从此处拉取（请参阅[在 Key Vault 中存储凭据](store-credentials-in-key-vault.md)了解详细信息）。 | 是 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如果未指定，则使用默认 Azure Integration Runtime。 | 对于源为“No”，对于接收器为“Yes” |

示例：带有 IFD、使用 IFD 身份验证的本地 Dynamics****

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

支持使用以下属性从/向 Dynamics 复制数据。

| properties | 描述 | 必选 |
|:--- |:--- |:--- |
| type | 数据集的类型属性必须设置为 **DynamicsEntity**、**DynamicsCrmEntity** 或 **CommonDataServiceForAppsEntity**。 |是 |
| entityName | 要检索的实体的逻辑名称。 | 源为“No”（如果指定了活动源中的“query”），接收器为“Yes” |

**例子：**

```json
{
    "name": "DynamicsDataset",
    "properties": {
        "type": "DynamicsEntity",
        "schema": [],
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

若要从 Dynamics 复制数据，复制活动的 **source** 节需要支持以下属性。

| properties | 描述 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动源的 type 属性必须设置为 **DynamicsSource**、**DynamicsCrmSource** 或 **CommonDataServiceForAppsSource**。 | 是 |
| query | FetchXML 是在 Dynamics（联机和本地）中使用的专属查询语言。 请参阅以下示例。 若要了解详细信息，请参阅[使用 FetchXML 生成查询](https://msdn.microsoft.com/library/gg328332.aspx)。 | 否（如果指定了数据集中的“entityName”） |

>[!NOTE]
>即使在 FetchXML 查询中配置的列投影不包含 PK 列，也将始终复制该列。

> [!IMPORTANT]
>- 从 Dynamics 复制数据时，从 Dynamics 到接收器的显式列映射为可选，但若要确保确定性的复制结果，则强烈建议使用它。
>- 在创作 UI 中导入架构时，ADF 通过对 Dynamics 查询结果中的前几行进行采样来初始化源列列表，将省略顶部行中不含任何值的列。 如果没有明确的映射，则相同的行为也适用于复制执行。 可以查看列并将更多列添加到映射，这将在副本运行时起作用。

**例子：**

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

若要将数据复制到 Dynamics，复制活动的 **sink** 节需要支持以下属性。

| properties | 描述 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动接收器的 type 属性必须设置为 **DynamicsSink**、**DynamicsCrmSink** 或 **CommonDataServiceForAppsSink**。 | 是 |
| writeBehavior | 操作的写入行为。<br/>允许的值是 **"Upsert"。** | 是 |
| alternateKeyName | 指定在实体上定义的可选密钥名称以执行“更新插入”。 | 否 |
| writeBatchSize | 每批中写入到 Dynamics 的数据行计数。 | 否（默认值为 10） |
| ignoreNullValues | 指示是否忽略 null 值从输入数据（键字段除外）期间写入操作。<br/>允许的值**是真****假**的。<br>- **True**：保留目标中的数据对象时进行更新插入/更新操作保持不变。 插入在执行插入操作时定义的默认值。<br/>- **False**：执行更新插入/更新操作时为 NULL 更新目标对象中的数据。 执行插入操作时插入 NULL 值。 | 否（默认值为 false） |

>[!NOTE]
>"**写入BatchSize"** 的接收器的默认值和 Dynamics 接收器的复制活动"**[并行副本](copy-activity-performance.md#parallel-copy)**"均为 10。 因此，会将 100 条记录同时提交到 Dynamics。

对于 Dynamics 365（联机版），存在[每个组织进行 2 次并发批量调用](https://msdn.microsoft.com/library/jj863631.aspx#Run-time%20limitations)的限制。 如果超出此限制，则会在执行第一个请求之前引发“服务器忙”错误。 保持“writeBatchSize”小于或等于 10 可避免这种并发调用的限制。

**"writeBatchSize"** 和"**并行副本**"的最佳组合取决于实体的架构，例如列数、行大小、连接到这些调用的插件/工作流/工作流活动的数量等。根据 Dynamics 服务，10 个 writeBatchSize = 10 并行副本的默认设置是建议，对于大多数 Dynamics 实体来说，这适用于大多数 Dynamics 实体，但可能不是最佳性能。 你可以通过在复制活动设置中调整组合来调整性能。

**例子：**

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
| AttributeTypeCode.Boolean | Boolean | ✓ | ✓ |
| AttributeType.Customer | Guid | ✓ | |
| AttributeType.DateTime | Datetime | ✓ | ✓ |
| AttributeType.Decimal | Decimal | ✓ | ✓ |
| AttributeType.Double | Double | ✓ | ✓ |
| AttributeType.EntityName | String | ✓ | ✓ |
| AttributeType.Integer | Int32 | ✓ | ✓ |
| AttributeType.Lookup | Guid | ✓ | ✓（关联单个目标） |
| AttributeType.ManagedProperty | Boolean | ✓ | |
| AttributeType.Memo | String | ✓ | ✓ |
| AttributeType.Money | Decimal | ✓ | ✓ |
| AttributeType.Owner | Guid | ✓ | |
| AttributeType.Picklist | Int32 | ✓ | ✓ |
| AttributeType.Uniqueidentifier | Guid | ✓ | ✓ |
| AttributeType.String | String | ✓ | ✓ |
| AttributeType.State | Int32 | ✓ | ✓ |
| AttributeType.Status | Int32 | ✓ | ✓ |

> [!NOTE]
> Dynamics 数据类型 AttributeType.CalendarRules、AttributeType.MultiSelectPicklist 和 AttributeType.PartyList 不受支持。

## <a name="lookup-activity-properties"></a>Lookup 活动属性

若要了解有关属性的详细信息，请查看 [Lookup 活动](control-flow-lookup-activity.md)。

## <a name="next-steps"></a>后续步骤
有关数据工厂中复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。
