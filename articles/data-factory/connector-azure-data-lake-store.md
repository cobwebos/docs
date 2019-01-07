---
title: 使用数据工厂向/从 Azure Data Lake Storage Gen1 复制数据 | Microsoft Docs
description: 了解如何使用数据工厂将数据从支持的源数据存储复制到 Azure Data Lake Store，或从 Data Lake Store 复制到支持的接收器存储。
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/05/2018
ms.author: jingwang
ms.openlocfilehash: 411223c2ef7b1400f2019ebc6704b3c4a5d41235
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/14/2018
ms.locfileid: "53387023"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen1-by-using-azure-data-factory"></a>使用 Azure 数据工厂向/从 Azure Data Lake Storage Gen1 复制数据
> [!div class="op_single_selector" title1="选择您正在使用的 Data Factory 服务的版本:"]
> * [版本 1](v1/data-factory-azure-datalake-connector.md)
> * [当前版本](connector-azure-data-lake-store.md)

本文概述了如何使用 Azure 数据工厂中的复制活动向/从 Azure Data Lake Storage Gen1（以前称为 Azure Data Lake Store）复制数据。 本文是在[复制活动概述](copy-activity-overview.md)的基础上编写的。

## <a name="supported-capabilities"></a>支持的功能

可将数据从任一支持的源数据存储复制到 Azure Data Lake Store，或从 Azure Data Lake Store 复制到任一支持的接收器数据存储。 请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

具体而言，此 Azure Data Lake Store 连接器支持：

- 使用以下身份验证方法之一复制文件：**服务主体**或 **Azure 资源的托管标识**。
- 按原样复制文件，或者使用[支持的文件格式和压缩编解码器](supported-file-formats-and-compression-codecs.md)分析/生成文件。

> [!IMPORTANT]
> 如果使用自承载集成运行时复制数据，请配置企业防火墙以允许通过端口 443 到 `<ADLS account name>.azuredatalakestore.net` 和 `login.microsoftonline.com/<tenant>/oauth2/token` 的出站流量。 后者是 Azure 安全令牌服务，集成运行时需要与之通信以获取访问令牌。

## <a name="get-started"></a>入门

> [!TIP]
> 有关使用 Azure Data Lake Store 连接器的演练，请参阅[将数据加载到 Azure Data Lake Store](load-azure-data-lake-store.md)。

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

对于特定于 Azure Data Lake Store 的数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

Azure Data Lake Store 链接服务支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | `type` 属性必须设置为 **AzureDataLakeStore**。 | 是 |
| dataLakeStoreUri | Azure Data Lake Store 帐户相关信息。 此信息采用以下格式之一：`https://[accountname].azuredatalakestore.net/webhdfs/v1` 或 `adl://[accountname].azuredatalakestore.net/`。 | 是 |
| subscriptionId | Data Lake Store 帐户所属的 Azure 订阅 ID。 | 接收器所需 |
| resourceGroupName | Data Lake Store 帐户所属的 Azure 资源组名称。 | 接收器所需 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如果数据存储位于专用网络中，则可以使用 Azure 集成运行时或自承载集成运行时。 如果未指定此属性，将使用默认的 Azure 集成运行时。 |否 |

### <a name="use-service-principal-authentication"></a>使用服务主体身份验证

要使用服务主体身份验证，请在 Azure Active Directory 中注册一个应用程序实体并授予其访问 Data Lake Store 的权限。 有关详细步骤，请参阅[服务到服务身份验证](../data-lake-store/data-lake-store-authenticate-using-active-directory.md)。 记下下面的值，这些值用于定义链接服务：

- 应用程序 ID
- 应用程序密钥
- 租户 ID

