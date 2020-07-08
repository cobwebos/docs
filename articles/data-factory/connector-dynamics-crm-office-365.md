---
title: 在 Dynamics (Common Data Service) 中复制数据
description: 了解如何通过在数据工厂管道中使用复制活动，将数据从 Microsoft Dynamics CRM 或 Microsoft Dynamics 365 （Common Data Service）复制到支持的接收器数据存储，或者从支持的源数据存储复制到 Dynamics CRM 或 Dynamics 365。
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
ms.date: 06/10/2020
ms.openlocfilehash: a7a8af505394b5bf860778b9872434cdacf54210
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84887013"
---
# <a name="copy-data-from-and-to-dynamics-365-common-data-service-or-dynamics-crm-by-using-azure-data-factory"></a>使用 Azure 数据工厂从/向 Dynamics 365 (Common Data Service) 或 Dynamics CRM 复制数据
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述如何使用 Azure 数据工厂中的复制活动将数据从和复制到 Microsoft Dynamics 365 和 Microsoft Dynamics CRM。 它基于提供复制活动的一般概述的[复制活动概述](copy-activity-overview.md)一文。

## <a name="supported-capabilities"></a>支持的功能

以下活动支持此连接器：

- [复制活动](copy-activity-overview.md)和[支持的源和接收器矩阵](copy-activity-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)

可以将数据从 Dynamics 365 (Common Data Service) 或 Dynamics CRM 复制到任何支持的接收器数据存储。 还可以将数据从任何支持的源数据存储复制到 Dynamics 365 (Common Data Service) 或 Dynamics CRM。 有关复制活动支持作为源和接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

对于联机和本地，此 Dynamics 连接器支持 Dynamics 版本7到9。 更具体地说：

- 版本7映射到 Dynamics CRM 2015。
- 版本8映射到 Dynamics CRM 2016 和 Dynamics 365 的早期版本。
- 版本9映射到 Dynamics 365 的更高版本。

请参阅下表，了解 Dynamics 版本和产品的支持的身份验证类型和配置。

