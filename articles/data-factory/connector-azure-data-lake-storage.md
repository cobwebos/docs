---
title: 将数据复制到或从 Azure 数据湖存储第 2 代使用数据工厂 |Microsoft Docs
description: 了解如何使用 Azure 数据工厂向 / 从 Azure 数据湖存储第 2 代复制数据。
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: jingwang
ms.openlocfilehash: 6425fdfe89ca2f4c47aaf0e5ffd1dac7767b5020
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67057935"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen2-using-azure-data-factory"></a>使用 Azure 数据工厂向/从 Azure Data Lake Storage Gen2 复制数据

Azure 数据湖存储第 2 代是一套功能专用于大数据分析内置[Azure Blob 存储](../storage/blobs/storage-blobs-introduction.md)。 可用于通过使用这两种文件系统和对象存储范例接口与你的数据。

本文概述如何向/从 Azure Data Lake Storage Gen2 复制数据。 若要了解 Azure 数据工厂，请阅读[介绍性文章](introduction.md)。

## <a name="supported-capabilities"></a>支持的功能

此 Azure 数据湖存储第 2 代连接器支持以下活动：

- [复制活动](copy-activity-overview.md)与[支持源或接收器的矩阵](copy-activity-overview.md)
- [映射数据流](concepts-data-flow-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)
- [GetMetadata 活动](control-flow-get-metadata-activity.md)

具体而言，使用此连接器可以：

- 将数据复制的 Azure 资源的身份验证使用的帐户密钥、 服务主体或管理的标识。
- 将文件或分析或生成具有的文件时复制[支持的文件格式和压缩编解码器](supported-file-formats-and-compression-codecs.md)。

>[!TIP]
>如果启用层次结构命名空间，目前没有任何互操作性的 Blob 和 Data Lake 存储第 2 代 Api 之间的操作。 如果遇到错误"错误代码 = FilesystemNotFound"并显示消息"不存在指定的文件系统"，它由指定的接收器文件系统其他位置创建通过 Blob API 而不是 Data Lake 存储第 2 代 API。 若要解决此问题，请使用作为 Blob 容器的名称不存在的名称指定的新文件系统。 然后数据工厂会自动创建该文件系统数据复制过程。

>[!NOTE]
>如果启用**允许受信任的 Microsoft 服务访问此存储帐户**选项在 Azure 存储防火墙设置中，Azure 集成运行时不会连接到数据湖存储第 2 代，并显示禁止访问的错误。 因为数据工厂不被视为受信任的 Microsoft 服务，会出现错误消息。 使用自承载的集成运行时改为连接。

## <a name="get-started"></a>开始使用

>[!TIP]
>有关如何使用数据湖存储第 2 代连接器的演练，请参阅[将数据加载到 Azure 数据湖存储第 2 代](load-azure-data-lake-storage-gen2.md)。

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

以下部分提供有关用于定义到数据湖存储第 2 代的特定数据工厂实体的属性的信息。

## <a name="linked-service-properties"></a>链接服务属性

Azure 数据湖存储第 2 代连接器支持以下身份验证类型。 请参阅有关详细信息的相应部分：

