---
title: 在 Azure Data Lake Storage Gen2 中复制和转换数据
description: 了解如何使用 Azure 数据工厂向/从 Azure Data Lake Storage Gen2 复制数据，并在 Azure Data Lake Storage Gen2 中转换数据。
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/25/2020
ms.openlocfilehash: efa4ec42396a51cbbc93a53e5892177bad0d87fb
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83649667"
---
# <a name="copy-and-transform-data-in-azure-data-lake-storage-gen2-using-azure-data-factory"></a>使用 Azure 数据工厂在 Azure Data Lake Storage Gen2 中复制和转换数据

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Lake Storage Gen2 (ADLS Gen2) 是一组专用于大数据分析的功能，内置于 [Azure Blob 存储](../storage/blobs/storage-blobs-introduction.md)中。 可使用它通过文件系统和对象存储范例与数据进行交互。

本文概述了如何使用 Azure 数据工厂中的 Copy 活动从/向 Azure Data Lake Storage Gen2 复制数据，并使用数据流在 Azure Data Lake Storage Gen2 中转换数据。 若要了解 Azure 数据工厂，请阅读[介绍性文章](introduction.md)。

>[!TIP]
>对于数据湖或数据仓库迁移方案，若要了解详细信息，请参阅[使用 Azure 数据工厂将数据从数据湖或数据仓库迁移到 Azure](data-migration-guidance-overview.md)。

## <a name="supported-capabilities"></a>支持的功能

以下活动支持此 Azure Data Lake Storage Gen2 连接器：

- 包含[支持的源/接收器矩阵](copy-activity-overview.md)的 [Copy 活动](copy-activity-overview.md)
- [映射数据流](concepts-data-flow-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)
- [GetMetadata 活动](control-flow-get-metadata-activity.md)
- [Delete 活动](delete-activity.md)

对于 Copy 活动，可以借助此连接器执行以下操作：

