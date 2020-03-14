---
title: 使用 Azure 数据工厂中的复制活动保存元数据和 Acl
description: 了解如何在复制过程中使用 Azure 数据工厂中的复制活动保存元数据和 Acl。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: jingwang
ms.openlocfilehash: 056909f5fd5838e5ae50fb84bd3535029d862acf
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79260834"
---
#  <a name="preserve-metadata-and-acls-using-copy-activity-in-azure-data-factory"></a>使用 Azure 数据工厂中的复制活动保存元数据和 Acl

使用 Azure 数据工厂复制活动将数据从源复制到接收器时，在以下情况下，还可以保留元数据和 Acl。

## <a name="preserve-metadata"></a>保留用于 lake 迁移的元数据

将数据从一个 data lake 迁移到另一个 data lake （包括[Amazon S3](connector-amazon-simple-storage-service.md)、 [Azure Blob](connector-azure-blob-storage.md)和[Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)）时，可以选择保留文件元数据和数据。

复制活动支持在数据复制期间保留以下属性：

- **所有客户指定的元数据** 
- 以下**五个数据存储内置系统属性**： `contentType`、`contentLanguage` （Amazon S3 除外）、`contentEncoding`、`contentDisposition`、`cacheControl`。

当你按原样将文件从 Amazon S3/Azure Data Lake Storage Gen2/Azure Blob 复制到具有二进制格式的 Azure Data Lake Storage Gen2/Azure Blob 时，你可以在 "活动创作" 的 "**复制活动** > **设置**" 选项卡上或复制数据工具中的 "**设置**" 页上找到**保留**选项。

![复制活动保留元数据](./media/copy-activity-preserve-metadata/copy-activity-preserve-metadata.png)

下面是复制活动 JSON 配置的示例（请参阅 `preserve`）： 

```json
"activities":[
    {
        "name": "CopyFromGen1ToGen2",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "BinarySource",
                "storeSettings": {
                    "type": "AmazonS3ReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "BinarySink",
                "storeSettings": {
                    "type": "AzureBlobFSWriteSettings"
                }
            },
            "preserve": [
                "Attributes"
            ]
        },
        "inputs": [
            {
                "referenceName": "<Binary dataset Amazon S3/Azure Blob/ADLS Gen2 source>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Binary dataset for Azure Blob/ADLS Gen2 sink>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="preserve-acls"></a>将 Acl 从 Data Lake Storage Gen1 保留到 Gen2

从 Azure Data Lake Storage Gen1 升级到 Gen2 时，可以选择保留 POSIX 访问控制列表（Acl）以及数据文件。 有关访问控制的详细信息，请参阅 Azure Data Lake Storage Gen2 中 Azure Data Lake Storage Gen1 和[access control](../storage/blobs/data-lake-storage-access-control.md)中的[访问控制](../data-lake-store/data-lake-store-access-control.md)。

复制活动支持在数据复制期间保留以下类型的 Acl。 您可以选择一个或多个类型：

- **ACL**：复制并保留对文件和目录的 POSIX 访问控制列表。 它将完整的现有 Acl 从源复制到接收器。 
- **所有者**：复制并保留文件和目录的拥有用户。 需要超级用户访问接收器 Data Lake Storage Gen2。
- **组**：复制并保留所属的文件和目录组。 需要超级用户对接收器 Data Lake Storage Gen2 或拥有用户的访问权限（如果拥有用户也是目标组的成员）。

如果指定从文件夹复制，则如果 `recursive` 设置为 true，则数据工厂将复制该给定文件夹的 Acl 以及该文件夹下的文件和目录。 如果指定从单个文件复制，则会复制该文件上的 Acl。

>[!NOTE]
>使用 ADF 将 Acl 保留 Data Lake Storage Gen1 到 Gen2 时，Gen2's 对应文件夹/文件上的现有 Acl 将被覆盖。

>[!IMPORTANT]
>选择保留 Acl 时，请确保为数据工厂授予足够高的权限，以便在接收器 Data Lake Storage Gen2 帐户上运行。 例如，使用帐户密钥身份验证，或将存储 Blob 数据所有者角色分配给服务主体或托管标识。

如果将源配置 Data Lake Storage Gen1 为二进制格式或二进制复制选项，并将 "Data Lake Storage Gen2 接收器" 设置为 "二进制格式" 或 "二进制复制" 选项，则可以在 "复制数据" 工具的 "**设置**" 页上或在活动创作的 "**复制活动**" > "**设置**" 选项卡上找到 "**保留**" 选项。

![Data Lake Storage Gen1 Gen2 保留 ACL](./media/connector-azure-data-lake-storage/adls-gen2-preserve-acl.png)

下面是复制活动 JSON 配置的示例（请参阅 `preserve`）： 

```json
"activities":[
    {
        "name": "CopyFromGen1ToGen2",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "BinarySource",
                "storeSettings": {
                    "type": "AzureDataLakeStoreReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "BinarySink",
                "storeSettings": {
                    "type": "AzureBlobFSWriteSettings"
                }
            },
            "preserve": [
                "ACL",
                "Owner",
                "Group"
            ]
        },
        "inputs": [
            {
                "referenceName": "<Binary dataset name for Azure Data Lake Storage Gen1 source>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Binary dataset name for Azure Data Lake Storage Gen2 sink>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="next-steps"></a>后续步骤

请参阅其他复制活动文章：

- [复制活动概述](copy-activity-overview.md)
- [复制活动性能](copy-activity-performance.md)
