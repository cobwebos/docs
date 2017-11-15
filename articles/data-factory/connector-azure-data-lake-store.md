---
title: "使用数据工厂向/从 Azure Data Lake Store 复制数据 | Microsoft Docs"
description: "了解如何使用数据工厂将数据从支持的源数据存储复制到 Azure Data Lake Store，或从 Data Lake Store 复制到支持的接收器存储。"
services: data-factory
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 11/01/2017
ms.author: jingwang
ms.openlocfilehash: 6ef76763859482d24c088f58fe361882cc4a619b
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2017
---
# <a name="copy-data-to-or-from-azure-data-lake-store-by-using-azure-data-factory"></a>使用 Azure 数据工厂向/从 Azure Data Lake Store 复制数据
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [版本 1 - GA](v1/data-factory-azure-datalake-connector.md)
> * [版本 2 - 预览版](connector-azure-data-lake-store.md)

本文概述了如何使用 Azure 数据工厂中的复制活动向/从 Azure Data Lake Store 复制数据。 本文基于概述复制活动总体的[复制活动概述](copy-activity-overview.md)一文。

> [!NOTE]
> 本文适用于目前处于预览状态的数据工厂版本 2。 如果使用数据工厂服务第 1 版（已正式推出 (GA)），请参阅 [V1 中的 Azure Data Lake Store 连接器](v1/data-factory-azure-datalake-connector.md)。

## <a name="supported-capabilities"></a>支持的功能

可将数据从任一支持的源数据存储复制到 Azure Data Lake Store，或从 Azure Data Lake Store 复制到任一支持的接收器数据存储。 有关复制活动支持作为源或接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

具体而言，此 Azure Data Lake Store 连接器支持：

- 使用**服务主体**身份验证复制文件。
- 按原样复制文件，或者使用[支持的文件格式和压缩编解码器](supported-file-formats-and-compression-codecs.md)分析/生成文件。

## <a name="get-started"></a>入门
可以使用 .NET SDK、Python SDK、Azure PowerShell、REST API 或 Azure 资源管理器模板创建包含复制活动的管道。 有关创建包含复制活动的管道的分步说明，请参阅[复制活动教程](quickstart-create-data-factory-dot-net.md)。 

对于特定于 Azure Data Lake Store 的数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

可以通过使用服务主体身份验证，创建 Azure Data Lake Store 链接的服务。

要使用服务主体身份验证，请在 Azure Active Directory (Azure AD) 中注册一个应用程序实体并授予其访问 Data Lake Store 的权限。 有关详细步骤，请参阅[服务到服务身份验证](../data-lake-store/data-lake-store-authenticate-using-active-directory.md)。 记下下面的值，这些值用于定义链接服务：

- 应用程序 ID
- 应用程序密钥
- 租户 ID

