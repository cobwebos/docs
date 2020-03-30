---
title: 使用 Azure 数据工厂中的复制活动保留元数据和 ACL
description: 了解如何使用 Azure 数据工厂中的复制活动在复制期间保留元数据和 ACL。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: jingwang
ms.openlocfilehash: b73cd73a18d286f221c7be2c624719e1d23d7c06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153822"
---
#  <a name="preserve-metadata-and-acls-using-copy-activity-in-azure-data-factory"></a>使用 Azure 数据工厂中的复制活动保留元数据和 ACL

当使用 Azure 数据工厂复制活动将数据从源复制到接收器时，在以下情况下，还可以保留元数据和 ACL。

## <a name="preserve-metadata-for-lake-migration"></a><a name="preserve-metadata"></a> 保留数据湖迁移的元数据

在将数据从一个数据湖迁移到另一个数据湖（包括 [Amazon S3](connector-amazon-simple-storage-service.md)、[Azure Blob](connector-azure-blob-storage.md) 和 [Azure data lake Storage Gen2](connector-azure-data-lake-storage.md)）时，可以选择保留文件元数据和数据。

复制活动支持在数据复制期间保留以下属性：

- **所有客户指定的元数据** 
- 和以下**五个数据存储内置系统属性**`contentType`： （`contentLanguage`亚马逊S3除外）， `contentEncoding` `contentDisposition`， `cacheControl`。

按原样将文件从 Amazon S3/Azure Data Lake Storage Gen2/Azure Blob 复制到二进制格式的 Azure Data Lake Storage Gen2/Azure Blob 时，可以在用于活动创作的“复制活动” > “设置”选项卡或复制数据工具的“设置”页上找到“保留”选项****************。

![复制活动保留元数据](./media/copy-activity-preserve-metadata/copy-activity-preserve-metadata.png)

下面是复制活动 JSON 配置的示例（请参阅 `preserve`）： 

```json
"activities":[
    {
        "name": "CopyAndPreserveMetadata",
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

## <a name="preserve-acls-from-data-lake-storage-gen1gen2-to-gen2"></a><a name="preserve-acls"></a>保留从数据存储湖存储第 1 代/第 2 代到第 2 代的 ACL

当您从 Azure 数据存储库第 1 代升级到第 2 代或在 ADLS Gen2 之间复制数据时，可以选择保留 POSIX 访问控制列表 （ACL） 以及数据文件。 有关访问控制的详细信息，请参阅[Azure 数据湖存储 Gen1 中的访问控制](../data-lake-store/data-lake-store-access-control.md)以及[Azure 数据湖存储 Gen2 中的访问控制](../storage/blobs/data-lake-storage-access-control.md)。

复制活动支持在数据复制期间保留以下类型的 ACL。 您可以选择一个或多个类型：

- **ACL**：复制并保留文件和目录上的 POSIX 访问控制列表。 它将完整的现有 ACL 从源复制到接收器。 
- **所有者**：复制并保留文件和目录的拥有用户。 需要超级用户访问接收器数据存储第 2 代。
- **组**：复制并保留拥有的文件和目录组。 需要超级用户访问接收器数据存储 Gen2 或拥有的用户（如果拥有的用户也是目标组的成员）。

如果指定从文件夹复制，数据工厂将复制该给定文件夹的 ACL 以及该文件夹下的文件和目录（如果`recursive`设置为 true）。 如果指定从单个文件复制，则复制该文件上的 ACL。

>[!NOTE]
>当您使用 ADF 保留从数据存储湖存储 Gen1/Gen2 到 Gen2 的 ACL 时，接收器 Gen2 对应文件夹/文件上的现有 ACL 将被覆盖。

>[!IMPORTANT]
>当您选择保留 ACL 时，请确保授予足够高的权限，以便数据工厂对接收器数据存储 Gen2 帐户进行操作。 例如，使用帐户密钥身份验证或将存储 Blob 数据所有者角色分配给服务主体或托管标识。

当您将源配置为具有二进制格式或二进制副本选项的数据存储湖存储 Gen1/Gen2，并且以二进制格式或二进制复制选项作为数据存储湖存储 Gen2 接收器时，您可以在"复制数据工具**中的设置"** 页上或活动创作的 **"复制活动** > **设置"** 选项卡上找到 **"保留**"选项。

![数据湖存储第 1 代/第 2 代保留 ACL](./media/connector-azure-data-lake-storage/adls-gen2-preserve-acl.png)

下面是复制活动 JSON 配置的示例（请参阅 `preserve`）： 

```json
"activities":[
    {
        "name": "CopyAndPreserveACLs",
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
                "referenceName": "<Binary dataset name for Azure Data Lake Storage Gen1/Gen2 source>",
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
