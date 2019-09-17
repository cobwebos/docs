---
title: 使用数据工厂向/从 Azure Data Lake Storage Gen2 复制数据 | Microsoft Docs
description: 了解如何使用 Azure 数据工厂向/从 Azure Data Lake Storage Gen2 复制数据。
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: jingwang
ms.openlocfilehash: 8f190f6b933c61072df9af954c8db01497e35e82
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/16/2019
ms.locfileid: "71010234"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen2-using-azure-data-factory"></a>使用 Azure 数据工厂向/从 Azure Data Lake Storage Gen2 复制数据

Azure Data Lake Storage Gen2 （ADLS Gen2）是专用于[Azure Blob 存储](../storage/blobs/storage-blobs-introduction.md)中内置的大数据分析的一组功能。 它可使用文件系统和对象存储范例与数据进行交互。

本文概述如何向/从 Azure Data Lake Storage Gen2 复制数据。 若要了解 Azure 数据工厂，请阅读[介绍性文章](introduction.md)。

## <a name="supported-capabilities"></a>支持的功能

此 Azure Data Lake Storage Gen2 连接器支持以下活动：

- 带有[支持的源或接收器矩阵](copy-activity-overview.md)的[复制活动](copy-activity-overview.md)
- [映射数据流](concepts-data-flow-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)
- [GetMetadata 活动](control-flow-get-metadata-activity.md)
- [删除活动](delete-activity.md)

具体而言，使用此连接器可以：

- 通过使用帐户密钥、服务主体或托管标识进行 Azure 资源身份验证来复制数据。
- 按原样复制文件，或使用[支持的文件格式和压缩编解码器](supported-file-formats-and-compression-codecs.md)分析/生成文件。

>[!TIP]
>如果启用分层命名空间，则当前 Blob 和 Data Lake Storage Gen2 API 之间没有操作的互操作性。 如果遇到“ErrorCode=FilesystemNotFound”错误，详细消息为“指定的文件系统不存在”，这是由于指定的接收器文件系统是通过 Blob API 而非其他地方的 Data Lake Storage Gen2 API 创建的。 若要解决此问题，请指定其名称不同于已存在的 Blob 容器名称的一个新文件系统。 然后，数据工厂在复制数据期间将自动创建该文件系统。

>[!NOTE]
>如果在 Azure 存储防火墙设置上启用“允许受信任的 Microsoft 服务访问此存储帐户”选项，则使用 Azure Integration Runtime 连接 Data Lake Storage Gen2 将失败并显示“已禁止”错误。 由于不将数据工厂视为受信任的 Microsoft 服务，因此会显示错误消息。 请改用自承载集成运行时进行连接。

## <a name="get-started"></a>开始使用

>[!TIP]
>有关使用 Data Lake Storage Gen2 连接器的演练，请参阅[将数据加载到 Azure Data Lake Storage Gen2](load-azure-data-lake-storage-gen2.md)。

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

以下部分介绍了用于定义 Data Lake Storage Gen2 特定的数据工厂实体的属性。

## <a name="linked-service-properties"></a>链接服务属性

Azure Data Lake Storage Gen2 连接器支持以下身份验证类型。 请参阅相应部分的了解详细信息：

