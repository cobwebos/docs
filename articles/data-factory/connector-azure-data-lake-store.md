---
title: 将数据复制到或从 Azure 数据湖存储 Gen1 使用数据工厂 |Microsoft Docs
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
ms.date: 05/13/2019
ms.author: jingwang
ms.openlocfilehash: aedfa381f6520a5295467821097b38dd28dcd60c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67057912"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen1-using-azure-data-factory"></a>将数据复制到或从 Azure 数据湖存储 Gen1 使用 Azure 数据工厂
> [!div class="op_single_selector" title1="选择要使用的 Azure 数据工厂的版本："]
> * [版本 1](v1/data-factory-azure-datalake-connector.md)
> * [当前版本](connector-azure-data-lake-store.md)

本文概述了如何向 / 从 Azure 数据湖存储 Gen1 复制数据。 若要了解 Azure 数据工厂，请阅读[介绍性文章](introduction.md)。

## <a name="supported-capabilities"></a>支持的功能

此 Azure 数据湖存储 Gen1 连接器支持以下活动：

- [复制活动](copy-activity-overview.md)与[支持源或接收器的矩阵](copy-activity-overview.md)
- [映射数据流](concepts-data-flow-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)
- [GetMetadata 活动](control-flow-get-metadata-activity.md)

具体而言，使用此连接器可以：

- 将文件复制使用的身份验证的以下方法之一： 服务主体或托管的 Azure 资源的标识。
- 将文件或分析或生成具有的文件时复制[支持的文件格式和压缩编解码器](supported-file-formats-and-compression-codecs.md)。

> [!IMPORTANT]
> 如果使用自承载的 integration runtime 复制数据，请配置企业防火墙以允许出站流量`<ADLS account name>.azuredatalakestore.net`和`login.microsoftonline.com/<tenant>/oauth2/token`端口 443 上。 后者是 Azure 安全令牌服务，集成运行时需要与之通信以获取访问令牌。

## <a name="get-started"></a>开始使用

> [!TIP]
> 有关如何使用 Azure Data Lake Store 连接器的演练，请参阅[将数据加载到 Azure Data Lake Store](load-azure-data-lake-store.md)。

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

以下部分提供有关用于定义 Azure Data Lake Store 到特定的数据工厂实体的属性的信息。

## <a name="linked-service-properties"></a>链接服务属性

Azure Data Lake Store 链接服务支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | `type` 属性必须设置为 **AzureDataLakeStore**。 | 是 |
| dataLakeStoreUri | Azure Data Lake Store 帐户相关信息。 此信息采用以下格式之一：`https://[accountname].azuredatalakestore.net/webhdfs/v1` 或 `adl://[accountname].azuredatalakestore.net/`。 | 是 |
| subscriptionId | Data Lake Store 帐户所属的 Azure 订阅 ID。 | 接收器所需 |
| resourceGroupName | Data Lake Store 帐户所属的 Azure 资源组名称。 | 接收器所需 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如果数据存储位于专用网络中，可以使用 Azure 集成运行时或自承载的集成运行时。 如果未指定此属性，则使用默认 Azure 集成运行时。 |否 |

### <a name="use-service-principal-authentication"></a>使用服务主体身份验证

要使用服务主体身份验证，请在 Azure Active Directory 中注册一个应用程序实体并授予其访问 Data Lake Store 的权限。 有关详细步骤，请参阅[服务到服务身份验证](../data-lake-store/data-lake-store-authenticate-using-active-directory.md)。 记下下面的值，这些值用于定义链接服务：

- 应用程序 ID
- 应用程序密钥
- 租户 ID

