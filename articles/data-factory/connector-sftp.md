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
ms.date: 08/18/2020
ms.openlocfilehash: 8ec950ddabd3844618c878471d2e1391979e2056
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2020
ms.locfileid: "88521365"
---
# <a name="copy-data-from-and-to-the-sftp-server-by-using-azure-data-factory"></a>使用 Azure 数据工厂从/向 SFTP 服务器复制数据

> [!div class="op_single_selector" title1="选择要使用的数据工厂服务的版本："]
> * [版本 1](v1/data-factory-sftp-connector.md)
> * [当前版本](connector-sftp.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述了如何从/向安全 FTP (SFTP) 服务器复制数据。 若要了解 Azure 数据工厂，请阅读[介绍性文章](introduction.md)。

## <a name="supported-capabilities"></a>支持的功能

以下活动支持 SFTP 连接器：

- 带有[支持的源或接收器矩阵](copy-activity-overview.md)的[复制活动](copy-activity-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)
- [GetMetadata 活动](control-flow-get-metadata-activity.md)
- [Delete 活动](delete-activity.md)

具体而言，SFTP 连接器支持：

- 使用基本身份验证或 SshPublicKey 身份验证从/向 SFTP 服务器复制文件。
- 按原样复制文件，或者通过使用[支持的文件格式和压缩编解码器](supported-file-formats-and-compression-codecs.md)分析或生成文件来复制文件。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

对于特定于 SFTP 的数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

SFTP 链接服务支持以下属性：

| 属性 | 描述 | 必需 |
|:--- |:--- |:--- |
| type | type 属性必须设置为 Sftp。 |是 |
| host | SFTP 服务器的名称或 IP 地址。 |是 |
| port | SFTP 服务器侦听的端口。<br/>允许的值为整数，默认值为 22。 |否 |
| skipHostKeyValidation | 指定是否要跳过主机密钥验证。<br/>允许的值为 *true* 和 *true*（默认值）。  | 否 |
| hostKeyFingerprint | 指定主机密钥的指纹。 | 是（如果“skipHostKeyValidation”设置为 false）。  |
| authenticationType | 指定身份验证类型。<br/>允许的值为 Basic 和 SshPublicKey 。 有关更多属性，请参阅[使用基本身份验证](#use-basic-authentication)部分。 有关 JSON 示例，请参阅[使用 SSH 公钥身份验证](#use-ssh-public-key-authentication)部分。 |是 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 若要了解详细信息，请参阅[先决条件](#prerequisites)部分。 如果未指定集成运行时，服务会使用默认的 Azure Integration Runtime。 |否 |

### <a name="use-basic-authentication"></a>使用基本身份验证

若要使用基本身份验证，请将“authenticationType”属性设置为“Basic” ，并指定下列属性以及上一部分介绍的 SFTP 连接器泛型属性：

| 属性 | 描述 | 必须 |
|:--- |:--- |:--- |
| userName | 有权访问 SFTP 服务器的用户。 |是 |
| password | 用户 (userName) 的密码。 将此字段标记为 SecureString 以安全地将其存储在数据工厂中，或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 | 是 |

**示例：**

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
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="use-ssh-public-key-authentication"></a>使用 SSH 公钥身份验证

要使用 SSH 公钥身份验证，请将“authenticationType”属性设置为“SshPublicKey”，并指定除上一部分所述 SFTP 连接器泛型属性以外的下列属性：

| 属性 | 描述 | 必须 |
|:--- |:--- |:--- |
| userName | 有权访问 SFTP 服务器的用户。 |是 |
| privateKeyPath | 指定集成运行时可以访问的私钥文件的绝对路径。 只有在“connectVia”中指定了自承载类型的集成运行时的情况下，此项才适用。 | 指定 `privateKeyPath` 或 `privateKeyContent`。  |
| privateKeyContent | Base64 编码的 SSH 私钥内容。 SSH 私钥应采用 OpenSSH 格式。 将此字段标记为 SecureString 以安全地将其存储在数据工厂中，或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 | 指定 `privateKeyPath` 或 `privateKeyContent`。 |
| passPhrase | 如果密钥文件受通行短语保护，请指定用于解密私钥的通行短语或密码。 将此字段标记为 SecureString 以安全地将其存储在数据工厂中，或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 | 是（如果私钥文件受通行短语的保护）。 |

> [!NOTE]
> SFTP 连接器支持 RSA/DSA OpenSSH 密钥。 请确保密钥文件内容以“-----BEGIN [RSA/DSA] PRIVATE KEY-----”开头。 如果私钥文件是 PPK 格式的文件，请使用 PuTTY 工具将其从 PPK 格式转换为 OpenSSH 格式。 

**示例 1：使用私钥文件路径进行 SshPublicKey 身份验证**

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
            "referenceName": "<name of integration runtime>",
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
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

SFTP 支持基于格式的数据集中 `location` 设置下的以下属性：

| 属性   | 描述                                                  | 必需 |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | 数据集中 `location` 下的 type 属性必须设置为 SftpLocation。 | 是      |
| folderPath | 文件夹的路径。 如果要使用通配符来筛选文件夹，请跳过此设置并在活动源设置中指定路径。 | 否       |
| fileName   | 指定的 folderPath 下的文件名。 如果要使用通配符来筛选文件，请跳过此设置并在活动源设置中指定文件名。 | 否       |

**示例：**

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

SFTP 支持基于格式的复制源中 `storeSettings` 设置下的以下属性：

| 属性                 | 描述                                                  | 必需                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | `storeSettings` 下的 type 属性必须设置为 SftpReadSettings。 | 是                                           |
| 找到要复制的文件 |  |  |
| 选项 1：静态路径<br> | 从数据集中指定的文件夹/文件路径复制。 若要复制文件夹中的所有文件，请另外将 `wildcardFileName` 指定为 `*`。 |  |
| 选项 2：通配符<br>- wildcardFolderPath | 带有通配符的文件夹路径，用于筛选源文件夹。 <br>允许的通配符为 `*`（匹配零个或零个以上的字符）和 `?`（匹配零个或单个字符）；如果实际文件夹名称中包含通配符或此转义字符，请使用 `^` 进行转义。 <br>如需更多示例，请参阅[文件夹和文件筛选器示例](#folder-and-file-filter-examples)。 | 否                                            |
| 选项 2：通配符<br>- wildcardFileName | 指定的 folderPath/wildcardFolderPath 下带有通配符的文件名，用于筛选源文件。 <br>允许的通配符为 `*`（匹配零个或零个以上的字符）和 `?`（匹配零个或单个字符）；如果实际文件夹名称中包含通配符或此转义字符，请使用 `^` 进行转义。  如需更多示例，请参阅[文件夹和文件筛选器示例](#folder-and-file-filter-examples)。 | 是 |
| 选项 3：文件列表<br>- fileListPath | 表示要复制指定文件集。 指向一个文本文件，其中包含要复制的文件列表（每行一个文件，带有数据集中所配置路径的相对路径）。<br/>使用此选项时，请不要在数据集中指定文件名。 如需更多示例，请参阅[文件列表示例](#file-list-examples)。 |否 |
| ***其他设置*** |  | |
| recursive | 指示是要从子文件夹中以递归方式读取数据，还是只从指定的文件夹中读取数据。 当 recursive 设置为 true 且接收器是基于文件的存储时，将不会在接收器上复制或创建空的文件夹或子文件夹。 <br>允许的值为 *true*（默认值）和 *false*。<br>如果配置 `fileListPath`，则此属性不适用。 |否 |
| deleteFilesAfterCompletion | 指示是否会在二进制文件成功移到目标存储后将其从源存储中删除。 文件删除按文件进行。因此，当复制活动失败时，你会看到一些文件已经复制到目标并从源中删除，而另一些文件仍保留在源存储中。 <br/>此属性仅在二进制复制方案中有效，其中数据源存储为 Blob、ADLS Gen1、ADLS Gen2、S3、Google 云存储、文件、Azure 文件、SFTP 或 FTP。 默认值：false。 |否 |
| modifiedDatetimeStart    | 文件根据“上次修改时间”属性进行筛选。 <br>如果文件的上次修改时间在 `modifiedDatetimeStart` 到 `modifiedDatetimeEnd` 之间的范围内，则会选中这些文件。 该时间应用于 UTC 时区，格式为“2018-12-01T05:00:00Z”。 <br> 属性可以为 NULL，这意味着不向数据集应用任何文件特性筛选器。  如果 `modifiedDatetimeStart` 具有日期/时间值，但 `modifiedDatetimeEnd` 为 NULL，则意味着将选中“上次修改时间”属性大于或等于该日期/时间值的文件。  如果 `modifiedDatetimeEnd` 具有日期/时间值，但 `modifiedDatetimeStart` 为 NULL，则意味着将选中“上次修改时间”属性小于该日期/时间值的文件。<br/>如果配置 `fileListPath`，则此属性不适用。 | 否                                            |
| modifiedDatetimeEnd      | 同上。                                               | 否                                            |
| maxConcurrentConnections | 可以同时连接到存储区存储的连接数。 仅在要限制与数据存储的并发连接时指定一个值。 | 否                                            |

**示例：**

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

### <a name="sftp-as-a-sink"></a>以 SFTP 作为接收器

[!INCLUDE [data-factory-v2-file-sink-formats](../../includes/data-factory-v2-file-sink-formats.md)]

SFTP 支持基于格式的复制接收器中 `storeSettings` 设置下的以下属性：

| 属性                 | 描述                                                  | 必需 |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | `storeSettings` 下的 type 属性必须设置为 SftpWriteSettings。 | 是      |
| copyBehavior             | 定义以基于文件的数据存储中的文件为源时的复制行为。<br/><br/>允许值包括：<br/><b>- PreserveHierarchy（默认）</b>：将文件层次结构保留到目标文件夹中。 指向源文件夹的源文件相对路径与指向目标文件夹的目标文件相对路径相同。<br/><b>- FlattenHierarchy</b>：源文件夹中的所有文件都位于目标文件夹的第一级中。 目标文件具有自动生成的名称。 <br/><b>- MergeFiles</b>：将源文件夹中的所有文件合并到一个文件中。 如果指定了文件名，则合并文件的名称为指定名称。 否则，它是自动生成的文件名。 | 否       |
| maxConcurrentConnections | 可以同时连接到存储区存储的连接数。 仅在要限制与数据存储的并发连接时指定一个值。 | 否       |
| useTempFileRename | 指示是将其上传到临时文件并重命名，还是将其直接写入到目标文件夹或文件位置。 默认情况下，Azure 数据工厂先将数据写入到临时文件，然后在上传完成时重命名文件。 采取此顺序有助于 (1) 避免可能会导致文件损坏的冲突（如果有其他进程对同一文件进行写入操作）；(2) 在整个传输过程中确保文件的原始版本存在。 如果 SFTP 服务器不支持重命名操作，请禁用此选项，并确保不会对目标文件进行并发写入操作。 有关详细信息，请查看此表末尾的故障排除提示。 | 否。 默认值为 *true*。 |
| operationTimeout | 每个对 SFTP 服务器的写入请求超时之前的等待时间。默认值为 60 分钟 (01:00:00)。|否 |

>[!TIP]
>如果将数据写入 SFTP 时遇到“UserErrorSftpPathNotFound”、“UserErrorSftpPermissionDenied”或“SftpOperationFail”错误，并且你使用的 SFTP 用户确实有适当的权限，请查看 SFTP 服务器是否支持文件重命名操作。 如果不支持，请禁用“使用临时文件上传”(`useTempFileRename`) 选项，然后重试。 若要详细了解此属性，请查看上面的表。 如果将自承载集成运行时用于执行复制活动，请确保使用 4.6 或更高版本。

**示例：**

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

本部分介绍对文件夹路径和文件名使用通配符筛选器后产生的行为。

| folderPath | fileName | recursive | 源文件夹结构和筛选器结果（用**粗体**表示的文件已检索）|
|:--- |:--- |:--- |:--- |
| `Folder*` | （为空，使用默认值） | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | （为空，使用默认值） | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>文件列表示例

此表介绍了在复制活动源中使用文件列表路径时产生的行为。 假设有以下源文件夹结构，并且要复制粗体类型的文件：

| 示例源结构                                      | FileListToCopy.txt 中的内容                             | Azure 数据工厂配置                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| root<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;元数据<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **在数据集中：**<br>- 文件夹路径：`root/FolderA`<br><br>**在复制活动源中：**<br>- 文件列表路径：`root/Metadata/FileListToCopy.txt` <br><br>文件列表路径指向同一数据存储中的一个文本文件，该文件包含要复制的文件列表（每行一个文件，带有数据集中所配置路径的相对路径）。 |

## <a name="lookup-activity-properties"></a>查找活动属性

有关查找活动属性的信息，请参阅 [Azure 数据工厂中的查找活动](control-flow-lookup-activity.md)。

## <a name="getmetadata-activity-properties"></a>GetMetadata 活动属性

有关 GetMetadata 活动属性的信息，请参阅 [Azure 数据工厂中的 GetMetadata 活动](control-flow-get-metadata-activity.md)。 

## <a name="delete-activity-properties"></a>Delete 活动属性

有关删除活动属性的信息，请参阅 [Azure 数据工厂中的删除活动](delete-activity.md)。

## <a name="legacy-models"></a>旧模型

>[!NOTE]
>仍会按原样支持以下模型，以实现后向兼容性。 建议你使用前面讨论的新模型，因为 Azure 数据工厂创作 UI 已切换到生成新模型。

### <a name="legacy-dataset-model"></a>旧数据集模型

| 属性 | 描述 | 必需 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为 FileShare。 |是 |
| folderPath | 文件夹的路径。 支持通配符筛选器。 允许的通配符为 `*`（匹配零个或零个以上的字符）和 `?`（匹配零个或单个字符）；如果实际文件名称中包含通配符或此转义字符，请使用 `^` 进行转义。 <br/><br/>示例：“rootfolder/subfolder/”，请参阅[文件夹和文件筛选器示例](#folder-and-file-filter-examples)中的更多示例。 |是 |
| fileName |  指定的“folderPath”下的文件的“名称或通配符筛选器”。 如果没有为此属性指定任何值，则数据集会指向文件夹中的所有文件。 <br/><br/>对于筛选器，允许的通配符为 `*`（匹配零个或零个以上的字符）和 `?`（匹配零个或单个字符）。<br/>- 示例 1：`"fileName": "*.csv"`<br/>- 示例 2：`"fileName": "???20180427.txt"`<br/>如果实际文件夹名内具有通配符或此转义符，请使用 `^` 进行转义。 |否 |
| modifiedDatetimeStart | 文件根据“上次修改时间”属性进行筛选。 如果文件的上次修改时间在 `modifiedDatetimeStart` 到 `modifiedDatetimeEnd` 之间的范围内，则会选中这些文件。 该时间应用于 UTC 时区，格式为“2018-12-01T05:00:00Z”。 <br/><br/> 当你要对大量文件进行文件筛选时，启用此设置会影响数据移动的整体性能。 <br/><br/> 属性可以为 NULL，这意味着不向数据集应用任何文件特性筛选器。  如果 `modifiedDatetimeStart` 具有日期/时间值，但 `modifiedDatetimeEnd` 为 NULL，则意味着将选中“上次修改时间”属性大于或等于该日期/时间值的文件。  如果 `modifiedDatetimeEnd` 具有日期/时间值，但 `modifiedDatetimeStart` 为 NULL，则意味着将选中“上次修改时间”属性小于该日期/时间值的文件。| 否 |
| modifiedDatetimeEnd | 文件根据“上次修改时间”属性进行筛选。 如果文件的上次修改时间在 `modifiedDatetimeStart` 到 `modifiedDatetimeEnd` 之间的范围内，则会选中这些文件。 该时间应用于 UTC 时区，格式为“2018-12-01T05:00:00Z”。 <br/><br/> 当你要对大量文件进行文件筛选时，启用此设置会影响数据移动的整体性能。 <br/><br/> 属性可以为 NULL，这意味着不向数据集应用任何文件特性筛选器。  如果 `modifiedDatetimeStart` 具有日期/时间值，但 `modifiedDatetimeEnd` 为 NULL，则意味着将选中“上次修改时间”属性大于或等于该日期/时间值的文件。  如果 `modifiedDatetimeEnd` 具有日期/时间值，但 `modifiedDatetimeStart` 为 NULL，则意味着将选中“上次修改时间”属性小于该日期/时间值的文件。| 否 |
| format | 若要在基于文件的存储之间按原样复制文件（二进制副本），可以在输入和输出数据集定义中跳过格式节。<br/><br/>若要分析具有特定格式的文件，以下是受支持的文件格式类型：TextFormat、JsonFormat、AvroFormat、OrcFormat 和 ParquetFormat    。 请将格式中的“type”属性设置为上述值之一。 有关详细信息，请参阅[文本格式](supported-file-formats-and-compression-codecs-legacy.md#text-format)、[Json 格式](supported-file-formats-and-compression-codecs-legacy.md#json-format)、[Avro 格式](supported-file-formats-and-compression-codecs-legacy.md#avro-format)、[Orc 格式](supported-file-formats-and-compression-codecs-legacy.md#orc-format)和 [Parquet 格式](supported-file-formats-and-compression-codecs-legacy.md#parquet-format)部分。 |否（仅适用于二进制复制方案） |
| compression | 指定数据的压缩类型和级别。 有关详细信息，请参阅[受支持的文件格式和压缩编解码器](supported-file-formats-and-compression-codecs-legacy.md#compression-support)。<br/>支持的类型为 *GZip*、*Deflate*、*BZip2* 和 *ZipDeflate*。<br/>支持的级别为“最佳”和“最快”。 |否 |

>[!TIP]
>如需复制文件夹下的所有文件，请仅指定 *folderPath*。<br>若要复制具有指定名称的单个文件，请使用文件夹部分指定 folderPath 并使用文件名指定 fileName。<br>如需复制文件夹下的一部分文件，请使用文件夹部分指定 folderPath 并使用通配符筛选器指定 fileName。

>[!NOTE]
>如果你将“fileFilter”属性用于文件筛选器，则系统仍按原样支持它，但我们建议你从现在起使用添加到“fileName”的新筛选器功能。 

**示例：**

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

| 属性 | 描述 | 必需 |
|:--- |:--- |:--- |
| type | 复制活动源的 type 属性必须设置为 FileSystemSource |是 |
| recursive | 指示是要从子文件夹中以递归方式读取数据，还是只从指定的文件夹中读取数据。 当 recursive 设置为 true 且接收器是基于文件的存储时，将不会在接收器上复制或创建空的文件夹和子文件夹。<br/>允许的值为 true（默认值）和 false | 否 |
| maxConcurrentConnections | 可以同时连接到存储区存储的连接数。 仅在要限制与数据存储的并发连接时指定一个数字。 | 否 |

**示例：**

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
有关可供 Azure 数据工厂中的复制活动用作源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。
