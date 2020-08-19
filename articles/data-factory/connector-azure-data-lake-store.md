---
title: 向/从 Azure Data Lake Storage Gen1 复制数据
description: 了解如何使用数据工厂将数据从支持的源数据存储复制到 Azure Data Lake Store，或从 Data Lake Store 复制到支持的接收器存储。
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/18/2020
ms.openlocfilehash: 542f9a95e4a124cb8b369dfc670fc85cd7e2a9d4
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2020
ms.locfileid: "88553217"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen1-using-azure-data-factory"></a>使用 Azure 数据工厂向/从 Azure Data Lake Storage Gen1 复制数据

> [!div class="op_single_selector" title1="选择要使用的 Azure 数据工厂的版本："]
>
> * [版本 1](v1/data-factory-azure-datalake-connector.md)
> * [当前版本](connector-azure-data-lake-store.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述了如何向/从 Azure Data Lake Storage Gen1 复制数据。 若要了解 Azure 数据工厂，请阅读[介绍性文章](introduction.md)。

## <a name="supported-capabilities"></a>支持的功能

以下活动支持此 Azure Data Lake Storage Gen1 连接器：

- 包含[支持的源/接收器矩阵](copy-activity-overview.md)的 [Copy 活动](copy-activity-overview.md) 
- [映射数据流](concepts-data-flow-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)
- [GetMetadata 活动](control-flow-get-metadata-activity.md)
- [Delete 活动](delete-activity.md)

更确切地说，可以借助此连接器执行以下操作：

- 使用以下身份验证方法之一复制文件：服务主体或 Azure 资源托管标识。
- 按原样复制文件，或使用[支持的文件格式和压缩编解码器](supported-file-formats-and-compression-codecs.md)分析或生成文件。
- 在复制到 Azure Data Lake Storage Gen2 时[保留 ACL](#preserve-acls-to-data-lake-storage-gen2)。

> [!IMPORTANT]
> 如果使用自承载集成运行时复制数据，请将企业防火墙配置为，允许通过端口 443 流向 `<ADLS account name>.azuredatalakestore.net` 和 `login.microsoftonline.com/<tenant>/oauth2/token` 的出站流量。 后者是 Azure 安全令牌服务，集成运行时需要与之通信以获取访问令牌。

## <a name="get-started"></a>入门

> [!TIP]
> 有关如何使用 Azure Data Lake Store 连接器的演练，请参阅[将数据加载到 Azure Data Lake Store](load-azure-data-lake-store.md)。

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下面各部分介绍了用于定义特定于 Azure Data Lake Store 的数据工厂实体的属性。

## <a name="linked-service-properties"></a>链接服务属性

Azure Data Lake Store 链接服务支持以下属性：

| properties | 说明 | 必选 |
|:--- |:--- |:--- |
| type | `type` 属性必须设置为 **AzureDataLakeStore**。 | 是 |
| dataLakeStoreUri | Azure Data Lake Store 帐户相关信息。 此信息采用以下格式之一：`https://[accountname].azuredatalakestore.net/webhdfs/v1` 或 `adl://[accountname].azuredatalakestore.net/`。 | 是 |
| subscriptionId | Data Lake Store 帐户所属的 Azure 订阅 ID。 | 接收器所需 |
| resourceGroupName | Data Lake Store 帐户所属的 Azure 资源组名称。 | 接收器所需 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如果数据存储位于专用网络，可以使用 Azure Integration Runtime 或自承载集成运行时。 如果没有指定此属性，则使用默认的 Azure Integration Runtime。 |否 |

### <a name="use-service-principal-authentication"></a>使用服务主体身份验证

若要使用服务主体身份验证，请按照以下步骤操作。

1. 在 Azure Active Directory 中，注册一个应用实体，并向它授予对 Data Lake Store 的访问权限。 有关详细步骤，请参阅[服务到服务身份验证](../data-lake-store/data-lake-store-authenticate-using-active-directory.md)。 记下下面的值，这些值用于定义链接服务：

    - 应用程序 ID
    - 应用程序密钥
    - 租户 ID

2. 向服务主体授予适当权限。 有关 Data Lake Storage Gen1 中的权限工作原理的示例，请参阅 [Azure Data Lake Storage Gen1 中的访问控制](../data-lake-store/data-lake-store-access-control.md#common-scenarios-related-to-permissions)。

    - **作为源**：在“数据资源管理器” > “访问权限”中，请为所有上游文件夹（含根文件夹）授予至少“执行”权限，并为要复制的文件授予“读取”权限。 对于递归，可以选择添加到“此文件夹和所有子文件夹”，并添加为“访问权限和默认权限项”。  对帐户级别访问控制 (IAM) 没有要求。
    - **作为接收器**：在“数据资源管理器” > “访问权限”中，请为所有上游文件夹（含根文件夹）授予至少“执行”权限，并为接收器文件夹授予“写入”权限。 对于递归，可以选择添加到“此文件夹和所有子文件夹”，并添加为“访问权限和默认权限项”。 

支持以下属性：

| properties | 说明 | 必选 |
|:--- |:--- |:--- |
| servicePrincipalId | 指定应用程序的客户端 ID。 | 是 |
| servicePrincipalKey | 指定应用程序的密钥。 将此字段标记为 `SecureString` 以安全地将其存储在数据工厂中或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 | 是 |
| tenant | 指定应用驻留所在的租户的信息（如域名或租户 ID）。 可将鼠标悬停在 Azure 门户右上角进行检索。 | 是 |
| azureCloudType | 对于 "服务主体身份验证"，请指定你的 Azure Active Directory 应用程序注册到的 Azure 云环境的类型。 <br/> 允许的值为 **AzurePublic**、 **AzureChina**、 **AzureUsGovernment**和 **AzureGermany**。 默认情况下，使用数据工厂的云环境。 | 否 |

**示例：**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="use-managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>使用托管标识进行 Azure 资源身份验证

可将数据工厂与代表此特定数据工厂的 [Azure 资源托管标识](data-factory-service-identity.md)相关联。 可以像使用自己的服务主体一样，直接使用此托管标识进行 Data Lake Store 身份验证。 此指定工厂可通过此方法访问以及向/从 Data Lake Store 复制数据。

若要使用 Azure 资源托管标识身份验证，请按照以下步骤操作。

1. 通过复制与工厂一起生成的“服务标识应用程序 ID”值，[检索数据工厂托管标识信息](data-factory-service-identity.md#retrieve-managed-identity)。

2. 向托管标识授予对 Data Lake Store 的访问权限。 有关 Data Lake Storage Gen1 中的权限工作原理的示例，请参阅 [Azure Data Lake Storage Gen1 中的访问控制](../data-lake-store/data-lake-store-access-control.md#common-scenarios-related-to-permissions)。

    - **作为源**：在“数据资源管理器” > “访问权限”中，请为所有上游文件夹（含根文件夹）授予至少“执行”权限，并为要复制的文件授予“读取”权限。 对于递归，可以选择添加到“此文件夹和所有子文件夹”，并添加为“访问权限和默认权限项”。  对帐户级别访问控制 (IAM) 没有要求。
    - **作为接收器**：在“数据资源管理器” > “访问权限”中，请为所有上游文件夹（含根文件夹）授予至少“执行”权限，并为接收器文件夹授予“写入”权限。 对于递归，可以选择添加到“此文件夹和所有子文件夹”，并添加为“访问权限和默认权限项”。 

在 Azure 数据工厂中，除了链接服务中的常规 Data Lake Store 信息以外，不需要指定任何属性。

**示例：**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
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

在基于格式的数据集中的 `location` 设置下，Azure Data Lake Store Gen1 支持以下属性：

| properties   | 描述                                                  | 必需 |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | 数据集中的 `location` 下的 type 属性必须设置为 AzureDataLakeStoreLocation。 | 是      |
| folderPath | 文件夹路径。 若要使用通配符来筛选文件夹，请跳过此设置，并在活动源设置中指定它。 | 否       |
| fileName   | 给定 folderPath 下的文件名。 若要使用通配符来筛选文件，请跳过此设置，并在活动源设置中指定它。 | 否       |

**示例：**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<ADLS Gen1 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureDataLakeStoreLocation",
                "folderPath": "root/folder/subfolder"
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

有关可用于定义活动的各个部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)。 本部分提供 Azure Data Lake Store 源和接收器支持的属性列表。

### <a name="azure-data-lake-store-as-source"></a>Azure Data Lake Store 作为源

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

在基于格式的复制源中的 `storeSettings` 设置下，Azure Data Lake Store Gen1 支持以下属性：

| properties                 | 描述                                                  | 必需                                     |
| ------------------------ | ------------------------------------------------------------ | -------------------------------------------- |
| type                     | `storeSettings` 下的 type 属性必须设置为 AzureDataLakeStoreReadSettings。 | 是                                          |
| 找到要复制的文件： |  |  |
| 选项 1：静态路径<br> | 从数据集中指定的给定文件夹/文件路径复制。 若要复制文件夹中的所有文件，请另外将 `wildcardFileName` 指定为 `*`。 |  |
| 选项2：名称范围<br>- listAfter | 检索其名称在此值之后 (独占) 的文件夹/文件。 它利用 ADLS Gen1 的服务端筛选器，该筛选器提供比通配符筛选器更好的性能。 <br/>数据工厂将此筛选器应用于数据集中定义的路径，并且仅支持一个实体级别。 请参阅 [名称范围筛选器示例](#name-range-filter-examples)中的更多示例。 | 否 |
| 选项2：名称范围<br/>- listBefore | 检索其名称在此值之前的文件夹/文件 (包含) 中按字母顺序排列。 它利用 ADLS Gen1 的服务端筛选器，该筛选器提供比通配符筛选器更好的性能。<br>数据工厂将此筛选器应用于数据集中定义的路径，并且仅支持一个实体级别。 请参阅 [名称范围筛选器示例](#name-range-filter-examples)中的更多示例。 | 否 |
| 选项 3：通配符<br>- wildcardFolderPath | 带有通配符的文件夹路径，用于筛选源文件夹。 <br>允许的通配符为：`*`（匹配零个或更多个字符）和 `?`（匹配零个或单个字符）；如果实际文件夹名称中包含通配符或此转义字符，请使用 `^` 进行转义。 <br>请参阅[文件夹和文件筛选器示例](#folder-and-file-filter-examples)中的更多示例。 | 否                                            |
| 选项 3：通配符<br>- wildcardFileName | 给定的 folderPath/wildcardFolderPath 下带有通配符的文件名，用于筛选源文件。 <br>允许的通配符为：`*`（匹配零个或更多个字符）和 `?`（匹配零个或单个字符）；如果实际文件夹名称中包含通配符或此转义字符，请使用 `^` 进行转义。  请参阅[文件夹和文件筛选器示例](#folder-and-file-filter-examples)中的更多示例。 | 是 |
| 选项 4：文件列表<br>- fileListPath | 指明复制给定文件集。 指向包含要复制的文件列表的文本文件，每行一个文件（即数据集中所配置路径的相对路径）。<br/>使用此选项时，请不要在数据集中指定文件名。 请参阅[文件列表示例](#file-list-examples)中的更多示例。 |否 |
| 其他设置： |  | |
| recursive | 指示是要从子文件夹中以递归方式读取数据，还是只从指定的文件夹中读取数据。 请注意，当 recursive 设置为 true 且接收器是基于文件的存储时，将不会在接收器上复制或创建空的文件夹或子文件夹。 <br>允许的值为 **true**（默认值）和 **false**。<br>如果配置 `fileListPath`，则此属性不适用。 |否 |
| deleteFilesAfterCompletion | 指示是否会在二进制文件成功移到目标存储后将其从源存储中删除。 文件删除按文件进行。因此，当复制活动失败时，你会看到一些文件已经复制到目标并从源中删除，而另一些文件仍保留在源存储中。 <br/>此属性仅在二进制复制方案中有效，其中数据源存储为 Blob、ADLS Gen1、ADLS Gen2、S3、Google 云存储、文件、Azure 文件、SFTP 或 FTP。 默认值：false。 |否 |
| modifiedDatetimeStart    | 基于属性“上次修改时间”的文件筛选器。 <br>如果文件的上次修改时间在 `modifiedDatetimeStart` 和 `modifiedDatetimeEnd` 之间的时间范围内，则将选中这些文件。 该时间应用于 UTC 时区，格式为“2018-12-01T05:00:00Z”。 <br> 属性可以为 NULL，这意味着不向数据集应用任何文件属性筛选器。  如果 `modifiedDatetimeStart` 具有日期/时间值，但 `modifiedDatetimeEnd` 为 NULL，则意味着将选中“上次修改时间”属性大于或等于该日期/时间值的文件。  如果 `modifiedDatetimeEnd` 具有日期/时间值，但 `modifiedDatetimeStart` 为 NULL，则意味着将选中“上次修改时间”属性小于该日期/时间值的文件。<br/>如果配置 `fileListPath`，则此属性不适用。 | 否                                            |
| modifiedDatetimeEnd      | 同上。                                               | 否                                           |
| maxConcurrentConnections | 可以同时连接到存储库的连接数。 仅在要限制与数据存储的并发连接时指定。 | 否                                           |

**示例：**

```json
"activities":[
    {
        "name": "CopyFromADLSGen1",
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
                    "type": "AzureDataLakeStoreReadSettings",
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

### <a name="azure-data-lake-store-as-sink"></a>Azure Data Lake Store 作为接收器

[!INCLUDE [data-factory-v2-file-sink-formats](../../includes/data-factory-v2-file-sink-formats.md)]

在基于格式的复制接收器中的 `storeSettings` 设置下，Azure Data Lake Store Gen1 支持以下属性：

| properties                 | 描述                                                  | 必需 |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | `storeSettings` 下的 type 属性必须设置为 AzureDataLakeStoreWriteSettings。 | 是      |
| copyBehavior             | 定义以基于文件的数据存储中的文件为源时的复制行为。<br/><br/>允许值包括：<br/><b>- PreserveHierarchy（默认）</b>：将文件层次结构保留到目标文件夹中。 指向源文件夹的源文件相对路径与指向目标文件夹的目标文件相对路径相同。<br/><b>- FlattenHierarchy</b>：源文件夹中的所有文件都位于目标文件夹的第一级中。 目标文件具有自动生成的名称。 <br/><b>- MergeFiles</b>：将源文件夹中的所有文件合并到一个文件中。 如果指定了文件名，则合并文件的名称为指定名称。 否则，它是自动生成的文件名。 | 否       |
| expiryDateTime | 指定已写入文件的到期时间。 此时间在应用时采用 UTC 时间，格式为“2020-03-01T08:00:00Z”。 默认值为 NULL；也就是说，已写入文件永不到期。 | 否 |
| maxConcurrentConnections | 可以同时连接到数据存储的连接数。 仅在要限制与数据存储的并发连接时指定。 | 否       |

**示例：**

```json
"activities":[
    {
        "name": "CopyToADLSGen1",
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
                    "type": "AzureDataLakeStoreWriteSettings",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```
### <a name="name-range-filter-examples"></a>名称范围筛选器示例

本部分介绍了名称范围筛选器的结果行为。

| 示例源结构 | ADF 配置 | 结果 |
|:--- |:--- |:--- |
|root<br/>&nbsp;&nbsp;&nbsp;&nbsp;的<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;ax150<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file2.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;ax.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;b<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;bx.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;ansi-c<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file4.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;cx.csv| 在数据集中：<br>- 文件夹路径：`root`<br><br>在复制活动源中：<br>-列表后： `a`<br>-前面列出： `b`| 然后，将复制以下文件：<br><br>root<br/>&nbsp;&nbsp;&nbsp;&nbsp;ax150<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file2.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;ax.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;b<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file3.csv |

### <a name="folder-and-file-filter-examples"></a>文件夹和文件筛选器示例

本部分介绍使用通配符筛选器生成文件夹路径和文件名的行为。

| folderPath | fileName | recursive | 源文件夹结构和筛选器结果（用**粗体**表示的文件已检索）|
|:--- |:--- |:--- |:--- |
| `Folder*` | （为空，使用默认值） | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | （为空，使用默认值） | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>文件列表示例

本部分介绍了在复制活动源中使用文件列表路径时的结果行为。

假设有以下源文件夹结构，并且要复制加粗显示的文件：

| 示例源结构                                      | FileListToCopy.txt 中的内容                             | ADF 配置                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| root<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;元数据<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | 在数据集中：<br>- 文件夹路径：`root/FolderA`<br><br>在复制活动源中：<br>- 文件列表路径：`root/Metadata/FileListToCopy.txt` <br><br>文件列表路径指向同一数据存储中的文本文件，其中包含要复制的文件列表，每行一个文件，以及在数据集中配置的路径的相对路径。 |

### <a name="examples-of-behavior-of-the-copy-operation"></a>复制操作的行为示例

本部分介绍将 `recursive` 和 `copyBehavior` 值进行不同组合所产生的复制操作行为。

| recursive | copyBehavior | 源文件夹结构 | 生成目标 |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用与源相同的结构创建目标 Folder1：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5。 |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用以下结构创建目标 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 的自动生成的名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2 的自动生成的名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File3 的自动生成的名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File4 的自动生成的名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File5 的自动生成的名称 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用以下结构创建目标 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File5 内容合并到一个自动生成文件名的文件中。 |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用以下结构创建目标 Folder1：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>没有选取包含 File3、File4 和 File5 的 Subfolder1。 |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用以下结构创建目标 Folder1：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 的自动生成的名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2 的自动生成的名称<br/><br/>没有选取包含 File3、File4 和 File5 的 Subfolder1。 |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用以下结构创建目标 Folder1：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 的内容将合并到一个文件中，且自动生成文件名。 File1 的自动生成的名称<br/><br/>没有选取包含 File3、File4 和 File5 的 Subfolder1。 |

## <a name="preserve-acls-to-data-lake-storage-gen2"></a>将 ACL 保留到 Data Lake Storage Gen2

>[!TIP]
>有关将数据从 Azure Data Lake Storage Gen1 复制到 Gen2 中的一般演练和最佳做法，请参阅[使用 Azure 数据工厂将数据从 Azure Data Lake Storage Gen1 复制到 Gen2](load-azure-data-lake-storage-gen2-from-gen1.md)。

若要在从 Data Lake Storage Gen1 升级到 Data Lake Storage Gen2 时复制访问控制列表 (ACL) 和数据文件，请参阅[从 Data Lake Storage Gen1 保留 ACL](copy-activity-preserve-metadata.md#preserve-acls)。

## <a name="mapping-data-flow-properties"></a>映射数据流属性

在映射数据流时转换数据时，可以从 Azure Data Lake Storage Gen1 读取和写入以下格式的文件：
* [Avro](format-avro.md#mapping-data-flow-properties)
* [带分隔符的文本](format-delimited-text.md#mapping-data-flow-properties)
* [Excel](format-excel.md#mapping-data-flow-properties)
* [JSON](format-json.md#mapping-data-flow-properties)
* [Parquet](format-parquet.md#mapping-data-flow-properties)

格式特定的设置位于该格式的文档中。 有关详细信息，请参阅映射数据流中 [的映射数据流](data-flow-source.md) 和 [接收器转换](data-flow-sink.md)中的源转换。

### <a name="source-transformation"></a>源转换

在源转换中，可以在 Azure Data Lake Storage Gen1 的容器、文件夹或单独的文件中进行读取。 通过“源选项”选项卡，可以管理文件的读取方式。 

![源选项](media/data-flow/sourceOptions1.png "源选项")

**通配符路径：** 如果使用通配符模式，则是指示 ADF 在单个“源转换”中遍历每个匹配的文件夹和文件。 这是在单个流中处理多个文件的有效方法。 使用将鼠标悬停在现有通配符模式上时出现的“+”号来添加多个通配符匹配模式。

从源容器中，选择与模式匹配的一系列文件。 数据集中只能指定容器。 因此，通配符路径必须也包含根文件夹中的文件夹路径。

通配符示例：

* ```*``` 表示任意字符集
* ```**``` 表示递归目录嵌套
* ```?``` 替换一个字符
* ```[]``` 与括号中众多字符中的一个匹配

* ```/data/sales/**/*.csv``` 获取 /data/sales 下的所有 csv 文件
* ```/data/sales/20??/**/``` 获取 20 世纪的所有文件
* ```/data/sales/*/*/*.csv``` 获取比 /data/sales 低两个级别的 csv 文件
* ```/data/sales/2004/*/12/[XY]1?.csv``` 获取 2004 年 12 月的所有以 X 或 Y 开头且前缀为两位数的 csv 文件

**分区根路径：** 如果文件源中存在 ```key=value``` 格式（例如 year=2019）的分区文件夹，则可以向该分区文件夹树的顶层分配数据流中的列名称。

首先，设置一个通配符，用于包括分区文件夹以及要读取的叶文件的所有路径。

![分区源文件设置](media/data-flow/partfile2.png "分区文件设置")

使用“分区根路径”设置来定义文件夹结构的顶级。 通过数据预览查看数据内容时会看到，ADF 会添加在每个文件夹级别中找到的已解析的分区。

![分区根路径](media/data-flow/partfile1.png "分区根路径预览")

**文件列表：** 这是一个文件集。 创建一个文本文件，其中包含要处理的相对路径文件的列表。 指向此文本文件。

**用于存储文件名的列：** 将源文件的名称存储在数据的列中。 请在此处输入新列名称以存储文件名字符串。

**完成后：** 数据流运行后，可以选择不对源文件执行任何操作、删除源文件或移动源文件。 移动路径是相对路径。

要将源文件移到其他位置进行后期处理，请首先选择“移动”以执行文件操作。 然后，设置“从”目录。 如果未对路径使用任何通配符，则“从”设置中的文件夹将是与源文件夹相同的文件夹。

如果源路径包含通配符，则语法将如下所示：

```/data/sales/20??/**/*.csv```

可将“从”指定为

```/data/sales```

将“到”指定为

```/backup/priorSales```

在此例中，源自 /data/sales 下的所有文件都将移动到 /backup/priorSales。

> [!NOTE]
> 仅当从在管道中使用“执行数据流”活动的管道运行进程（管道调试或执行运行）中启动数据流时，文件操作才会运行。 文件操作不会在数据流调试模式下运行。

**按上次修改时间筛选：** 通过指定上次修改的日期范围，可以筛选要处理的文件。 所有日期时间均采用 UTC 格式。 

### <a name="sink-properties"></a>接收器属性

在接收器转换中，可以写入到 Azure Data Lake Storage Gen1 中的容器或文件夹。 通过“设置”选项卡，可以管理如何写入文件。

![接收器选项​​](media/data-flow/file-sink-settings.png "接收器选项​​")

**清除文件夹：** 确定在写入数据之前是否清除目标文件夹。

**文件名选项：** 确定目标文件在目标文件夹中的命名方式。 文件名选项有：
   * **默认**：允许 Spark 根据 PART 默认值为文件命名。
   * **模式**：输入一种模式，该模式枚举每个分区的输出文件。 例如，loans[n].csv 将创建 loans1.csv、loans2.csv 等等。
   * **每个分区**：为每个分区输入一个文件名。
   * **作为列中的数据**：将输出文件设置为列的值。 此路径是数据集容器而非目标文件夹的相对路径。 如果数据集中有文件夹路径，则会将其重写。
   * **输出到一个文件**：将分区输出文件合并为一个命名的文件。 此路径是数据集文件夹的相对路径。 请注意，合并操作可能会因为节点大小而失败。 对于大型数据集，不建议使用此选项。

**全部引用：** 确定是否将所有值括在引号中

## <a name="lookup-activity-properties"></a>Lookup 活动属性

若要了解有关属性的详细信息，请查看 [Lookup 活动](control-flow-lookup-activity.md)。

## <a name="getmetadata-activity-properties"></a>GetMetadata 活动属性

若要了解有关属性的详细信息，请查看 [GetMetadata 活动](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Delete 活动属性

若要详细了解这些属性，请查看 [Delete 活动](delete-activity.md)

## <a name="legacy-models"></a>旧模型

>[!NOTE]
>仍按原样支持以下模型，以实现向后兼容性。 建议你以后使用前面部分中提到的新模型，ADF 创作 UI 已经切换到生成新模型。

### <a name="legacy-dataset-model"></a>旧数据集模型

| properties | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为 AzureDataLakeStoreFile。 |是 |
| folderPath | Data Lake Store 中的文件夹的路径。 如果未指定，它指向根目录。 <br/><br/>支持通配符筛选器。 允许的通配符为：`*`（匹配零个或更多字符）和 `?`（匹配零个或单个字符）。 如果实际文件夹名称内有通配符或 `^`，请使用此转义字符进行转义。 <br/><br/>例如，rootfolder/subfolder/。 请参阅[文件夹和文件筛选器示例](#folder-and-file-filter-examples)中的更多示例。 |否 |
| fileName | 指定“folderPath”下的文件的“名称或通配符筛选器”。 如果没有为此属性指定任何值，则数据集会指向文件夹中的所有文件。 <br/><br/>对于筛选器，允许的通配符为：`*`（匹配零个或更多字符）和 `?`（匹配零个或单个字符）。<br/>- 示例 1：`"fileName": "*.csv"`<br/>- 示例 2：`"fileName": "???20180427.txt"`<br/>如果实际文件名内有通配符或 `^`，请使用此转义字符进行转义。<br/><br/>如果没有为输出数据集指定 fileName，并且没有在活动接收器中指定 preserveHierarchy，则复制活动会自动生成采用以下模式的文件名称：“Data.[activity run ID GUID].[GUID if FlattenHierarchy].[format if configured].[compression if configured]”（例如，“Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz”）。 如果使用表名称（而不是查询）从表格源进行复制，则名称模式为“[table name].[format].[compression if configured]”（例如“MyTable.csv”）。 |否 |
| modifiedDatetimeStart | 基于属性“上次修改时间”的文件筛选器。 如果文件的上次修改时间在 `modifiedDatetimeStart` 和 `modifiedDatetimeEnd` 之间的时间范围内，则将选中这些文件。 该时间应用于 UTC 时区，格式为“2018-12-01T05:00:00Z”。 <br/><br/> 当你要从大量文件中进行文件筛选时，启用此设置将影响数据移动的整体性能。 <br/><br/> 属性可以为 NULL，这意味着不向数据集应用任何文件特性筛选器。 如果 `modifiedDatetimeStart` 具有日期/时间值，但 `modifiedDatetimeEnd` 为 NULL，则意味着将选中“上次修改时间”属性大于或等于该日期/时间值的文件。 如果 `modifiedDatetimeEnd` 具有日期/时间值，但 `modifiedDatetimeStart` 为 NULL，则意味着将选中“上次修改时间”属性小于该日期/时间值的文件。| 否 |
| modifiedDatetimeEnd | 基于属性“上次修改时间”的文件筛选器。 如果文件的上次修改时间在 `modifiedDatetimeStart` 和 `modifiedDatetimeEnd` 之间的时间范围内，则将选中这些文件。 该时间应用于 UTC 时区，格式为“2018-12-01T05:00:00Z”。 <br/><br/> 当你要从大量文件中进行文件筛选时，启用此设置将影响数据移动的整体性能。 <br/><br/> 属性可以为 NULL，这意味着不向数据集应用任何文件特性筛选器。 如果 `modifiedDatetimeStart` 具有日期/时间值，但 `modifiedDatetimeEnd` 为 NULL，则意味着将选中“上次修改时间”属性大于或等于该日期/时间值的文件。 如果 `modifiedDatetimeEnd` 具有日期/时间值，但 `modifiedDatetimeStart` 为 NULL，则意味着将选中“上次修改时间”属性小于该日期/时间值的文件。| 否 |
| format | 若要在基于文件的存储之间按原样复制文件（二进制副本），可以在输入和输出数据集定义中跳过格式节。<br/><br/>若要分析或生成具有特定格式的文件，以下是受支持的文件格式类型：TextFormat、JsonFormat、AvroFormat、OrcFormat 和 ParquetFormat    。 请将 **format** 中的 **type** 属性设置为上述值之一。 有关详细信息，请参阅[文本格式](supported-file-formats-and-compression-codecs-legacy.md#text-format)、[JSON 格式](supported-file-formats-and-compression-codecs-legacy.md#json-format)、[Avro 格式](supported-file-formats-and-compression-codecs-legacy.md#avro-format)、[Orc 格式](supported-file-formats-and-compression-codecs-legacy.md#orc-format)和 [Parquet 格式](supported-file-formats-and-compression-codecs-legacy.md#parquet-format)部分。 |否（仅适用于二进制复制方案） |
| compression | 指定数据的压缩类型和级别。 有关详细信息，请参阅[受支持的文件格式和压缩编解码器](supported-file-formats-and-compression-codecs-legacy.md#compression-support)。<br/>支持的类型为 **GZip**、**Deflate**、**BZip2** 和 **ZipDeflate**。<br/>支持的级别为“最佳”和“最快”。 |否 |

>[!TIP]
>如需复制文件夹下的所有文件，请仅指定 **folderPath**。<br>若要复制一个具有特定名称的文件，请指定带有文件夹部分的 folderPath，以及带有文件名的 fileName。<br>若要复制文件夹下的文件子集，请指定带有文件夹部分的 folderPath，以及带有通配符筛选器的 fileName。 

**示例：**

```json
{
    "name": "ADLSDataset",
    "properties": {
        "type": "AzureDataLakeStoreFile",
        "linkedServiceName":{
            "referenceName": "<ADLS linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "datalake/myfolder/",
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

| 属性 | 描述 | 必需 |
|:--- |:--- |:--- |
| type | 复制活动源的 `type` 属性必须设置为 AzureDataLakeStoreSource。 |是 |
| recursive | 指示是要从子文件夹中以递归方式读取数据，还是只从指定的文件夹中读取数据。 当 `recursive` 设置为 true 且接收器是基于文件的存储时，将不会在接收器上复制或创建空的文件夹或子文件夹。 允许的值为 **true**（默认值）和 **false**。 | 否 |
| maxConcurrentConnections | 可以同时连接到数据存储的连接数。 仅在要限制与数据存储的并发连接时指定。 | 否 |

**示例：**

```json
"activities":[
    {
        "name": "CopyFromADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ADLS Gen1 input dataset name>",
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
                "type": "AzureDataLakeStoreSource",
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

| 属性 | 描述 | 必需 |
|:--- |:--- |:--- |
| type | 复制活动接收器的 `type` 属性必须设置为 AzureDataLakeStoreSink。 |是 |
| copyBehavior | 定义以基于文件的数据存储中的文件为源时的复制行为。<br/><br/>允许值包括：<br/><b>- PreserveHierarchy（默认）</b>：将文件层次结构保留到目标文件夹中。 指向源文件夹的源文件相对路径与指向目标文件夹的目标文件相对路径相同。<br/><b>- FlattenHierarchy</b>：源文件夹中的所有文件都位于目标文件夹的第一级中。 目标文件具有自动生成的名称。 <br/><b>- MergeFiles</b>：将源文件夹中的所有文件合并到一个文件中。 如果指定了文件名，则合并文件的名称为指定名称。 否则，会自动生成文件名。 | 否 |
| maxConcurrentConnections | 可以同时连接到数据存储的连接数。 仅在要限制与数据存储的并发连接时指定。 | 否 |

**示例：**

```json
"activities":[
    {
        "name": "CopyToADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ADLS Gen1 output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDataLakeStoreSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

## <a name="next-steps"></a>后续步骤

有关 Azure 数据工厂中复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。