- [帐户密钥身份验证](#account-key-authentication)
- [服务主体身份验证](#service-principal-authentication)
- [Azure 资源的托管标识身份验证](#managed-identity)

>[!NOTE]
>使用 PolyBase 将数据载入 SQL 数据仓库时，如果源 Data Lake Storage Gen2 中未配置虚拟网络终结点，则必须使用 PolyBase 所需的托管标识身份验证。 请参阅[托管标识身份验证](#managed-identity)部分，其中提供了更多配置先决条件。

### <a name="account-key-authentication"></a>帐户密钥身份验证

若要使用存储帐户密钥身份验证，需支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | type 属性必须设置为 AzureBlobFS。 |是 |
| url | Data Lake Storage Gen2 的终结点，其模式为 `https://<accountname>.dfs.core.windows.net`。 | 是 |
| accountKey | Data Lake Storage Gen2 的帐户密钥 将此字段标记为 SecureString 以安全地将其存储在数据工厂中或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 |是 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 可使用 Azure Integration Runtime 或自承载集成运行时（如果数据存储位于专用网络）。 如果未指定此属性，则使用默认的 Azure Integration Runtime。 |否 |

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

若要使用服务主体身份验证，请执行以下步骤。

1. 遵循[将应用程序注册到 Azure AD 租户](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant)，在 Azure Active Directory (Azure AD) 中注册一个应用程序实体。 记下下面的值，这些值用于定义链接服务：

    - 应用程序 ID
    - 应用程序密钥
    - 租户 ID

2. 向服务主体授予适当的权限。 在[文件和目录上的访问控制列表](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)中详细了解 Data Lake Storage Gen2 中的权限的工作原理

    - **作为源**：在存储资源管理器，至少授予从源文件系统开始的“执行”权限，并授予“读取”权限以复制文件。 或者，在访问控制 (IAM) 中至少授予“存储 Blob 数据读取者”角色。
    - **作为接收器**：在存储资源管理器，至少授予从接收器文件系统开始的“执行”权限，并授予接收器文件夹的“写入”权限。 或者，在访问控制 (IAM) 中至少授予“存储 Blob 数据参与者”角色。

>[!NOTE]
>若要列出从帐户级别开始的文件夹或测试连接，需要在 IAM 中使用“存储 Blob 数据读取者”权限为服务主体授予对存储帐户的权限。 对于以下情况需要这样做：
>- 使用**复制数据工具**创作复制管道。
>- 在创作期间使用**数据工厂 UI** 测试连接和浏览文件夹。 
>如果你担心在帐户级别授予权限，请在创作期间，跳过测试连接，并输入已授予权限的父路径，然后选择从该指定路径进行浏览。 只要在要复制的文件上授予服务主体适当的权限，复制活动就可以正常工作。

链接服务支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | type 属性必须设置为 AzureBlobFS。 |是 |
| url | Data Lake Storage Gen2 的终结点，其模式为 `https://<accountname>.dfs.core.windows.net`。 | 是 |
| servicePrincipalId | 指定应用程序的客户端 ID。 | 是 |
| servicePrincipalKey | 指定应用程序的密钥。 将此字段标记为 `SecureString`，以便安全地将其存储在数据工厂中。 或者，可以[引用 Azure Key Vault 中存储的机密](store-credentials-in-key-vault.md)。 | 是 |
| 租户 | 指定应用程序的租户信息（域名或租户 ID）。 将鼠标悬停在 Azure 门户右上角进行检索。 | 是 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 可使用 Azure Integration Runtime 或自承载集成运行时（如果数据存储位于专用网络）。 如果未指定，则使用默认 Azure Integration Runtime。 |否 |

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

可将数据工厂与代表此特定数据工厂的 [Azure 资源托管标识](data-factory-service-identity.md)相关联。 可以像使用自己的服务主体一样，直接使用此托管标识进行 Data Lake Storage Gen2 身份验证。 此指定工厂可通过此方法访问以及向/从 Data Lake Storage Gen2 复制数据。

若要使用 Azure 资源的托管标识身份验证，请执行以下步骤。

1. 通过复制与工厂一起生成的**服务标识应用程序 ID** 值，[检索数据工厂托管标识信息](data-factory-service-identity.md#retrieve-managed-identity)。

2. 向托管标识授予适当的权限。 在[文件和目录上的访问控制列表](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)中详细了解 Data Lake Storage Gen2 中的权限的工作原理。

    - **作为源**：在存储资源管理器，至少授予从源文件系统开始的“执行”权限，并授予“读取”权限以复制文件。 或者，在访问控制 (IAM) 中至少授予“存储 Blob 数据读取者”角色。
    - **作为接收器**：在存储资源管理器，至少授予从接收器文件系统开始的“执行”权限，并授予接收器文件夹的“写入”权限。 或者，在访问控制 (IAM) 中至少授予“存储 Blob 数据参与者”角色。

>[!NOTE]
>若要列出从帐户级别开始的文件夹或测试连接，需要在 IAM 中使用“存储 Blob 数据读取者”权限为托管标识授予对存储帐户的权限。 对于以下情况需要这样做：
>- 使用**复制数据工具**创作复制管道。
>- 在创作期间使用**数据工厂 UI** 测试连接和浏览文件夹。 
>如果你担心在帐户级别授予权限，请在创作期间，跳过测试连接，并输入已授予权限的父路径，然后选择从该指定路径进行浏览。 只要在要复制的文件上授予服务主体适当的权限，复制活动就可以正常工作。

>[!IMPORTANT]
>如果在使用 Data Lake Storage Gen2 托管标识身份验证时使用 PolyBase 将 Data Lake Storage Gen2 中的数据载入 SQL 数据仓库，请确保同时遵循[此指南](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage)中的步骤 1 和 2，以便 1) 将 SQL 数据库服务器注册到 Azure Active Directory (Azure AD)，2) 将存储 Blob 数据参与者角色分配到 SQL 数据库服务器；剩余的任务将由数据工厂处理。 如果源 Data Lake Storage Gen2 中已配置 Azure 虚拟网络终结点，若要使用 PolyBase 从中加载数据，必须使用 PolyBase 所需的托管标识身份验证。

链接服务支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | type 属性必须设置为 AzureBlobFS。 |是 |
| url | Data Lake Storage Gen2 的终结点，其模式为 `https://<accountname>.dfs.core.windows.net`。 | 是 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 可使用 Azure Integration Runtime 或自承载集成运行时（如果数据存储位于专用网络）。 如果未指定，则使用默认 Azure Integration Runtime。 |否 |

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

- 对于**Parquet （分隔文本、json、avro 和二进制格式**），请参阅[Parquet，分隔文本、json、Avro 和二进制格式数据集](#format-based-dataset)部分。
- 有关**ORC 格式**等其他格式，请参阅[其他格式数据集](#other-format-dataset)部分。

### <a name="format-based-dataset"></a>Parquet，分隔文本、JSON、Avro 和二进制格式数据集

若要将数据复制到**Parquet、带分隔符的文本、avro 或二进制格式**，请参阅基于格式的数据集和支持的设置的[Parquet 格式](format-parquet.md)、[带分隔符的文本格式](format-delimited-text.md)、 [avro 格式](format-avro.md)和[二进制格式](format-binary.md)一文。 基于格式的数据集中 `location` 设置下的 Data Lake Storage Gen2 支持以下属性：

| 属性   | 说明                                                  | 必选 |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | 数据集中 `location` 下的 type 属性必须设置为 **AzureBlobFSLocation**。 | 是      |
| fileSystem | Data Lake Storage Gen2 文件系统名称。                              | 否       |
| folderPath | 给定文件系统下的文件夹路径。 如果要使用通配符筛选文件夹，请跳过此设置并在活动源设置中指定。 | 否       |
| fileName   | 给定 fileSystem + folderPath 下的文件名。 如果要使用通配符筛选文件，请跳过此设置并在活动源设置中指定。 | 否       |

> [!NOTE]
> copy、lookup 或 GetMetadata 活动仍然按原样支持下一部分中提到的带有 Parquet 或文本格式的 **AzureBlobFSFile** 类型数据集，以实现向后兼容。 但它不能与映射数据流功能配合使用。 我们建议今后使用新的模型。 数据工厂创作 UI 会生成这些新类型。

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

若要将数据与**ORC 格式**的 Data Lake Storage Gen2 一起复制，支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为 AzureBlobFSFile。 |是 |
| folderPath | Data Lake Storage Gen2 中的文件夹的路径。 如果未指定，它指向根目录。 <br/><br/>支持通配符筛选器。 允许的通配符为：`*`（匹配零个或更多字符）和 `?`（匹配零个或单个字符）。 如果实际文件夹名内具有通配符或此转义符，请使用 `^` 进行转义。 <br/><br/>示例：filesystem/folder/。 请参阅[文件夹和文件筛选器示例](#folder-and-file-filter-examples)中的更多示例。 |否 |
| fileName | 指定“folderPath”下的文件的“名称或通配符筛选器”。 如果没有为此属性指定任何值，则数据集会指向文件夹中的所有文件。 <br/><br/>对于筛选器，允许的通配符为：`*`（匹配零个或更多字符）和 `?`（匹配零个或单个字符）。<br/>- 示例 1：`"fileName": "*.csv"`<br/>- 示例 2：`"fileName": "???20180427.txt"`<br/>如果实际文件名内具有通配符或此转义符，请使用 `^` 进行转义。<br/><br/>如果没有为输出数据集指定 fileName，并且没有在活动接收器中指定 preserveHierarchy，则复制活动会自动生成采用以下模式的文件名称：“*Data.[activity run ID GUID].[GUID if FlattenHierarchy].[format if configured].[compression if configured]* ”，例如“Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz”。 如果使用表名称而不是查询从表格源进行复制，则名称模式为“[table name].[format].[compression if configured]”，例如“MyTable.csv”。 |否 |
| modifiedDatetimeStart | 基于属性“上次修改时间”的文件筛选器。 如果文件的上次修改时间在 `modifiedDatetimeStart` 和 `modifiedDatetimeEnd` 之间的时间范围内，则将选中这些文件。 该时间应用于 UTC 时区，格式为“2018-12-01T05:00:00Z”。 <br/><br/> 当你要从大量文件中进行文件筛选时，启用此设置将影响数据移动的整体性能。 <br/><br/> 属性可以为 NULL，这意味着不向数据集应用任何文件特性筛选器。 如果 `modifiedDatetimeStart` 具有日期/时间值，但 `modifiedDatetimeEnd` 为 NULL，则意味着将选中“上次修改时间”属性大于或等于该日期/时间值的文件。 如果 `modifiedDatetimeEnd` 具有日期/时间值，但 `modifiedDatetimeStart` 为 NULL，则意味着将选中“上次修改时间”属性小于该日期/时间值的文件。| 否 |
| modifiedDatetimeEnd | 基于属性“上次修改时间”的文件筛选器。 如果文件的上次修改时间在 `modifiedDatetimeStart` 和 `modifiedDatetimeEnd` 之间的时间范围内，则将选中这些文件。 该时间应用于 UTC 时区，格式为“2018-12-01T05:00:00Z”。 <br/><br/> 当你要从大量文件中进行文件筛选时，启用此设置将影响数据移动的整体性能。 <br/><br/> 属性可以为 NULL，这意味着不向数据集应用任何文件特性筛选器。 如果 `modifiedDatetimeStart` 具有日期/时间值，但 `modifiedDatetimeEnd` 为 NULL，则意味着将选中“上次修改时间”属性大于或等于该日期/时间值的文件。 如果 `modifiedDatetimeEnd` 具有日期/时间值，但 `modifiedDatetimeStart` 为 NULL，则意味着将选中“上次修改时间”属性小于该日期/时间值的文件。| 否 |
| format | 若要在基于文件的存储之间按原样复制文件（二进制副本），可以在输入和输出数据集定义中跳过格式节。<br/><br/>如果要分析或生成具有特定格式的文件，以下是受支持的文件格式类型：TextFormat、JsonFormat、AvroFormat、OrcFormat 和 ParquetFormat。 请将 **format** 中的 **type** 属性设置为上述值之一。 有关详细信息，请参阅[文本格式](supported-file-formats-and-compression-codecs.md#text-format)、[JSON 格式](supported-file-formats-and-compression-codecs.md#json-format)、[Avro 格式](supported-file-formats-and-compression-codecs.md#avro-format)、[ORC 格式](supported-file-formats-and-compression-codecs.md#orc-format)和 [Parquet 格式](supported-file-formats-and-compression-codecs.md#parquet-format)部分。 |否（仅适用于二进制复制方案） |
| compression | 指定数据的压缩类型和级别。 有关详细信息，请参阅[受支持的文件格式和压缩编解码器](supported-file-formats-and-compression-codecs.md#compression-support)。<br/>支持的类型为 **GZip**、**Deflate**、**BZip2** 和 **ZipDeflate**。<br/>支持的级别为“最佳”和“最快”。 |否 |

>[!TIP]
>如需复制文件夹下的所有文件，请仅指定 **folderPath**。<br>如需复制具有给定名称的单个文件，请指定文件夹部分的 **folderPath** 和文件名部分的 **fileName**。<br>如需复制文件夹下的文件子集，请指定文件夹部分的 **folderPath** 和通配符筛选器部分的 **fileName**。 

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

要完整了解可供活动定义使用的各个部分和属性，请参阅[复制活动配置](copy-activity-overview.md#configuration)和[管道和活动](concepts-pipelines-activities.md)。 本部分列出了 Data Lake Storage Gen2 源和接收器支持的属性。

### <a name="azure-data-lake-storage-gen2-as-a-source-type"></a>Azure Data Lake Storage Gen2 作为源类型

- 若要从**Parquet 复制、分隔文本、json、avro 和二进制格式**，请参阅[Parquet，分隔文本、json、avro 和二进制格式源](#format-based-source)部分。
- 若要从其他格式（如**ORC 格式**）进行复制，请参阅[其他格式源](#other-format-source)部分。

#### <a name="format-based-source"></a>Parquet，分隔文本、JSON、Avro 和二进制格式源

若要从 Parquet 复制数据 **，分隔文本、JSON、avro 和二进制格式**，请参考基于格式的复制活动源和受支持的[Parquet 格式](format-parquet.md)、[分隔文本格式](format-delimited-text.md)、 [avro 格式](format-avro.md)和[二进制格式](format-binary.md)一文设置。 基于格式的复制源中的 `storeSettings` 设置下的 Data Lake Storage Gen2 支持以下属性：

| 属性                 | 说明                                                  | 必选                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | `storeSettings` 下的 type 属性必须设置为 **AzureBlobFSReadSetting**。 | 是                                           |
| recursive                | 指示是要从子文件夹中以递归方式读取数据，还是只从指定的文件夹中读取数据。 当 recursive 设置为 true 且接收器是基于文件的存储时，将不会在接收器上复制或创建空的文件夹或子文件夹。 允许的值为 **true**（默认值）和 **false**。 | 否                                            |
| wildcardFolderPath       | 数据集中配置的给定文件系统下包含通配符的文件夹路径，用于筛选源文件夹。 <br>允许的通配符为：`*`（匹配零个或更多字符）和 `?`（匹配零个或单个字符）。 如果实际文件夹名内具有通配符或此转义符，请使用 `^` 进行转义。 <br>请参阅[文件夹和文件筛选器示例](#folder-and-file-filter-examples)中的更多示例。 | 否                                            |
| wildcardFileName         | 给定的文件系统 + folderPath/wildcardFolderPath 下包含通配符的文件名，用于筛选源文件。 <br>允许的通配符为：`*`（匹配零个或更多字符）和 `?`（匹配零个或单个字符）。 如果实际文件夹名内具有通配符或此转义符，请使用 `^` 进行转义。 请参阅[文件夹和文件筛选器示例](#folder-and-file-filter-examples)中的更多示例。 | 如果数据集中未指定 `fileName`，则为“是” |
| modifiedDatetimeStart    | 基于属性“上次修改时间”的文件筛选器。 如果文件的上次修改时间在 `modifiedDatetimeStart` 和 `modifiedDatetimeEnd` 之间的时间范围内，则将选中这些文件。 该时间应用于 UTC 时区，格式为“2018-12-01T05:00:00Z”。 <br> 属性可以为 NULL，这意味着不向数据集应用任何文件特性筛选器。 如果 `modifiedDatetimeStart` 具有日期/时间值，但 `modifiedDatetimeEnd` 为 NULL，则意味着将选中“上次修改时间”属性大于或等于该日期/时间值的文件。 如果 `modifiedDatetimeEnd` 具有日期/时间值，但 `modifiedDatetimeStart` 为 NULL，则意味着将选中“上次修改时间”属性小于该日期/时间值的文件。 | 否                                            |
| modifiedDatetimeEnd      | 同上。                                               | 否                                            |
| maxConcurrentConnections | 可以同时连接到存储库的连接数。 仅在要限制与数据存储的并发连接时指定。 | 否                                            |

> [!NOTE]
> 对于 Parquet 或带分隔符的文本格式，仍然按原样支持下一部分中提到的 **AzureBlobFSSource** 类型复制活动源，以实现向后兼容性。 我们建议今后使用新的模型。 数据工厂创作 UI 会生成这些新类型。

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

若要从**ORC 格式**的 Data Lake Storage Gen2 中复制数据，复制活动**源**部分支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动源的 type 属性必须设置为 AzureBlobFSSource。 |是 |
| recursive | 指示是要从子文件夹中以递归方式读取数据，还是只从指定的文件夹中读取数据。 当 recursive 设置为 true 且接收器是基于文件的存储时，将不会在接收器上复制或创建空的文件夹或子文件夹。<br/>允许的值为 **true**（默认值）和 **false**。 | 否 |
| maxConcurrentConnections | 可以同时连接到数据存储的连接数。 仅在要限制与数据存储的并发连接时指定。 | 否 |

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

- 若要复制到**Parquet （分隔文本、json、avro 和二进制格式**），请参阅[Parquet，分隔文本、json、avro 和二进制格式接收器](#format-based-sink)部分。
- 若要复制到其他格式（如**ORC/JSON 格式**），请参阅[其他格式接收器](#other-format-sink)部分。

#### <a name="format-based-sink"></a>Parquet，分隔文本、JSON、Avro 和二进制格式接收器

若要将数据复制到**Parquet （分隔文本、JSON、Avro 和二进制格式**），请参阅[Parquet 格式](format-parquet.md)、[分隔文本格式](format-delimited-text.md)、 [avro 格式](format-avro.md)和基于格式的复制活动接收器上的[二进制格式](format-binary.md)文章和支持设置。 基于格式的复制接收器中的 `storeSettings` 设置下的 Data Lake Storage Gen2 支持以下属性：

| 属性                 | 说明                                                  | 必选 |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | `storeSettings` 下的 type 属性必须设置为 **AzureBlobFSWriteSetting**。 | 是      |
| copyBehavior             | 定义以基于文件的数据存储中的文件为源时的复制行为。<br/><br/>允许值包括：<br/><b>- PreserveHierarchy（默认）</b>：将文件层次结构保留到目标文件夹中。 指向源文件夹的源文件相对路径与指向目标文件夹的目标文件相对路径相同。<br/><b>- FlattenHierarchy</b>：源文件夹中的所有文件都位于目标文件夹的第一级中。 目标文件具有自动生成的名称。 <br/><b>- MergeFiles</b>：将源文件夹中的所有文件合并到一个文件中。 如果指定了文件名，则合并文件的名称为指定名称。 否则，它是自动生成的文件名。 | 否       |
| maxConcurrentConnections | 可以同时连接到数据存储的连接数。 仅在要限制与数据存储的并发连接时指定。 | 否       |

> [!NOTE]
> 对于 Parquet 或带分隔符的文本格式，仍然按原样支持下一部分中提到的 **AzureBlobFSSink** 类型复制活动接收器，以实现向后兼容性。 我们建议今后使用新的模型。 数据工厂创作 UI 会生成这些新类型。

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

若要将数据复制到**ORC 格式**的 Data Lake Storage Gen2， **sink**部分支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动接收器的 type 属性必须设置为 AzureBlobFSSink。 |是 |
| copyBehavior | 定义以基于文件的数据存储中的文件为源时的复制行为。<br/><br/>允许值包括：<br/><b>- PreserveHierarchy（默认）</b>：将文件层次结构保留到目标文件夹中。 指向源文件夹的源文件相对路径与指向目标文件夹的目标文件相对路径相同。<br/><b>- FlattenHierarchy</b>：源文件夹中的所有文件都位于目标文件夹的第一级中。 目标文件具有自动生成的名称。 <br/><b>- MergeFiles</b>：将源文件夹中的所有文件合并到一个文件中。 如果指定了文件名，则合并文件的名称为指定名称。 否则，它是自动生成的文件名。 | 否 |
| maxConcurrentConnections | 可以同时连接到数据存储的连接数。 仅在要限制与数据存储的并发连接时指定。 | 否 |

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
| `Folder*` | （为空，使用默认值） | 假 | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | （为空，使用默认值） | 真 | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | 假 | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | 真 | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="some-recursive-and-copybehavior-examples"></a>一些 recursive 和 copyBehavior 示例

本节介绍了将 recursive 和 copyBehavior 值进行不同组合所产生的复制操作行为。

| recursive | copyBehavior | 源文件夹结构 | 生成目标 |
|:--- |:--- |:--- |:--- |
| 真 |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用与源相同的结构创建目标 Folder1：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| 真 |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用以下结构创建目标 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 的自动生成的名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2 的自动生成的名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File3 的自动生成的名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File4 的自动生成的名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File5 的自动生成的名称 |
| 真 |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用以下结构创建目标 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File5 的内容将合并到一个文件中，且自动生成文件名。 |
| 假 |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用以下结构创建目标 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>不会选取带有 File3、File4 和 File5 的 Subfolder1。 |
| 假 |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用以下结构创建目标 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 的自动生成的名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2 的自动生成的名称<br/><br/>不会选取带有 File3、File4 和 File5 的 Subfolder1。 |
| 假 |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用以下结构创建目标 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 的内容将合并到一个文件中，且自动生成文件名。 File1 的自动生成的名称<br/><br/>不会选取带有 File3、File4 和 File5 的 Subfolder1。 |

## <a name="preserve-acls-from-data-lake-storage-gen1"></a>保留 Data Lake Storage Gen1 中的 Acl

>[!TIP]
>若要将数据从 Azure Data Lake Storage Gen1 复制到一般的 Gen2 中，请参阅[使用 Azure 数据工厂将 Azure Data Lake Storage Gen1 数据复制到 Gen2](load-azure-data-lake-storage-gen2-from-gen1.md)和最佳实践。

将 Azure Data Lake Storage Gen1 中的文件复制到 Gen2 时，可以选择保留 POSIX 访问控制列表（Acl）和数据。 有关访问控制的详细信息，请参阅 Azure Data Lake Storage Gen2 中 Azure Data Lake Storage Gen1 和[access control](../storage/blobs/data-lake-storage-access-control.md)中的[访问控制](../data-lake-store/data-lake-store-access-control.md)。

可以使用 Azure 数据工厂复制活动保存以下类型的 Acl。 您可以选择一个或多个类型：

- **ACL**：复制并保留对文件和目录的 POSIX 访问控制列表。 它将完整的现有 Acl 从源复制到接收器。 
- **所有者**：复制并保留文件和目录的拥有用户。 需要超级用户访问接收器 Data Lake Storage Gen2。
- **组**：复制并保留所属的文件和目录组。 需要超级用户对接收器 Data Lake Storage Gen2 或拥有用户的访问权限（如果拥有用户也是目标组的成员）。

如果指定从文件夹复制，则如果`recursive`将设置为 true，则数据工厂将复制该给定文件夹及其下的文件和目录的 acl。 如果指定从单个文件复制，则会复制该文件上的 Acl。

>[!IMPORTANT]
>选择保留 Acl 时，请确保为数据工厂授予足够高的权限，以便在接收器 Data Lake Storage Gen2 帐户上运行。 例如，使用帐户密钥身份验证，或将存储 Blob 数据所有者角色分配给服务主体或托管标识。

将 source 作为 Data Lake Storage Gen1 配置为具有二进制复制选项或二进制格式，并将接收器配置为带有二进制复制选项或二进制格式的 Data Lake Storage Gen2 时，可以在 "**复制数据工具设置** **" 页**上或在用于活动创作的**复制活动** > **设置**选项卡。

![Data Lake Storage Gen1 Gen2 保留 ACL](./media/connector-azure-data-lake-storage/adls-gen2-preserve-acl.png)

下面是 JSON 配置的示例（请参阅`preserve`）： 

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

在映射数据流功能中了解有关[源转换](data-flow-source.md)和[接收器转换](data-flow-sink.md)的详细信息。

## <a name="lookup-activity-properties"></a>查找活动属性

若要了解有关属性的详细信息，请检查[查找活动](control-flow-lookup-activity.md)。

## <a name="getmetadata-activity-properties"></a>GetMetadata 活动属性

若要了解有关属性的详细信息，请查看[GetMetadata 活动](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>删除活动属性

若要了解有关属性的详细信息，请选中 "[删除活动](delete-activity.md)"
## <a name="next-steps"></a>后续步骤

有关数据工厂中复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md##supported-data-stores-and-formats)。