>[!TIP]
> 请确保在 Azure Data Lake Store 中授予服务主体适当的权限：
>- 作为源，至少授予“读取 + 执行”数据访问权限才能列出和复制文件夹内容，或者授予“读取”权限来复制单个文件。 对帐户级别访问控制没有要求。
>- 作为接收器，则至少授予“写入 + 执行”数据访问权限，才能在文件夹中创建子项目。 如果使用 Azure IR 来增强复制功能（源和接收器都在云中），为了让数据工厂检测到 Data Lake Store区域，请至少授予帐户访问控制 (IAM) 中的“读者”角色。 如果需要避免此 IAM 角色，通过 Data Lake Store 的位置[创建 Azure IR](create-azure-integration-runtime.md#create-azure-ir)，并在 Data Lake Store 链接服务中进行关联，如下面的示例所示。

支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | type 属性必须设置为 **AzureDataLakeStore**。 | 是 |
| dataLakeStoreUri | Azure Data Lake Store 帐户相关信息。 此信息采用以下格式之一：`https://[accountname].azuredatalakestore.net/webhdfs/v1` 或 `adl://[accountname].azuredatalakestore.net/`。 | 是 |
| servicePrincipalId | 指定应用程序的客户端 ID。 | 是 |
| servicePrincipalKey | 指定应用程序的密钥。 将此字段标记为 SecureString。 | 是 |
| tenant | 指定应用程序的租户信息（域名或租户 ID）。 可将鼠标悬停在 Azure 门户右上角进行检索。 | 是 |
| subscriptionId | Data Lake Store 帐户所属的 Azure 订阅 ID。 | 接收器所需 |
| resourceGroupName | Data Lake Store 帐户所属的 Azure 资源组名称。 | 接收器所需 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如果数据存储位于专用网络，则可以使用 Azure 集成运行时或自承载集成运行时。 如果未指定，则使用默认 Azure 集成运行时。 |否 |

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

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各个部分和属性的完整列表，请参阅数据集一文。 本部分提供 Azure Data Lake Store 数据集支持的属性列表。

要向/从 Azure Data Lake Store 复制数据，请将数据集的 type 属性设置为“AzureDataLakeStoreFile”。 支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为：**AzureDataLakeStoreFile** |是 |
| folderPath | 到文件存储中的容器和文件夹的路径。 例如：rootfolder/subfolder/ |是 |
| fileName | 如果需要向/从特定文件复制，则在 folderPath 中指定文件名。 如果没有为此属性指定任何值，则数据集会指向文件夹中的所有文件。<br/><br/>如果没有为输出数据集指定 fileName，并且没有在活动接收器中指定“preserveHierarchy”，则复制活动会自动生成采用以下格式的文件名：`Data.[activity run id GUID].[GUID if FlattenHierarchy].[format if configured].[compression if configured]`。 例如：`Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz`。 |否 |
| 格式 | 如果想要在基于文件的存储之间**按原样复制文件**（二进制副本），可以在输入和输出数据集定义中跳过格式节。<br/><br/>如果想要分析或生成具有特定格式的文件，则下面是支持的文件格式类型：**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat**、**ParquetFormat**。 请将格式中的 **type** 属性设置为上述值之一。 有关详细信息，请参阅[文本格式](supported-file-formats-and-compression-codecs.md#text-format)、[Json 格式](supported-file-formats-and-compression-codecs.md#json-format)、[Avro 格式](supported-file-formats-and-compression-codecs.md#avro-format)、[Orc 格式](supported-file-formats-and-compression-codecs.md#orc-format)和 [Parquet 格式](supported-file-formats-and-compression-codecs.md#parquet-format)部分。 |否（仅适用于二进制复制方案） |
| compression | 指定数据的压缩类型和级别。 有关详细信息，请参阅[受支持的文件格式和压缩编解码器](supported-file-formats-and-compression-codecs.md#compression-support)。<br/>支持的类型为：**GZip**、**Deflate**、**BZip2** 和 **ZipDeflate**。<br/>支持的级别为：**最佳**和**最快**。 |否 |

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

有关可用于定义活动的各个部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 Azure Data Lake 源和接收器支持的属性列表。

### <a name="azure-data-lake-store-as-source"></a>Azure Data Lake Store 作为源

要从 Azure Data Lake Store 复制数据，请将复制活动中的源类型设置为“AzureDataLakeStoreSource”。 复制活动**源**部分支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动源的 type 属性必须设置为：**AzureDataLakeStoreSource** |是 |
| recursive | 指示是要从子文件夹中以递归方式读取数据，还是只从指定的文件夹中读取数据。<br/>允许的值为：true（默认）、false | 否 |

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

要向 Azure Blob 复制数据，请将复制活动中的接收器类型设置为“AzureDataLakeStoreSink”。 接收器部分支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动接收器的 type 属性必须设置为：**AzureDataLakeStoreSink** |是 |
| copyBehavior | 定义以基于文件的数据存储中的文件为源时的复制行为。<br/><br/>允许值包括：<br/><b>- PreserveHierarchy（默认值）</b>：保留目标文件夹中的文件层次结构。 从源文件到源文件夹的相对路径与从目标文件到目标文件夹的相对路径相同。<br/><b>- FlattenHierarchy</b>：源文件夹中的所有文件都位于目标文件夹的第一级。 目标文件具有自动生成的名称。 <br/><b>- MergeFiles</b>：将源文件夹中的所有文件合并到一个文件中。 如果指定文件/Blob 名称，则合并的文件名称将为指定的名称；否则，会自动生成文件名。 | 否 |

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

### <a name="recursive-and-copybehavior-examples"></a>recursive 和 copyBehavior 示例

本节介绍了将 recursive 和 copyBehavior 值进行不同组合所产生的复制操作行为。

| recursive | copyBehavior | 源文件夹结构 | 生成目标 |
|:--- |:--- |:--- |:--- |
| 是 |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用与源相同的结构创建目标文件夹 Folder1：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5。 |
| 是 |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用以下结构创建目标 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 的自动生成名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2 的自动生成名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File3 的自动生成名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File4 的自动生成名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File5 的自动生成名称 |
| 是 |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用以下结构创建目标 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File 5 的内容将合并到一个文件中，且自动生成文件名 |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用以下结构创建目标文件夹 Folder1<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>不会选取包含 File3、File4 和 File5 的 Subfolder1。 |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用以下结构创建目标文件夹 Folder1<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 的自动生成名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2 的自动生成名称<br/><br/>不会选取包含 File3、File4 和 File5 的 Subfolder1。 |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用以下结构创建目标文件夹 Folder1<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 的内容将合并到一个文件中，且自动生成文件名。 File1 的自动生成名称<br/><br/>不会选取包含 File3、File4 和 File5 的 Subfolder1。 |

## <a name="next-steps"></a>后续步骤
有关 Azure 数据工厂中复制活动支持作为源和接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md##supported-data-stores-and-formats)。