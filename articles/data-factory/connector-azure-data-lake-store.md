---
title: 使用数据工厂向/从 Azure Data Lake Store 复制数据 | Microsoft Docs
description: 了解如何使用数据工厂将数据从支持的源数据存储复制到 Azure Data Lake Store，或从 Data Lake Store 复制到支持的接收器存储。
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: f346accd318689bbaa10080111d07e3fedd84556
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="copy-data-to-or-from-azure-data-lake-store-by-using-azure-data-factory"></a>使用 Azure 数据工厂向/从 Azure Data Lake Store 复制数据
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [版本 1 - 正式版](v1/data-factory-azure-datalake-connector.md)
> * [版本 2 - 预览版](connector-azure-data-lake-store.md)

本文概述了如何使用 Azure 数据工厂中的复制活动向/从 Azure Data Lake Store 复制数据。 它是基于概述复制活动总体的[复制活动概述](copy-activity-overview.md)一文。

> [!NOTE]
> 本文适用于目前处于预览状态的数据工厂版本 2。 如果使用数据工厂服务第 1 版（已正式推出 (GA)），请参阅 [V1 中的 Azure Data Lake Store 连接器](v1/data-factory-azure-datalake-connector.md)。

## <a name="supported-capabilities"></a>支持的功能

可将数据从任一支持的源数据存储复制到 Azure Data Lake Store，或从 Azure Data Lake Store 复制到任一支持的接收器数据存储。 有关复制活动支持作为源或接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

具体而言，此 Azure Data Lake Store 连接器支持：

- 使用**服务主体**或者**托管服务标识 (MSI)** 身份验证复制文件。
- 按原样复制文件，或者使用[支持的文件格式和压缩编解码器](supported-file-formats-and-compression-codecs.md)分析/生成文件。

> [!IMPORTANT]
> 如果使用自承载 Integration Runtime 复制数据，请配置企业防火墙以允许通过端口 443 到 `<ADLS account name>.azuredatalakestore.net` 和 `login.microsoftonline.com/<tenant>/oauth2/token` 的出站流量。 后者是 Azure 安全令牌服务 (STS)，IR 需要与之通信以获取访问令牌。

## <a name="get-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

对于特定于 Azure Data Lake Store 的数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

Azure Data Lake Store 链接服务支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | type 属性必须设置为 **AzureDataLakeStore**。 | 是 |
| dataLakeStoreUri | Azure Data Lake Store 帐户相关信息。 此信息采用以下格式之一：`https://[accountname].azuredatalakestore.net/webhdfs/v1` 或 `adl://[accountname].azuredatalakestore.net/`。 | 是 |
| subscriptionId | Data Lake Store 帐户所属的 Azure 订阅 ID。 | 接收器所需 |
| resourceGroupName | Data Lake Store 帐户所属的 Azure 资源组名称。 | 接收器所需 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如果数据存储位于专用网络，则可以使用 Azure 集成运行时或自承载集成运行时。 如果未指定，则使用默认 Azure 集成运行时。 |否 |

有关不同身份验证类型的更多属性和 JSON 示例，请分别参阅以下部分：

