---
title: 将数据复制到 Azure Data Lake Storage Gen1
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
ms.date: 12/12/2019
ms.openlocfilehash: 701695c849a7f94abdba83f962806ecab3f21282
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75440880"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen1-using-azure-data-factory"></a>使用 Azure 数据工厂将数据复制到 Azure Data Lake Storage Gen1 或从中复制数据

> [!div class="op_single_selector" title1="选择要使用的 Azure 数据工厂的版本："]
> * [版本 1](v1/data-factory-azure-datalake-connector.md)
> * [当前版本](connector-azure-data-lake-store.md)

本文概述了如何将数据复制到 Azure Data Lake Storage Gen1。 若要了解 Azure 数据工厂，请阅读[介绍性文章](introduction.md)。

## <a name="supported-capabilities"></a>支持的功能

以下活动支持此 Azure Data Lake Storage Gen1 连接器：

- 带有[支持的源或接收器矩阵](copy-activity-overview.md)的[复制活动](copy-activity-overview.md) 
- [映射数据流](concepts-data-flow-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)
- [GetMetadata 活动](control-flow-get-metadata-activity.md)
- [删除活动](delete-activity.md)

具体而言，利用此连接器，你可以：

- 使用以下身份验证方法之一复制文件： Azure 资源的服务主体或托管标识。
- 按原样复制文件，或者用[支持的文件格式和压缩编解码器](supported-file-formats-and-compression-codecs.md)分析或生成文件。
- 在复制到 Azure Data Lake Storage Gen2 时[保留 acl](#preserve-acls-to-data-lake-storage-gen2) 。

> [!IMPORTANT]
> 如果使用自承载 integration runtime 复制数据，请将企业防火墙配置为允许在端口443上的出站流量 `<ADLS account name>.azuredatalakestore.net` 和 `login.microsoftonline.com/<tenant>/oauth2/token`。 后者是 Azure 安全令牌服务，集成运行时需要与之通信以获取访问令牌。

## <a name="get-started"></a>开始体验

> [!TIP]
> 有关如何使用 Azure Data Lake Store 连接器的演练，请参阅将[数据加载到 Azure Data Lake Store](load-azure-data-lake-store.md)中。

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

以下各节提供了有关用于定义特定于 Azure Data Lake Store 的数据工厂实体的属性的信息。

## <a name="linked-service-properties"></a>链接服务属性

Azure Data Lake Store 链接服务支持以下属性：

| 属性 | Description | 需要 |
|:--- |:--- |:--- |
| type | `type` 属性必须设置为 **AzureDataLakeStore**。 | 是 |
| dataLakeStoreUri | Azure Data Lake Store 帐户相关信息。 此信息采用以下格式之一：`https://[accountname].azuredatalakestore.net/webhdfs/v1` 或 `adl://[accountname].azuredatalakestore.net/`。 | 是 |
| subscriptionId | Data Lake Store 帐户所属的 Azure 订阅 ID。 | 接收器所需 |
| resourceGroupName | Data Lake Store 帐户所属的 Azure 资源组名称。 | 接收器所需 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如果数据存储位于专用网络，则可以使用 Azure 集成运行时或自承载集成运行时。 如果未指定此属性，则使用默认的 Azure 集成运行时。 |否 |

### <a name="use-service-principal-authentication"></a>使用服务主体身份验证

若要使用服务主体身份验证，请执行以下步骤。

1. 在 Azure Active Directory 中注册一个应用程序实体，并向其授予对 Data Lake Store 的访问权限。 有关详细步骤，请参阅[服务到服务身份验证](../data-lake-store/data-lake-store-authenticate-using-active-directory.md)。 记下下面的值，这些值用于定义链接服务：

    - 应用程序 ID
    - 应用程序密钥
    - 租户 ID

2. 向服务主体授予适当的权限。 请参阅 Data Lake Storage Gen1 的权限在 Azure Data Lake Storage Gen1 中的[访问控制](../data-lake-store/data-lake-store-access-control.md#common-scenarios-related-to-permissions)中的工作原理示例。

    - **作为源**：在**数据资源管理器**中 > **访问权限**，至少授予对所有上游文件夹（包括根）的**Execute**权限以及要复制的文件的**读取**权限。 对于递归，可以选择添加到“此文件夹和所有子文件夹”，并添加为“访问权限和默认权限项”。 帐户级别访问控制（IAM）不需要。
    - **作为接收器**：在**数据资源管理器**中 > **访问权限**，请至少授予对所有上游文件夹（包括根）的**Execute**权限以及接收器文件夹的**写入**权限。 对于递归，可以选择添加到“此文件夹和所有子文件夹”，并添加为“访问权限和默认权限项”。 如果使用 Azure 集成运行时来复制（源和接收器都在云中），请在 IAM 中至少授予 "**读取**者" 角色，以允许数据工厂检测 Data Lake Store 区域。 若要避免此 IAM 角色，请使用 Data Lake Store 的位置显式[创建 Azure 集成运行时](create-azure-integration-runtime.md#create-azure-ir)。 例如，如果你的 Data Lake Store 在西欧中，则创建一个将 "西欧" 设置为 "" 的 Azure 集成运行时。 将它们关联到 Data Lake Store 链接服务中，如下面的示例中所示。

支持以下属性：

| 属性 | Description | 需要 |
|:--- |:--- |:--- |
| servicePrincipalId | 指定应用程序的客户端 ID。 | 是 |
| servicePrincipalKey | 指定应用程序的密钥。 将此字段标记为 `SecureString` 以安全地将其存储在数据工厂中或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 | 是 |
| tenant | 指定应用程序所驻留的租户信息，例如域名或租户 ID。 可将鼠标悬停在 Azure 门户右上角进行检索。 | 是 |

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

### <a name="managed-identity"></a>使用托管标识进行 Azure 资源身份验证

可将数据工厂与代表此特定数据工厂的 [Azure 资源托管标识](data-factory-service-identity.md)相关联。 可以像使用自己的服务主体一样，直接使用此托管标识进行 Data Lake Store 身份验证。 此指定工厂可通过此方法访问以及向/从 Data Lake Store 复制数据。

若要将托管标识用于 Azure 资源身份验证，请执行以下步骤。

1. 通过复制与工厂一起生成的“服务标识应用程序 ID”值，[检索数据工厂托管标识信息](data-factory-service-identity.md#retrieve-managed-identity)。

2. 向托管标识授予对 Data Lake Store 的访问权限。 请参阅 Data Lake Storage Gen1 的权限在 Azure Data Lake Storage Gen1 中的[访问控制](../data-lake-store/data-lake-store-access-control.md#common-scenarios-related-to-permissions)中的工作原理示例。

    - **作为源**：在**数据资源管理器**中 > **访问权限**，至少授予对所有上游文件夹（包括根）的**Execute**权限以及要复制的文件的**读取**权限。 对于递归，可以选择添加到“此文件夹和所有子文件夹”，并添加为“访问权限和默认权限项”。 帐户级别访问控制（IAM）不需要。
    - **作为接收器**：在**数据资源管理器**中 > **访问权限**，请至少授予对所有上游文件夹（包括根）的**Execute**权限以及接收器文件夹的**写入**权限。 对于递归，可以选择添加到“此文件夹和所有子文件夹”，并添加为“访问权限和默认权限项”。 如果使用 Azure 集成运行时来复制（源和接收器都在云中），请在 IAM 中至少授予 "**读取**者" 角色，以允许数据工厂检测 Data Lake Store 区域。 若要避免此 IAM 角色，请使用 Data Lake Store 的位置显式[创建 Azure 集成运行时](create-azure-integration-runtime.md#create-azure-ir)。 将它们关联到 Data Lake Store 链接服务中，如下面的示例中所示。

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

基于格式的数据集中 `location` 设置下的 Azure Data Lake Store Gen1 支持以下属性：

| 属性   | Description                                                  | 需要 |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | 数据集中 `location` 下的 type 属性必须设置为**AzureDataLakeStoreLocation**。 | 是      |
| folderPath | 文件夹的路径。 如果要使用通配符筛选文件夹，请跳过此设置并在 "活动源" 设置中指定它。 | 否       |
| fileName   | 给定 folderPath 下的文件名。 如果要使用通配符来筛选文件，请跳过此设置并在 "活动源" 设置中指定它。 | 否       |

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

基于格式的复制源中 `storeSettings` 设置下的 Azure Data Lake Store Gen1 支持以下属性：

| 属性                 | Description                                                  | 需要                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | `storeSettings` 下的 type 属性必须设置为**AzureDataLakeStoreReadSetting**。 | 是                                           |
| recursive                | 指示是要从子文件夹中以递归方式读取数据，还是只从指定的文件夹中读取数据。 当 recursive 设置为 true 且接收器是基于文件的存储时，不会在接收器上复制或创建空的文件夹或子文件夹。 允许的值为 **true**（默认值）和 **false**。 | 否                                            |
| wildcardFolderPath       | 用于筛选源文件夹的带通配符的文件夹路径。 <br>允许的通配符 `*` （与零个或多个字符匹配）和 `?` （与零个或一个字符匹配）。 如果你的实际文件夹名称中有通配符或此转义字符，请使用 `^` 进行转义。 <br>请参阅[文件夹和文件筛选器示例](#folder-and-file-filter-examples)中的更多示例。 | 否                                            |
| wildcardFileName         | 在给定的 folderPath/wildcardFolderPath 下包含通配符的文件名用于筛选源文件。 <br>允许的通配符 `*` （与零个或多个字符匹配）和 `?` （与零个或一个字符匹配）。 如果你的实际文件夹名称中有通配符或此转义字符，请使用 `^` 进行转义。 请参阅[文件夹和文件筛选器示例](#folder-and-file-filter-examples)中的更多示例。 | 如果未在数据集中指定 `fileName` 则为 Yes |
| modifiedDatetimeStart    | 基于上次修改的属性筛选文件。 如果文件的上次修改时间在 `modifiedDatetimeStart` 和 `modifiedDatetimeEnd`之间的时间范围内，则选择这些文件。 此时间应用于 UTC 时区，格式为 "2018-12-01T05：00： 00Z"。 <br> 属性可以为 NULL，这意味着不会对数据集应用任何文件属性筛选器。 如果 `modifiedDatetimeStart` 具有日期时间值，但 `modifiedDatetimeEnd` 为 NULL，则表示已选择最后修改的属性大于或等于 datetime 值的文件。 当 `modifiedDatetimeEnd` 具有日期时间值，但 `modifiedDatetimeStart` 为 NULL 时，表示已选择 "上次修改时间" 属性小于 "日期时间" 值的文件。 | 否                                            |
| modifiedDatetimeEnd      | 同上。                                               | 否                                            |
| maxConcurrentConnections | 要同时连接到存储存储的连接数。 仅在要限制与数据存储的并发连接时指定。 | 否                                            |

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
                    "type": "DelimitedTextReadSetting",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AzureDataLakeStoreReadSetting",
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

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

基于格式的复制接收器 `storeSettings` 设置下的 Azure Data Lake Store Gen1 支持以下属性：

| 属性                 | Description                                                  | 需要 |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | `storeSettings` 下的 type 属性必须设置为**AzureDataLakeStoreWriteSetting**。 | 是      |
| copyBehavior             | 定义以基于文件的数据存储中的文件为源时的复制行为。<br/><br/>允许值包括：<br/><b>- PreserveHierarchy（默认值）</b>：保留目标文件夹中的文件层次结构。 指向源文件夹的源文件相对路径与指向目标文件夹的目标文件相对路径相同。<br/><b>- FlattenHierarchy</b>：源文件夹中的所有文件都位于目标文件夹的第一级。 目标文件具有自动生成的名称。 <br/><b>- MergeFiles</b>：将源文件夹中的所有文件合并到一个文件中。 如果指定了文件名，则合并文件的名称为指定名称。 否则，它是自动生成的文件名。 | 否       |
| maxConcurrentConnections | 并发连接到数据存储的连接数。 仅在要限制与数据存储的并发连接时指定。 | 否       |

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
                    "type": "AzureDataLakeStoreWriteSetting",
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
| `Folder*` | （空，使用默认值） | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | （空，使用默认值） | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="examples-of-behavior-of-the-copy-operation"></a>复制操作的行为示例

本部分介绍将 `recursive` 和 `copyBehavior` 值进行不同组合所产生的复制操作行为。

| recursive | copyBehavior | 源文件夹结构 | 生成目标 |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用与源相同的结构创建目标 Folder1：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5。 |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用以下结构创建目标 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 的自动生成的名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2 的自动生成的名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File3 的自动生成的名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File4 的自动生成的名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File5 的自动生成的名称 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用以下结构创建目标 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File5 内容将合并到一个文件中，并自动生成文件名。 |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用以下结构创建目标 Folder1：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>未选取具有 File3、File4 和 File5 的不会选取。 |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用以下结构创建目标 Folder1：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 的自动生成的名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2 的自动生成的名称<br/><br/>未选取具有 File3、File4 和 File5 的不会选取。 |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用以下结构创建目标 Folder1：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 的内容将合并到一个文件中，并自动生成文件名。 File1 的自动生成的名称<br/><br/>未选取具有 File3、File4 和 File5 的不会选取。 |

## <a name="preserve-acls-to-data-lake-storage-gen2"></a>将 Acl 保留到 Data Lake Storage Gen2

>[!TIP]
>若要将数据从 Azure Data Lake Storage Gen1 复制到一般的 Gen2 中，请参阅[使用 Azure 数据工厂将 Azure Data Lake Storage Gen1 数据复制到 Gen2](load-azure-data-lake-storage-gen2-from-gen1.md)和最佳实践。

如果要在从 Data Lake Storage Gen1 升级到 Data Lake Storage Gen2 时，将访问控制列表（Acl）与数据文件一起复制，请参阅[保留 Data Lake Storage Gen1 中的 acl](copy-activity-preserve-metadata.md#preserve-acls)。

## <a name="mapping-data-flow-properties"></a>映射数据流属性

在映射数据流中转换数据时，可以通过 JSON、Avro、带分隔符的文本或 Parquet 格式 Azure Data Lake Storage Gen1 读取和写入文件。 有关详细信息，请参阅映射数据流功能中的[源转换](data-flow-source.md)和[接收器转换](data-flow-sink.md)。

### <a name="source-transformation"></a>源转换

在源转换中，可以在 Azure Data Lake Storage Gen1 的容器、文件夹或单独的文件中进行读取。 通过 "**源选项**" 选项卡，您可以管理文件的读取方式。 

![源选项](media/data-flow/sourceOptions1.png "源选项")

**通配符路径：** 使用通配符模式将指示 ADF 通过单个源转换循环遍历每个匹配的文件夹和文件。 这是在单个流中处理多个文件的有效方法。 添加多个通配符匹配模式，并在将鼠标悬停在现有通配符模式上时显示的 + 符号。

从源容器中，选择与模式匹配的一系列文件。 只能在数据集中指定容器。 因此，你的通配符路径必须包含根文件夹中的文件夹路径。

通配符示例：

* ```*``` 表示任意字符集
* ```**``` 表示递归目录嵌套
* ```?``` 替换一个字符
* ```[]``` 匹配括号中的一个或多个字符

* ```/data/sales/**/*.csv``` 获取/data/sales 下的所有 csv 文件。
* ```/data/sales/20??/**``` 获取20世纪的所有文件
* ```/data/sales/2004/*/12/[XY]1?.csv``` 获取2004年12月开始的所有 csv 文件，以两位数作为前缀的 X 或 Y

**分区根路径：** 如果文件源中的分区文件夹的格式 ```key=value``` （例如年 = 2019），则可以将该分区文件夹树的顶层分配给数据流数据流中的列名称。

首先，设置一个通配符，以包括所有作为分区文件夹的路径，以及要读取的叶文件。

![分区源文件设置](media/data-flow/partfile2.png "分区文件设置")

使用 "分区根路径" 设置来定义文件夹结构的顶层。 通过数据预览查看数据的内容时，会看到 ADF 会添加在每个文件夹级别中找到的已解析分区。

![分区根路径](media/data-flow/partfile1.png "分区根路径预览")

**文件列表：** 这是一个文件集。 创建一个文本文件，其中包含要处理的相对路径文件的列表。 指向此文本文件。

**要存储文件名的列：** 将源文件的名称存储在数据中的列中。 在此处输入新的列名来存储文件名字符串。

**完成后：** 选择在数据流运行后对源文件执行任何操作、删除源文件或移动源文件。 移动的路径是相对路径。

若要将源文件移到其他位置，请先选择 "移动" 进行文件操作。 然后，设置 "从" 目录。 如果没有为路径使用任何通配符，则 "源" 设置将是与源文件夹相同的文件夹。

如果源路径带有通配符，则语法如下所示：

```/data/sales/20??/**/*.csv```

你可以指定 "from" 作为

```/data/sales```

和 "to" as

```/backup/priorSales```

在这种情况下，/data/sales 下的所有文件都将移动到/backup/priorSales。

> [!NOTE]
> 仅当您从管道运行（管道调试或执行运行）中的数据流开始使用管道中的 "执行数据流" 活动时，才运行文件操作。 文件操作*不会*在数据流调试模式下运行。

**按上次修改时间筛选：** 您可以通过指定上次修改的日期范围来筛选处理的文件。 所有日期时间都采用 UTC 格式。 

### <a name="sink-properties"></a>接收器属性

在接收器转换中，可以将 Azure Data Lake Storage Gen1 中的容器或文件夹写入。 "**设置**" 选项卡允许您管理文件的写入方式。

![接收器选项](media/data-flow/file-sink-settings.png "接收器选项")

**清除文件夹：** 确定在写入数据之前是否清除目标文件夹。

**文件名选项：** 确定目标文件在目标文件夹中的命名方式。 文件名选项包括：
   * **默认**：允许 SPARK 基于部分默认值命名文件。
   * **模式**：输入枚举每个分区的输出文件的模式。 例如，**贷款 [n] .csv**将创建 loans1、loans2，依此类推。
   * **每个分区**：每个分区输入一个文件名。
   * **As 列中的数据**：将输出文件设置为列的值。 该路径相对于数据集容器，而不是目标文件夹。
   * **输出到单个文件**：将已分区的输出文件合并为单个命名文件。 该路径相对于数据集文件夹。 请注意，根据节点大小，te merge 操作可能会失败。 对于大型数据集，不建议使用此选项。

**全部引用：** 确定是否将所有值括在引号中

## <a name="lookup-activity-properties"></a>查找活动属性

若要了解有关属性的详细信息，请检查[查找活动](control-flow-lookup-activity.md)。

## <a name="getmetadata-activity-properties"></a>GetMetadata 活动属性

若要了解有关属性的详细信息，请查看[GetMetadata 活动](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>删除活动属性

若要了解有关属性的详细信息，请选中 "[删除活动](delete-activity.md)"

## <a name="legacy-models"></a>传统模型

>[!NOTE]
>对于向后兼容性，仍支持以下模型。 建议使用前面部分中提到的新模型，然后 ADF 创作 UI 已经切换为生成新模型。

### <a name="legacy-dataset-model"></a>旧数据集模型

| 属性 | Description | 需要 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为**AzureDataLakeStoreFile**。 |是 |
| folderPath | Data Lake Store 中的文件夹的路径。 如果未指定，它指向根目录。 <br/><br/>支持通配符筛选器。 允许的通配符 `*` （与零个或多个字符匹配）和 `?` （与零个或一个字符匹配）。 如果你的实际文件夹名称中有通配符或此转义字符，请使用 `^` 进行转义。 <br/><br/>例如： rootfolder/子文件夹/。 请参阅[文件夹和文件筛选器示例](#folder-and-file-filter-examples)中的更多示例。 |否 |
| fileName | 指定 "folderPath" 下的文件的名称或通配符筛选器。 如果没有为此属性指定任何值，则数据集会指向文件夹中的所有文件。 <br/><br/>对于筛选器，允许的通配符 `*` （与零个或多个字符匹配）和 `?` （与零个或一个字符匹配）。<br/>- 示例 1：`"fileName": "*.csv"`<br/>- 示例 2：`"fileName": "???20180427.txt"`<br/>如果你的实际文件名中包含通配符或此转义字符，请使用 `^` 进行转义。<br/><br/>如果没有为输出数据集指定 fileName，并且没有在活动接收器中指定**preserveHierarchy** ，则复制活动会自动生成具有以下模式的文件名： "*Data. [活动运行 ID GUID]。[GUID if FlattenHierarchy]。[格式（如果已配置）。[已配置压缩]* ，例如 "0a405f8a-93ff-4c6f-b3be-f69616f1df7a. gz"。 如果使用表名而不是查询从表格源复制，则名称模式为 " *[表名称]. [format]。[压缩（如果已配置）* "，例如" MyTable "。 |否 |
| modifiedDatetimeStart | 基于上次修改的属性筛选文件。 如果文件的上次修改时间在 `modifiedDatetimeStart` 和 `modifiedDatetimeEnd`之间的时间范围内，则选择这些文件。 此时间应用于 UTC 时区，格式为 "2018-12-01T05：00： 00Z"。 <br/><br/> 如果要对大量文件执行文件筛选，则启用此设置会影响数据移动的总体性能。 <br/><br/> 属性可以为 NULL，这意味着不会对数据集应用任何文件属性筛选器。 如果 `modifiedDatetimeStart` 具有日期时间值，但 `modifiedDatetimeEnd` 为 NULL，则表示已选择最后修改的属性大于或等于 datetime 值的文件。 当 `modifiedDatetimeEnd` 具有日期时间值，但 `modifiedDatetimeStart` 为 NULL 时，表示已选择 "上次修改时间" 属性小于 "日期时间" 值的文件。| 否 |
| modifiedDatetimeEnd | 基于上次修改的属性筛选文件。 如果文件的上次修改时间在 `modifiedDatetimeStart` 和 `modifiedDatetimeEnd`之间的时间范围内，则选择这些文件。 此时间应用于 UTC 时区，格式为 "2018-12-01T05：00： 00Z"。 <br/><br/> 如果要对大量文件执行文件筛选，则启用此设置会影响数据移动的总体性能。 <br/><br/> 属性可以为 NULL，这意味着不会对数据集应用任何文件属性筛选器。 如果 `modifiedDatetimeStart` 具有日期时间值，但 `modifiedDatetimeEnd` 为 NULL，则表示已选择最后修改的属性大于或等于 datetime 值的文件。 当 `modifiedDatetimeEnd` 具有日期时间值，但 `modifiedDatetimeStart` 为 NULL 时，表示已选择 "上次修改时间" 属性小于 "日期时间" 值的文件。| 否 |
| format | 如果要在基于文件的存储之间按原样复制文件（二进制副本），请在输入和输出数据集定义中跳过格式部分。<br/><br/>如果想要分析或生成具有特定格式的文件，则下面是支持的文件格式类型：**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat** 和 **ParquetFormat**。 请将 **format** 中的 **type** 属性设置为上述值之一。 有关详细信息，请参阅[文本格式](supported-file-formats-and-compression-codecs-legacy.md#text-format)、[JSON 格式](supported-file-formats-and-compression-codecs-legacy.md#json-format)、[Avro 格式](supported-file-formats-and-compression-codecs-legacy.md#avro-format)、[Orc 格式](supported-file-formats-and-compression-codecs-legacy.md#orc-format)和 [Parquet 格式](supported-file-formats-and-compression-codecs-legacy.md#parquet-format)部分。 |否（仅适用于二进制复制方案） |
| compression | 指定数据的压缩类型和级别。 有关详细信息，请参阅[受支持的文件格式和压缩编解码器](supported-file-formats-and-compression-codecs-legacy.md#compression-support)。<br/>支持的类型为 **GZip**、**Deflate**、**BZip2** 和 **ZipDeflate**。<br/>支持的级别为“最佳”和“最快”。 |否 |

>[!TIP]
>如需复制文件夹下的所有文件，请仅指定 **folderPath**。<br>若要复制具有特定名称的单个文件，请使用带有文件名的文件夹部分和**文件名**指定**folderPath** 。<br>若要复制文件夹下的部分文件，请使用带有通配符筛选器的文件夹部分和**文件名**指定**folderPath** 。 

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

| 属性 | Description | 需要 |
|:--- |:--- |:--- |
| type | 复制活动源的 `type` 属性必须设置为**AzureDataLakeStoreSource**。 |是 |
| recursive | 指示是要从子文件夹中以递归方式读取数据，还是只从指定的文件夹中读取数据。 如果 `recursive` 设置为 true 且接收器是基于文件的存储，则不会在接收器上复制或创建空文件夹或子文件夹。 允许的值为 **true**（默认值）和 **false**。 | 否 |
| maxConcurrentConnections | 并发连接到数据存储的连接数。 仅在要限制与数据存储的并发连接时指定。 | 否 |

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

| 属性 | Description | 需要 |
|:--- |:--- |:--- |
| type | 复制活动接收器的 `type` 属性必须设置为**AzureDataLakeStoreSink**。 |是 |
| copyBehavior | 定义以基于文件的数据存储中的文件为源时的复制行为。<br/><br/>允许值包括：<br/><b>- PreserveHierarchy（默认值）</b>：保留目标文件夹中的文件层次结构。 指向源文件夹的源文件相对路径与指向目标文件夹的目标文件相对路径相同。<br/><b>- FlattenHierarchy</b>：源文件夹中的所有文件都位于目标文件夹的第一级。 目标文件具有自动生成的名称。 <br/><b>- MergeFiles</b>：将源文件夹中的所有文件合并到一个文件中。 如果指定了文件名，则合并文件的名称为指定名称。 否则，会自动生成文件名。 | 否 |
| maxConcurrentConnections | 并发连接到数据存储的连接数。 仅在要限制与数据存储的并发连接时指定。 | 否 |

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

有关 Azure 数据工厂中复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md##supported-data-stores-and-formats)。