>[!IMPORTANT]
> 授予服务主体适当的权限在 Data Lake Store 中：
>- **作为源**：在“数据资源管理器” > “访问权限”中，至少授予“读取 + 执行”权限以列出和复制文件夹和子文件夹中的文件。    或者，可以授予“读取”权限以复制单个文件。  对于递归，可以选择添加到“此文件夹和所有子文件夹”，并添加为“访问权限和默认权限项”。   对帐户级别的访问控制 (IAM) 没有要求。
>- **作为接收器**：在“数据资源管理器” > “访问权限”中，至少授予“写入 + 执行”权限以在文件夹中创建子项。    对于递归，可以选择添加到“此文件夹和所有子文件夹”，并添加为“访问权限和默认权限项”。   如果您使用的 Azure 集成运行时复制 （源和接收器都在云中），在 IAM，授予至少**读取器**角色，以便让数据工厂检测到 Data Lake Store 的区域。 若要避免此 IAM 角色，请使用 Data Lake Store 的位置显式[创建 Azure 集成运行时](create-azure-integration-runtime.md#create-azure-ir)。 例如，如果 Data Lake Store 西欧，创建 Azure 集成运行时位置设为"欧洲西部。" 将其关联的 Data Lake Store 链接服务中为下面的示例中所示。

>[!NOTE]
>若要列出从根目录开始的文件夹，必须设置要**在根级别使用“执行”权限**授予的服务主体权限。 对于以下情况需要这样做：
>- **复制数据工具**创作复制管道。
>- 在创作期间使用**数据工厂 UI** 测试连接和浏览文件夹。
>如果你担心授予根级别的权限，则跳过连接测试，在创作过程中手动输入的路径。 复制活动的工作，只要服务主体授予与要复制文件的正确权限。

支持以下属性：

| 属性 | 说明 | 需要 |
|:--- |:--- |:--- |
| servicePrincipalId | 指定应用程序的客户端 ID。 | 是 |
| servicePrincipalKey | 指定应用程序的密钥。 将此字段标记为 `SecureString` 以安全地将其存储在数据工厂中或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 | 是 |
| tenant | 指定的租户信息，如域名或租户的 ID，你的应用程序所在。 可将鼠标悬停在 Azure 门户右上角进行检索。 | 是 |

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

若要使用 Azure 资源的托管标识身份验证，请执行以下操作：

1. 通过复制与工厂一起生成的“服务标识应用程序 ID”值，[检索数据工厂托管标识信息](data-factory-service-identity.md#retrieve-managed-identity)。
2. 按照下面的说明向该托管标识授予对 Data Lake Store 的访问权限，所用方式与对服务主体使用的方式相同。

>[!IMPORTANT]
> 确保在 Data Lake Store 中授予数据工厂托管标识适当的权限：
>- **作为源**：在“数据资源管理器” > “访问权限”中，至少授予“读取 + 执行”权限以列出和复制文件夹和子文件夹中的文件。    或者，可以授予“读取”权限以复制单个文件。  对于递归，可以选择添加到“此文件夹和所有子文件夹”，并添加为“访问权限和默认权限项”。   对帐户级别的访问控制 (IAM) 没有要求。
>- **作为接收器**：在“数据资源管理器” > “访问权限”中，至少授予“写入 + 执行”权限以在文件夹中创建子项。    对于递归，可以选择添加到“此文件夹和所有子文件夹”，并添加为“访问权限和默认权限项”。   如果您使用的 Azure 集成运行时复制 （源和接收器都在云中），在 IAM，授予至少**读取器**角色，以便让数据工厂检测到 Data Lake Store 的区域。 若要避免此 IAM 角色，请使用 Data Lake Store 的位置显式[创建 Azure 集成运行时](create-azure-integration-runtime.md#create-azure-ir)。 将其关联的 Data Lake Store 链接服务中为下面的示例中所示。

>[!NOTE]
>若要列出从根目录开始的文件夹，必须设置**在根级别使用“执行”权限**授予的托管标识权限。 对于以下情况需要这样做：
>- **复制数据工具**创作复制管道。
>- 在创作期间使用**数据工厂 UI** 测试连接和浏览文件夹。
>如果你担心授予根级别的权限，则跳过连接测试，在创作过程中手动输入的路径。 复制活动的工作，只要具有文件要复制的适当权限授予托管的标识。

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

- Parquet 和带分隔符的文本格式，请参阅[Parquet 和带分隔符的文本格式的数据集](#parquet-and-delimited-text-format-dataset)部分。
- ORC、 Avro、 JSON 或二进制格式等其他格式，请参阅[其他格式数据集](#other-format-dataset)部分。

### <a name="parquet-and-delimited-text-format-dataset"></a>Parquet 和带分隔符的文本格式数据集

若要以 parquet 或带分隔符的文本格式向 / 从 Azure 数据湖存储 Gen1 复制数据，请参阅[Parquet 格式](format-parquet.md)并[分隔符的文本格式](format-delimited-text.md)基于格式的数据集和支持的设置上的文章。 Azure 数据湖存储 Gen1 下支持以下属性`location`中基于格式的数据集的设置：

| 属性   | 说明                                                  | 必选 |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | 下面的类型属性`location`在数据集中必须设置为**AzureDataLakeStoreLocation**。 | 是      |
| folderPath | 文件夹的路径。 如果你想要使用通配符来筛选文件夹，将跳过此设置，在活动源设置中指定。 | 否       |
| fileName   | 给定 folderPath 下的文件名。 如果你想要使用通配符来筛选文件，将跳过此设置，在活动源设置中指定。 | 否       |

> [!NOTE]
>
> **AzureDataLakeStoreFile**仍受支持类型使用以下部分所述的 parquet 或文本格式的数据集，因为用于复制、 查找和 GetMetadata 活动的向后兼容性。 但它不能用于映射数据流功能。 我们建议使用今后此新模型。 数据工厂制作 UI 生成这些新类型。

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

### <a name="other-format-dataset"></a>其他格式数据集

若要将数据复制到和从 Azure 数据湖存储 Gen1 ORC、 Avro、 JSON 或二进制格式中，支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为**AzureDataLakeStoreFile**。 |是 |
| folderPath | Data Lake Store 中的文件夹的路径。 如果未指定，它指向根目录。 <br/><br/>支持通配符筛选器。 允许的通配符为`*`（匹配零个或多个字符） 和`?`(匹配零或单个字符)。 使用`^`进行转义，如果你的实际文件夹名称具有通配符或在此转义字符。 <br/><br/>例如： rootfolder/subfolder /。 请参阅[文件夹和文件筛选器示例](#folder-and-file-filter-examples)中的更多示例。 |否 |
| fileName | 指定的"folderPath"下的文件的名称或通配符筛选器。 如果没有为此属性指定任何值，则数据集会指向文件夹中的所有文件。 <br/><br/>对于筛选器，是允许的通配符`*`（匹配零个或多个字符） 和`?`(匹配零或单个字符)。<br/>- 示例 1：`"fileName": "*.csv"`<br/>- 示例 2：`"fileName": "???20180427.txt"`<br/>使用`^`进行转义，如果你的实际文件名称具有通配符或在此转义字符。<br/><br/>如果没有为输出数据集指定 fileName，并且没有在活动接收器中指定 preserveHierarchy，则复制活动会自动生成采用以下模式的文件名称  ："*数据。[活动运行 ID GUID]。[GUID 如果 FlattenHierarchy]。[格式如果配置]。[压缩如果配置]* "，例如，"Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz"。 如果您从表格源复制而不是查询中使用表名称，名称的模式是" *[表名]。 [format]。[压缩如果配置]* "，例如，"MyTable.csv"。 |否 |
| modifiedDatetimeStart | 基于上次修改该属性的文件筛选器。 如果其上次修改的时间之间的时间范围内，在选择了文件`modifiedDatetimeStart`和`modifiedDatetimeEnd`。 时间应用于 UTC 时区，格式为"2018年-12-01T05:00:00Z"。 <br/><br/> 数据移动的整体性能受时要执行大量的文件与文件筛选器启用此设置。 <br/><br/> 属性可以为 NULL，这意味着任何文件属性筛选器应用于数据集。 当`modifiedDatetimeStart`具有日期时间值，但`modifiedDatetimeEnd`为 NULL，这意味着其最后一个修改后的属性是比更大的文件或选择日期时间值等于。 当`modifiedDatetimeEnd`具有日期时间值，但`modifiedDatetimeStart`为 NULL，它意味着将选择其最后一个修改后的属性小于 datetime 值的文件。| 否 |
| modifiedDatetimeEnd | 基于上次修改该属性的文件筛选器。 如果其上次修改的时间之间的时间范围内，在选择了文件`modifiedDatetimeStart`和`modifiedDatetimeEnd`。 时间应用于 UTC 时区，格式为"2018年-12-01T05:00:00Z"。 <br/><br/> 数据移动的整体性能受时要执行大量的文件与文件筛选器启用此设置。 <br/><br/> 属性可以为 NULL，这意味着任何文件属性筛选器应用于数据集。 当`modifiedDatetimeStart`具有日期时间值，但`modifiedDatetimeEnd`为 NULL，这意味着其最后一个修改后的属性是比更大的文件或选择日期时间值等于。 当`modifiedDatetimeEnd`具有日期时间值，但`modifiedDatetimeStart`为 NULL，它意味着将选择其最后一个修改后的属性小于 datetime 值的文件。| 否 |
| format | 如果你想要将文件之间基于文件的存储 （二进制副本） 按原样复制，跳过这两个输入和输出数据集定义中的格式部分。<br/><br/>若要分析或生成具有特定格式的文件，以下是受支持的文件格式类型：TextFormat、JsonFormat、AvroFormat、OrcFormat 和 ParquetFormat      。 请将 **format** 中的 **type** 属性设置为上述值之一。 有关详细信息，请参阅[文本格式](supported-file-formats-and-compression-codecs.md#text-format)、[JSON 格式](supported-file-formats-and-compression-codecs.md#json-format)、[Avro 格式](supported-file-formats-and-compression-codecs.md#avro-format)、[Orc 格式](supported-file-formats-and-compression-codecs.md#orc-format)和 [Parquet 格式](supported-file-formats-and-compression-codecs.md#parquet-format)部分。 |否（仅适用于二进制复制方案） |
| compression | 指定数据的压缩类型和级别。 有关详细信息，请参阅[受支持的文件格式和压缩编解码器](supported-file-formats-and-compression-codecs.md#compression-support)。<br/>支持的类型为 **GZip**、**Deflate**、**BZip2** 和 **ZipDeflate**。<br/>支持的级别为“最佳”  和“最快”  。 |否 |


>[!TIP]
>如需复制文件夹下的所有文件，请仅指定 **folderPath**。<br>若要复制包含特定名称的单个文件，指定**folderPath**文件夹部分并**fileName**用文件名称。<br>若要复制的文件夹下的文件子集，请指定**folderPath**文件夹部分以及**文件名**使用通配符筛选器。 

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

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各个部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)。 本部分提供 Azure Data Lake Store 源和接收器支持的属性列表。

### <a name="azure-data-lake-store-as-source"></a>Azure Data Lake Store 作为源

- 若要复制 parquet 或带分隔符的文本格式，请参阅[Parquet 和带分隔符的文本格式源](#parquet-and-delimited-text-format-source)部分。
- 若要从 ORC、 Avro、 JSON 或二进制格式等其他格式复制，请参阅[其他格式源](#other-format-source)部分。

#### <a name="parquet-and-delimited-text-format-source"></a>Parquet 和带分隔符的文本格式源

若要将数据从 Azure 数据湖存储 Gen1 复制 parquet 或带分隔符的文本格式，请参阅[Parquet 格式](format-parquet.md)并[分隔符的文本格式](format-delimited-text.md)基于格式的复制活动源服务器和受支持的设置上的文章。 Azure 数据湖存储 Gen1 下支持以下属性`storeSettings`基于格式的复制源中的设置：

| 属性                 | 说明                                                  | 必选                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | 下面的类型属性`storeSettings`必须设置为**AzureDataLakeStoreReadSetting**。 | 是                                           |
| recursive                | 指示是要从子文件夹中以递归方式读取数据，还是只从指定的文件夹中读取数据。 当 recursive 设置为 true 且接收器是基于文件的存储，一个空文件夹或子文件夹不是复制或在接收器上创建。 允许的值为 **true**（默认值）和 **false**。 | 否                                            |
| wildcardFolderPath       | 带有通配符的文件夹路径，用于筛选源文件夹。 <br>允许的通配符为`*`（匹配零个或多个字符） 和`?`(匹配零或单个字符)。 使用`^`进行转义，如果你的实际文件夹名称具有通配符或在此转义字符。 <br>请参阅[文件夹和文件筛选器示例](#folder-and-file-filter-examples)中的更多示例。 | 否                                            |
| wildcardFileName         | 给定的 folderPath/wildcardFolderPath 下带有通配符的文件名，用于筛选源文件。 <br>允许的通配符为`*`（匹配零个或多个字符） 和`?`(匹配零或单个字符)。 使用`^`进行转义，如果你的实际文件夹名称具有通配符或在此转义字符。 请参阅[文件夹和文件筛选器示例](#folder-and-file-filter-examples)中的更多示例。 | 是如果`fileName`未指定数据集中 |
| modifiedDatetimeStart    | 基于上次修改该属性的文件筛选器。 如果其上次修改的时间之间的时间范围内，在选择了文件`modifiedDatetimeStart`和`modifiedDatetimeEnd`。 时间应用于 UTC 时区，格式为"2018年-12-01T05:00:00Z"。 <br> 属性可以为 NULL，这意味着任何文件属性筛选器应用于数据集。 当`modifiedDatetimeStart`具有日期时间值，但`modifiedDatetimeEnd`为 NULL，这意味着其最后一个修改后的属性是比更大的文件或选择日期时间值等于。 当`modifiedDatetimeEnd`具有日期时间值，但`modifiedDatetimeStart`为 NULL，它意味着将选择其最后一个修改后的属性小于 datetime 值的文件。 | 否                                            |
| modifiedDatetimeEnd      | 同上。                                               | 否                                            |
| maxConcurrentConnections | 若要同时连接到存储存储的连接数。 仅在要限制与数据存储的并发连接时指定。 | 否                                            |

> [!NOTE]
> Parquet 或带分隔符的文本格式**AzureDataLakeStoreSource**仍受支持类型的复制活动源中的以下部分所述，因为是为了向后兼容。 我们建议使用今后此新模型。 数据工厂制作 UI 生成这些新类型。

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

#### <a name="other-format-source"></a>其他格式源

若要从 Azure 数据湖存储 Gen1 ORC、 Avro、 JSON 或二进制格式中复制数据，支持以下属性将复制活动中**源**部分：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | `type`的复制活动源的属性必须设置为**AzureDataLakeStoreSource**。 |是 |
| recursive | 指示是要从子文件夹中以递归方式读取数据，还是只从指定的文件夹中读取数据。 当`recursive`设置为 true 且接收器是基于文件的存储，一个空文件夹或子文件夹不是复制或在接收器上创建。 允许的值为 **true**（默认值）和 **false**。 | 否 |
| maxConcurrentConnections | 若要同时连接到数据存储的连接数。 仅在要限制与数据存储的并发连接时指定。 | 否 |

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

### <a name="azure-data-lake-store-as-sink"></a>Azure Data Lake Store 作为接收器

- 若要将复制到 parquet 和带分隔符的文本格式，请参阅[Parquet 和带分隔符的文本格式接收器](#parquet-and-delimited-text-format-sink)部分。
- 若要将复制到 ORC、 Avro、 JSON 或二进制格式等其他格式，请参阅[其他格式接收器](#other-format-sink)部分。

#### <a name="parquet-and-delimited-text-format-sink"></a>Parquet 和带分隔符的文本格式接收器

若要将数据复制到 Azure 数据湖存储 Gen1，parquet 或带分隔符的文本格式，请参阅[Parquet 格式](format-parquet.md)并[分隔符的文本格式](format-delimited-text.md)基于格式的复制活动接收器和受支持的设置上的文章。 Azure 数据湖存储 Gen1 下支持以下属性`storeSettings`格式基于复制接收器中的设置：

| 属性                 | 说明                                                  | 必选 |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | 下面的类型属性`storeSettings`必须设置为**AzureDataLakeStoreWriteSetting**。 | 是      |
| copyBehavior             | 定义以基于文件的数据存储中的文件为源时的复制行为。<br/><br/>允许值包括：<br/><b>- PreserveHierarchy（默认）</b>：将文件层次结构保留到目标文件夹中。 指向源文件夹的源文件相对路径与指向目标文件夹的目标文件相对路径相同。<br/><b>- FlattenHierarchy</b>：源文件夹中的所有文件都位于目标文件夹的第一级中。 目标文件具有自动生成的名称。 <br/><b>- MergeFiles</b>：将源文件夹中的所有文件合并到一个文件中。 如果指定了文件名，则合并文件的名称为指定名称。 否则，它是自动生成的文件名。 | 否       |
| maxConcurrentConnections | 若要同时连接到数据存储的连接数。 仅在要限制与数据存储的并发连接时指定。 | 否       |

> [!NOTE]
> Parquet 或带分隔符的文本格式**AzureDataLakeStoreSink**仍受支持类型复制活动接收器中的以下部分所述，因为是为了向后兼容。 我们建议使用今后此新模型。 数据工厂制作 UI 生成这些新类型。

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

#### <a name="other-format-sink"></a>其他格式接收器

若要将数据复制到 Azure 数据湖存储 Gen1 ORC、 Avro、 JSON 或二进制格式中，支持以下属性中**接收器**部分：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | `type`的复制活动接收器的属性必须设置为**AzureDataLakeStoreSink**。 |是 |
| copyBehavior | 定义以基于文件的数据存储中的文件为源时的复制行为。<br/><br/>允许值包括：<br/><b>- PreserveHierarchy（默认）</b>：将文件层次结构保留到目标文件夹中。 指向源文件夹的源文件相对路径与指向目标文件夹的目标文件相对路径相同。<br/><b>- FlattenHierarchy</b>：源文件夹中的所有文件都位于目标文件夹的第一级中。 目标文件具有自动生成的名称。 <br/><b>- MergeFiles</b>：将源文件夹中的所有文件合并到一个文件中。 如果指定了文件名，则合并文件的名称为指定名称。 否则，会自动生成文件名。 | 否 |
| maxConcurrentConnections | 若要同时连接到数据存储的连接数。 仅在要限制与数据存储的并发连接时指定。 | 否 |

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

### <a name="folder-and-file-filter-examples"></a>文件夹和文件筛选器示例

本部分介绍使用通配符筛选器生成文件夹路径和文件名的行为。

| folderPath | fileName | recursive | 源文件夹结构和筛选器结果（用**粗体**表示的文件已检索）|
|:--- |:--- |:--- |:--- |
| `Folder*` | （为空，使用默认值） | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | （为空，使用默认值） | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="examples-of-behavior-of-the-copy-operation"></a>复制操作的行为示例

本部分介绍将 `recursive` 和 `copyBehavior` 值进行不同组合所产生的复制操作行为。

| recursive | copyBehavior | 源文件夹结构 | 生成目标 |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用与源相同的结构创建目标 Folder1：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5。 |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用以下结构创建目标 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 的自动生成的名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2 的自动生成的名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File3 的自动生成的名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File4 的自动生成的名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File5 的自动生成的名称 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用以下结构创建目标 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File5 内容将合并到一个文件，且自动生成文件名。 |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用以下结构创建目标 Folder1：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>不被选取 File3、 File4，与 File5 的 Subfolder1。 |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用以下结构创建目标 Folder1：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 的自动生成的名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2 的自动生成的名称<br/><br/>不被选取 File3、 File4，与 File5 的 Subfolder1。 |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用以下结构创建目标 Folder1：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 的内容将合并到一个文件中使用自动生成文件名。 File1 的自动生成的名称<br/><br/>不被选取 File3、 File4，与 File5 的 Subfolder1。 |

## <a name="preserve-acls-to-data-lake-storage-gen2"></a>保留到数据湖存储第 2 代的 Acl

如果想要复制的访问控制以及数据文件列表 (Acl)，从数据湖存储 Gen1 升级到数据湖存储第 2 代时，请参阅[从数据湖存储 Gen1 保留 Acl](connector-azure-data-lake-storage.md#preserve-acls-from-data-lake-storage-gen1)。

## <a name="mapping-data-flow-properties"></a>映射数据流属性

详细了解如何[源转换](data-flow-source.md)并[接收器转换](data-flow-sink.md)中映射数据流功能。

## <a name="next-steps"></a>后续步骤

有关 Azure 数据工厂中复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md##supported-data-stores-and-formats)。