| Dynamics 版本 | 身份验证类型 | 链接的服务示例 |
|:--- |:--- |:--- |
| Common Data Service <br/><br/> Dynamics 365 联机 <br/><br/> Dynamics CRM 联机 | Azure Active Directory （Azure AD）服务主体 <br/><br/> Office 365 | [Dynamics online 和 Azure AD 服务-主体或 Office 365 身份验证](#dynamics-365-and-dynamics-crm-online) |
| 本地 Dynamics 365，具有面向 internet 的部署（IFD） <br/><br/> 带有 IFD 的本地 Dynamics CRM 2016 <br/><br/> 带有 IFD 的本地 Dynamics CRM 2015 | IFD | [与 IFD 和 IFD 身份验证的本地动态](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

具体而言，对于 Dynamics 365，支持以下应用程序类型：

- Dynamics 365 for Sales
- Dynamics 365 for Customer Service
- Dynamics 365 for Field Service
- Dynamics 365 for Project Service Automation
- Dynamics 365 for Marketing

此连接器不支持其他应用程序类型，如财务、运营和人才。

此 Dynamics 连接器基于 [Dynamics XRM 工具](https://docs.microsoft.com/dynamics365/customer-engagement/developer/build-windows-client-applications-xrm-tools)构建。

>[!TIP]
>若要从 Dynamics 365 财务和操作复制数据，可以使用[DYNAMICS AX 连接器](connector-dynamics-ax.md)。

## <a name="prerequisites"></a>先决条件

若要将此连接器与 Azure AD 服务主体身份验证配合使用，必须在 Common Data Service 或 Dynamics 中设置服务器到服务器（S2S）身份验证。 有关详细步骤，请参阅[此文](https://docs.microsoft.com/powerapps/developer/common-data-service/build-web-applications-server-server-s2s-authentication)。

## <a name="get-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

以下部分提供了有关各属性的详细信息，这些属性用于定义特定于 Dynamics 的数据工厂实体。

## <a name="linked-service-properties"></a>链接服务属性

Dynamics 链接服务支持以下属性。

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 和 Dynamics CRM online

| Property | 描述 | 必需 |
|:--- |:--- |:--- |
| type | Type 属性必须设置为 "Dynamics"、"DynamicsCrm" 或 "CommonDataServiceForApps"。 | 是 |
| deploymentType | Dynamics 实例的部署类型。 对于 Dynamics Online，此值必须为 "Online"。 | 是 |
| serviceUri | Dynamics 实例的服务 URL。 例如 https://adfdynamics.crm.dynamics.com。 | 是 |
| authenticationType | 要连接到 Dynamics 服务器的身份验证类型。 有效值为 "AADServicePrincipal" 和 "Office365"。 | 是 |
| servicePrincipalId | Azure AD 应用程序的客户端 ID。 | 当身份验证为 "AADServicePrincipal" 时为 "是" |
| servicePrincipalCredentialType | 用于服务主体身份验证的凭据类型。 有效值为 "ServicePrincipalKey" 和 "ServicePrincipalCert"。 | 当身份验证为 "AADServicePrincipal" 时为 "是" |
| servicePrincipalCredential | 服务主体凭据。 <br/><br/>使用 "ServicePrincipalKey" 作为凭据类型时， `servicePrincipalCredential` 可以是 Azure 数据工厂在链接的服务部署时对其进行加密的字符串。 也可以是对 Azure Key Vault 中机密的引用。 <br/><br/>如果使用 "ServicePrincipalCert" 作为凭据，则 `servicePrincipalCredential` 必须是对 Azure Key Vault 中的证书的引用。 | 当身份验证为 "AADServicePrincipal" 时为 "是" |
| username | 要连接到 Dynamics 的用户名。 | 当身份验证为 "Office365" 时为 "是" |
| password | 指定为用户名的用户帐户的密码。 将此字段标记为 "SecureString" 以将其安全地存储在数据工厂中，或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 | 当身份验证为 "Office365" 时为 "是" |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如果未指定任何值，则属性使用默认的 Azure 集成运行时。 | 对于源为 "否"，如果源链接服务没有集成运行时，则为 "是" |

>[!NOTE]
>Dynamics 连接器以前使用了可选的**organizationName**属性来标识 dynamics CRM 或 dynamics 365 联机实例。 尽管该属性仍然有效，但建议改为指定新的**serviceUri**属性，以获得更好的实例发现性能。

#### <a name="example-dynamics-online-using-azure-ad-service-principal-and-key-authentication"></a>示例：使用 Azure AD 服务进行 Dynamics online-主体和密钥身份验证

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
#### <a name="example-dynamics-online-using-azure-ad-service-principal-and-certificate-authentication"></a>示例：使用 Azure AD 服务进行 Dynamics online-主体身份验证和证书身份验证

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

#### <a name="example-dynamics-online-using-office-365-authentication"></a>示例：使用 Office 365 身份验证的 Dynamics online

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

与 Dynamics online 比较的其他属性是**主机名**和**端口**。

| Property | 描述 | 必需 |
|:--- |:--- |:--- |
| type | Type 属性必须设置为 "Dynamics"、"DynamicsCrm" 或 "CommonDataServiceForApps"。 | 是的。 |
| deploymentType | Dynamics 实例的部署类型。 对于与 IFD 的本地动态，值必须为 "OnPremisesWithIfd"。| 是的。 |
| hostName | 本地 Dynamics 服务器的主机名称。 | 是的。 |
| port | 本地 Dynamics 服务器的端口。 | 不能。 默认值为443。 |
| organizationName | Dynamics 实例的组织名称。 | 是的。 |
| authenticationType | 要连接到 Dynamics 服务器的身份验证类型。 为带有 IFD 的本地 Dynamics 指定“Ifd” | 是的。 |
| username | 要连接到 Dynamics 的用户名。 | 是的。 |
| password | 为用户名指定的用户帐户的密码。 你可以将此字段标记为 "SecureString" 以将其安全地存储在数据工厂中。 也可以在 Key Vault 中存储密码，并允许复制活动在执行数据复制时从此处拉取。 在[在 Key Vault 中存储凭据](store-credentials-in-key-vault.md)中了解详细信息。 | 是的。 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如果未指定任何值，则属性使用默认的 Azure 集成运行时。 | 对于 source 为 No; 对于 sink，为 yes。 |

#### <a name="example-dynamics-on-premises-with-ifd-using-ifd-authentication"></a>示例：带有 IFD、使用 IFD 身份验证的本地 Dynamics

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

若要从/向 Dynamics 复制数据，支持以下属性：

| Property | 描述 | 必需 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为 "DynamicsEntity"、"DynamicsCrmEntity" 或 "CommonDataServiceForAppsEntity"。 |是 |
| entityName | 要检索的实体的逻辑名称。 | 如果活动源指定为 "query"，则对于源为 "否"，对于接收器为 "是" |

#### <a name="example"></a>示例

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

若要从 Dynamics 复制数据，复制活动**源**部分支持以下属性：

| Property | 描述 | 必需 |
|:--- |:--- |:--- |
| type | 复制活动源的 type 属性必须设置为 "DynamicsSource"、"DynamicsCrmSource" 或 "CommonDataServiceForAppsSource"。 | 是 |
| 查询 | FetchXML 是在 Dynamics online 和本地使用的专有查询语言。 请参阅以下示例。 若要了解详细信息，请参阅[使用 FetchXML 生成查询](https://msdn.microsoft.com/library/gg328332.aspx)。 | 如果 `entityName` 在数据集中指定，则为 No |

>[!NOTE]
>即使在 FetchXML 查询中配置的列投影不包含 PK 列，也将始终复制该列。

> [!IMPORTANT]
>- 从 Dynamics 复制数据时，可以选择从 Dynamics 到 sink 的显式列映射。 但我们强烈建议使用映射来确保确定性的复制结果。
>- 当数据工厂在创作 UI 中导入架构时，它将推断架构。 它通过采样 Dynamics query 结果中的前几行来初始化源列列表来实现此目的。 在这种情况下，将省略顶部行中没有值的列。 如果没有明确的映射，则相同的行为也适用于复制执行。 您可以查看更多的列并将其添加到在复制运行时中遵守的映射。

#### <a name="example"></a>示例

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

若要将数据复制到 Dynamics，复制活动**接收器**部分支持以下属性：

| Property | 描述 | 必需 |
|:--- |:--- |:--- |
| type | 复制活动接收器的 type 属性必须设置为 "DynamicsSink"、"DynamicsCrmSink" 或 "CommonDataServiceForAppsSink"。 | 是的。 |
| writeBehavior | 操作的写入行为。 值必须为 "Upsert"。 | 是 |
| alternateKeyName | 在实体上定义以执行 upsert 的备用密钥名称。 | 不能。 |
| writeBatchSize | 每批中写入到 Dynamics 的数据行计数。 | 不能。 默认值为 10。 |
| ignoreNullValues | 在写操作过程中是否忽略键字段之外的输入数据中的 null 值。<br/><br/>有效值为**TRUE**和**FALSE**：<ul><li>**TRUE**：执行 upsert 或更新操作时，目标对象中的数据保持不变。 插入在执行插入操作时定义的默认值。</li><li>**FALSE**：执行 upsert 或更新操作时，将目标对象中的数据更新为 null 值。 执行插入操作时插入 null 值。</li></ul> | 不能。 默认值为**FALSE**。 |

>[!NOTE]
>对于 Dynamics sink，接收器**writeBatchSize**和复制活动**[parallelCopies](copy-activity-performance-features.md#parallel-copy)** 的默认值均为10。 因此，默认情况下会将100记录同时提交到 Dynamics。

对于 Dynamics 365 online，[每个组织的并发批处理调用](https://msdn.microsoft.com/library/jj863631.aspx#Run-time%20limitations)数限制为两个。 如果超过该限制，则在第一个请求运行之前会引发 "服务器忙" 异常。 将**writeBatchSize**保持为10或更低，以避免这种限制的并发调用。

**WriteBatchSize**和**parallelCopies**的最佳组合取决于实体的架构。 架构元素包括与这些调用挂钩的列数、行大小和插件数、工作流或工作流活动数。 **WriteBatchSize** （10） &times; **parallelCopies** （10）的默认设置是根据 Dynamics 服务的建议。 此值适用于大多数 Dynamics 实体，尽管它可能无法提供最佳性能。 你可以通过在复制活动设置中调整组合来调整性能。

#### <a name="example"></a>示例

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

从 Dynamics 复制数据时，下表显示了从 Dynamics 数据类型到数据工厂临时数据类型的映射。 若要了解复制活动如何映射到源架构和数据类型映射到接收器，请参阅[架构和数据类型映射](copy-activity-schema-and-type-mapping.md)。

使用以下映射表，在基于源 Dynamics 数据类型的数据集结构中配置相应的数据工厂数据类型：

| Dynamics 数据类型 | 数据工厂临时数据类型 | 支持用作源 | 支持用作接收器 |
|:--- |:--- |:--- |:--- |
| AttributeTypeCode.BigInt | Long | ✓ | ✓ |
| AttributeTypeCode.Boolean | 布尔 | ✓ | ✓ |
| AttributeType.Customer | GUID | ✓ | ✓（请参阅[指导](#writing-data-to-a-lookup-field)） |
| AttributeType.DateTime | datetime | ✓ | ✓ |
| AttributeType.Decimal | 小数 | ✓ | ✓ |
| AttributeType.Double | Double | ✓ | ✓ |
| AttributeType.EntityName | String | ✓ | ✓ |
| AttributeType.Integer | Int32 | ✓ | ✓ |
| AttributeType.Lookup | GUID | ✓ | ✓（请参阅[指导](#writing-data-to-a-lookup-field)） |
| AttributeType.ManagedProperty | 布尔 | ✓ | |
| AttributeType.Memo | String | ✓ | ✓ |
| AttributeType.Money | 小数 | ✓ | ✓ |
| AttributeType.Owner | GUID | ✓ | ✓（请参阅[指导](#writing-data-to-a-lookup-field)） |
| AttributeType.Picklist | Int32 | ✓ | ✓ |
| AttributeType.Uniqueidentifier | GUID | ✓ | ✓ |
| AttributeType.String | String | ✓ | ✓ |
| AttributeType.State | Int32 | ✓ | ✓ |
| AttributeType.Status | Int32 | ✓ | ✓ |

> [!NOTE]
> Dynamics 数据类型**AttributeType、attributetype.calendarrules**、 **AttributeType**和**AttributeType**不受支持。

## <a name="writing-data-to-a-lookup-field"></a>将数据写入查找字段

若要将数据写入包含多个目标（如 Customer 和所有者）的查找字段，请按照以下指南和示例操作：

1. 使源包含字段值和相应的目标实体名称。
   - 如果所有记录都映射到相同的目标实体，请确保满足以下条件之一：
      - 源数据具有存储目标实体名称的列。
      - 你已在复制活动源中添加了一个额外的列来定义目标实体。
   - 如果不同的记录映射到不同的目标实体，请确保源数据包含存储相应目标实体名称的列。

1. 将值和实体引用列从源映射到接收器。 实体引用列必须映射到具有特殊命名模式的虚拟列 `{lookup_field_name}@EntityReference` 。 列实际上并不存在于 Dynamics 中。 它用于指示此列是给定设定多目标查找字段的元数据列。

例如，假定源包含以下两列：

- **GUID**类型的**CustomerField**列，它是 Dynamics 中目标实体的主键值。
- **String**类型的**目标**列，它是目标实体的逻辑名称。

还假设要将此类数据复制到类型为**Customer**的 sink Dynamics 实体字段**CustomerField** 。

在 "复制-活动列" 映射中，按如下所示映射两列：

- **CustomerField**到**CustomerField**。 此映射是正常的字段映射。
- **目标**为**CustomerField \@ EntityReference**。 接收器列是表示实体引用的虚拟列。 在映射中输入这样的字段名称，因为它们不会通过导入架构来显示。

![Dynamics 查找字段映射](./media/connector-dynamics-crm-office-365/connector-dynamics-lookup-field-column-mapping.png)

如果所有源记录都映射到相同的目标实体，并且源数据不包含目标实体名称，以下是快捷方式：在复制活动源中，添加其他列。 使用模式命名新列 `{lookup_field_name}@EntityReference` ，将值设置为目标实体名称，然后继续按正常方式进行列映射。 如果源和接收器列名称相同，则还可以跳过显式列映射，因为默认情况下，复制活动会按名称映射列。

![Dynamics 查找字段添加实体引用列](./media/connector-dynamics-crm-office-365/connector-dynamics-add-entity-reference-column.png)

## <a name="lookup-activity-properties"></a>Lookup 活动属性

若要了解有关属性的详细信息，请参阅[查找活动](control-flow-lookup-activity.md)。

## <a name="next-steps"></a>后续步骤
有关数据存储中的复制活动支持作为源和接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。