- [使用服务主体身份验证](#using-service-principal-authentication)
- [使用托管服务标识身份验证](#using-managed-service-identity-authentication)

### <a name="using-service-principal-authentication"></a>使用服务主体身份验证

要使用服务主体身份验证，请在 Azure Active Directory (Azure AD) 中注册一个应用程序实体并授予其访问 Data Lake Store 的权限。 有关详细步骤，请参阅[服务到服务身份验证](../data-lake-store/data-lake-store-authenticate-using-active-directory.md)。 记下下面的值，这些值用于定义链接服务：

- 应用程序 ID
- 应用程序密钥
- 租户 ID

>[!IMPORTANT]
> 请确保在 Azure Data Lake Store 中授予服务主体适当的权限：
>- **作为源**，在数据资源管理器 ->“访问权限”中，至少授予“读取 + 执行”权限以便列出和复制文件夹/子文件夹中的文件，或授予“读取”权限以便复制单个文件，并选择以添加为“访问权限和默认权限项”。 对帐户级别访问控制 (IAM) 没有要求。
>- **作为接收器**，在数据资源管理器 ->“访问权限”中，至少授予“写入 + 执行”权限以便在文件夹中创建子项目，并选择以添加为“访问权限和默认权限项”。 如果使用 Azure IR 复制（源和接收器都在云中），请在“访问控制(标识和访问管理)”中，至少授予“读者”角色，以便让数据工厂检测 Data Lake Store 区域。 如果需要避免此 IAM 角色，请通过 Data Lake Store 的位置显式[创建 Azure IR](create-azure-integration-runtime.md#create-azure-ir)，并在 Data Lake Store 链接服务中进行关联，如下面的示例所示。

支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| servicePrincipalId | 指定应用程序的客户端 ID。 | 是 |
| servicePrincipalKey | 指定应用程序的密钥。 将此字段标记为 SecureString 以安全地将其存储在数据工厂中或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 | 是 |
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

### <a name="using-managed-service-identity-authentication"></a>使用托管服务标识身份验证

可将数据工厂与代表此特定数据工厂的[托管服务标识](data-factory-service-identity.md)相关联。 可以像使用自己的服务主体一样，直接使用此服务标识进行 Data Lake Store 身份验证。 此指定工厂可通过此方法访问以及从/向 Data Lake Store 复制数据。

若要使用托管服务标识 (MSI) 身份验证，请执行以下操作：

1. 通过复制与工厂一起生成的“服务标识应用程序 ID”的值[检索数据工厂服务标识](data-factory-service-identity.md#retrieve-service-identity)。
2. 按照下面的说明以对服务主体所做的相同方式，向该服务标识授予对 Data Lake Store 的访问权限。

>[!IMPORTANT]
> 确保在 Azure Data Lake Store 中授予数据工厂服务标识适当的权限：
>- **作为源**，在数据资源管理器 ->“访问权限”中，至少授予“读取 + 执行”权限以便列出和复制文件夹/子文件夹中的文件，或授予“读取”权限以便复制单个文件，并选择以添加为“访问权限和默认权限项”。 对帐户级别访问控制 (IAM) 没有要求。
>- **作为接收器**，在数据资源管理器 ->“访问权限”中，至少授予“写入 + 执行”权限以便在文件夹中创建子项目，并选择以添加为“访问权限和默认权限项”。 如果使用 Azure IR 复制（源和接收器都在云中），请在“访问控制(标识和访问管理)”中，至少授予“读者”角色，以便让数据工厂检测 Data Lake Store 区域。 如果需要避免此 IAM 角色，请通过 Data Lake Store 的位置显式[创建 Azure IR](create-azure-integration-runtime.md#create-azure-ir)，并在 Data Lake Store 链接服务中进行关联，如下面的示例所示。

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

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 Azure Data Lake 源和接收器支持的属性列表。

### <a name="azure-data-lake-store-as-source"></a>Azure Data Lake Store 作为源

要从 Azure Data Lake Store 复制数据，请将复制活动中的源类型设置为“AzureDataLakeStoreSource”。 复制活动**源**部分支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动源的 type 属性必须设置为：**AzureDataLakeStoreSource** |是 |
| recursive | 指示是要从子文件夹中以递归方式读取数据，还是只从指定的文件夹中读取数据。 当 recursive 设置为 true 且接收器是基于文件的存储时，将不会在接收器上复制/创建空的文件夹/子文件夹。<br/>允许的值为：true（默认）、false | 否 |

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

若要向 Azure Data Lake Store 复制数据，请将复制活动中的接收器类型设置为“AzureDataLakeStoreSink”。 接收器部分支持以下属性：

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
有关 Azure 数据工厂中复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md##supported-data-stores-and-formats)。
