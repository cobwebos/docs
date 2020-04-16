---
title: 在 Azure Blob 存储中复制和转换数据
description: 了解如何使用数据工厂向/从 Blob 存储复制数据，以及如何在 Blob 存储中转换数据。
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/09/2020
ms.openlocfilehash: b04ff409c95980a1569a2709a475dd8ec74d59b3
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415481"
---
# <a name="copy-and-transform-data-in-azure-blob-storage-by-using-azure-data-factory"></a>使用 Azure 数据工厂在 Azure Blob 存储中复制和转换数据

> [!div class="op_single_selector" title1="选择所使用的数据工厂服务版本："]
> * [版本 1](v1/data-factory-azure-blob-connector.md)
> * [当前版本](connector-azure-blob-storage.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述了如何使用 Azure 数据工厂中的复制活动从 Azure Blob 存储复制数据，以及使用数据流在 Azure Blob 存储中转换数据。 若要了解 Azure 数据工厂，请阅读[介绍性文章](introduction.md)。

>[!TIP]
>对于数据湖或数据仓库迁移方案，请从[使用 Azure 数据工厂将数据从数据湖或数据仓库迁移到 Azure](data-migration-guidance-overview.md)中了解详细信息。

## <a name="supported-capabilities"></a>支持的功能

以下活动支持此 Azure Blob 连接器：

- 带有[支持的源或接收器矩阵](copy-activity-overview.md)的[复制活动](copy-activity-overview.md)
- [映射数据流](concepts-data-flow-overview.md)
- [查找活动](control-flow-lookup-activity.md)
- [获取元数据活动](control-flow-get-metadata-activity.md)
- [删除活动](delete-activity.md)

对于复制活动，此 Blob 存储连接器支持：

- 向/从常规用途的 Azure 存储帐户和热/冷 Blob 存储复制 Blob。 
- 使用帐户密钥身份验证、服务共享访问签名身份验证、服务主体身份验证或 Azure 资源的托管标识身份验证复制 Blob。
- 从块、追加或页 Blob 中复制 Blob，并将数据仅复制到块 Blob。
- 按原样复制 Blob，或者使用[支持的文件格式和压缩编解码器](supported-file-formats-and-compression-codecs.md)分析或生成 Blob。
- [在复制期间保留文件元数据](#preserve-metadata-during-copy)。

>[!IMPORTANT]
>如果在 Azure 存储防火墙设置上启用了“允许信任的 Microsoft 服务访问此存储帐户”**** 选项，并且要使用 Azure 集成运行时连接到 Blob 存储，则必须使用[托管标识身份验证](#managed-identity)。

## <a name="get-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

对于特定于 Blob 存储的数据工厂实体，以下部分提供了有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

Azure Blob 连接器支持以下身份验证类型，有关详细信息，请参阅相应的部分：

- [帐户密钥身份验证](#account-key-authentication)
- [共享访问签名身份验证](#shared-access-signature-authentication)
- [服务主体身份验证](#service-principal-authentication)
- [Azure 资源身份验证的托管标识](#managed-identity)

>[!NOTE]
>使用 PolyBase 将数据载入 SQL 数据仓库时，如果源或暂存 Blob 存储配置了虚拟网络终结点，则必须按照 PolyBase 的要求使用托管标识身份验证，并使用版本 3.18 或更高版本的自承载集成运行时。 请参阅[托管标识身份验证](#managed-identity)部分，其中提供了更多配置先决条件。

>[!NOTE]
>HDInsights 和 Azure 机器学习活动仅支持 Azure Blob 存储帐户密钥身份验证。

### <a name="account-key-authentication"></a>帐户密钥身份验证

若要使用存储帐户密钥身份验证，需支持以下属性：

| properties | 说明 | 必选 |
|:--- |:--- |:--- |
| type | type 属性必须设置为 **AzureBlobStorage**（建议）或 **AzureStorage**（参阅下面的注释）。 |是 |
| connectionString | 为 connectionString 属性指定连接到存储所需的信息。 <br/> 还可以将帐户密钥放在 Azure 密钥保管库中，并从连接字符串中拉取 `accountKey` 配置。 有关更多详细信息，请参阅以下示例和[在 Azure 密钥保管库中存储凭据](store-credentials-in-key-vault.md)一文。 |是 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如果数据存储位于专用网络，则可以使用 Azure 集成运行时或自承载集成运行时。 如果未指定，则使用默认 Azure Integration Runtime。 |否 |

>[!NOTE]
>使用帐户密钥身份验证时，不支持辅助 Blob 服务终结点。 可以使用其他身份验证类型。

>[!NOTE]
>如果使用的是“AzureStorage”类型链接服务，它仍然按原样受支持，但建议今后使用此新的“AzureBlobStorage”链接服务类型。

**例子：**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**示例：在 Azure 密钥保管库中存储帐户密钥**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;",
            "accountKey": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }            
    }
}
```

### <a name="shared-access-signature-authentication"></a>共享访问签名身份验证

共享访问签名对存储帐户中的资源提供委托访问。 使用共享访问签名可以在指定的时间内授予客户端对存储帐户中对象的有限访问权限。 无需共享帐户访问密钥。 共享访问签名是一个 URI，在其查询参数中包含对存储资源已验证访问所需的所有信息。 若要使用共享访问签名访问存储资源，客户端只需将共享访问签名传入到相应的构造函数或方法。 有关共享访问签名的详细信息，请参阅[共享访问签名：了解共享访问签名模型](../storage/common/storage-dotnet-shared-access-signature-part-1.md)。

> [!NOTE]
>- 数据工厂现在同时支持**服务共享访问签名**和**帐户共享访问签名**。 有关共享访问签名的详细信息，请参阅[使用共享访问签名 (SAS) 授予对 Azure 存储资源的有限访问权限](../storage/common/storage-sas-overview.md)。
>- 在稍后的数据集配置中，文件夹路径是从容器级别开始的绝对路径。 需要配置与 SAS URI 中的路径一致的路径。

若要使用共享访问签名身份验证，需支持以下属性：

| properties | 说明 | 必选 |
|:--- |:--- |:--- |
| type | type 属性必须设置为 **AzureBlobStorage**（建议）或 **AzureStorage**（参阅下面的注释）。 |是 |
| sasUri | 指定存储资源（例如 Blob/容器）的共享访问签名 URI。 <br/>将此字段标记为 SecureString，以便安全地将其存储在数据工厂中。 还可以将 SAS 令牌放在 Azure 密钥保管库中，以利用自动轮换以及删除令牌部分。 有关更多详细信息，请参阅以下示例和[在 Azure 密钥保管库中存储凭据](store-credentials-in-key-vault.md)一文。 |是 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如果数据存储位于专用网络，则可以使用 Azure 集成运行时或自承载集成运行时。 如果未指定，则使用默认 Azure Integration Runtime。 |否 |

>[!NOTE]
>如果使用的是“AzureStorage”类型链接服务，它仍然按原样受支持，但建议今后使用此新的“AzureBlobStorage”链接服务类型。

**例子：**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource e.g. https://<container>.blob.core.windows.net/?sv=<storage version>&amp;st=<start time>&amp;se=<expire time>&amp;sr=<resource>&amp;sp=<permissions>&amp;sip=<ip range>&amp;spr=<protocol>&amp;sig=<signature>>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**示例：在 Azure 密钥保管库中存储帐户密钥**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource without token e.g. https://<container>.blob.core.windows.net/>"
            },
            "sasToken": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

在创建共享访问签名 URI 时，请注意以下几点：

- 根据链接服务（读取、写入、读/写）在数据工厂中的用法，设置针对对象的适当读/写权限。
- 根据需要设置“到期时间”****。 确保存储对象的访问权限不会在管道的活动期限内过期。
- 应该根据需要在正确的容器/Blob 中创建 URI。 数据工厂可以使用 Blob 的共享访问签名 URI 访问该特定 Blob。 数据工厂可以使用 Blob 存储容器的共享访问签名 URI 迭代该容器中的 Blob。 以后若要提供更多/更少对象的访问权限或需要更新共享访问签名 URI，请记得使用新 URI 更新链接服务。

### <a name="service-principal-authentication"></a>服务主体身份验证

有关 Azure 存储服务主体身份验证的一般信息，请参阅[使用 Azure Active Directory 对 Azure 存储访问进行身份验证](../storage/common/storage-auth-aad.md)。

若要使用服务主体身份验证，请执行以下步骤：

1. 遵循[将应用程序注册到 Azure AD 租户](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant)，在 Azure Active Directory (Azure AD) 中注册一个应用程序实体。 记下下面的值，这些值用于定义链接服务：

    - 应用程序 ID
    - 应用程序密钥
    - 租户 ID

2. 授予服务主体在 Azure Blob 存储中的适当权限。 如需了解角色的更多详情，请参阅[使用 RBAC 管理对 Azure 存储数据的访问权限](../storage/common/storage-auth-aad-rbac.md)。

    - 作为源****，在访问控制 (IAM) 中，至少授予“存储 Blob 数据读取者”**** 角色。
    - **对于接收器**，请在访问控制 (IAM) 中，至少授予“存储 Blob 数据参与者”角色。****

Azure Blob 存储链接服务支持以下属性：

| properties | 说明 | 必选 |
|:--- |:--- |:--- |
| type | type 属性必须设置为 **AzureBlobStorage**。 |是 |
| serviceEndpoint | 使用 `https://<accountName>.blob.core.windows.net/` 模式指定 Azure Blob 存储服务终结点。 |是 |
| servicePrincipalId | 指定应用程序的客户端 ID。 | 是 |
| servicePrincipalKey | 指定应用程序的密钥。 将此字段标记为**SecureString，** 以将其安全地存储在数据工厂中，或[引用存储在 Azure 密钥保管库中的机密](store-credentials-in-key-vault.md)。 | 是 |
| tenant | 指定应用程序的租户信息（域名或租户 ID）。 将鼠标悬停在 Azure 门户右上角进行检索。 | 是 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如果数据存储位于专用网络，则可以使用 Azure 集成运行时或自承载集成运行时。 如果未指定，则使用默认 Azure Integration Runtime。 |否 |

>[!NOTE]
>服务主体身份验证仅受“AzureBlobStorage”类型链接服务的支持，而不受以前的“AzureStorage”类型链接服务的支持。

**例子：**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {            
            "serviceEndpoint": "https://<accountName>.blob.core.windows.net/",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>" 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>Azure 资源身份验证的托管标识

可将数据工厂与代表此特定数据工厂的 [Azure 资源托管标识](data-factory-service-identity.md)相关联。 可以像使用自己的服务主体一样，直接使用此托管标识进行 Blob 存储身份验证。 此指定工厂可通过此方法访问以及从/向 Blob 存储复制数据。

有关 Azure 存储身份验证的常规信息，请参阅[使用 Azure Active Directory 验证对 Azure 存储的访问权限](../storage/common/storage-auth-aad.md)。 若要使用 Azure 资源的托管标识身份验证，请执行以下步骤：

1. 通过复制与工厂一起生成的**托管标识对象 ID**的值来[检索数据工厂托管标识信息](data-factory-service-identity.md#retrieve-managed-identity)。

2. 授予托管标识在 Azure Blob 存储中的适当权限。 如需了解角色的更多详情，请参阅[使用 RBAC 管理对 Azure 存储数据的访问权限](../storage/common/storage-auth-aad-rbac.md)。

    - 作为源****，在访问控制 (IAM) 中，至少授予“存储 Blob 数据读取者”**** 角色。
    - **对于接收器**，请在访问控制 (IAM) 中，至少授予“存储 Blob 数据参与者”角色。****

>[!IMPORTANT]
>如果使用 PolyBase 将 Blob（作为源或暂存）中的数据载入 SQL 数据仓库，则在对 Blob 使用托管标识身份验证时，请确保还按照[此指南](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage)中的步骤 1 和步骤 2 完成以下任务：1) 向 Azure Active Directory (Azure AD) 注册 SQL 数据库服务器，2) 将存储 Blob 数据参与者角色分配给 SQL 数据库服务器；其余的任务由数据工厂处理。 如果 Blob 存储配置了 Azure 虚拟网络终结点，要使用 PolyBase 从其中加载数据，必须使用 PolyBase 所需的托管标识身份验证。

Azure Blob 存储链接服务支持以下属性：

| properties | 说明 | 必选 |
|:--- |:--- |:--- |
| type | type 属性必须设置为 **AzureBlobStorage**。 |是 |
| serviceEndpoint | 使用 `https://<accountName>.blob.core.windows.net/` 模式指定 Azure Blob 存储服务终结点。 |是 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如果数据存储位于专用网络，则可以使用 Azure 集成运行时或自承载集成运行时。 如果未指定，则使用默认 Azure Integration Runtime。 |否 |

> [!NOTE]
> Azure 资源的托管标识身份验证仅受“AzureBlobStorage”类型链接服务支持，而不受以前的“AzureStorage”类型链接服务支持。 

**例子：**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {            
            "serviceEndpoint": "https://<accountName>.blob.core.windows.net/"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

基于格式的数据集中 `location` 设置下的 Azure Blob 支持以下属性：

| properties   | 说明                                                  | 必选 |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | 数据集中位置的 type 属性必须设置为 AzureBlobStorageLocation****。 | 是      |
| 容器 (container)  | Blob 容器。                                          | 是      |
| folderPath | 给定容器下的文件夹路径。 如果要使用通配符筛选文件夹，请跳过此设置并在活动源设置中指定。 | 否       |
| fileName   | 给定容器 + folderPath 下的文件名。 如果要使用通配符筛选文件，请跳过此设置并在活动源设置中指定。 | 否       |

**例子：**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 Blob 存储源和接收器支持的属性列表。

### <a name="blob-storage-as-a-source-type"></a>将 Blob 存储用作源类型

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

基于格式的复制源中 `storeSettings` 设置下的 Azure Blob 支持以下属性：

| properties                 | 说明                                                  | 必选                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | 下`storeSettings`的类型属性必须设置为**AzureBlob 存储读取设置**。 | 是                                           |
| recursive                | 指示是要从子文件夹中以递归方式读取数据，还是只从指定的文件夹中读取数据。 请注意，当 recursive 设置为 true 且接收器是基于文件的存储时，将不会在接收器上复制或创建空的文件夹或子文件夹。 允许的值**为 true（** 默认值）和**false**。 | 否                                            |
| 前缀                   | 在数据集中配置为筛选源 blob 的给定容器下的 blob 名称的前缀。 其名称以此前缀开头的 Blob 被选中。 <br>仅当未指定 `wildcardFolderPath` 和 `wildcardFileName` 属性时适用。 | 否                                                          |
| wildcardFolderPath       | 数据集中配置的给定容器下包含通配符的文件夹路径，用于筛选源文件夹。 <br>允许的通配符为：`*`（匹配零个或更多个字符）和 `?`（匹配零个或单个字符）；如果实际文件夹名称中包含通配符或此转义字符，请使用 `^` 进行转义。 <br>请参阅[文件夹和文件筛选器示例](#folder-and-file-filter-examples)中的更多示例。 | 否                                            |
| wildcardFileName         | 给定的容器 + folderPath/wildcardFolderPath 下带有通配符的文件名，用于筛选源文件。 <br>允许的通配符为：`*`（匹配零个或更多个字符）和 `?`（匹配零个或单个字符）；如果实际文件夹名称中包含通配符或此转义字符，请使用 `^` 进行转义。  请参阅[文件夹和文件筛选器示例](#folder-and-file-filter-examples)中的更多示例。 | 如果数据集中未指定 `fileName`，则为“是” |
| modifiedDatetimeStart    | 基于属性的文件筛选器：上次修改。 如果文件的上次修改时间在 `modifiedDatetimeStart` 和 `modifiedDatetimeEnd` 之间的时间范围内，则将选中这些文件。 该时间应用于 UTC 时区，格式为“2018-12-01T05:00:00Z”。 <br> 属性可以为 NULL，这意味着不向数据集应用任何文件特性筛选器。  如果 `modifiedDatetimeStart` 具有日期/时间值，但 `modifiedDatetimeEnd` 为 NULL，则意味着将选中“上次修改时间”属性大于或等于该日期/时间值的文件。  如果 `modifiedDatetimeEnd` 具有日期/时间值，但 `modifiedDatetimeStart` 为 NULL，则意味着将选中“上次修改时间”属性小于该日期/时间值的文件。 | 否                                            |
| modifiedDatetimeEnd      | 同上。                                               | 否                                            |
| maxConcurrentConnections | 可以同时连接到存储库的连接数。 仅在要限制与数据存储的并发连接时指定。 | 否                                            |

> [!NOTE]
> 对于 Parquet/带分隔符的文本格式，仍然按原样支持下一部分中提到的 **BlobSource** 类型复制活动源，以实现向后兼容性。 建议你继续使用此新模型，并且 ADF 创作 UI 已切换为生成这些新类型。

**例子：**

```json
"activities":[
    {
        "name": "CopyFromBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
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
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true,
                    "wildcardFolderPath": "myfolder*A",
                    "wildcardFileName": "*.csv"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="blob-storage-as-a-sink-type"></a>用作接收器类型的 Blob 存储

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

基于格式的复制接收器中 `storeSettings` 设置下的 Azure Blob 支持以下属性：

| properties                 | 说明                                                  | 必选 |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | 下`storeSettings`的类型属性必须设置为**AzureBlob 存储写入设置**。 | 是      |
| copyBehavior             | 定义以基于文件的数据存储中的文件为源时的复制行为。<br/><br/>允许值包括：<br/><b>- PreserveHierarchy（默认值）</b>：保留目标文件夹中的文件层次结构。 从源文件到源文件夹的相对路径与从目标文件到目标文件夹的相对路径相同。<br/><b>- FlattenHierarchy</b>：源文件夹中的所有文件都位于目标文件夹的第一级。 目标文件具有自动生成的名称。 <br/><b>- MergeFiles</b>：将源文件夹中的所有文件合并到一个文件中。 如果指定了文件名或 Blob 名称，则合并文件的名称为指定名称。 否则，它是自动生成的文件名。 | 否       |
| 块大小因MB | 指定用于写入数据以阻止 Blob 的 MB 中的块大小。 [了解有关块 Blob 的更多](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs)。 <br/>允许的值**介于 4 到 100 MB 之间**。 <br/>默认情况下，ADF 会根据您的源存储类型和数据自动确定块大小。 对于非二进制复制到 Blob，默认块大小为 100 MB，以便最多容纳 4.95 TB 数据。 当数据不大时，它可能是最佳的，尤其是在使用自托管集成运行时时，网络不佳导致操作超时或性能问题。 您可以显式指定块大小，同时确保块SizeInMB_50000足够大以存储数据，否则复制活动运行将失败。 | 否 |
| maxConcurrentConnections | 可以同时连接到存储库的连接数。 仅在要限制与数据存储的并发连接时指定。 | 否       |

**例子：**

```json
"activities":[
    {
        "name": "CopyFromBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Parquet output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "ParquetSink",
                "storeSettings":{
                    "type": "AzureBlobStorageWriteSettings",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>文件夹和文件筛选器示例

本部分介绍使用通配符筛选器生成文件夹路径和文件名的行为。

| folderPath | fileName | recursive | 源文件夹结构和筛选结果（检索**粗体**文件）|
|:--- |:--- |:--- |:--- |
| `container/Folder*` | （为空，使用默认值） | false | 容器 (container)<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | （为空，使用默认值） | true | 容器 (container)<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**文件3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | `*.csv` | false | 容器 (container)<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | `*.csv` | true | 容器 (container)<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**文件3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="some-recursive-and-copybehavior-examples"></a>一些 recursive 和 copyBehavior 示例

本节介绍了将 recursive 和 copyBehavior 值进行不同组合所产生的复制操作行为。

| recursive | copyBehavior | 源文件夹结构 | 生成目标 |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用与源相同的结构创建目标文件夹 Folder1：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用以下结构创建目标 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 的自动生成的名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2 的自动生成的名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File3 的自动生成的名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File4 的自动生成的名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File5 的自动生成的名称 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用以下结构创建目标 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File5 的内容将合并到一个文件中，且自动生成文件名。 |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用以下结构创建目标文件夹 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>不会选取带有 File3、File4 和 File5 的 Subfolder1。 |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用以下结构创建目标文件夹 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 的自动生成的名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2 的自动生成的名称<br/><br/>不会选取带有 File3、File4 和 File5 的 Subfolder1。 |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用以下结构创建目标文件夹 Folder1<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 的内容将合并到一个文件中，且自动生成文件名。 File1 的自动生成的名称<br/><br/>不会选取带有 File3、File4 和 File5 的 Subfolder1。 |

## <a name="preserve-metadata-during-copy"></a>在复制期间保留元数据

将文件从 Amazon S3/Azure Blob/Azure Data Lake Storage Gen2 复制到 Azure Data Lake Storage Gen2/Azure Blob 时，可以选择保留文件元数据和数据。 从[保留元数据](copy-activity-preserve-metadata.md#preserve-metadata)中了解更多信息。

## <a name="mapping-data-flow-properties"></a>映射数据流属性

在映射数据流中转换数据时，可以从 Azure Blob 存储中读取和写入 JSON、Avro、分隔文本或镶木地板格式的文件。 有关详细信息，请参阅映射数据流功能中的[源转换](data-flow-source.md)和[接收器转换](data-flow-sink.md)。

### <a name="source-transformation"></a>源转换

在源转换中，可以从 Azure Blob 存储中的容器、文件夹或单个文件读取。 "**源"选项**选项卡允许您管理文件读取的方式。 

![源选项](media/data-flow/sourceOptions1.png "源选项")

**通配符路径：** 使用通配符模式将指示 ADF 在单个源转换中循环访问每个匹配的文件夹和文件。 这是处理单个流中的多个文件的有效方法。 使用将鼠标悬停在现有通配符模式上时出现的 + 符号添加多个通配符匹配模式。

从源容器中选择一系列与模式匹配的文件。 只能在数据集中指定容器。 因此，通配符路径还必须包括根文件夹中的文件夹路径。

通配符示例：

* ```*```表示任意一组字符
* ```**```表示递归目录嵌套
* ```?```替换一个字符
* ```[]```匹配括号中更多字符之一

* ```/data/sales/**/*.csv```获取 /数据/销售下的所有 csv 文件
* ```/data/sales/20??/**/```获取 20 世纪的所有文件
* ```/data/sales/*/*/*.csv```获取 csv 文件下 /数据/销售两个级别
* ```/data/sales/2004/*/12/[XY]1?.csv```在 2004 年 12 月获取所有 csv 文件，以 X 或 Y 开头，以两位数字开头

**分区根路径：** 如果文件源中的文件夹已采用```key=value```格式（例如，year_2019）进行分区，则可以将该分区文件夹树的顶层分配给数据流数据流中的列名称。

首先，设置通配符以包括分区文件夹的所有路径以及您希望读取的叶文件。

![分区源文件设置](media/data-flow/partfile2.png "分区文件设置")

使用分区根路径设置定义文件夹结构的顶层。 当您通过数据预览查看数据内容时，您将看到 ADF 将添加在每个文件夹级别中找到的已解析分区。

![分区根路径](media/data-flow/partfile1.png "分区根路径预览")

**文件列表：** 这是一个文件集。 创建包含要处理的相对路径文件的列表的文本文件。 指向此文本文件。

**要存储文件名的列：** 将源文件的名称存储在数据中的列中。 在此处输入新的列名以存储文件名字符串。

**完成后：** 选择在数据流运行后对源文件不执行任何操作、删除源文件或移动源文件。 移动的路径是相对的。

要将源文件移到处理后的另一个位置，首先选择文件操作的"移动"。 然后，设置"from"目录。 如果您没有为路径使用任何通配符，则"from"设置将与源文件夹位于同一文件夹。

如果您有具有通配符的源路径，则语法如下所示：

```/data/sales/20??/**/*.csv```

您可以将"从"指定为

```/data/sales```

和"到"作为

```/backup/priorSales```

在这种情况下，在 /data/sales 下获取的所有文件将移动到 /备份/优先销售。

> [!NOTE]
> 仅当从管道运行（管道调试或执行运行）启动数据流时，文件操作才会运行，该流使用管道中的执行数据流活动。 文件操作*不在*数据流调试模式下运行。

**按上次修改进行筛选：** 您可以通过指定上次修改文件的日期范围来筛选处理的文件。 所有日期时间均以 UTC 表示。 

### <a name="sink-properties"></a>接收器属性

在接收器转换中，可以写入 Azure Blob 存储中的容器或文件夹。 通过 **"设置"** 选项卡，您可以管理文件的写入方式。

![接收器选项](media/data-flow/file-sink-settings.png "接收器选项")

**清除文件夹：** 确定在写入数据之前是否清除目标文件夹。

**文件名选项：** 确定目标文件在目标文件夹中的命名方式。 文件名选项为：
   * **默认值**：允许 Spark 根据 PART 默认值命名文件。
   * **模式**：输入一种模式，以枚举每个分区的输出文件。 例如，**贷款\n\csv**将创建贷款1.csv，贷款2.csv，等等。
   * **每个分区**：每个分区输入一个文件名。
   * **作为列中的数据**：将输出文件设置为列的值。 路径相对于数据集容器，而不是目标文件夹。 如果数据集中包含文件夹路径，则该路径将被覆盖。
   * **输出到单个文件**：将分区的输出文件合并到单个命名文件中。 路径与数据集文件夹相关。 请注意，te 合并操作可能会根据节点大小失败。 不建议对大型数据集使用此选项。

**报价所有：** 确定是否将报价中的所有值括起来

## <a name="lookup-activity-properties"></a>Lookup 活动属性

若要了解有关属性的详细信息，请查看 [Lookup 活动](control-flow-lookup-activity.md)。

## <a name="getmetadata-activity-properties"></a>GetMetadata 活动属性

若要了解有关属性的详细信息，请查看 [GetMetadata 活动](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Delete 活动属性

若要了解有关属性的详细信息，请查看 [Delete 活动](delete-activity.md)

## <a name="legacy-models"></a>旧模型

>[!NOTE]
>仍按原样支持以下模型，以实现向后兼容性。 建议你以后使用前面部分中提到的新模型，ADF 创作 UI 已经切换到生成新模型。

### <a name="legacy-dataset-model"></a>旧数据集模型

| properties | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 数据集的类型属性必须设置为**AzureBlob**。 |是 |
| folderPath | 到 Blob 存储中的容器和文件夹的路径。 <br/><br/>不包含容器名称的路径支持通配符筛选器。 允许的通配符为：`*`（匹配零个或更多个字符）和 `?`（匹配零个或单个字符）；如果实际文件夹名称中包含通配符或此转义字符，请使用 `^` 进行转义。 <br/><br/>示例：“myblobcontainer/myblobfolder/”，请参阅[文件夹和文件筛选器示例](#folder-and-file-filter-examples)中的更多示例。 |对于复制/查找活动，为“是”；对于 GetMetadata 活动，为“否” |
| fileName | 指定的“folderPath”下 blob 的名称或通配符筛选器****。 如果没有为此属性指定任何值，则数据集会指向文件夹中的所有 Blob。 <br/><br/>对于筛选器，允许的通配符为：`*`（匹配零个或更多字符）和 `?`（匹配零个或单个字符）。<br/>- 示例 1：`"fileName": "*.csv"`<br/>- 示例 2：`"fileName": "???20180427.txt"`<br/>如果实际文件名内具有通配符或此转义符，请使用 `^` 进行转义。<br/><br/>当未为输出数据集指定文件名并在活动接收器中未指定**保留层次结构**时，复制活动将自动生成具有以下模式的 blob 名称："*数据"。活动运行 ID GUID]。[GUID 如果扁平层次结构]。[格式（如果已配置）。"[压缩如果配置]"，* 例如"数据.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz";如果使用表名而不是查询从表格源进行复制，则名称模式为 *"[表名]"。格式*。[如果配置压缩]"，* 例如"MyTable.csv"。 |否 |
| modifiedDatetimeStart | 基于属性的文件筛选器：上次修改。 如果文件的上次修改时间在 `modifiedDatetimeStart` 和 `modifiedDatetimeEnd` 之间的时间范围内，则将选中这些文件。 该时间应用于 UTC 时区，格式为“2018-12-01T05:00:00Z”。 <br/><br/> 请注意，当你要从大量文件中进行文件筛选时，启用此设置将影响数据移动的整体性能。 <br/><br/> 属性可以为 NULL，这意味着不向数据集应用任何文件属性筛选器。  如果 `modifiedDatetimeStart` 具有日期/时间值，但 `modifiedDatetimeEnd` 为 NULL，则意味着将选中“上次修改时间”属性大于或等于该日期/时间值的文件。  如果 `modifiedDatetimeEnd` 具有日期/时间值，但 `modifiedDatetimeStart` 为 NULL，则意味着将选中“上次修改时间”属性小于该日期/时间值的文件。| 否 |
| modifiedDatetimeEnd | 基于属性的文件筛选器：上次修改。 如果文件的上次修改时间在 `modifiedDatetimeStart` 和 `modifiedDatetimeEnd` 之间的时间范围内，则将选中这些文件。 该时间应用于 UTC 时区，格式为“2018-12-01T05:00:00Z”。 <br/><br/> 请注意，当你要从大量文件中进行文件筛选时，启用此设置将影响数据移动的整体性能。 <br/><br/> 属性可以为 NULL，这意味着不向数据集应用任何文件属性筛选器。  如果 `modifiedDatetimeStart` 具有日期/时间值，但 `modifiedDatetimeEnd` 为 NULL，则意味着将选中“上次修改时间”属性大于或等于该日期/时间值的文件。  如果 `modifiedDatetimeEnd` 具有日期/时间值，但 `modifiedDatetimeStart` 为 NULL，则意味着将选中“上次修改时间”属性小于该日期/时间值的文件。| 否 |
| format | 若要在基于文件的存储之间按原样复制文件（二进制副本），可以在输入和输出数据集定义中跳过格式节。<br/><br/>如果想要分析或生成具有特定格式的文件，则下面是支持的文件格式类型：**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat** 和 **ParquetFormat**。 将**格式**下**的类型**属性设置为这些值之一。 有关详细信息，请参阅[文本格式](supported-file-formats-and-compression-codecs-legacy.md#text-format) [、JSON 格式](supported-file-formats-and-compression-codecs-legacy.md#json-format)[、Avro 格式](supported-file-formats-and-compression-codecs-legacy.md#avro-format)[、Orc 格式](supported-file-formats-and-compression-codecs-legacy.md#orc-format)和[Parquet 格式](supported-file-formats-and-compression-codecs-legacy.md#parquet-format)部分。 |否（仅适用于二进制复制方案） |
| compression | 指定数据的压缩类型和级别。 有关详细信息，请参阅[受支持的文件格式和压缩编解码器](supported-file-formats-and-compression-codecs-legacy.md#compression-support)。<br/>支持的类型是 GZip、Deflate、BZip2 和**ZipDeflate**。 **GZip** **Deflate** **BZip2**<br/>支持的级别**是最佳**和**最快的**。 |否 |

>[!TIP]
>如需复制文件夹下的所有 blob，请仅指定 **** folderPath。<br>如需复制具有给定名称的单个 blob，请指定文件夹部分的 **** folderPath 和文件名部分的 fileName****。<br>如需复制文件夹下的一部分 blob，请指定文件夹部分的 **** folderPath 和通配符筛选器部分的 fileName****。 

**例子：**

```json
{
    "name": "AzureBlobDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
            "referenceName": "<Azure Blob storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
            "fileName": "*",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

### <a name="legacy-copy-activity-source-model"></a>旧复制活动源模型

| properties | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 必须将复制活动源的类型属性设置为**BlobSource**。 |是 |
| recursive | 指示是要从子文件夹中以递归方式读取数据，还是只从指定的文件夹中读取数据。 请注意，当 recursive 设置为 true 且接收器是基于文件的存储时，将不会在接收器上复制或创建空的文件夹或子文件夹。<br/>允许的值**为 true（** 默认值）和**false**。 | 否 |
| maxConcurrentConnections | 可以同时连接到存储库的连接数。 仅在要限制与数据存储的并发连接时指定。 | 否 |

**例子：**

```json
"activities":[
    {
        "name": "CopyFromBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Blob input dataset name>",
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
                "type": "BlobSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="legacy-copy-activity-sink-model"></a>旧复制活动接收器模型

| properties | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动接收器的类型属性必须设置为**BlobSink**。 |是 |
| copyBehavior | 定义以基于文件的数据存储中的文件为源时的复制行为。<br/><br/>允许值包括：<br/><b>- PreserveHierarchy（默认值）</b>：保留目标文件夹中的文件层次结构。 从源文件到源文件夹的相对路径与从目标文件到目标文件夹的相对路径相同。<br/><b>- FlattenHierarchy</b>：源文件夹中的所有文件都位于目标文件夹的第一级。 目标文件具有自动生成的名称。 <br/><b>- MergeFiles</b>：将源文件夹中的所有文件合并到一个文件中。 如果指定了文件名或 Blob 名称，则合并文件的名称为指定名称。 否则，它是自动生成的文件名。 | 否 |
| maxConcurrentConnections | 可以同时连接到存储库的连接数。 仅在要限制与数据存储的并发连接时指定。 | 否 |

**例子：**

```json
"activities":[
    {
        "name": "CopyToBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Blob output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "BlobSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

## <a name="next-steps"></a>后续步骤

有关数据工厂中复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。