- [帐户密钥身份验证](#account-key-authentication)
- [服务主体身份验证](#service-principal-authentication)
- [Azure 资源的托管标识身份验证](#managed-identity)

### <a name="account-key-authentication"></a>帐户密钥身份验证

若要使用存储帐户密钥身份验证，需支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | type 属性必须设置为 AzureBlobFS  。 |是 |
| url | 终结点的数据湖存储第 2 代的模式`https://<accountname>.dfs.core.windows.net`。 | 是 |
| accountKey | 数据湖存储第 2 代的帐户密钥。 将此字段标记为 SecureString 以安全地将其存储在数据工厂中或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 |是 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如果数据存储位于专用网络，可以使用 Azure 集成运行时或自承载的集成运行时。 如果未指定此属性，则使用默认 Azure 集成运行时。 |否 |

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

若要使用服务主体身份验证，请按照下列步骤。

1. 按照中的步骤注册 Azure Active Directory (Azure AD) 中的应用程序实体[向 Azure AD 租户注册应用程序](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant)。 记下下面的值，这些值用于定义链接服务：

    - 应用程序 ID
    - 应用程序密钥
    - 租户 ID

2. 向服务主体授予适当的权限。

    - **作为源**：在 Azure 存储资源管理器，请至少授予**读取 + 执行**权限才能列出和复制文件夹和子文件夹中的文件。 或者，可以授予“读取”权限以复制单个文件。  或者，在访问控制 (IAM)，授予至少**存储 Blob 数据读取器**角色。
    - **作为接收器**：在存储资源管理器，请至少授予**写入 + 执行**文件夹中创建子项目的权限。 或者，在访问控制 (IAM)，授予至少**存储 Blob 数据参与者**角色。

>[!NOTE]
>向列表名称开头的文件夹从帐户级别，或若要测试连接，您需要设置的权限被授予对服务主体**存储帐户的"存储 Blob 数据读取者"权限在 IAM**。 对于以下情况需要这样做：
>- **复制数据工具**创作复制管道。
>- 在创作期间使用**数据工厂 UI** 测试连接和浏览文件夹。 
>如果你担心授予在帐户级别的权限，则可以跳过测试连接和输入的路径手动在创作过程。 复制活动仍然起作用，只要通过复制文件的适当权限授予服务主体。

链接服务支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | type 属性必须设置为 AzureBlobFS  。 |是 |
| url | 终结点的数据湖存储第 2 代的模式`https://<accountname>.dfs.core.windows.net`。 | 是 |
| servicePrincipalId | 指定应用程序的客户端 ID。 | 是 |
| servicePrincipalKey | 指定应用程序的密钥。 将为此字段标记`SecureString`安全地将其存储在数据工厂。 或者，你可以[引用 Azure Key Vault 中存储的机密](store-credentials-in-key-vault.md)。 | 是 |
| tenant | 指定应用程序的租户信息（域名或租户 ID）。 通过将鼠标悬停鼠标在 Azure 门户的右上角进行检索。 | 是 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如果数据存储位于专用网络，可以使用 Azure 集成运行时或自承载的集成运行时。 如果未指定，则使用默认 Azure 集成运行时。 |否 |

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

### <a name="managed-identity"></a> Azure 资源的托管标识身份验证

可将数据工厂与代表此特定数据工厂的 [Azure 资源托管标识](data-factory-service-identity.md)相关联。 对于数据湖存储第 2 代身份验证，类似于使用服务主体，可以直接使用此托管的标识。 它允许此指定的工厂访问和复制数据到或从你的数据湖存储第 2 代。

若要使用 Azure 资源的身份验证管理的标识，请按照下列步骤。

1. [检索的数据工厂托管标识信息](data-factory-service-identity.md#retrieve-managed-identity)通过复制的值**服务标识应用程序 ID**与工厂一起生成。

2. 向托管标识授予适当的权限。

    - **作为源**：在存储资源管理器，请至少授予**读取 + 执行**权限才能列出和复制文件夹和子文件夹中的文件。 或者，可以授予“读取”权限以复制单个文件。  或者，在访问控制 (IAM)，授予至少**存储 Blob 数据读取器**角色。
    - **作为接收器**：在存储资源管理器，请至少授予**写入 + 执行**文件夹中创建子项目的权限。 或者，在访问控制 (IAM)，授予至少**存储 Blob 数据参与者**角色。

>[!NOTE]
>为列表名称开头的文件夹从帐户级别，或若要测试连接，您需要设置的权限被授予对托管标识**存储帐户的"存储 Blob 数据读取者"权限在 IAM**。 对于以下情况需要这样做：
>- **复制数据工具**创作复制管道。
>- 在创作期间使用**数据工厂 UI** 测试连接和浏览文件夹。 
>如果你担心授予在帐户级别的权限，则可以跳过测试连接和输入的路径手动在创作过程。 复制活动仍然起作用，只要具有文件要复制的适当权限授予托管的标识。

>[!IMPORTANT]
>如果使用 PolyBase 将数据从数据湖存储第 2 代载入 SQL 数据仓库中，使用数据湖存储第 2 代托管的标识身份验证时，请确保还遵循步骤 1 和 2 中的[本指南](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage)。 按照的说明与 Azure Active Directory (Azure AD) 注册你的 SQL 数据库服务器。 您还将使用基于角色的访问控制的存储 Blob 数据参与者角色分配到 SQL 数据库服务器。 数据工厂处理其余部分。 如果你的数据湖存储第 2 代配置为使用 Azure 虚拟网络终结点以使用 PolyBase 将数据从其加载，则必须使用托管的标识身份验证。

链接服务支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | type 属性必须设置为 AzureBlobFS  。 |是 |
| url | 终结点的数据湖存储第 2 代的模式`https://<accountname>.dfs.core.windows.net`。 | 是 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如果数据存储位于专用网络，可以使用 Azure 集成运行时或自承载的集成运行时。 如果未指定，则使用默认 Azure 集成运行时。 |否 |

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

有关的各部分和属性可用于定义数据集的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)。

- Parquet 和带分隔符的文本格式，请参阅[Parquet 和带分隔符的文本格式的数据集](#parquet-and-delimited-text-format-dataset)部分。
- ORC、 Avro、 JSON 或二进制格式等其他格式，请参阅[其他格式数据集](#other-format-dataset)部分。

### <a name="parquet-and-delimited-text-format-dataset"></a>Parquet 和带分隔符的文本格式数据集

若要将数据与数据湖存储第 2 代复制 parquet 或带分隔符的文本格式，请参阅[Parquet 格式](format-parquet.md)并[分隔符的文本格式](format-delimited-text.md)基于格式的数据集和支持的设置上的文章。 数据湖存储第 2 代下支持以下属性`location`中基于格式的数据集的设置：

| 属性   | 说明                                                  | 必选 |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | 下面的类型属性`location`在数据集中必须设置为**AzureBlobFSLocation**。 | 是      |
| fileSystem | 数据湖存储第 2 代文件系统名称。                              | 否       |
| folderPath | 在给定的文件系统文件夹的路径。 如果你想要使用通配符来筛选文件夹，将跳过此设置，在活动源设置中指定。 | 否       |
| fileName   | 给定 fileSystem + folderPath 下的文件名。 如果你想要使用通配符来筛选文件，将跳过此设置，在活动源设置中指定。 | 否       |

> [!NOTE]
> **AzureBlobFSFile**仍受支持类型使用以下部分所述的 parquet 或文本格式的数据集，因为用于复制、 查找或向后兼容性的 GetMetadata 活动。 但它不能用于映射数据流功能。 我们建议使用今后此新模型。 数据工厂制作 UI 生成这些新类型。

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

### <a name="other-format-dataset"></a>其他格式数据集

若要将数据复制到和从数据湖存储第 2 代中 ORC、 Avro、 JSON 或二进制格式，支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为 AzureBlobFSFile  。 |是 |
| folderPath | 数据湖存储第 2 代中的文件夹的路径。 如果未指定，它指向根目录。 <br/><br/>支持通配符筛选器。 允许的通配符为`*`（匹配零个或多个字符） 和`?`(匹配零或单个字符)。 使用`^`进行转义，如果你的实际文件夹名称包含一个通配符或此转义字符位于内。 <br/><br/>示例： 文件系统/文件夹 /。 请参阅[文件夹和文件筛选器示例](#folder-and-file-filter-examples)中的更多示例。 |否 |
| fileName | 指定的"folderPath"下的文件的名称或通配符筛选器。 如果没有为此属性指定任何值，则数据集会指向文件夹中的所有文件。 <br/><br/>对于筛选器，是允许的通配符`*`（匹配零个或多个字符） 和`?`(匹配零或单个字符)。<br/>- 示例 1：`"fileName": "*.csv"`<br/>- 示例 2：`"fileName": "???20180427.txt"`<br/>使用`^`进行转义，如果你的实际文件名称包含一个通配符或此转义字符位于内。<br/><br/>如果没有为输出数据集指定 fileName，并且没有在活动接收器中指定 preserveHierarchy，则复制活动会自动生成采用以下模式的文件名称  ："*数据。[活动运行 ID GUID]。[GUID 如果 FlattenHierarchy]。[格式如果配置]。[压缩如果配置]* "，例如，"Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz"。 如果使用表名而不查询表格从源复制，名称的模式是" *[表名]。 [format]。[压缩如果配置]* "，例如，"MyTable.csv"。 |否 |
| modifiedDatetimeStart | 基于上次修改该属性的文件筛选器。 如果其上次修改的时间之间的时间范围内，在选择了文件`modifiedDatetimeStart`和`modifiedDatetimeEnd`。 时间应用于 UTC 时区，格式为"2018年-12-01T05:00:00Z"。 <br/><br/> 数据移动的整体性能受时要执行大量的文件与文件筛选器启用此设置。 <br/><br/> 属性可以为 NULL，这意味着任何文件属性筛选器应用于数据集。 当`modifiedDatetimeStart`具有日期时间值，但`modifiedDatetimeEnd`为 NULL，这意味着其最后一个修改后的属性是比更大的文件或选择日期时间值等于。 当`modifiedDatetimeEnd`具有日期时间值，但`modifiedDatetimeStart`为 NULL，它意味着将选择其最后一个修改后的属性小于 datetime 值的文件。| 否 |
| modifiedDatetimeEnd | 基于上次修改该属性的文件筛选器。 如果其上次修改的时间之间的时间范围内，在选择了文件`modifiedDatetimeStart`和`modifiedDatetimeEnd`。 时间应用于 UTC 时区，格式为"2018年-12-01T05:00:00Z"。 <br/><br/> 数据移动的整体性能受时要执行大量的文件与文件筛选器启用此设置。 <br/><br/> 属性可以为 NULL，这意味着任何文件属性筛选器应用于数据集。 当`modifiedDatetimeStart`具有日期时间值，但`modifiedDatetimeEnd`为 NULL，这意味着其最后一个修改后的属性是比更大的文件或选择日期时间值等于。 当`modifiedDatetimeEnd`具有日期时间值，但`modifiedDatetimeStart`为 NULL，它意味着将选择其最后一个修改后的属性小于 datetime 值的文件。| 否 |
| format | 若要在基于文件的存储之间按原样复制文件（二进制副本），可以在输入和输出数据集定义中跳过格式节。<br/><br/>如果要分析或生成具有特定格式的文件，以下是受支持的文件格式类型：TextFormat、JsonFormat、AvroFormat、OrcFormat 和 ParquetFormat      。 请将 **format** 中的 **type** 属性设置为上述值之一。 有关详细信息，请参阅[文本格式](supported-file-formats-and-compression-codecs.md#text-format)， [JSON 格式](supported-file-formats-and-compression-codecs.md#json-format)， [Avro 格式](supported-file-formats-and-compression-codecs.md#avro-format)， [ORC 格式](supported-file-formats-and-compression-codecs.md#orc-format)，和[Parquet 格式](supported-file-formats-and-compression-codecs.md#parquet-format)部分。 |否（仅适用于二进制复制方案） |
| compression | 指定数据的压缩类型和级别。 有关详细信息，请参阅[受支持的文件格式和压缩编解码器](supported-file-formats-and-compression-codecs.md#compression-support)。<br/>支持的类型为 **GZip**、**Deflate**、**BZip2** 和 **ZipDeflate**。<br/>支持的级别为“最佳”  和“最快”  。 |否 |

>[!TIP]
>如需复制文件夹下的所有文件，请仅指定 **folderPath**。<br>若要复制包含具有给定名称的单个文件，指定**folderPath**文件夹部分并**fileName**用文件名称。<br>若要复制的文件夹下的文件子集，请指定**folderPath**文件夹部分以及**文件名**使用通配符筛选器。 

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

## <a name="copy-activity-properties"></a>复制活动属性

有关的各部分和属性可用于定义活动的完整列表，请参阅[将复制活动配置](copy-activity-overview.md#configuration)并[管道和活动](concepts-pipelines-activities.md)。 本部分列出了 Data Lake Storage Gen2 源和接收器支持的属性。

### <a name="azure-data-lake-storage-gen2-as-a-source-type"></a>Azure 数据湖存储第 2 代作为源类型

- 若要复制 parquet 或带分隔符的文本格式，请参阅[Parquet 和带分隔符的文本格式源](#parquet-and-delimited-text-format-source)部分。
- 若要从 ORC、 Avro、 JSON 或二进制格式等其他格式复制，请参阅[其他格式源](#other-format-source)部分。

#### <a name="parquet-and-delimited-text-format-source"></a>Parquet 和带分隔符的文本格式源

若要将数据从数据湖存储第 2 代复制 parquet 或带分隔符的文本格式，请参阅[Parquet 格式](format-parquet.md)并[分隔符的文本格式](format-delimited-text.md)基于格式的复制活动源服务器和受支持的设置上的文章。 数据湖存储第 2 代下支持以下属性`storeSettings`基于格式的复制源中的设置：

| 属性                 | 说明                                                  | 必选                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | `storeSettings` 下的 type 属性必须设置为 **AzureBlobFSReadSetting**。 | 是                                           |
| recursive                | 指示是要从子文件夹中以递归方式读取数据，还是只从指定的文件夹中读取数据。 当 recursive 设置为 true 且接收器是基于文件的存储，一个空文件夹或子文件夹不是复制或在接收器上创建。 允许的值为 **true**（默认值）和 **false**。 | 否                                            |
| wildcardFolderPath       | 数据集中配置的给定文件系统下包含通配符的文件夹路径，用于筛选源文件夹。 <br>允许的通配符为`*`（匹配零个或多个字符） 和`?`(匹配零或单个字符)。 使用`^`进行转义，如果你的实际文件夹名称具有通配符或在此转义字符。 <br>请参阅[文件夹和文件筛选器示例](#folder-and-file-filter-examples)中的更多示例。 | 否                                            |
| wildcardFileName         | 给定的文件系统 + folderPath/wildcardFolderPath 下包含通配符的文件名，用于筛选源文件。 <br>允许的通配符为`*`（匹配零个或多个字符） 和`?`(匹配零或单个字符)。 使用`^`进行转义，如果你的实际文件夹名称具有通配符或在此转义字符。 请参阅[文件夹和文件筛选器示例](#folder-and-file-filter-examples)中的更多示例。 | 是如果`fileName`未指定数据集中 |
| modifiedDatetimeStart    | 基于上次修改该属性的文件筛选器。 如果其上次修改的时间之间的时间范围内，在选择了文件`modifiedDatetimeStart`和`modifiedDatetimeEnd`。 时间应用于 UTC 时区，格式为"2018年-12-01T05:00:00Z"。 <br> 属性可以为 NULL，这意味着，任何文件属性筛选器应用于数据集。 当`modifiedDatetimeStart`具有日期时间值，但`modifiedDatetimeEnd`为 NULL，则意味着其上次修改属性的文件是大于或等于日期时间值所选。 当`modifiedDatetimeEnd`具有日期时间值，但`modifiedDatetimeStart`为 NULL，这意味着，其最后一个修改后的属性小于 datetime 值的文件处于选中状态。 | 否                                            |
| modifiedDatetimeEnd      | 同上。                                               | 否                                            |
| maxConcurrentConnections | 若要同时连接到存储存储的连接数。 仅在要限制与数据存储的并发连接时指定。 | 否                                            |

> [!NOTE]
> 对于带分隔符的文本格式的 parquet **AzureBlobFSSource**仍受支持类型的复制活动源中的以下部分所述，因为是为了向后兼容。 我们建议使用今后此新模型。 数据工厂制作 UI 生成这些新类型。

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
                    "type": "DelimitedTextReadSetting",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AzureBlobFSReadSetting",
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

若要从数据湖存储第 2 代 ORC、 Avro、 JSON 或二进制格式中复制数据，支持以下属性将复制活动中**源**部分：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动源的 type 属性必须设置为 AzureBlobFSSource  。 |是 |
| recursive | 指示是要从子文件夹中以递归方式读取数据，还是只从指定的文件夹中读取数据。 当 recursive 设置为 true 且接收器是基于文件的存储，一个空文件夹或子文件夹不是复制或在接收器上创建。<br/>允许的值为 **true**（默认值）和 **false**。 | 否 |
| maxConcurrentConnections | 若要同时连接到数据存储的连接数。 仅在要限制与数据存储的并发连接时指定。 | 否 |

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

### <a name="azure-data-lake-storage-gen2-as-a-sink-type"></a>Azure Data Lake Storage Gen2 作为接收器类型

- 若要将复制到 parquet 或带分隔符的文本格式，请参阅[Parquet 和带分隔符的文本格式接收器](#parquet-and-delimited-text-format-sink)部分。
- 若要将复制到 ORC、 Avro、 JSON 或二进制格式等其他格式，请参阅[其他格式接收器](#other-format-sink)部分。

#### <a name="parquet-and-delimited-text-format-sink"></a>Parquet 和带分隔符的文本格式接收器

若要将数据复制到数据湖存储第 2 代中，以 parquet 或带分隔符的文本格式，请参阅[Parquet 格式](format-parquet.md)并[分隔符的文本格式](format-delimited-text.md)基于格式的复制活动接收器和受支持的设置上的文章。 数据湖存储第 2 代下支持以下属性`storeSettings`格式基于复制接收器中的设置：

| 属性                 | 说明                                                  | 必选 |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | `storeSettings` 下的 type 属性必须设置为 **AzureBlobFSWriteSetting**。 | 是      |
| copyBehavior             | 定义以基于文件的数据存储中的文件为源时的复制行为。<br/><br/>允许值包括：<br/><b>- PreserveHierarchy（默认）</b>：将文件层次结构保留到目标文件夹中。 指向源文件夹的源文件相对路径与指向目标文件夹的目标文件相对路径相同。<br/><b>- FlattenHierarchy</b>：源文件夹中的所有文件都位于目标文件夹的第一级中。 目标文件具有自动生成的名称。 <br/><b>- MergeFiles</b>：将源文件夹中的所有文件合并到一个文件中。 如果指定了文件名，则合并文件的名称为指定名称。 否则，它是自动生成的文件名。 | 否       |
| maxConcurrentConnections | 若要同时连接到数据存储的连接数。 仅在要限制与数据存储的并发连接时指定。 | 否       |

> [!NOTE]
> 对于带分隔符的文本格式的 parquet **AzureBlobFSSink**仍受支持类型复制活动接收器中的以下部分所述，因为是为了向后兼容。 我们建议使用今后此新模型。 数据工厂制作 UI 生成这些新类型。

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
                    "type": "AzureBlobFSWriteSetting",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

#### <a name="other-format-sink"></a>其他格式接收器

若要将数据复制到数据湖存储第 2 代中 ORC、 Avro、 JSON 或二进制格式，支持以下属性中**接收器**部分：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动接收器的 type 属性必须设置为 AzureBlobFSSink  。 |是 |
| copyBehavior | 定义以基于文件的数据存储中的文件为源时的复制行为。<br/><br/>允许值包括：<br/><b>- PreserveHierarchy（默认）</b>：将文件层次结构保留到目标文件夹中。 指向源文件夹的源文件相对路径与指向目标文件夹的目标文件相对路径相同。<br/><b>- FlattenHierarchy</b>：源文件夹中的所有文件都位于目标文件夹的第一级中。 目标文件具有自动生成的名称。 <br/><b>- MergeFiles</b>：将源文件夹中的所有文件合并到一个文件中。 如果指定了文件名，则合并文件的名称为指定名称。 否则，它是自动生成的文件名。 | 否 |
| maxConcurrentConnections | 若要同时连接到数据存储的连接数。 仅在要限制与数据存储的并发连接时指定。 | 否 |

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

### <a name="folder-and-file-filter-examples"></a>文件夹和文件筛选器示例

本部分介绍使用通配符筛选器生成文件夹路径和文件名的行为。

| folderPath | fileName | recursive | 源文件夹结构和筛选器结果（用**粗体**表示的文件已检索）|
|:--- |:--- |:--- |:--- |
| `Folder*` | （为空，使用默认值） | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | （为空，使用默认值） | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="some-recursive-and-copybehavior-examples"></a>一些 recursive 和 copyBehavior 示例

本部分介绍了 recursive 和 copyBehavior 值的不同组合的复制操作的结果行为。

| recursive | copyBehavior | 源文件夹结构 | 生成目标 |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用与源相同的结构创建目标 Folder1：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用以下结构创建目标 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 的自动生成的名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2 的自动生成的名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File3 的自动生成的名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File4 的自动生成的名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File5 的自动生成的名称 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用以下结构创建目标 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File5 的内容将合并到一个文件中，且自动生成文件名。 |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用以下结构创建目标 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>不被选取 File3、 File4，与 File5 的 Subfolder1。 |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用以下结构创建目标 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 的自动生成的名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2 的自动生成的名称<br/><br/>不被选取 File3、 File4，与 File5 的 Subfolder1。 |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用以下结构创建目标 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 的内容将合并到一个文件中，且自动生成文件名。 File1 的自动生成的名称<br/><br/>不被选取 File3、 File4，与 File5 的 Subfolder1。 |

## <a name="preserve-acls-from-data-lake-storage-gen1"></a>保留从数据湖存储 Gen1 Acl

>[!TIP]
>若要将数据复制从 Azure 数据湖存储 Gen1 到第 2 代通常情况下，请参阅[将数据从 Azure 数据湖存储 Gen1 复制到使用 Azure 数据工厂第 2 代](load-azure-data-lake-storage-gen2-from-gen1.md)演练和最佳做法。

当将文件从 Azure 数据湖存储 Gen1 复制到第 2 代时，您可以选择保留以及数据的 POSIX 访问控制列表 (Acl)。 访问控制的详细信息，请参阅[Azure 数据湖存储 Gen1 中的访问控制](../data-lake-store/data-lake-store-access-control.md)并[Azure 数据湖存储第 2 代中的访问控制](../storage/blobs/data-lake-storage-access-control.md)。

可以通过使用 Azure 数据工厂复制活动保留以下类型的 Acl。 您可以选择一个或多个类型：

- **ACL**:复制并保留文件和目录上的 POSIX 访问控制列表。 它将完整的现有 Acl 复制从源到接收器。 
- **所有者**：复制并保留文件和目录的拥有用户。 到接收器数据湖存储第 2 代的超级用户访问权限是必需的。
- **组**:复制并保留文件和目录的拥有组。 超级用户访问到接收器数据湖存储 Gen2 或拥有用户 （如果拥有用户也是目标组的成员） 是必需的。

如果指定要从文件夹中复制，数据工厂复制该给定的文件夹的文件和在其下的目录的 Acl，如果`recursive`设置为 true。 如果指定要从单个文件复制，复制该文件上的 Acl。

>[!IMPORTANT]
>当您选择将 Acl 保存时，请确保授予较高，数据工厂就能针对您的接收器数据湖存储第 2 代帐户操作的足够权限。 例如，使用帐户密钥身份验证，或将存储 Blob 数据所有者角色分配给服务主体或托管标识。

当使用配置源作为数据湖存储 Gen1 使用二进制副本选项或二进制格式和接收器数据湖存储第 2 代作为二进制副本选项或二进制格式时，您可以找到**保留**选项卡上**复制数据工具设置**页上或在**复制活动** > **设置**活动创作选项卡。

![数据湖存储 Gen1 到第 2 代保留 ACL](./media/connector-azure-data-lake-storage/adls-gen2-preserve-acl.png)

下面是 JSON 配置的一个示例 (请参阅`preserve`): 

```json
"activities":[
    {
        "name": "CopyFromGen1ToGen2",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "AzureDataLakeStoreSource",
                "recursive": true
            },
            "sink": {
                "type": "AzureBlobFSSink",
                "copyBehavior": "PreserveHierarchy"
            },
            "preserve": [
                "ACL",
                "Owner",
                "Group"
            ]
        },
        "inputs": [
            {
                "referenceName": "<Azure Data Lake Storage Gen1 input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Data Lake Storage Gen2 output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="mapping-data-flow-properties"></a>映射数据流属性

详细了解如何[源转换](data-flow-source.md)并[接收器转换](data-flow-sink.md)中映射数据流功能。

## <a name="next-steps"></a>后续步骤

有关数据工厂中复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md##supported-data-stores-and-formats)。
