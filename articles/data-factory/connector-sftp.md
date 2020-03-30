---
title: 从/向 SFTP 服务器复制数据
description: 了解如何使用 Azure 数据工厂从/向 SFTP 服务器复制数据。
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/02/2020
ms.openlocfilehash: 06428d4a9c4a4178212d16d42b8b3adffb5c9718
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78250291"
---
# <a name="copy-data-from-and-to-sftp-server-using-azure-data-factory"></a>使用 Azure 数据工厂从/向 SFTP 服务器复制数据

> [!div class="op_single_selector" title1="选择所使用的数据工厂服务版本："]
> * [版本 1](v1/data-factory-sftp-connector.md)
> * [当前版本](connector-sftp.md)

本文概述了如何从/向 SFTP 服务器复制数据。 若要了解 Azure 数据工厂，请阅读[介绍性文章](introduction.md)。

## <a name="supported-capabilities"></a>支持的功能

以下活动支持此 SFTP 连接器：

- 带有[支持的源或接收器矩阵](copy-activity-overview.md)的[复制活动](copy-activity-overview.md)
- [查找活动](control-flow-lookup-activity.md)
- [获取元数据活动](control-flow-get-metadata-activity.md)
- [删除活动](delete-activity.md)

具体而言，此 SFTP 连接器支持：

- 使用**基本**或 **SshPublicKey** 身份验证从/向 SFTP 复制文件。
- 按现在的形式复制文件或使用[受支持的文件格式和压缩编解码器](supported-file-formats-and-compression-codecs.md)分析/生成文件。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

对于特定于 SFTP 的数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

SFTP 链接的服务支持以下属性：