>[!IMPORTANT]
> 请确保在 Data Lake Store 中授予服务主体适当的权限：
>- **作为源**：在“数据资源管理器” > “访问权限”中，至少授予“读取 + 执行”权限以列出和复制文件夹和子文件夹中的文件。 或者，可以授予“读取”权限以复制单个文件。 对于递归，可以选择添加到“此文件夹和所有子文件夹”，并添加为“访问权限和默认权限项”。 对帐户级访问控制 (IAM) 没有要求。
>- **作为接收器**：在“数据资源管理器” > “访问权限”中，至少授予“写入 + 执行”权限以在文件夹中创建子项。 对于递归，可以选择添加到“此文件夹和所有子文件夹”，并添加为“访问权限和默认权限项”。 如果使用 Azure 集成运行时复制（源和接收器都在云中），请在 IAM 中至少授予“读取者”角色，让数据工厂检测 Data Lake Store 的区域。 若要避免此 IAM 角色，请使用 Data Lake Store 的位置显式[创建 Azure 集成运行时](create-azure-integration-runtime.md#create-azure-ir)。 在 Data Lake Store 链接服务中按以下示例所示关联这些对象。

>[!NOTE]
>若要列出从根目录开始的文件夹，必须设置要**在根级别使用“执行”权限**授予的服务主体权限。 对于以下情况需要这样做：
>- 使用**复制数据工具**创作复制管道。
>- 在创作期间使用**数据工厂 UI** 测试连接和浏览文件夹。

支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| servicePrincipalId | 指定应用程序的客户端 ID。 | 是 |
| servicePrincipalKey | 指定应用程序的密钥。 将此字段标记为 `SecureString` 以安全地将其存储在数据工厂中或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 | 是 |
| tenant | 指定应用程序的租户信息（域名或租户 ID）。 可将鼠标悬停在 Azure 门户右上角进行检索。 | 是 |

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

可将数据工厂与代表此特定数据工厂的 [Azure 资源托管标识](data-factory-service-identity.md)相关联。 可以像使用自己的服务主体一样，直接使用此服务标识进行 Data Lake Store 身份验证。 此指定工厂可通过此方法访问以及向/从 Data Lake Store 复制数据。

若要使用 Azure 资源的托管标识身份验证，请执行以下操作：

1. 通过复制与工厂一起生成的“服务标识应用程序 ID”值[检索数据工厂服务标识](data-factory-service-identity.md#retrieve-service-identity)。
2. 按照下面的说明以对服务主体采用的相同方式，向该服务标识授予对 Data Lake Store 的访问权限。

>[!IMPORTANT]
> 确保在 Data Lake Store 中授予数据工厂服务标识适当的权限：
>- **作为源**：在“数据资源管理器” > “访问权限”中，至少授予“读取 + 执行”权限以列出和复制文件夹和子文件夹中的文件。 或者，可以授予“读取”权限以复制单个文件。 对于递归，可以选择添加到“此文件夹和所有子文件夹”，并添加为“访问权限和默认权限项”。 对帐户级访问控制 (IAM) 没有要求。
>- **作为接收器**：在“数据资源管理器” > “访问权限”中，至少授予“写入 + 执行”权限以在文件夹中创建子项。 对于递归，可以选择添加到“此文件夹和所有子文件夹”，并添加为“访问权限和默认权限项”。 如果使用 Azure 集成运行时复制（源和接收器都在云中），请在 IAM 中至少授予“读取者”角色，让数据工厂检测 Data Lake Store 的区域。 若要避免此 IAM 角色，请使用 Data Lake Store 的位置显式[创建 Azure 集成运行时](create-azure-integration-runtime.md#create-azure-ir)。 在 Data Lake Store 链接服务中按以下示例所示关联这些对象。

>[!NOTE]
>若要列出从根目录开始的文件夹，必须设置要**在根级别使用“执行”权限**授予的服务主体权限。 对于以下情况需要这样做：
>- 使用**复制数据工具**创作复制管道。
>- 在创作期间使用**数据工厂 UI** 测试连接和浏览文件夹。

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

若要向/从 Azure Data Lake Store 复制数据，请将数据集的 `type` 属性设置为 **AzureDataLakeStoreFile**。 支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为：**AzureDataLakeStoreFile** |是 |
| folderPath | Data Lake Store 中的文件夹的路径。 不支持通配符筛选器。 如果未指定，它指向根目录。 例如：rootfolder/subfolder/ |否 |
| fileName | 指定“folderPath”下的文件的“名称或通配符筛选器”。 如果没有为此属性指定任何值，则数据集会指向文件夹中的所有文件。 <br/><br/>对于筛选器，允许的通配符为：`*`（匹配零个或更多字符）和 `?`（匹配零个或单个字符）。<br/>- 示例 1：`"fileName": "*.csv"`<br/>- 示例 2：`"fileName": "???20180427.txt"`<br/>如果实际文件名内具有通配符或此转义符，请使用 `^` 进行转义。<br/><br/>如果没有为输出数据集指定 fileName，并且没有在活动接收器中指定 **preserveHierarchy**，则复制活动会自动生成采用以下模式的文件名：“*Data.[activity run id GUID].[GUID if FlattenHierarchy].[format if configured].[compression if configured]*”。 例如“Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz”。 如果使用表名称而不是查询从表格源进行复制，则名称模式为“[table name].[format].[compression if configured]”。 例如“MyTable.csv”。 |否 |
| 格式 | 如果想要在基于文件的存储之间按原样复制文件（二进制副本），可以在输入和输出数据集定义中跳过格式节。<br/><br/>若要分析或生成具有特定格式的文件，以下是受支持的文件格式类型：**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat**、**ParquetFormat**。 请将格式中的“type”属性设置为上述值之一。 有关详细信息，请参阅[文本格式](supported-file-formats-and-compression-codecs.md#text-format)、[Json 格式](supported-file-formats-and-compression-codecs.md#json-format)、[Avro 格式](supported-file-formats-and-compression-codecs.md#avro-format)、[Orc 格式](supported-file-formats-and-compression-codecs.md#orc-format)和 [Parquet 格式](supported-file-formats-and-compression-codecs.md#parquet-format)部分。 |否（仅适用于二进制复制方案） |
| compression | 指定数据的压缩类型和级别。 有关详细信息，请参阅[受支持的文件格式和压缩编解码器](supported-file-formats-and-compression-codecs.md#compression-support)。<br/>支持的类型为：**GZip**、**Deflate**、**BZip2** 和 **ZipDeflate**。<br/>支持的级别为：**Optimal** 和 **Fastest**。 |否 |


>[!TIP]
>如需复制文件夹下的所有文件，请仅指定 **folderPath**。<br>如需复制具有特定名称的单个文件，请指定文件夹部分的 **folderPath** 和文件名部分的 **fileName**。<br>如需复制文件夹下的文件子集，请指定文件夹部分的 **folderPath** 和通配符筛选器部分的 **fileName**。 

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
            "fileName": "myfile.csv.gz",
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

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各个部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)。 本部分提供 Azure Data Lake Store 源和接收器支持的属性列表。

### <a name="azure-data-lake-store-as-source"></a>Azure Data Lake Store 作为源

若要从 Data Lake Store 复制数据，请将复制活动中的源类型设置为 **AzureDataLakeStoreSource**。 复制活动 **source** 节支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动源的 `type` 属性必须设置为：**AzureDataLakeStoreSource**。 |是 |
| recursive | 指示是要从子文件夹中以递归方式读取数据，还是只从指定的文件夹中读取数据。 请注意，当 `recursive` 设置为 true 且接收器是基于文件的存储时，将不会在接收器上复制或创建空的文件夹或子文件夹。 允许的值为 **true**（默认值）和 **false**。 | 否 |

**示例：**

```json
"activities":[
    {
        "name": "CopyFromADLS",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ADLS input dataset name>",
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

### <a name="azure-data-lake-store-as-sink"></a>Azure Data Lake Store 作为接收器

若要向 Data Lake Store 复制数据，请将复制活动中的接收器类型设置为 **AzureDataLakeStoreSink**。 接收器部分支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动接收器的 `type` 属性必须设置为：**AzureDataLakeStoreSink**。 |是 |
| copyBehavior | 定义以基于文件的数据存储中的文件为源时的复制行为。<br/><br/>允许值包括：<br/><b>- PreserveHierarchy（默认值）</b>：保留目标文件夹中的文件层次结构。 指向源文件夹的源文件相对路径与指向目标文件夹的目标文件相对路径相同。<br/><b>- FlattenHierarchy</b>：源文件夹中的所有文件都位于目标文件夹的第一级别。 目标文件具有自动生成的名称。 <br/><b>- MergeFiles</b>：将源文件夹中的所有文件合并到一个文件中。 如果指定了文件名/Blob 名称，则合并文件的名称为指定的名称。 否则，会自动生成文件名。 | 否 |

**示例：**

```json
"activities":[
    {
        "name": "CopyToADLS",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ADLS output dataset name>",
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

### <a name="examples-of-behavior-of-the-copy-operation"></a>复制操作的行为示例

本部分介绍将 `recursive` 和 `copyBehavior` 值进行不同组合所产生的复制操作行为。

| recursive | copyBehavior | 源文件夹结构 | 生成目标 |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用与源相同的结构创建目标 Folder1：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5。 |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用以下结构创建目标 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 的自动生成名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2 的自动生成名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File3 的自动生成名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File4 的自动生成名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File5 的自动生成名称 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用以下结构创建目标 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File 5 的内容将合并到一个文件中，且自动生成文件名。 |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用以下结构创建目标 Folder1：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>不会选取包含 File3、File4 和 File5 的 Subfolder1。 |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用以下结构创建目标 Folder1：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 的自动生成名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2 的自动生成名称<br/><br/>不会选取包含 File3、File4 和 File5 的 Subfolder1。 |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用以下结构创建目标 Folder1：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 的内容将合并到一个文件中，且自动生成文件名。 File1 的自动生成名称<br/><br/>不会选取包含 File3、File4 和 File5 的 Subfolder1。 |

## <a name="next-steps"></a>后续步骤
有关 Azure 数据工厂中复制活动支持用作源和接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md##supported-data-stores-and-formats)。