- 通过使用帐户密钥、服务主体或 Azure 资源托管标识身份验证，从/向 Azure Data Lake Storage Gen2 复制数据。
- 按原样复制文件，或使用[支持的文件格式和压缩编解码器](supported-file-formats-and-compression-codecs.md)分析或生成文件。
- [在复制期间保留文件元数据](#preserve-metadata-during-copy)。
- 从 Azure Data Lake Storage Gen1/Gen2 复制时[保留 ACL](#preserve-acls)。

>[!IMPORTANT]
>如果你在 Azure 存储防火墙设置上启用“允许受信任的 Microsoft 服务访问此存储帐户”选项，并要使用 Azure Integration Runtime 连接到 Data Lake Storage Gen2，则必须对 ADLS Gen2 使用[托管标识身份验证](#managed-identity)。


## <a name="get-started"></a>入门

>[!TIP]
>有关如何使用 Data Lake Storage Gen2 连接器的演练，请参阅[将数据加载到 Azure Data Lake Storage Gen2](load-azure-data-lake-storage-gen2.md)。

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下面各部分介绍了用于定义特定于 Data Lake Storage Gen2 的数据工厂实体的属性。

## <a name="linked-service-properties"></a>链接服务属性

Azure Data Lake Storage Gen2 连接器支持以下身份验证类型。 有关详细信息，请参阅相应的部分：

- [帐户密钥身份验证](#account-key-authentication)
- [服务主体身份验证](#service-principal-authentication)
- [Azure 资源的托管标识身份验证](#managed-identity)

>[!NOTE]
>当你使用 PolyBase 将数据加载到 SQL 数据仓库时，如果源 Data Lake Storage Gen2 配置有虚拟网络终结点，你必须按 PolyBase 要求使用托管标识身份验证。 若要了解更多配置先决条件，请参阅[托管标识身份验证](#managed-identity)部分。

### <a name="account-key-authentication"></a>帐户密钥身份验证

若要使用存储帐户密钥身份验证，需支持以下属性：

| properties | 说明 | 必选 |
|:--- |:--- |:--- |
| type | type 属性必须设置为 AzureBlobFS。 |是 |
| url | Data Lake Storage Gen2 的终结点，采用 `https://<accountname>.dfs.core.windows.net` 模式。 | 是 |
| accountKey | Data Lake Storage Gen2 帐户密钥。 将此字段标记为 SecureString 以安全地将其存储在数据工厂中或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 |是 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如果数据存储位于专用网络，可以使用 Azure Integration Runtime 或自承载集成运行时。 如果没有指定此属性，则使用默认的 Azure Integration Runtime。 |否 |

>[!NOTE]
>使用帐户密钥身份验证时，不支持辅助 ADLS 文件系统终结点。 可以使用其他身份验证类型。

**示例：**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
            "accountkey": { 
                "type": "SecureString", 
                "value": "<accountkey>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="service-principal-authentication"></a>服务主体身份验证

若要使用服务主体身份验证，请按照以下步骤操作。

1. 按照[向 Azure AD 租户注册应用](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant)中的步骤操作，在 Azure Active Directory (Azure AD) 中注册一个应用实体。 记下下面的值，这些值用于定义链接服务：

    - 应用程序 ID
    - 应用程序密钥
    - 租户 ID

2. 向服务主体授予适当权限。 有关 Data Lake Storage Gen2 中的权限工作原理的示例，请参阅[文件和目录上的访问控制列表](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)

    - **作为源**：在存储资源管理器中，请为所有上游文件夹和文件系统授予至少“执行”权限，并为要复制的文件授予“读取”权限。 或者，在访问控制 (IAM) 中，授予至少“存储 Blob 数据读取者”角色。
    - **作为接收器**：在存储资源管理器中，请为所有上游文件夹和文件系统授予至少“执行”权限，并为接收器文件夹授予“写入”权限。 或者，在访问控制 (IAM) 中，授予至少“存储 Blob 数据参与者”角色。

>[!NOTE]
>如果你使用数据工厂 UI 进行创作，并且没有在 IAM 中为服务主体设置“存储 Blob 数据读取者/参与者”角色，请在执行测试连接或浏览/导航文件夹时，选择“测试与文件路径的连接”或“从指定路径浏览”，然后指定具有“读取 + 执行”权限的路径，以继续操作。

链接服务支持以下属性：

| properties | 说明 | 必选 |
|:--- |:--- |:--- |
| type | type 属性必须设置为 AzureBlobFS。 |是 |
| url | Data Lake Storage Gen2 的终结点，采用 `https://<accountname>.dfs.core.windows.net` 模式。 | 是 |
| servicePrincipalId | 指定应用程序的客户端 ID。 | 是 |
| servicePrincipalKey | 指定应用程序的密钥。 将此字段标记为 `SecureString`，以将它安全地存储在数据工厂中。 或者，也可以[引用 Azure Key Vault 中存储的机密](store-credentials-in-key-vault.md)。 | 是 |
| tenant | 指定应用程序的租户信息（域名或租户 ID）。 通过将鼠标悬停在 Azure 门户的右上角来检索它。 | 是 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如果数据存储位于专用网络，可以使用 Azure Integration Runtime 或自承载集成运行时。 如果没有指定，则使用默认的 Azure Integration Runtime。 |否 |

**示例：**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
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

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a> Azure 资源的托管标识身份验证

可将数据工厂与代表此特定数据工厂的 [Azure 资源托管标识](data-factory-service-identity.md)相关联。 可以直接使用此托管标识进行 Data Lake Storage Gen2 身份验证，就像使用你自己的服务主体一样。 这样，此指定的工厂就可以访问以及向/从 Data Lake Storage Gen2 复制数据。

若要使用 Azure 资源托管标识身份验证，请按照以下步骤操作。

1. 通过复制与工厂一起生成的“托管标识对象 ID”值，[检索数据工厂托管标识信息](data-factory-service-identity.md#retrieve-managed-identity)。

2. 向托管标识授予适当权限。 有关 Data Lake Storage Gen2 中的权限工作原理的示例，请参阅[文件和目录上的访问控制列表](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)。

    - **作为源**：在存储资源管理器中，请为所有上游文件夹和文件系统授予至少“执行”权限，并为要复制的文件授予“读取”权限。 或者，在访问控制 (IAM) 中，授予至少“存储 Blob 数据读取者”角色。
    - **作为接收器**：在存储资源管理器中，请为所有上游文件夹和文件系统授予至少“执行”权限，并为接收器文件夹授予“写入”权限。 或者，在访问控制 (IAM) 中，授予至少“存储 Blob 数据参与者”角色。

>[!NOTE]
>如果你使用数据工厂 UI 进行创作，并且没有在 IAM 中为托管标识设置“存储 Blob 数据读取者/参与者”角色，请在执行测试连接或浏览/导航文件夹时，选择“测试与文件路径的连接”或“从指定路径浏览”，然后指定具有“读取 + 执行”权限的路径，以继续操作。

>[!IMPORTANT]
>如果在对 Data Lake Storage Gen2 使用托管标识身份验证时使用 PolyBase 将数据从 Data Lake Storage Gen2 加载到 SQL 数据仓库，请务必也遵循[本指南](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage)中的第 1 步和第 2 步：1) 向 Azure Active Directory (Azure AD) 注册 SQL Database 服务器，2) 向 SQL Database 服务器分配“存储 Blob 数据参与者”角色；其余由数据工厂处理。 如果 Data Lake Storage Gen2 配置有 Azure 虚拟网络终结点，必须按 PolyBase 的要求使用托管标识身份验证，才能使用 PolyBase 加载其中的数据。

链接服务支持以下属性：

| properties | 说明 | 必选 |
|:--- |:--- |:--- |
| type | type 属性必须设置为 AzureBlobFS。 |是 |
| url | Data Lake Storage Gen2 的终结点，采用 `https://<accountname>.dfs.core.windows.net` 模式。 | 是 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如果数据存储位于专用网络，可以使用 Azure Integration Runtime 或自承载集成运行时。 如果没有指定，则使用默认的 Azure Integration Runtime。 |否 |

**示例：**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各个部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)。

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

在基于格式的数据集中的 `location` 设置下，Data Lake Storage Gen2 支持以下属性：

| properties   | 说明                                                  | 必选 |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | 数据集中的 `location` 下的 type 属性必须设置为 AzureBlobFSLocation。 | 是      |
| fileSystem | Data Lake Storage Gen2 文件系统名称。                              | 否       |
| folderPath | 给定文件系统下的文件夹路径。 若要使用通配符来筛选文件夹，请跳过此设置，并在活动源设置中指定它。 | 否       |
| fileName   | 给定 fileSystem + folderPath 下的文件名。 若要使用通配符来筛选文件，请跳过此设置，并在活动源设置中指定它。 | 否       |

**示例：**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Data Lake Storage Gen2 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobFSLocation",
                "fileSystem": "filesystemname",
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

有关可用于定义活动的各个部分和属性的完整列表，请参阅 [Copy 活动配置](copy-activity-overview.md#configuration)和[管道和活动](concepts-pipelines-activities.md)。 本部分列出了 Data Lake Storage Gen2 源和接收器支持的属性。

### <a name="azure-data-lake-storage-gen2-as-a-source-type"></a>Azure Data Lake Storage Gen2 作为源类型

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

从 ADLS Gen2 复制数据的方式有多种：

- 从数据集中指定的给定路径复制。
- 有关针对文件夹路径或文件名的通配符筛选器，请参阅 `wildcardFolderPath` 和 `wildcardFileName`。
- 将给定文本文件中定义的文件复制为文件集（请参阅 `fileListPath`）。

在基于格式的复制源中的 `storeSettings` 设置下，Data Lake Storage Gen2 支持以下属性：

| properties                 | 说明                                                  | 必选                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | `storeSettings` 下的 type 属性必须设置为 AzureBlobFSReadSettings。 | 是                                           |
| 找到要复制的文件： |  |  |
| 选项 1：静态路径<br> | 从数据集中指定的给定文件系统或文件夹/文件路径复制。 若要复制文件系统/文件夹中的所有文件，请另外将 `wildcardFileName` 指定为 `*`。 |  |
| 选项 2：通配符<br>- wildcardFolderPath | 数据集中配置的给定文件系统下带有通配符的文件夹路径，用于筛选源文件夹。 <br>允许的通配符为：`*`（匹配零个或更多个字符）和 `?`（匹配零个或单个字符）；如果实际文件夹名称中包含通配符或此转义字符，请使用 `^` 进行转义。 <br>请参阅[文件夹和文件筛选器示例](#folder-and-file-filter-examples)中的更多示例。 | 否                                            |
| 选项 2：通配符<br>- wildcardFileName | 给定文件系统 + folderPath/wildcardFolderPath 下带有通配符的文件名，用于筛选源文件。 <br>允许的通配符为：`*`（匹配零个或更多个字符）和 `?`（匹配零个或单个字符）；如果实际文件夹名称中包含通配符或此转义字符，请使用 `^` 进行转义。  请参阅[文件夹和文件筛选器示例](#folder-and-file-filter-examples)中的更多示例。 | 是 |
| 选项 3：文件列表<br>- fileListPath | 指明复制给定文件集。 指向包含要复制的文件列表的文本文件，其中每行一个文件，即在数据集中配置的路径的相对路径。<br/>使用此选项时，请不要在数据集中指定文件名。 请参阅[文件列表示例](#file-list-examples)中的更多示例。 |否 |
| 其他设置： |  | |
| recursive | 指示是要从子文件夹中以递归方式读取数据，还是只从指定的文件夹中读取数据。 请注意，当 recursive 设置为 true 且接收器是基于文件的存储时，将不会在接收器上复制或创建空的文件夹或子文件夹。 <br>允许的值为 **true**（默认值）和 **false**。<br>如果你配置 `fileListPath`，则此属性不适用。 |否 |
| modifiedDatetimeStart    | 基于属性“上次修改时间”的文件筛选器。 <br>如果文件的上次修改时间在 `modifiedDatetimeStart` 和 `modifiedDatetimeEnd` 之间的时间范围内，则将选中这些文件。 该时间应用于 UTC 时区，格式为“2018-12-01T05:00:00Z”。 <br> 属性可以为 NULL，这意味着不向数据集应用任何文件特性筛选器。  如果 `modifiedDatetimeStart` 具有日期/时间值，但 `modifiedDatetimeEnd` 为 NULL，则意味着将选中“上次修改时间”属性大于或等于该日期/时间值的文件。  如果 `modifiedDatetimeEnd` 具有日期/时间值，但 `modifiedDatetimeStart` 为 NULL，则意味着将选中“上次修改时间”属性小于该日期/时间值的文件。<br/>如果你配置 `fileListPath`，则此属性不适用。 | 否                                            |
| modifiedDatetimeEnd      | 同上。                                               | 否                                            |
| maxConcurrentConnections | 同时连接到存储的连接数。 仅当要限制连接到数据存储的并发连接时，才指定此属性。 | 否                                            |

**示例：**

```json
"activities":[
    {
        "name": "CopyFromADLSGen2",
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
                    "type": "AzureBlobFSReadSettings",
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

### <a name="azure-data-lake-storage-gen2-as-a-sink-type"></a>Azure Data Lake Storage Gen2 作为接收器类型

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

在基于格式的复制接收器中的 `storeSettings` 设置下，Data Lake Storage Gen2 支持以下属性：

| properties                 | 说明                                                  | 必选 |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | `storeSettings` 下的 type 属性必须设置为 AzureBlobFSWriteSettings。 | 是      |
| copyBehavior             | 定义以基于文件的数据存储中的文件为源时的复制行为。<br/><br/>允许值包括：<br/><b>- PreserveHierarchy（默认）</b>：将文件层次结构保留到目标文件夹中。 指向源文件夹的源文件相对路径与指向目标文件夹的目标文件相对路径相同。<br/><b>- FlattenHierarchy</b>：源文件夹中的所有文件都位于目标文件夹的第一级中。 目标文件具有自动生成的名称。 <br/><b>- MergeFiles</b>：将源文件夹中的所有文件合并到一个文件中。 如果指定了文件名，则合并文件的名称为指定名称。 否则，它是自动生成的文件名。 | 否       |
| blockSizeInMB | 指定用于将数据写入到 ADLS Gen2 的块大小（以 MB 为单位）。 详细了解[块 Blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs)。 <br/>允许的值介于 4MB 和 100MB 之间。 <br/>默认情况下，ADF 自动根据源存储类型和数据确定块大小。 对于复制到 ADLS Gen2 的非二进制副本，默认块大小为 100MB，这样最多能容纳 4.95TB 数据。 当数据不大时，这可能不是最佳选择，尤其是在网络很差的情况下使用自承载集成运行时会导致操作超时或性能问题。 可以显式指定块大小，同时确保 blockSizeInMB*50000 足够大来存储数据，否则复制活动运行会失败。 | 否 |
| maxConcurrentConnections | 同时连接到数据存储的连接数。 仅当要限制连接到数据存储的并发连接时，才指定此属性。 | 否       |

**示例：**

```json
"activities":[
    {
        "name": "CopyToADLSGen2",
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
                    "type": "AzureBlobFSWriteSettings",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>文件夹和文件筛选器示例

本部分介绍使用通配符筛选器生成文件夹路径和文件名的行为。

| folderPath | fileName | recursive | 源文件夹结构和筛选器结果（用**粗体**表示的文件已检索）|
|:--- |:--- |:--- |:--- |
| `Folder*` | （是空的，使用默认值） | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | （是空的，使用默认值） | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>文件列表示例

此部分介绍了在复制活动源中使用文件列表路径的结果行为。

假设有以下源文件夹结构，并且要复制加粗显示的文件：

| 示例源结构                                      | FileListToCopy.txt 中的内容                             | ADF 配置                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| filesystem<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metadata<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | 在数据集中：<br>- 文件系统：`filesystem`<br>- 文件夹路径：`FolderA`<br><br>在复制活动源中：<br>- 文件列表路径：`filesystem/Metadata/FileListToCopy.txt` <br><br>文件列表路径指向同一数据存储中的文本文件，其中包含要复制的文件列表，每行一个文件，以及在数据集中配置的路径的相对路径。 |


### <a name="some-recursive-and-copybehavior-examples"></a>一些 recursive 和 copyBehavior 示例

此部分介绍了针对 recursive 和 copyBehavior 值的不同组合执行复制操作的结果行为。

| recursive | copyBehavior | 源文件夹结构 | 生成目标 |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用与源相同的结构创建目标 Folder1：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用以下结构创建目标 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 的自动生成的名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2 的自动生成的名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File3 的自动生成的名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File4 的自动生成的名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File5 的自动生成的名称 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用以下结构创建目标 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File5 的内容将合并到一个文件中，且自动生成文件名。 |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用以下结构创建目标 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>没有选取包含 File3、File4 和 File5 的 Subfolder1。 |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用以下结构创建目标 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 的自动生成的名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2 的自动生成的名称<br/><br/>没有选取包含 File3、File4 和 File5 的 Subfolder1。 |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用以下结构创建目标 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 的内容将合并到一个文件中，且自动生成文件名。 File1 的自动生成的名称<br/><br/>没有选取包含 File3、File4 和 File5 的 Subfolder1。 |

## <a name="preserve-metadata-during-copy"></a>在复制期间保留元数据

在将文件从 Amazon S3/Azure Blob/Azure Data Lake Storage Gen2 复制到 Azure Data Lake Storage Gen2/Azure Blob 时，可以选择保留文件元数据以及数据。 若要了解详细信息，请参阅[保留元数据](copy-activity-preserve-metadata.md#preserve-metadata)。

## <a name="preserve-acls-from-data-lake-storage-gen1gen2"></a><a name="preserve-acls"></a> 从 Data Lake Storage Gen1/Gen2 保留 ACL

在将文件从 Azure Data Lake Storage Gen1/Gen2 复制到 Gen2 时，可以选择保留 POSIX 访问控制列表 (ACL) 以及数据。 若要了解详细信息，请参阅[将 ACL 从 Data Lake Storage Gen1/Gen2 保留到 Gen2](copy-activity-preserve-metadata.md#preserve-acls)。

>[!TIP]
>有关将数据从 Azure Data Lake Storage Gen1 复制到 Gen2 中的一般演练和最佳做法，请参阅[使用 Azure 数据工厂将数据从 Azure Data Lake Storage Gen1 复制到 Gen2](load-azure-data-lake-storage-gen2-from-gen1.md)。

## <a name="mapping-data-flow-properties"></a>映射数据流属性

在映射数据流中转换数据时，可以从 Azure Data Lake Storage Gen2 读取和写入格式为 JSON、Avro、带分隔符的文本或 Parquet 的文件。 有关详细信息，请参阅映射数据流功能中的[源转换](data-flow-source.md)和[接收器转换](data-flow-sink.md)。

### <a name="source-transformation"></a>源转换

在源转换中，可以从 Azure Data Lake Storage Gen2 中的容器、文件夹或单个文件进行读取。 通过“源选项”选项卡，可以管理如何读取文件。 

![源选项](media/data-flow/sourceOptions1.png "源选项")

通配符路径：使用通配符模式会指示 ADF 在一个源转换中遍历每个匹配的文件夹和文件。 这是在一个流中处理多个文件的有效方法。 使用将鼠标悬停在现有通配符模式上时出现的 + 号添加多个通配符匹配模式。

从源容器中，选择与模式匹配的一系列文件。 在数据集中，只能指定容器。 因此，通配符路径还必须包含根文件夹中的文件夹路径。

通配符示例：

* ```*```：表示任意字符集
* ```**```：表示递归目录嵌套
* ```?```：替换一个字符
* ```[]```：与括号内多个字符中的一个匹配

* ```/data/sales/**/*.csv```：获取 /data/sales 下的所有 csv 文件
* ```/data/sales/20??/**/```：获取 20 世纪的所有文件
* ```/data/sales/*/*/*.csv```：获取 /data/sales 下两个级别的 csv 文件
* ```/data/sales/2004/*/12/[XY]1?.csv```：获取时间为 2004 年 12 月、以 X 或 Y 开头且以两位数为前缀的所有 csv 文件

分区根路径：如果文件源中有 ```key=value``` 格式（例如 year=2019）的已分区文件夹，可以将此分区文件夹树的顶级分配给数据流中的列名称。

首先，设置一个通配符，以包括所有是已分区文件夹和要读取的叶文件的路径。

![分区源文件设置](media/data-flow/partfile2.png "分区文件设置")

使用“分区根路径”设置来定义文件夹结构的顶级。 通过数据预览来查看数据内容时，你会发现 ADF 添加在每个文件夹级别中找到的已解析分区。

![分区根路径](media/data-flow/partfile1.png "分区根路径预览")

文件列表：这是一个文件集。 创建一个文本文件，其中包含要处理的相对路径文件的列表。 指向此文本文件。

用于存储文件名的列：将源文件的名称存储在数据的列中。 请在此处输入新的列名称，以存储文件名字符串。

完成后：选择在数据流运行完成后对源文件不做任何操作、删除源文件或移动源文件。 移动路径是相对路径。

若要将源文件移动到另一个位置进行后处理，请先选择“移动”作为文件操作。 然后，设置“移动源”目录。 如果你没有对路径使用任何通配符，那么“移动源”设置就是与源文件夹相同的文件夹。

如果源路径包含通配符，语法如下所示：

```/data/sales/20??/**/*.csv```

可以将“移动源”指定为

```/data/sales```

并将“移动目标”指定为

```/backup/priorSales```

在此例中，源 /data/sales 下的所有文件都移动到 /backup/priorSales。

> [!NOTE]
> 仅当你从使用管道中的“执行数据流”活动的管道运行（管道调试或执行运行）启动数据流时，文件操作才会运行。 文件操作不会在数据流调试模式下运行。

按上次修改时间筛选：通过指定上次修改时间的日期范围，可以筛选要处理哪些文件。 所有日期/时间都采用 UTC 时间。 

### <a name="sink-properties"></a>接收器属性

在接收器转换中，可以写入到 Azure Data Lake Storage Gen2 中的容器或文件夹。 通过“设置”选项卡，可以管理如何写入文件。

![接收器选项​​](media/data-flow/file-sink-settings.png "接收器选项​​")

清除文件夹：确定是否在写入数据前清除目标文件夹。

文件名选项：确定目标文件在目标文件夹中的命名方式。 文件名选项为：
   * **默认**：允许 Spark 根据 PART 默认值来命名文件。
   * 模式：输入一种枚举每个分区的输出文件的模式。 例如，loans[n].csv 创建 loans1.csv、loans2.csv 等。
   * 每分区：每分区输入一个文件名。
   * 作为列中的数据：将输出文件设置为列的值。 路径相对于数据集容器，而不是目标文件夹。 如果数据集中有文件夹路径，则会被替代。
   * 输出到一个文件：将已分区输出文件合并为一个命名文件。 路径相对于数据集文件夹。 请注意，合并操作可能会因为节点大小而失败。 对于大型数据集，不建议使用此选项。

全部引用：确定是否将所有值括在引号中

## <a name="lookup-activity-properties"></a>Lookup 活动属性

若要详细了解属性，请查看 [Lookup 活动](control-flow-lookup-activity.md)。

## <a name="getmetadata-activity-properties"></a>GetMetadata 活动属性

若要详细了解属性，请查看 [GetMetadata 活动](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Delete 活动属性

若要详细了解属性，请查看 [Delete 活动](delete-activity.md)

## <a name="legacy-models"></a>旧模式

>[!NOTE]
>为了实现向后兼容性，仍然按原样支持以下模型。 建议使用上面几个部分中提到的新模型，并且 ADF 创作 UI 已经切换为生成新模型。

### <a name="legacy-dataset-model"></a>旧数据集模型

| properties | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为 AzureBlobFSFile。 |是 |
| folderPath | Data Lake Storage Gen2 中的文件夹路径。 如果未指定，它指向根目录。 <br/><br/>支持通配符筛选器。 允许的通配符为：`*`（匹配零个或多个字符）和 `?`（匹配零个或一个字符）。 如果实际文件夹名称内有通配符或 `^`，请使用此转义字符进行转义。 <br/><br/>示例：filesystem/folder/。 请参阅[文件夹和文件筛选器示例](#folder-and-file-filter-examples)中的更多示例。 |否 |
| fileName | 指定的“folderPath”下的文件的名称或通配符筛选器。 如果没有为此属性指定任何值，则数据集会指向文件夹中的所有文件。 <br/><br/>对于筛选器，允许的通配符为 `*`（匹配零个或多个字符）和 `?`（匹配零个或一个字符）。<br/>- 示例 1：`"fileName": "*.csv"`<br/>- 示例 2：`"fileName": "???20180427.txt"`<br/>如果实际文件名内有通配符或 `^`，请使用此转义字符进行转义。<br/><br/>如果没有为输出数据集指定 fileName，并且没有在活动接收器中指定 preserveHierarchy，则复制活动会自动生成采用以下模式的文件名称：“Data.[activity run ID GUID].[GUID if FlattenHierarchy].[format if configured].[compression if configured]”（例如，“Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz”）。 如果使用表名称（而不是查询）从表格源进行复制，则名称模式为“[table name].[format].[compression if configured]”（例如“MyTable.csv”）。 |否 |
| modifiedDatetimeStart | 基于“上次修改时间”特性的文件筛选器。 如果文件的上次修改时间介于时间范围 `modifiedDatetimeStart` 和 `modifiedDatetimeEnd` 之间，则选择这些文件。 此时间在应用时采用 UTC 时区，格式为“2018-12-01T05:00:00Z”。 <br/><br/> 若要从大量文件中筛选文件，启用此设置会影响数据移动的总体性能。 <br/><br/> 属性可以为 NULL；也就是说，不会向数据集应用任何文件特性筛选器。 如果 `modifiedDatetimeStart` 有日期/时间值，但 `modifiedDatetimeEnd` 为 NULL，这意味着选择“上次修改时间”特性大于或等于日期/时间值的文件。 如果 `modifiedDatetimeEnd` 有日期/时间值，但 `modifiedDatetimeStart` 为 NULL，这意味着选择“上次修改时间”特性小于日期/时间值的文件。| 否 |
| modifiedDatetimeEnd | 基于“上次修改时间”特性的文件筛选器。 如果文件的上次修改时间介于时间范围 `modifiedDatetimeStart` 和 `modifiedDatetimeEnd` 之间，则选择这些文件。 此时间在应用时采用 UTC 时区，格式为“2018-12-01T05:00:00Z”。 <br/><br/> 若要从大量文件中筛选文件，启用此设置会影响数据移动的总体性能。 <br/><br/> 属性可以为 NULL；也就是说，不会向数据集应用任何文件特性筛选器。 如果 `modifiedDatetimeStart` 有日期/时间值，但 `modifiedDatetimeEnd` 为 NULL，这意味着选择“上次修改时间”特性大于或等于日期/时间值的文件。 如果 `modifiedDatetimeEnd` 有日期/时间值，但 `modifiedDatetimeStart` 为 NULL，这意味着选择“上次修改时间”特性小于日期/时间值的文件。| 否 |
| format | 若要在基于文件的存储之间按原样复制文件（二进制副本），可以在输入和输出数据集定义中跳过格式节。<br/><br/>若要分析或生成具有特定格式的文件，以下是受支持的文件格式类型：TextFormat、JsonFormat、AvroFormat、OrcFormat 和 ParquetFormat    。 请将 **format** 中的 **type** 属性设置为上述值之一。 有关详细信息，请参阅[文本格式](supported-file-formats-and-compression-codecs-legacy.md#text-format)、[JSON 格式](supported-file-formats-and-compression-codecs-legacy.md#json-format)、[Avro 格式](supported-file-formats-and-compression-codecs-legacy.md#avro-format)、[Orc 格式](supported-file-formats-and-compression-codecs-legacy.md#orc-format)和 [Parquet 格式](supported-file-formats-and-compression-codecs-legacy.md#parquet-format)部分。 |否（仅适用于二进制复制方案） |
| compression | 指定数据的压缩类型和级别。 有关详细信息，请参阅[受支持的文件格式和压缩编解码器](supported-file-formats-and-compression-codecs-legacy.md#compression-support)。<br/>支持的类型为 **GZip**、**Deflate**、**BZip2** 和 **ZipDeflate**。<br/>支持的级别为“最佳”和“最快”。 |否 |

>[!TIP]
>如需复制文件夹下的所有文件，请仅指定 **folderPath**。<br>若要复制一个具有给定名称的文件，请指定带有文件夹部分的 folderPath，以及带有文件名的 fileName。<br>若要复制文件夹下的文件子集，请指定带有文件夹部分的 folderPath，以及带有通配符筛选器的 fileName。 

**示例：**

```json
{
    "name": "ADLSGen2Dataset",
    "properties": {
        "type": "AzureBlobFSFile",
        "linkedServiceName": {
            "referenceName": "<Azure Data Lake Storage Gen2 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "myfilesystem/myfolder",
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
| type | 复制活动源的 type 属性必须设置为 AzureBlobFSSource。 |是 |
| recursive | 指示是要从子文件夹中以递归方式读取数据，还是只从指定的文件夹中读取数据。 当 recursive 设置为 true 且接收器是基于文件的存储时，将不会在接收器上复制或创建空的文件夹或子文件夹。<br/>允许的值为 **true**（默认值）和 **false**。 | 否 |
| maxConcurrentConnections | 同时连接到数据存储的连接数。 仅当要限制连接到数据存储的并发连接时，才指定此属性。 | 否 |

**示例：**

```json
"activities":[
    {
        "name": "CopyFromADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ADLS Gen2 input dataset name>",
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
                "type": "AzureBlobFSSource",
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
| type | 复制活动接收器的 type 属性必须设置为 AzureBlobFSSink。 |是 |
| copyBehavior | 定义以基于文件的数据存储中的文件为源时的复制行为。<br/><br/>允许值包括：<br/><b>- PreserveHierarchy（默认）</b>：将文件层次结构保留到目标文件夹中。 指向源文件夹的源文件相对路径与指向目标文件夹的目标文件相对路径相同。<br/><b>- FlattenHierarchy</b>：源文件夹中的所有文件都位于目标文件夹的第一级中。 目标文件具有自动生成的名称。 <br/><b>- MergeFiles</b>：将源文件夹中的所有文件合并到一个文件中。 如果指定了文件名，则合并文件的名称为指定名称。 否则，它是自动生成的文件名。 | 否 |
| maxConcurrentConnections | 同时连接到数据存储的连接数。 仅当要限制连接到数据存储的并发连接时，才指定此属性。 | 否 |

**示例：**

```json
"activities":[
    {
        "name": "CopyToADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ADLS Gen2 output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureBlobFSSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

## <a name="next-steps"></a>后续步骤

有关数据工厂中复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。