| properties | 描述 | 必选 |
|:--- |:--- |:--- |
| type | type 属性必须设置为：**Sftp**。 |是 |
| host | SFTP 服务器的名称或 IP 地址。 |是 |
| port | SFTP 服务器侦听的端口。<br/>允许的值是：整数，默认值是 22****。 |否 |
| skipHostKeyValidation | 指定是否要跳过主机密钥验证。<br/>允许的值为：true、false（默认）********。  | 否 |
| hostKeyFingerprint | 指定主机密钥的指纹。 | 是（如果“skipHostKeyValidation”设置为 false）。  |
| authenticationType | 指定身份验证类型。<br/>允许的值为：Basic、SshPublicKey********。 有关其他属性和 JSON 示例，请分别参阅[使用基本身份验证](#using-basic-authentication)和[使用 SSH 公钥身份验证](#using-ssh-public-key-authentication)部分。 |是 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 从[先决条件](#prerequisites)部分了解更多信息。 如果未指定，则使用默认 Azure Integration Runtime。 |否 |

### <a name="using-basic-authentication"></a>使用基本身份验证

要使用基本身份验证，请将“authenticationType”属性设置为“基本”****，并指定除上一部分所述 SFTP 连接器泛型属性以外的下列属性：

| properties | 描述 | 必选 |
|:--- |:--- |:--- |
| userName | 有权访问 SFTP 服务器的用户。 |是 |
| password | 用户 (userName) 的密码。 将此字段标记为 SecureString 以安全地将其存储在数据工厂中或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 | 是 |

**例子：**

```json
{
    "name": "SftpLinkedService",
    "type": "linkedservices",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "authenticationType": "Basic",
            "userName": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="using-ssh-public-key-authentication"></a>使用 SSH 公钥身份验证

要使用 SSH 公钥身份验证，请将“authenticationType”属性设置为“SshPublicKey”****，并指定除上一部分所述 SFTP 连接器泛型属性以外的下列属性：

| properties | 描述 | 必选 |
|:--- |:--- |:--- |
| userName | 有权访问 SFTP 服务器的用户 |是 |
| privateKeyPath | 指定集成运行时可以访问的私钥文件的绝对路径。 仅当在“connectVia”中指定自承载类型的集成运行时时适用。 | 指定 `privateKeyPath` 或 `privateKeyContent`。  |
| privateKeyContent | Base64 编码的 SSH 私钥内容。 SSH 私钥应采用 OpenSSH 格式。 将此字段标记为 SecureString 以安全地将其存储在数据工厂中或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 | 指定 `privateKeyPath` 或 `privateKeyContent`。 |
| passPhrase | 如果密钥文件受通行短语保护，请指定用于解密私钥的通行短语/密码。 将此字段标记为 SecureString 以安全地将其存储在数据工厂中或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 | 如果私钥文件受通行短语的保护，则为 Yes。 |

> [!NOTE]
> SFTP 连接器支持 RSA/DSA OpenSSH 密钥。 请确保密钥文件内容以“-----BEGIN [RSA/DSA] PRIVATE KEY-----”开头。 如果私钥文件是 ppk 格式文件，请使用 Putty 工具从 .ppk 转换为 OpenSSH 格式。 

**示例 1：使用私钥 filePath 进行 SshPublicKey 身份验证**

```json
{
    "name": "SftpLinkedService",
    "type": "Linkedservices",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": true,
            "authenticationType": "SshPublicKey",
            "userName": "xxx",
            "privateKeyPath": "D:\\privatekey_openssh",
            "passPhrase": {
                "type": "SecureString",
                "value": "<pass phrase>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**示例 2：使用私钥内容进行 SshPublicKey 身份验证**

```json
{
    "name": "SftpLinkedService",
    "type": "Linkedservices",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": true,
            "authenticationType": "SshPublicKey",
            "userName": "<username>",
            "privateKeyContent": {
                "type": "SecureString",
                "value": "<base64 string of the private key content>"
            },
            "passPhrase": {
                "type": "SecureString",
                "value": "<pass phrase>"
            }
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

基于格式的数据集中 `location` 设置下的 SFTP 支持以下属性：

| properties   | 描述                                                  | 必选 |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | 数据集中 `location` 下的 type 属性必须设置为 **SftpLocation**。 | 是      |
| folderPath | 文件夹的路径。 如果要使用通配符筛选文件夹，请跳过此设置并在活动源设置中指定。 | 否       |
| fileName   | 给定 folderPath 下的文件名。 如果要使用通配符筛选文件，请跳过此设置并在活动源设置中指定。 | 否       |

**例子：**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<SFTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "SftpLocation",
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

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 SFTP 源支持的属性列表。

### <a name="sftp-as-source"></a>以 SFTP 作为源

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

基于格式的复制源中 `storeSettings` 设置下的 SFTP 支持以下属性：

| properties                 | 描述                                                  | 必选                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | 下`storeSettings`的类型属性必须设置为**SftpReadSettings**。 | 是                                           |
| recursive                | 指示是要从子文件夹中以递归方式读取数据，还是只从指定的文件夹中读取数据。 请注意，当 recursive 设置为 true 且接收器是基于文件的存储时，将不会在接收器上复制或创建空的文件夹或子文件夹。 允许的值**为 true（** 默认值）和**false**。 | 否                                            |
| wildcardFolderPath       | 带有通配符的文件夹路径，用于筛选源文件夹。 <br>允许的通配符为：`*`（匹配零个或更多个字符）和 `?`（匹配零个或单个字符）；如果实际文件夹名称中包含通配符或此转义字符，请使用 `^` 进行转义。 <br>请参阅[文件夹和文件筛选器示例](#folder-and-file-filter-examples)中的更多示例。 | 否                                            |
| wildcardFileName         | 给定的 folderPath/wildcardFolderPath 下带有通配符的文件名，用于筛选源文件。 <br>允许的通配符为：`*`（匹配零个或更多个字符）和 `?`（匹配零个或单个字符）；如果实际文件夹名称中包含通配符或此转义字符，请使用 `^` 进行转义。  请参阅[文件夹和文件筛选器示例](#folder-and-file-filter-examples)中的更多示例。 | 如果数据集中未指定 `fileName`，则为“是” |
| modifiedDatetimeStart    | 基于属性的文件筛选器：上次修改。 如果文件的上次修改时间在 `modifiedDatetimeStart` 和 `modifiedDatetimeEnd` 之间的时间范围内，则将选中这些文件。 该时间应用于 UTC 时区，格式为“2018-12-01T05:00:00Z”。 <br> 属性可以为 NULL，这意味着不向数据集应用任何文件特性筛选器。  如果 `modifiedDatetimeStart` 具有日期/时间值，但 `modifiedDatetimeEnd` 为 NULL，则意味着将选中“上次修改时间”属性大于或等于该日期/时间值的文件。  如果 `modifiedDatetimeEnd` 具有日期/时间值，但 `modifiedDatetimeStart` 为 NULL，则意味着将选中“上次修改时间”属性小于该日期/时间值的文件。 | 否                                            |
| modifiedDatetimeEnd      | 同上。                                               | 否                                            |
| maxConcurrentConnections | 可以同时连接到存储库的连接数。 仅在要限制与数据存储的并发连接时指定。 | 否                                            |

**例子：**

```json
"activities":[
    {
        "name": "CopyFromSFTP",
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
                    "type": "SftpReadSettings",
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

### <a name="sftp-as-sink"></a>SFTP 作为接收器

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

在基于格式的复制接收器的 `storeSettings` 设置下，SFTP 支持以下属性：

| properties                 | 描述                                                  | 必选 |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | 下`storeSettings`的类型属性必须设置为**SftpWriteSettings**。 | 是      |
| copyBehavior             | 定义以基于文件的数据存储中的文件为源时的复制行为。<br/><br/>允许值包括：<br/><b>- PreserveHierarchy（默认值）</b>：保留目标文件夹中的文件层次结构。 指向源文件夹的源文件相对路径与指向目标文件夹的目标文件相对路径相同。<br/><b>- FlattenHierarchy</b>：源文件夹中的所有文件都位于目标文件夹的第一级。 目标文件具有自动生成的名称。 <br/><b>- MergeFiles</b>：将源文件夹中的所有文件合并到一个文件中。 如果指定了文件名，则合并文件的名称为指定名称。 否则，它是自动生成的文件名。 | 否       |
| maxConcurrentConnections | 可以同时连接到数据存储的连接数。 仅在要限制与数据存储的并发连接时指定。 | 否       |
| 使用TempFile重命名 | 指示是上载到临时文件并重命名，还是直接写入目标文件夹/文件位置。 默认情况下，ADF 首先写入临时文件，然后在上载完成后进行文件重命名，以便 1） 避免冲突写入导致损坏的文件，如果您有其他进程写入同一文件，和 2） 确保文件的原始版本存在期间整个转移。 如果您的 SFTP 服务器不支持重命名操作，请禁用此选项并确保没有并发写入目标文件。 请参阅下表下方的故障排除提示。 | 不是。 默认值为 true。 |
| operationTimeout | 每个写入请求到 SFTP 服务器前的等待时间超时。默认值为 60 分钟（01：00：00）。|否 |

>[!TIP]
>如果在将数据写入 SFTP 时遇到"用户错误SftpPathNotfound"、"用户错误"或"Sftp操作失败"的错误，并且您使用的 SFTP 用户确实具有适当的权限，请检查您的 SFTP 服务器是否支持文件重命名操作 - 如果没有，请禁用"使用临时文件上载"（）`useTempFileRename`选项，然后重试。 从上表中了解有关此属性的更多内容。 如果使用自托管集成运行时进行复制，请确保使用版本 4.6 或以上。

**例子：**

```json
"activities":[
    {
        "name": "CopyToSFTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
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
                "type": "<source type>"
            },
            "sink": {
                "type": "BinarySink",
                "storeSettings":{
                    "type": "SftpWriteSettings",
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
| `Folder*` | （为空，使用默认值） | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | （为空，使用默认值） | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**文件3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**文件3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

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

| properties | 描述 | 必选 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为：FileShare**** |是 |
| folderPath | 文件夹路径。 支持通配符筛选器，允许的通配符为：`*`（匹配零个或更多个字符）和 `?`（匹配零个或单个字符）；如果实际文件名中包含通配符或此转义字符，请使用 `^` 进行转义。 <br/><br/>示例：“rootfolder/subfolder/”，请参阅[文件夹和文件筛选器示例](#folder-and-file-filter-examples)中的更多示例。 |是 |
| fileName |  指定“folderPath”下的文件的“名称或通配符筛选器”****。 如果没有为此属性指定任何值，则数据集会指向文件夹中的所有文件。 <br/><br/>对于筛选器，允许的通配符为：`*`（匹配零个或更多字符）和 `?`（匹配零个或单个字符）。<br/>- 示例 1：`"fileName": "*.csv"`<br/>- 示例 2：`"fileName": "???20180427.txt"`<br/>如果实际文件夹名内具有通配符或此转义符，请使用 `^` 进行转义。 |否 |
| modifiedDatetimeStart | 基于属性的文件筛选器：上次修改。 如果文件的上次修改时间在 `modifiedDatetimeStart` 和 `modifiedDatetimeEnd` 之间的时间范围内，则将选中这些文件。 该时间应用于 UTC 时区，格式为“2018-12-01T05:00:00Z”。 <br/><br/> 请注意，当你要从大量文件中进行文件筛选时，启用此设置将影响数据移动的整体性能。 <br/><br/> 属性可以为 NULL，这意味着不向数据集应用任何文件属性筛选器。  如果 `modifiedDatetimeStart` 具有日期/时间值，但 `modifiedDatetimeEnd` 为 NULL，则意味着将选中“上次修改时间”属性大于或等于该日期/时间值的文件。  如果 `modifiedDatetimeEnd` 具有日期/时间值，但 `modifiedDatetimeStart` 为 NULL，则意味着将选中“上次修改时间”属性小于该日期/时间值的文件。| 否 |
| modifiedDatetimeEnd | 基于属性的文件筛选器：上次修改。 如果文件的上次修改时间在 `modifiedDatetimeStart` 和 `modifiedDatetimeEnd` 之间的时间范围内，则将选中这些文件。 该时间应用于 UTC 时区，格式为“2018-12-01T05:00:00Z”。 <br/><br/> 请注意，当你要从大量文件中进行文件筛选时，启用此设置将影响数据移动的整体性能。 <br/><br/> 属性可以为 NULL，这意味着不向数据集应用任何文件属性筛选器。  如果 `modifiedDatetimeStart` 具有日期/时间值，但 `modifiedDatetimeEnd` 为 NULL，则意味着将选中“上次修改时间”属性大于或等于该日期/时间值的文件。  如果 `modifiedDatetimeEnd` 具有日期/时间值，但 `modifiedDatetimeStart` 为 NULL，则意味着将选中“上次修改时间”属性小于该日期/时间值的文件。| 否 |
| format | 如果想要在基于文件的存储之间按原样复制文件****（二进制副本），可以在输入和输出数据集定义中跳过格式节。<br/><br/>如果需要分析具有特定格式的文件，支持分析以下文件格式类型：TextFormat、JsonFormat、AvroFormat、OrcFormat 和 ParquetFormat********************。 请将格式中的“type”属性设置为上述值之一****。 有关详细信息，请参阅[文本格式](supported-file-formats-and-compression-codecs-legacy.md#text-format)、[Json 格式](supported-file-formats-and-compression-codecs-legacy.md#json-format)、[Avro 格式](supported-file-formats-and-compression-codecs-legacy.md#avro-format)、[Orc 格式](supported-file-formats-and-compression-codecs-legacy.md#orc-format)和 [Parquet 格式](supported-file-formats-and-compression-codecs-legacy.md#parquet-format)部分。 |否（仅适用于二进制复制方案） |
| compression | 指定数据的压缩类型和级别。 有关详细信息，请参阅[受支持的文件格式和压缩编解码器](supported-file-formats-and-compression-codecs-legacy.md#compression-support)。<br/>支持的类型为：GZip、Deflate、BZip2 和 ZipDeflate****************。<br/>支持的级别为：最佳和最快********。 |否 |

>[!TIP]
>如需复制文件夹下的所有文件，请仅指定 **folderPath**。<br>如需复制具有给定名称的单个文件，请指定文件夹部分的 **folderPath** 和文件名部分的 **fileName**。<br>如需复制文件夹下的文件子集，请指定文件夹部分的 **folderPath** 和通配符筛选器部分的 **fileName**。

>[!NOTE]
>如果文件筛选器使用“fileFilter”属性，则在建议你今后使用添加到“fileName”的新筛选器功能时，仍按原样支持该属性。

**例子：**

```json
{
    "name": "SFTPDataset",
    "type": "Datasets",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<SFTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "folder/subfolder/",
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

| properties | 描述 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动源的 type 属性必须设置为：FileSystemSource**** |是 |
| recursive | 指示是要从子文件夹中以递归方式读取数据，还是只从指定的文件夹中读取数据。 当 recursive 设置为 true 且接收器是基于文件的存储时，将不会在接收器上复制/创建空的文件夹/子文件夹。<br/>允许的值是 **：true（** 默认 **），false** | 否 |
| maxConcurrentConnections | 可以同时连接到存储库的连接数。 仅在要限制与数据存储的并发连接时指定。 | 否 |

**例子：**

```json
"activities":[
    {
        "name": "CopyFromSFTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SFTP input dataset name>",
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
                "type": "FileSystemSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>后续步骤
有关 Azure 数据工厂中复制活动作为源和接收器支持的数据存储的列表，请参阅[受支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。
